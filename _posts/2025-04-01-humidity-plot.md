---
layout: post
title: "Live Humidity Plot"
date: 2025-04-01
---

This interactive Plotly chart shows real-time data pulled directly from a live CSV file hosted on GitHub.

I wanted a simple but robust setup to continuously log humidity (and temperature) data from an ESP32-based sensor, store it in a CSV file, and push it to GitHub where it could be visualized and accessed easily.

The data comes from a sensor connected to an ESP32 microcontroller.
Live output is served via JSON/requests from webserver running on the ESP32.
My Raspberry Pi collects this data every minutes via a cron job.
Every 1 hour, my bot [andersx-bot](https://github.com/andersx-bot/) automatically pushes it to a GitHub repository:
👉 [View the CSV on GitHub](https://github.com/andersx/data-upload)

This keeps things super lightweight and easy to scrape.

Rather than generating static plots, this page dynamically fetches the latest CSV data from GitHub and visualizes it using Plotly.js — entirely in your browser.

<iframe src="/assets/humidity_plot.html" width="100%" height="650" style="border:none;"></iframe>

---

### How it works

- **Hardware**: ESP32 with a humidity sensor - served data in JSON format over HTTP
- **Collector**: Raspberry Pi pulls data every 5 minuts via a cron job
- **Uploader**: GitHub bot commits+pushes to the CSV every few minutes
- **Frontend**: JavaScript fetches and visualizes the data live with Plotly on this page

This setup ensures the chart is always up-to-date without needing server-side rendering or plot regeneration, and cleanly separates the data collection from the visualization.
