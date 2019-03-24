---
layout: page
title: Projects
subtitle: What's been eating away my free time
---

This is a collection of my personal projects that I work on in my free time. Hope you like them.

---

## Topic Based Image Captioning

An automatic image caption generation system built using Deep Learning.

- Developed a multi-label image classifier using transfer learning on pre-trained VGG19 Net.
- Fed the results of the multi-label classifier as well as the image features extracted from VGG19 to the language model.
- Developed the model using merge model architecture.
- Applied Beam Search to generate captions during inference.
- _Tools_: Python, Keras-Tensoflow, MSCOCO-2017 Dataset.
- _Services_: Google Cloud
- _GitHub URL_: [Image-Captioning](https://github.com/shan18/Topic-Based-Image-Captioning)

<br/>

## Stock Bridge

A real-time online stock market simulator built using Django.  
**Website URL**: [https://stock-bridge.herokuapp.com](https://stock-bridge.herokuapp.com/)

- Built the entire user-company transaction system from scratch.
- Developed an automated system for fluctuating stock prices.
- Extensive usage of django signals, model managers and custom querysets.
- Devised a tiebreaker mechanism using the concept of coefficient of variation.
- Created a Bank System from which users can issue loans.
- Added the functionality of automatic intrest deduction from the users who have pending loan amounts.
- _Tools_: Python, Django, Django REST Framework, Bootstrap v4, chart.js
- _Services_: Amazon Web Services, Heroku, sendgrid
- _GitHub URL_: [Stock-Bridge](https://github.com/morphosis-nitmz/Stock-Bridge)

<br/>

## Code Warrior

An online judge platform built using Django.  
**Website URL**: [https://code-warrior-morphosis.herokuapp.com](https://code-warrior-morphosis.herokuapp.com/)

- Built the entire compilation, execution and submission evaluation module from scratch.
- Designed the platform to support languages: C, C++, and Python.
- Constructed a tiebreaker mechanism which uses user submission execution time for ranking users with the same score in the leaderboard.
- _Tools_: Python, Django, Bootstrap
- _Services_: Amazon Web Services, sendgrid
- _GitHub URL_: [Code-Warrior](https://github.com/morphosis-nitmz/Code-Warrior)

<br/>

## Kart

An E-commerce website built using Django.  
**Website URL**: [https://shan-kart.herokuapp.com](https://shan-kart.herokuapp.com/)

- Built the backend entirely on Django. Used jQuery in some places to make the website asynchronous.
- Used signals, custom model managers, and custom querysets extensively to keep most of the code logic within the models and to make the communication between the linked models effective.
- In case of a server error, setup sendgrid to send a detailed error report to website admins.
- Built the functionality to sell digital items by storing them in AWS S3 Storage.
- Render the order summary as a PDF and send it to the user after a successful transaction.
- _Tools_: Python, Django, Bootstrap, jQuery, Ajax, jsrender, chart.js
- _Services_: stripe, mailchimp, Amazon Web Services, heroku, sendgrid
- _GitHub URL_: [Kart](https://github.com/shan18/Kart)

<br/>

## Autoranking Amazon Reviews

Ranking the reviews on Amazon according to their helpfulness score.

- The problem was modeled as a regression problem. The performance was evaluated by using the coefficient of determination and rank correlation.
- Predictions were made based on various categories of features of the review text, and other metadata associated with the review, with the purpose of generating a rank for a given list of reviews.
- _Tools_: Python, Numpy, Pandas, textblob, scikit-learn
- _GitHub URL_: [Autoranking-Amazon-Reviews](https://github.com/shan18/Autoranking-Amazon-Reviews)

<br/>

## Morphosis, NIT Mizoram

Android App for the annual technical fest of NIT Mizoram.  
**Google Play Store URL**: [Morphosis](https://play.google.com/store/apps/details?id=com.nitmz.morphosis&hl=en)

- Contains all the information of various events to be conducted during the technical fest.
- Live notification updates.
- Contains a game called Scooby Dooby Doo, where participants have to answer various logical questions. The app gives live leaderboard updates.
- _Tools_: Java, Android Studio, Firebase
- _GitHub URL_: [Morphosis-2k17-Android](https://github.com/morphosis-nitmz/Morphosis-2k17-Android)

---
