---
layout: post
title:  "Raytracer"
summary: "A toy software raytracer for learning. Written in C++."
date:   2022-10-06 15:39:40
preview: /assets/ray_tracer_thumbnail.png
---

![Picture 1](/assets/ray_tracer_header.png)

## Overview

I wrote [a toy software raytracer](https://github.com/ReeCocho/ray-tracer) for learning purposes based on Peter Shirley's excellent article, [Ray Tracing in One Weekend](https://raytracing.github.io/books/RayTracingInOneWeekend.html). I've implemented some additional features of my own to improve performance including:

* Multithreaded image rendering.
* SIMD acceleration.
* Fast random float generation.
* BVH acceleration of the scene.

## Multithreading

When implementing multithreading, I wanted to make sure that my solution would scale well no matter how many cores I threw at it, and would minimize the amount of waiting that would need to be done.

My solution was to have each thread render an entire image, but distribute the samples across the threads, and then average the images at the end. For example, if you wanted to use 8 threads and 512 samples per pixel, each thread would render an entire image using `512 / 8 = 64` samples.

## SIMD and Randomness

I have two code paths for SIMD and non-SIMD math which can be toggled using a flag in `common.hpp`.

When choosing a random float algorithm, I only cared about speed as long as the resulting image looked good at a glance. After some testing, I settled on [Xoroshiro128+](https://en.wikipedia.org/wiki/Xoroshiro128%2B).

## Room For Improvement

As always, I'm constantly looking for ways to improve the codebase. Some ideas I have currently include:

* Support for triangle meshes, textures, and light sources.
* Rendering multiple images to create GIFs.
* A more efficient BVH generation algorihtm.
