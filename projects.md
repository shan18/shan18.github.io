---
layout: page
title: Projects
subtitle: What's been eating away my free time
---

This is a collection of my personal projects that I work on in my free time. Hope you like them.

---

## Stock Bridge

A real-time online stock market simulator built using Django.  
**Website URL**: [https://stock-bridge.herokuapp.com](https://stock-bridge.herokuapp.com/)

- Built the entire user-company transaction system from scratch.
- Made the fluctuations in stock prices completely automatic so that the fluctuations depend entirely on the number of transactions made during a fixed period of time.
- Built the backend entirely on Django. Used signals, custom model managers, and custom querysets extensively to keep most of the code logic within the models and to make the communication between the linked models effective.
- Used the concept of coefficient of variation as a tiebreaker for the leaderboard.
- Created a Bank model for the users to issue loan from and deduct interest from their loan amount accordingly.
- Created a notification panel called news which the users used to make wise transactions.
- Added the functionality to make the users see their previous transactions and corresponding net worth.
- *Tools*: Python, Django, Django REST Framework, Bootstrap v4, chart.js
- *Services*: Amazon Web Services, Heroku, sendgrid
- *GitHub URL*: [Stock-Bridge](https://github.com/morphosis-nitmz/Stock-Bridge)

<br/>

## Code Warrior

An online judge platform built using Django.  
**Website URL**: [https://code-warrior-morphosis.herokuapp.com](https://code-warrior-morphosis.herokuapp.com/)

- Built the entire compilation, execution and submission evaluation module from scratch.
- Made the platform to support following languages: C, C++, Python 2, Python 3
- Built the backend entirely on Django. Used signals, custom model managers, and custom querysets extensively to keep most of the code logic within the models and to make the communication between the linked models effective.
- Used user submission execution time as a tiebreaker for the leaderboard.
- Used AWS S3 Service to store user submissions.
- Added the functionality to make the users see their previous submissions.
- *Tools*: Python, Django, Bootstrap
- *Services*: Amazon Web Services, sendgrid
- *GitHub URL*: [Code-Warrior](https://github.com/morphosis-nitmz/Code-Warrior)

<br/>

## Kart

An E-commerce website built using Django.  
**Website URL**: [https://shan-kart.herokuapp.com](https://shan-kart.herokuapp.com/)

- Built the backend entirely on Django. Used jQuery in some places to make the website asynchronous.
- Used signals, custom model managers, and custom querysets extensively to keep most of the code logic within the models and to make the communication between the linked models effective.
- In case of a server error, setup sendgrid to send a detailed error report to website admins.
- Built the functionality to sell digital items by storing them in AWS S3 Storage.
- Render the order summary as a PDF and send it to the user after a successful transaction.
- *Tools*: Python, Django, Bootstrap, jQuery, Ajax, jsrender, chart.js
- *Services*: stripe, mailchimp, Amazon Web Services, heroku, sendgrid
- *GitHub URL*: [Kart](https://github.com/shan18/Kart)

<br/>

## Autoranking Amazon Reviews

Ranking the reviews on Amazon according to their helpfulness score.

- The problem was modeled as a regression problem. The performance was evaluated by using the coefficient of determination and rank correlation.
- Predictions were made based on various categories of features of the review text, and other metadata associated with the review, with the purpose of generating a rank for a given list of reviews.
- *Tools*: Python, Numpy, Pandas, textblob, scikit-learn
- *GitHub URL*: [Autoranking-Amazon-Reviews](https://github.com/shan18/Autoranking-Amazon-Reviews)

<br/>

## Morphosis, NIT Mizoram

Android App for the annual technical fest of NIT Mizoram.  
**Google Play Store URL**: [Morphosis](https://play.google.com/store/apps/details?id=com.nitmz.morphosis&hl=en)

- Contains all the information of various events to be conducted during the technical fest.
- Live notification updates.
- Contains a game called Scooby Dooby Doo, where participants have to answer various logical questions. The app gives live leaderboard updates.
- *Tools*: Java, Android Studio, Firebase
- *GitHub URL*: [Morphosis-2k17-Android](https://github.com/morphosis-nitmz/Morphosis-2k17-Android)

---
