---
layout: post
title: "Live Humidity Plot"
date: 2025-04-01
---

Curious about the humidity levels around my workspace? This interactive Plotly chart shows real-time data pulled directly from a live CSV file hosted on GitHub.

The data comes from a sensor connected to an ESP32 microcontroller. My Raspberry Pi collects this data and, via [andersx-bot](https://github.com/andersx-bot/), periodically pushes it to a GitHub repository:  
👉 [View the CSV on GitHub](https://github.com/andersx/data-upload)

Rather than generating static plots, this page dynamically fetches the latest CSV data and visualizes it using Plotly.js — entirely in your browser.

Check it out below:

<iframe src="/assets/humidity_plot.html" width="100%" height="650" style="border:none;"></iframe>

---

### How it works

- **Hardware**: ESP32 with a humidity sensor
- **Collector**: Raspberry Pi pulling data over serial or MQTT
- **Uploader**: GitHub bot commits to the CSV every few minutes
- **Frontend**: JavaScript fetches and visualizes the data live with Plotly

This setup ensures the chart is always up-to-date without needing server-side rendering or plot regeneration, and cleanly separates the data collection from the visualization.
