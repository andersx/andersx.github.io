---
layout: post
title: "cuRFP: Factorizing the largest possible dense matrix on a GPU - beyond cuBLAS"
date: 2026-04-18
lang: en
---

It is RAMageddon time and RAM is expensive these days, even more so on GPUs.
I wrote a small CUDA library to do matrix operations on symmetric matrices on GPU in rectangular full packed (RFP) format.
RFP format cuts memory usage from $$n^2$$ to $$n(n+1)/2$$ for common matrix operations on symmetric matrices like Cholesky factorization, certain inner products, etc.

My `cuRFP` library has virtually no overhead versus the standard cuBLAS library, just half the memory usage. 
The library has a PyTorch interface and is available on GitHub: [https://github.com/andersx/curfp](https://github.com/andersx/curfp)
#### Scaling beyond cuBLAS and PyTorch

Let's say that you want to solve problem like Kernel Ridge Regression (KRR).
This requires solving a matrix equation like

$$
\alpha = (K + \lambda I)^{-1}y
$$

The bottleneck for scaling this is the Cholesky factorization of the kernel matrix $K$, which needs to fit entirely in GPU memory.
`cuRFP` allows you to scale the problem size to twice the memory footprint compared to a dense matrix by storing the matrix in a packed format that reduces the memory usage from $$n^2$$ to $$n(n+1)/2$$.

I did some benchmarks of the Cholesky factorization with cuRFP versus CUDA's built-in dense Cholesky factorization in cuSOLVER, and PyTorch's wrapper around it.

I mostly use my RTX 5070 Ti GPU at home for hacking projects, and I am limited to 16 GB of memory. For bigger projects I've been using RunPod to rent H100s and H200s, but the price difference is significant, so I want to do as much as possible on my local GPU and fit on an H100 rather than an H200 if possible.

On an H200 with 141 GB of usable memory, here is how far I was able to get with each method:

| Method | Max size (H200) | Memory used |
|---|---|---|---|
| **cuRFP `SPFTRF`** | **262,144 × 262,144** | **~128 GB (packed)** |
| cuSOLVER `SPOTRF` | 185,362 × 185,362 | ~128 GB (dense) |
| PyTorch `torch.linalg.cholesky` | 131,072 × 131,072 | ~64 GB (dense + overhead) |


In the end I was able to fit a 262K $\times$ 262K matrix on an H200 and run the Cholesky factorization with `cuRFP`, which is close to the largest possible dense matrix that can fit in 141 GB of GPU memory.

I found substantial memory overhead in the PyTorch implementation, I believe there is a matrix copy even when calling `torch.linalg.cholesky(A, out=A)` and trying to do it in-place. This is a bit surprising since the PyTorch docs say that the `out` parameter should allow in-place operation, but I wasn't able to get it to work without the copy.

Calling cuSOLVER's `SPOTRF` directly from CUDA gives much better memory usage compared to PyTorch, but still hits the limit at 185K because of the dense storage format.

The plot below shows the timings and memory usage for the three methods on a range of matrix sizes.
There is slight additional overhead at small matrix sizes, this is negligible as the problem grows.

<div style="position:relative; left:50%; transform:translateX(-50%); width:min(100vw, 1200px); padding:0 20px; box-sizing:border-box;">
<div id="curfp-intro-plot" style="width:100%; height:500px;"></div>
</div>
<script>
  (function () {
    const plotId = 'curfp-intro-plot';
    const csvUrl = '{{ "/assets/benchmark_mini32.csv" | relative_url }}';
    const plotlyUrl = 'https://cdn.plot.ly/plotly-2.27.1.min.js';

    function loadPlotly() {
      if (window.Plotly) return Promise.resolve(window.Plotly);
      return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = plotlyUrl;
        script.onload = () => resolve(window.Plotly);
        script.onerror = () => reject(new Error('Failed to load Plotly.js'));
        document.head.appendChild(script);
      });
    }

    function parseCsv(text) {
      const lines = text.trim().split(/\r?\n/);
      const headers = lines[0].split(',');
      return lines.slice(1).map((line) => {
        const values = line.split(',');
        return headers.reduce((row, header, index) => {
          const value = values[index];
          row[header] = value === '' || value === undefined ? null : Number(value);
          return row;
        }, {});
      });
    }

    function timingTrace(rows, column, name, color, symbol) {
      const points = rows.filter((row) => row[column] > 0);
      return {
        x: points.map((row) => row.n),
        y: points.map((row) => row[column] / 1000),
        mode: 'lines+markers',
        name,
        line: { width: 2, dash: 'dash', color },
        marker: { size: 11, color, symbol },
        connectgaps: false,
        hovertemplate: 'n=%{x:,}<br>%{y:.4g} s<extra>' + name + '</extra>',
        xaxis: 'x', yaxis: 'y'
      };
    }

    function memTrace(rows, column, name, color, symbol) {
      const points = rows.filter((row) => row[column] > 0);
      return {
        x: points.map((row) => row.n),
        y: points.map((row) => row[column]),
        mode: 'lines+markers',
        name,
        line: { width: 2, dash: 'dash', color },
        marker: { size: 11, color, symbol },
        connectgaps: false,
        showlegend: false,
        hovertemplate: 'n=%{x:,}<br>%{y:.3g} GB<extra>' + name + '</extra>',
        xaxis: 'x2', yaxis: 'y2'
      };
    }

    Promise.all([loadPlotly(), fetch(csvUrl).then((r) => r.text())])
      .then(([, csvText]) => {
        const rows = parseCsv(csvText);
        const tickvals = rows.map((row) => row.n);
        const ticktext = tickvals.map((v) => String(v));

        const axisDefaults = {
          type: 'log',
          tickmode: 'array',
          tickvals,
          ticktext,
          gridcolor: '#333333',
          zerolinecolor: '#333333'
        };

        Plotly.newPlot(
          plotId,
          [
            timingTrace(rows, 'spftrf_rfp_ms',      'cuRFP SPFTRF',     '#1f77b4', 'circle'),
            timingTrace(rows, 'spotrf_cusolver_ms',  'cuSOLVER POTRF',   '#76b900', 'square'),
            timingTrace(rows, 'cholesky_torch_ms',   'PyTorch Cholesky', '#d62728', 'triangle-up'),
            memTrace(rows, 'rfp_mem_gb',  'cuRFP (RFP)',  '#1f77b4', 'circle'),
            memTrace(rows, 'dns_mem_gb',  'Dense',        '#aaaaaa',  'square')
          ],
          {
            grid: { rows: 1, columns: 2, pattern: 'independent' },
            paper_bgcolor: '#000000',
            plot_bgcolor: '#000000',
            font: { color: '#f2f2f2' },
            annotations: [
              { text: 'Cholesky factorization time', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.22, y: 1.08, font: { size: 13 } },
              { text: 'GPU memory usage', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.78, y: 1.08, font: { size: 13 } },
              { text: 'NVIDIA H200 · single precision (fp32) · up to 262,144 × 262,144', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.5, y: 1.18, font: { size: 18, color: '#aaaaaa' } }
            ],
            xaxis:  { ...axisDefaults, title: { text: 'Matrix dimension n' } },
            yaxis:  { title: { text: 'Time (s)' }, type: 'log', dtick: 1,
                      gridcolor: '#333333', zerolinecolor: '#333333' },
            xaxis2: { ...axisDefaults, title: { text: 'Matrix dimension n' } },
            yaxis2: { title: { text: 'Memory (GB)' },
                      gridcolor: '#333333', zerolinecolor: '#333333' },
            legend: {
              orientation: 'h', yanchor: 'bottom', y: -0.22,
              xanchor: 'center', x: 0.5
            },
            margin: { l: 70, r: 30, t: 80, b: 80 },
            hovermode: 'x unified'
          },
          { responsive: true, displayModeBar: false }
        );
      })
      .catch((error) => {
        const plot = document.getElementById(plotId);
        if (plot) plot.innerHTML = '<p><em>Failed to load benchmark plot.</em></p>';
        console.error(error);
      });
  })();
</script>

<br>

The Python interface for `cuRFP` is compatible with PyTorch, and most functions should be drop-in compatible with torch functions. Here's a small example to solve a linear system with a symmetric positive definite matrix using `cuRFP`.

```python
import torch
import curfp

n, k, nrhs = 4096, 128, 10

A = torch.randn(n, k, dtype=torch.float32, device="cuda") / k**0.5
C = torch.empty(n * (n + 1) // 2, dtype=torch.float32, device="cuda")
B = torch.randn(nrhs, n, dtype=torch.float32, device="cuda")

# Symmetric rank-k update: C = A @ A.T in RFP format
curfp.ssfrk(A, C)

# Regularize: C += I  (ensures positive definiteness)
curfp.add_to_diagonal(C, 1.0)

# Cholesky factorization in-place
curfp.spftrf(C)

# Solve (A @ A.T + I) @ X = B  in-place on B  (B is nrhs×n, rows are RHS)
curfp.spftrs(C, B)

```
The function names follow the name conventions from BLAS/LAPACK, so they might not always be totally obvious.
I mirrored every function from BLAS/LAPACK that operates on symmetric matrices, so check out the docs [https://github.com/andersx/curfp](https://github.com/andersx/curfp) for a full list of functions.

I did add one quality of life upgrade from BLAS notation, and that is that matrix dimensions etc are autodetected, so mostly you just need to call a function with one or two arguments.

#### OK, so what is Rectangular Full Packed (RFP) format?

The RFP format is a neat trick to store an $n \times n$ symmetric matrix using only $n(n+1)/2$ floats — exactly half the storage of a full dense matrix. The trick is packing the memory in such a way that standard BLAS/LAPACK operations can be applied directly to the packed submatrices by adjusting stride and dimension parameters. Matrix operations on RFP matrices require the same FLOPs as their dense counterparts, so there is no computational penalty, only the minuscule overhead of 2–3 extra BLAS calls.

Let's say we have a $5 \times 5$ symmetric matrix stored with the upper triangle convention. We will use <span style="color:#e06c75">red</span>, <span style="color:#61afef">blue</span>, and <span style="color:#98c379">green</span> to label the three blocks consistently throughout this post. For odd $n$, the RFP layout splits the matrix into two triangular diagonal blocks and one rectangular off-diagonal block:

$$
A = \left[
\begin{array}{ccccc}
\color{red}{11} & \color{red}{12} & \color{red}{13} & \color{green}{14} & \color{green}{15} \\
 & \color{red}{22} & \color{red}{23} & \color{green}{24} & \color{green}{25} \\
 &  & \color{red}{33} & \color{green}{34} & \color{green}{35} \\
 &  &  & \color{blue}{44} & \color{blue}{45} \\
 &  &  &  & \color{blue}{55}
\end{array}
\right]
$$

Here the red and blue blocks are symmetric diagonal blocks, and the green block is a rectangular off-diagonal block.

We can visualize this in RFP format:

$$
\operatorname{RFP}(A) = \left[
\begin{array}{ccc}
\color{red}{11} & \color{blue}{44} & \color{blue}{45} \\
\color{red}{12} & \color{red}{22} & \color{blue}{55} \\
\color{red}{13} & \color{red}{23} & \color{red}{33} \\
\color{green}{14} & \color{green}{24} & \color{green}{34} \\
\color{green}{15} & \color{green}{25} & \color{green}{35}
\end{array}
\right]
$$

The top $3 \times 3$ part contains the two triangular diagonal blocks folded together, while the bottom $2 \times 3$ part is the rectangular green coupling block.

The packing is slightly different for even $n$ (the fold point shifts by one row). Combined with the upper/lower triangle convention and an optional transposition of the whole packed rectangle (`transr`), there are 8 RFP layout variants in total. `cuRFP` handles all of them through a single parameter struct so the calling code never has to think about it.

#### Example 1: Symmetric rank-k update in RFP format: `SFRK`

This is probably the simplest example to show how RFP is implemented in practice.
Let's say you want to compute a symmetric matrix product $A = X X^\top$. If you do this with BLAS you have a few options:

- **GEMM:** Standard matrix-matrix multiplication which results in a full $n \times n$ matrix.
- **SYRK:** Better option — only computes the upper or lower triangle of the result and uses half the FLOPs, but still requires $n^2$ memory.
- **SFRK:** Computes the result directly in RFP format, giving the same FLOPs as SYRK but only half the memory. Available in MKL and Apple Accelerate; `cuRFP` brings it to CUDA.

To see why this works, split $X$ into the same row blocks as the RFP matrix:

$$
X = \begin{bmatrix}
X_1 \\
X_2
\end{bmatrix}
$$

where for the $5 \times 5$ example above, $X_1$ has 3 rows and $X_2$ has 2 rows. Then

$$
A = X X^\top =
\begin{bmatrix}
X_1 \\
X_2
\end{bmatrix}
\begin{bmatrix}
X_1^\top & X_2^\top
\end{bmatrix}
=
\begin{bmatrix}
\color{red}{X_1 X_1^\top} & \color{green}{X_1 X_2^\top} \\
\color{green}{X_2 X_1^\top} & \color{blue}{X_2 X_2^\top}
\end{bmatrix}.
$$

This maps directly to three BLAS calls:

$$
{\color{red} X_1 X_1^\top} \leftarrow \operatorname{SYRK}(X_1),
\qquad
{\color{green} X_2 X_1^\top} \leftarrow \operatorname{GEMM}(X_2, X_1^\top),
\qquad
{\color{blue} X_2 X_2^\top} \leftarrow \operatorname{SYRK}(X_2).
$$

So instead of forming one large dense matrix, `SFRK` computes two symmetric rank-$k$ updates for the diagonal blocks and one ordinary matrix multiply for the rectangular off-diagonal block. The RFP layout ensures the triangular diagonal blocks are stored such that SYRK can write results directly to the correct memory locations when stride and dimension parameters are set correctly — so `SFRK` is really just three BLAS calls.

In my code this is roughly how it looks (there is a bunch of code to determine the correct block sizes and offsets for the different RFP layouts, but the core of the `SFRK` operation is these three calls):
```cuda
    /* symmetric rank-k update on red diagonal block */
    cublasSsyrk(cb, p.fill1, opA,
        p.dim1, k, alpha, A1, lda, beta, C + p.off1, p.ldc);

    /* sgemm on green off-diagonal block */
    cublasSgemm(cb, opA, opAt,
        p.gemm_m, p.gemm_n, k,
        alpha, Ag1, lda, Ag2, lda, beta, C + p.offg, p.ldc);

    /* symmetric rank-k update on blue diagonal block */
    cublasSsyrk(cb, p.fill2, opA,
        p.dim2, k, alpha, A2, lda, beta, C + p.off2, p.ldc);
```
This has the same FLOPs as a normal symmetric matrix multiplication done with a big `SYRK` call, but only uses half the memory.
The only minor overhead comes from the extra logic to determine the correct parameters, and the additional overhead for the two extra BLAS calls, but in practice this is negligible compared to the memory savings and the fact that you can now fit much larger matrices on the GPU.

I like this approach a lot, since it will continue to be very performant in the future, as any improvements to `cuBLAS` will directly be used in `cuRFP`.

Below are the timings for `cuRFP`'s `SFRK` versus a dense `SYRK` in cuBLAS, and PyTorch's `mm` (which computes the full dense product) on an H200 GPU.
PyTorch has no direct counterpart to `SYRK` so I felt like the user might resort to using `torch.mm()` here.


<div id="curfp-sfrk-plot" style="width:100%; height:600px;"></div>
<script>
  (function () {
    const plotId = 'curfp-sfrk-plot';
    const csvUrl = '{{ "/assets/benchmark_mini32.csv" | relative_url }}';
    const plotlyUrl = 'https://cdn.plot.ly/plotly-2.27.1.min.js';

    function loadPlotly() {
      if (window.Plotly) return Promise.resolve(window.Plotly);
      return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = plotlyUrl;
        script.onload = () => resolve(window.Plotly);
        script.onerror = () => reject(new Error('Failed to load Plotly.js'));
        document.head.appendChild(script);
      });
    }

    function parseCsv(text) {
      const lines = text.trim().split(/\r?\n/);
      const headers = lines[0].split(',');
      return lines.slice(1).map((line) => {
        const values = line.split(',');
        return headers.reduce((row, header, index) => {
          const value = values[index];
          row[header] = value === '' || value === undefined ? null : Number(value);
          return row;
        }, {});
      });
    }

    function trace(rows, column, name, color, symbol) {
      const points = rows.filter((row) => row[column] > 0);
      return {
        x: points.map((row) => row.n),
        y: points.map((row) => row[column] / 1000),
        mode: 'lines+markers',
        name,
        line: { width: 2, dash: 'dash', color },
        marker: { size: 11, color, symbol },
        connectgaps: false,
        hovertemplate: 'n=%{x:,}<br>%{y:.4g} s<extra>' + name + '</extra>'
      };
    }

    Promise.all([loadPlotly(), fetch(csvUrl).then((r) => r.text())])
      .then(([, csvText]) => {
        const rows = parseCsv(csvText);
        const tickvals = rows.map((row) => row.n);
        const ticktext = tickvals.map((v) => String(v));

        Plotly.newPlot(
          plotId,
          [
            trace(rows, 'ssfrk_rfp_ms', 'cuRFP SFRK',      '#1f77b4', 'circle'),
            trace(rows, 'ssyrk_cb_ms',  'cuBLAS SYRK',     '#76b900', 'square'),
            trace(rows, 'mm_torch_ms',  'PyTorch mm',       '#d62728', 'triangle-up')
          ],
          {
            paper_bgcolor: '#000000',
            plot_bgcolor: '#000000',
            font: { color: '#f2f2f2' },
            annotations: [
              { text: 'Symmetric rank-k update (SFRK) timing · NVIDIA H200 · fp32', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.5, y: 1.12, font: { size: 18, color: '#aaaaaa' } }
            ],
            xaxis: {
              title: { text: 'Matrix dimension n' },
              type: 'log',
              tickmode: 'array',
              tickvals,
              ticktext,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            yaxis: {
              title: { text: 'Time (s)' },
              type: 'log',
              dtick: 1,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            legend: {
              orientation: 'h', yanchor: 'bottom', y: -0.25,
              xanchor: 'center', x: 0.5
            },
            margin: { l: 70, r: 30, t: 60, b: 80 },
            hovermode: 'x unified'
          },
          { responsive: true, displayModeBar: false }
        );
      })
      .catch((error) => {
        const plot = document.getElementById(plotId);
        if (plot) plot.innerHTML = '<p><em>Failed to load benchmark plot.</em></p>';
        console.error(error);
      });
  })();
</script>

<br>
The timings for `cuRFP` are very close to `cuBLAS` `SYRK`, so the three cuBLAS calls don't seem to have any detrimental effect at larger problem sizes.
Both `cuBLAS` and PyTorch only scale to n = 185K on an H200 as expected.
PyTorch is 2x slower since it computes both the upper and lower triangle via cuBLAS `GEMM`.

#### Example 2: Cholesky factorization

Now we are getting to the meat of it.
I really wanted to do this to be able to factorize larger matrices for kernel-ridge regression and Gaussian-process regression on GPUs.

The RFP approach can also be applied to Cholesky factorization, which is the main bottleneck for training and optimizing kernel-based methods.

The Cholesky factorization of a symmetric positive definite matrix $A$ is given by $A = L L^\top$, where $L$ is a lower triangular matrix.

We can apply the same block-decomposition trick. Splitting $A$ into the same red upper-left, blue lower-right, and green off-diagonal blocks, the factorization becomes:

$$
\begin{pmatrix}
{\color{red}A_{11}} & {\color{green}A_{21}^\top} \\
{\color{green}A_{21}} & {\color{blue}A_{22}}
\end{pmatrix}
=
\begin{pmatrix}
{\color{red}L_{11}} &  \\
{\color{green}L_{21}} & {\color{blue}L_{22}}
\end{pmatrix}
\begin{pmatrix}
{\color{red}L_{11}^\top} & {\color{green}L_{21}^\top} \\
 & {\color{blue}L_{22}^\top}
\end{pmatrix}
$$

where ${\color{red}L_{11}}$ is the lower-triangular Cholesky factor of the red diagonal block, ${\color{blue}L_{22}}$ is the lower-triangular factor of the blue diagonal block, and ${\color{green}L_{21}}$ is the dense green off-diagonal block of $L$.

Multiplying the two block matrices on the right and matching against $A$ gives three equations:

$$
{\color{red}L_{11} L_{11}^\top} = {\color{red}A_{11}}
$$

$$
{\color{green}L_{21}}\, {\color{red}L_{11}^\top} = {\color{green}A_{21}}
$$

$$
{\color{blue}L_{22} L_{22}^\top} = {\color{blue}A_{22}} - {\color{green}L_{21} L_{21}^\top}
$$

Each of these maps directly to one BLAS or cuSOLVER call, in this exact order:

$$
{\color{red}L_{11}} \;\;\leftarrow\;\; \operatorname{POTRF}({\color{red}A_{11}})
$$

$$
{\color{green}L_{21}} \;\;\leftarrow\;\; \operatorname{TRSM}({\color{red}L_{11}},\, {\color{green}A_{21}})
$$

$$
{\color{blue}A_{22}} \;\;\leftarrow\;\; {\color{blue}A_{22}} - {\color{green}L_{21} L_{21}^\top} \quad \text{in-place via} \;\operatorname{SYRK}
$$

$$
{\color{blue}L_{22}} \;\;\leftarrow\;\; \operatorname{POTRF}({\color{blue}A_{22}})
$$

The RFP layout makes sure that the three blocks can be indexed contiguously with a bit of logic to determine the offsets and strides.

In the end the `SPFTRF` routine for Cholesky factorization in `cuRFP` looks something like this:

```cuda
    /* Step 1: Cholesky of red diagonal block */
    cusolverDnSpotrf(cs, p.fill1, p.dim1,
        C + p.off1, p.ldc, work, lwork, devInfo);

    /* Step 2: triangular solve for green off-diagonal block */
    cublasStrsm(cb, CUBLAS_SIDE_RIGHT, p.fill1, CUBLAS_OP_T, CUBLAS_DIAG_NON_UNIT,
        p.gemm_m, p.dim1, &one, C + p.off1, p.ldc, C + p.offg, p.ldc);

    /* Step 3: rank-k update on blue diagonal block */
    cublasSsyrk(cb, p.fill2, CUBLAS_OP_N,
        p.dim2, p.dim1, &m_one, C + p.offg, p.ldc, &one, C + p.off2, p.ldc);

    /* Step 4: Cholesky of updated blue diagonal block */
    cusolverDnSpotrf(cs, p.fill2, p.dim2,
        C + p.off2, p.ldc, work, lwork, devInfo);
```

Here are the timings I got from running cuRFP on a H200 on my RunPod:
Again, there is no real timing difference between pytorch, directly calling cuBLAS or cuRFP.
But `curfp` can go further.

<div id="curfp-chol-plot" style="width:100%; height:600px;"></div>
<script>
  (function () {
    const plotId = 'curfp-chol-plot';
    const csvUrl = '{{ "/assets/benchmark_mini32.csv" | relative_url }}';
    const plotlyUrl = 'https://cdn.plot.ly/plotly-2.27.1.min.js';

    function loadPlotly() {
      if (window.Plotly) return Promise.resolve(window.Plotly);
      return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = plotlyUrl;
        script.onload = () => resolve(window.Plotly);
        script.onerror = () => reject(new Error('Failed to load Plotly.js'));
        document.head.appendChild(script);
      });
    }

    function parseCsv(text) {
      const lines = text.trim().split(/\r?\n/);
      const headers = lines[0].split(',');
      return lines.slice(1).map((line) => {
        const values = line.split(',');
        return headers.reduce((row, header, index) => {
          const value = values[index];
          row[header] = value === '' || value === undefined ? null : Number(value);
          return row;
        }, {});
      });
    }

    function trace(rows, column, name, color, symbol) {
      const points = rows.filter((row) => row[column] > 0);
      return {
        x: points.map((row) => row.n),
        y: points.map((row) => row[column] / 1000),
        mode: 'lines+markers',
        name,
        line: { width: 2, dash: 'dash', color },
        marker: { size: 11, color, symbol },
        connectgaps: false,
        hovertemplate: 'n=%{x:,}<br>%{y:.4g} s<extra>' + name + '</extra>'
      };
    }

    Promise.all([loadPlotly(), fetch(csvUrl).then((r) => r.text())])
      .then(([, csvText]) => {
        const rows = parseCsv(csvText);
        const tickvals = rows.map((row) => row.n);
        const ticktext = tickvals.map((v) => String(v));

        Plotly.newPlot(
          plotId,
          [
            trace(rows, 'spftrf_rfp_ms',     'cuRFP SPFTRF',     '#1f77b4', 'circle'),
            trace(rows, 'spotrf_cusolver_ms', 'cuSOLVER SPOTRF',  '#76b900', 'square'),
            trace(rows, 'cholesky_torch_ms',  'PyTorch Cholesky', '#d62728', 'triangle-up')
          ],
          {
            paper_bgcolor: '#000000',
            plot_bgcolor: '#000000',
            font: { color: '#f2f2f2' },
            annotations: [
              { text: 'Cholesky factorization timing · NVIDIA H200 · fp32', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.5, y: 1.12, font: { size: 18, color: '#aaaaaa' } }
            ],
            xaxis: {
              title: { text: 'Matrix dimension n' },
              type: 'log',
              tickmode: 'array',
              tickvals,
              ticktext,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            yaxis: {
              title: { text: 'Time (s)' },
              type: 'log',
              dtick: 1,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            legend: {
              orientation: 'h', yanchor: 'bottom', y: -0.25,
              xanchor: 'center', x: 0.5
            },
            margin: { l: 70, r: 30, t: 60, b: 80 },
            hovermode: 'x unified'
          },
          { responsive: true, displayModeBar: false }
        );
      })
      .catch((error) => {
        const plot = document.getElementById(plotId);
        if (plot) plot.innerHTML = '<p><em>Failed to load benchmark plot.</em></p>';
        console.error(error);
      });
  })();
</script>

<br>
To actually solve a linear system with the Cholesky factorization, you can use the `SPFTRS` routine in `cuRFP`, which performs a triangular solve using the Cholesky factor in RFP format. See the earlier code snippet for an example of how to use it.

The scaling result is similar to the two previous examples, with `cuRFP` scaling to larger matrices than cuBLAS and PyTorch with no overhead.

<div id="curfp-solve-plot" style="width:100%; height:600px;"></div>
<script>
  (function () {
    const plotId = 'curfp-solve-plot';
    const csvUrl = '{{ "/assets/benchmark_mini32.csv" | relative_url }}';
    const plotlyUrl = 'https://cdn.plot.ly/plotly-2.27.1.min.js';

    function loadPlotly() {
      if (window.Plotly) return Promise.resolve(window.Plotly);
      return new Promise((resolve, reject) => {
        const script = document.createElement('script');
        script.src = plotlyUrl;
        script.onload = () => resolve(window.Plotly);
        script.onerror = () => reject(new Error('Failed to load Plotly.js'));
        document.head.appendChild(script);
      });
    }

    function parseCsv(text) {
      const lines = text.trim().split(/\r?\n/);
      const headers = lines[0].split(',');
      return lines.slice(1).map((line) => {
        const values = line.split(',');
        return headers.reduce((row, header, index) => {
          const value = values[index];
          row[header] = value === '' || value === undefined ? null : Number(value);
          return row;
        }, {});
      });
    }

    function trace(rows, column, name, color, symbol) {
      const points = rows.filter((row) => row[column] > 0);
      return {
        x: points.map((row) => row.n),
        y: points.map((row) => row[column] / 1000),
        mode: 'lines+markers',
        name,
        line: { width: 2, dash: 'dash', color },
        marker: { size: 11, color, symbol },
        connectgaps: false,
        hovertemplate: 'n=%{x:,}<br>%{y:.4g} s<extra>' + name + '</extra>'
      };
    }

    Promise.all([loadPlotly(), fetch(csvUrl).then((r) => r.text())])
      .then(([, csvText]) => {
        const rows = parseCsv(csvText);
        const tickvals = rows.map((row) => row.n);
        const ticktext = tickvals.map((v) => String(v));

        Plotly.newPlot(
          plotId,
          [
            trace(rows, 'spftrs_rfp_ms',     'cuRFP SPFTRS',    '#1f77b4', 'circle'),
            trace(rows, 'spotrs_cusolver_ms', 'cuSOLVER SPOTRS', '#76b900', 'square'),
            trace(rows, 'solve_torch_ms',     'PyTorch solve',   '#d62728', 'triangle-up')
          ],
          {
            paper_bgcolor: '#000000',
            plot_bgcolor: '#000000',
            font: { color: '#f2f2f2' },
            annotations: [
              { text: 'Triangular solve (SPFTRS) timing · NVIDIA H200 · nrhs=64 · fp32', showarrow: false,
                xref: 'paper', yref: 'paper', x: 0.5, y: 1.12, font: { size: 18, color: '#aaaaaa' } }
            ],
            xaxis: {
              title: { text: 'Matrix dimension n' },
              type: 'log',
              tickmode: 'array',
              tickvals,
              ticktext,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            yaxis: {
              title: { text: 'Time (s)' },
              type: 'log',
              dtick: 1,
              gridcolor: '#333333',
              zerolinecolor: '#333333'
            },
            legend: {
              orientation: 'h', yanchor: 'bottom', y: -0.25,
              xanchor: 'center', x: 0.5
            },
            margin: { l: 70, r: 30, t: 60, b: 80 },
            hovermode: 'x unified'
          },
          { responsive: true, displayModeBar: false }
        );
      })
      .catch((error) => {
        const plot = document.getElementById(plotId);
        if (plot) plot.innerHTML = '<p><em>Failed to load benchmark plot.</em></p>';
        console.error(error);
      });
  })();
</script>

#### Installation

I plan to make this a pip package, but for now I am lacking a way to do CI testing with GPU support, so for now you will have to do something like:

```bash
git clone https://github.com/andersx/curfp.git
uv pip install -e curfp

```
Requirements are pretty mild: A working PyTorch installation with CUDA 12.8 or later.


#### Supported operations

`cuRFP` implements the following functions, all operating directly on the packed RFP buffer with no pack/unpack step:

All functions are available in both single (`s` prefix) and double (`d` prefix) precision, e.g. `ssfrk` / `dsfrk`, `spftrf` / `dpftrf`, etc.

| Function | Formula | Description |
|---|---|---|
| `ssfrk` / `dsfrk` | $C \leftarrow \alpha A A^\top + \beta C$ | Symmetric rank-$k$ update into RFP |
| `ssfr` / `dsfr` | $C \leftarrow \alpha x x^\top + C$ | Symmetric rank-1 update into RFP |
| `ssfr2` / `dsfr2` | $C \leftarrow \alpha (x y^\top + y x^\top) + C$ | Symmetric rank-2 update into RFP |
| `ssfr2k` / `dsfr2k` | $C \leftarrow \alpha (A B^\top + B A^\top) + \beta C$ | Symmetric rank-2$k$ update into RFP |
| `ssfmm` / `dsfmm` | $C \leftarrow \alpha A B + \beta C$ | Symmetric matrix-matrix multiply ($A$ in RFP) |
| `ssfmv` / `dsfmv` | $y \leftarrow \alpha A x + \beta y$ | Symmetric matrix-vector multiply ($A$ in RFP) |
| `spftrf` / `dpftrf` | $A = L L^\top$ | In-place Cholesky factorization in RFP |
| `spftrs` / `dpftrs` | $A X = B$ | Triangular solve using RFP Cholesky factor |
| `spftri` / `dpftri` | $A \leftarrow A^{-1}$ | Matrix inversion from RFP Cholesky factor |
| `slansf` / `dlansf` | $\|A\|$ | Matrix norm (max, 1-norm, or Frobenius) of RFP matrix |
| `spfcon` / `dpfcon` | $\kappa^{-1} = 1 / (\|A^{-1}\|_1 \cdot \|A\|_1)$ | Reciprocal condition number from Cholesky factor |
| `strttf` / `dtrttf` | — | Convert full triangular matrix → RFP |
| `stfttr` / `dtfttr` | — | Convert RFP matrix → full triangular |
| `rfp_diag_indices` | — | Flat indices of diagonal elements in RFP array |
| `add_to_diagonal` | $A \leftarrow A + \lambda I$ | Add scalar to diagonal in-place (e.g. regularization) |

All functions support all 8 RFP storage variants (`transr` × `uplo` × n parity) and accept an optional CUDA stream for asynchronous execution.

#### Conclusion

The library is in a good working state, and is available at [https://github.com/andersx/curfp](https://github.com/andersx/curfp) under the MIT license. I would greatly appreciate any PRs or open issues.

I am still figuring out a sustainable way to do the GPU-based CI testing and release a pip package, but in the meantime try it out and save yourself memory!

*-- Anders*
