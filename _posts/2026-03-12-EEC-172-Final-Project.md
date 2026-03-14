---
layout: post
title: "ECS 172 Final Project"
date: 2026-03-12
---

<style>
  .post-content p {
    text-align: justify !important;
  }
</style>

<h1 style="text-align: center; margin-bottom: 0.2em;">GIG</h1>
<div style="text-align: center; font-size: 1.2em; color: #5a6a7a; margin-bottom: 2em;">Gemini Image Generator</div>

## Project Description

We prototype a preference-informed image generator named GIG (Gemini Image Generator). Our system generates an image tailored to the user's preferences based on their response. The accelerometer on the CC3200 board will collect their responses. We interpret tilt in the y direction as how much they like/dislike the image, and tilt in the x direction as how much they want to weigh this image for the output of the final generation. At the end of the survey, we will send user preferences to the AWS Lambda function, which will make an API call to Gemini 3.1 Flash to create the final image.

## Main Logic

In our final main.c program, we handle the integration of all sensors and AWS logic for the final product. Our program functions by first displaying a welcome message and waiting for the user to press the mute button to start once the WiFi has been connected. After this, the first image to be shown to the user is fetched from our S3 bucket containing the binary versions of our images. The images are shown to the user in a random order, with no images being shown multiple times before a single generation. The current image set used in the code consists of 10 images, but this can be generalized to more images by adding more binaries to the bucket and file prefixes to the main code. While the user views this image, the accelerometer X and Y values are polled as in the rolling ball logic from lab 2 using I2C. Under the current image, the user is shown the current preference (P) value and the current weight (W) value. Preference ranges from -1 to 1, and weight ranges from 0 to 1. We then use the IR reading logic over the GPIO we developed in lab 3 to detect when the user presses the mute or last button. The mute button is used to proceed to the next image without generating (or automatically generating if all images have been seen within this generation run), while the last button is used to start generating immediately. The preferences are sent to the AWS IoT device shadow and used to trigger a Python script in AWS Lambda to generate. Notably, we avoid saving the entire image binary into memory for display, instead streaming 1024 bytes at a time from the binary and sending over SPI to the Adafruit SSD1351 OLED display. 

## AWS and Gemini Logic

To interface with the device shadow for posting of user preferences, we use the same posting logic as lab 4, but use a different top-level JSON category of “categories” followed by the weights and preferences for each entry. We found that interfacing with the S3 bucket for image fetching was easiest to achieve by making the bucket open to HTTP GET requests for the images without specific needs for certification (as we have with certificates and keys for the device shadow). 

We also added an IoT Rule to listen to the changes in AWS Shadow. Once it detects a change, it will invoke the AWS Lambda. The AWS Lambda receives category preference data (sentiment and weight) from the CC3200, builds a detailed prompt for Gemini, and calls Gemini 3.1 Flash to generate an image. The prompt we generated is listed in the appendix section below, and we utilize In-context learning to ensure Gemini has enough context to create images according to our preference. The generated image is then resized to 128x128 RGB565 for the OLED and uploaded to S3 as latest.bin for the board to fetch.

## Video Demo

[Demo 1](https://drive.google.com/file/d/1TdWCQVDXb7mLaO_GIa5s5-BlqEVtCD3g/view?usp=sharing)
[Demo 2](https://drive.google.com/file/d/1-qCOPq1Z8yDBNMBW8Gw79Ih4T1evTE1l/view?usp=sharing)
[Demo 3](https://drive.google.com/file/d/16mQrOCY7bVm9SjqJxtE2iI8xzXMGkIVn/view?usp=sharing)
