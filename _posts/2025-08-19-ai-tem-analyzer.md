---
layout: post
title: "Automating Catalyst Characterization: An AI-Powered Tool for TEM Particle Size Analysis"
date: 2025-08-19
image: "https://i.postimg.cc/QCj7YkNc/Screenshot-2025-08-19-at-5-42-36-PM.png"
image_alt: "AI Analyzer App"
summary: "A walk-through from dataset annotation to a working desktop app for TEM particle measurements."
---




A walk through how I built a custom computer vision tool, starting with manual data annotation and ending with a working desktop app that makes TEM particle measurements fast and reliable.

## The High Cost of a Simple Question

In materials science, a deceptively simple question shapes the outcome of an experiment: “What is the size distribution of my nanoparticles?” Transmission Electron Microscopy (TEM) images hold that answer, but turning those images into meaningful numbers has long been a frustrating bottleneck.

Manually outlining hundreds of particles is not only slow, it also introduces inconsistency. Results vary depending on who’s doing the tracing, making the data less reliable. I built this tool to get around that barrier: replacing a time-consuming, error-prone process with one that is objective, automated, and dramatically faster.

## The Development Journey: A Three-Step Process

### Step 1: Teaching the AI with Professional Tools

An AI model is only as good as the data it learns from. To build a solid foundation, I turned to **Roboflow**, a platform for managing computer vision datasets. I uploaded 80 TEM images covering the range of our samples, from 5 nm to 50 nm scale bars, and drew polygon masks around several thousand particles. Roboflow’s annotation and versioning tools streamlined the process, and just as importantly, made sure the dataset was clean and reproducible.



<img src="https://i.postimg.cc/L43Vph1x/Annotated-data.png" alt="Side-by-side comparison of a raw TEM image and the same image with manual annotations" class="rounded-lg border">
<p class="image-caption">Left: a raw TEM image. Right: the same image annotated in Roboflow, with each particle outlined by hand.</p>

### Step 2: Training a "Specialist" Model in the Cloud

Training a deep learning model takes a lot of computing power. To get access to GPUs without the price tag, I used Google Colab and the Detectron2 framework. Detectron2’s Mask R-CNN architecture is well-suited for instance segmentation, identifying and outlining individual objects in an image.

I fine-tuned the model on my annotated dataset until it could reliably pick out particles on images it had never seen before. The trained network was then saved into a single portable file, `model_final.pth`, which became the core “brain” of the application.

<img src="https://i.postimg.cc/1XmwtcqJ/model-predicted-data.png" alt="The trained AI model identifying particles on a new TEM image" class="rounded-lg border">
<p class="image-caption">The trained model applied to a new TEM image: particles automatically identified and segmented.</p>

### Step 3: From a Model File to an Intuitive Desktop App

A model file on its own isn’t very practical for day-to-day lab work. The final step was building a simple desktop application in Python using Tkinter. This app acts as a wrapper around the AI model: it loads `model_final.pth`, processes the user’s TEM images, runs predictions, and turns the raw segmentation masks into useful outputs like particle diameters, summary statistics, and histograms. What used to be a command-line workflow is now point-and-click.

## The Application in Action

In practice, the workflow is straightforward: select a folder of images, set the scale with two clicks, and press “Analyze.” Within seconds, the app generates side-by-side images for review, real-time stats like mean and standard deviation, and a particle size distribution histogram. If the AI mislabels something, it can be corrected with a click, and all results update instantly. Exporting the data is just as quick.

<iframe width="560" height="315" 
  src="https://www.youtube.com/embed/CMIcvvrr1Os?si=Hh0Pfk-wD5tQF4oS"
  title="YouTube video player"
  frameborder="0" 
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
  referrerpolicy="strict-origin-when-cross-origin" 
  allowfullscreen>
</iframe>

## Challenges & Solutions

Like any AI model, this one has its limitations. The key was to acknowledge them and design the app so they didn’t get in the way of reliable results.

**Challenge 1: False Positives.**  
Occasionally the AI confuses the carbon support film or an imaging artifact for a particle. To catch these cases, I added a quick review step: users can click any incorrectly identified particle to remove it, with all plots and stats updating in real time.

**Challenge 2: Overlapping Particles.**  
Dense, overlapping clusters are the hardest cases. The model sometimes merges particles together or misses a few. While it performs very well on dispersed samples, I’m planning to add a manual “draw your own mask” feature so users can quickly add particles the AI overlooks. This will make the tool more flexible for challenging datasets.

## Technology Stack

<div class="flex flex-wrap">
  <span class="tag">Python</span>
  <span class="tag">PyTorch</span>
  <span class="tag">Detectron2</span>
  <span class="tag">Google Colab</span>
  <span class="tag">Roboflow</span>
  <span class="tag">OpenCV</span>
  <span class="tag">Tkinter</span>
  <span class="tag">Matplotlib</span>
  <span class="tag">NumPy</span>
</div>
