---
layout: post
title: "ECS 172 Final Project"
date: 2026-03-12
---

## Project Description

We prototype a preference-informed image generator named GIG (Gemini Image Generator). Our system generates an image tailored to the user's preferences based on their response. The accelerometer on the CC3200 board will collect their responses. We interpret tilt in the y direction as how much they like/dislike the image, and tilt in the x direction as how much they want to weigh this image for the output of the final generation. At the end of the survey, we will send user preferences to the AWS Lambda function, which will make an API call to Gemini 3.1 Flash to create the final image.

## Video Demo

[Demo 1](https://drive.google.com/file/d/1TdWCQVDXb7mLaO_GIa5s5-BlqEVtCD3g/view?usp=sharing)
[Demo 2](https://drive.google.com/file/d/1-qCOPq1Z8yDBNMBW8Gw79Ih4T1evTE1l/view?usp=sharing)
[Demo 3](https://drive.google.com/file/d/16mQrOCY7bVm9SjqJxtE2iI8xzXMGkIVn/view?usp=sharing)
