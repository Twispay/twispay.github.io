# Hosted payment form documentation

## Table of contents

[Introduction](#introduction)  
[Prerequisites](#prerequisites)  
[How it works?](#how-it-works)  


## Introduction

The Twispay® hosted payment page is a securely web form designed to accept e-commerce transactions.

With this page in place, customers are redirected from the merchant's website to a payment form hosted by Twispay®,
so the merchant is no longer exposed to the sensitive payment details (Card Number, CVV, etc.) required to process a payment.   

***Note!** If your site uses one of [supported platforms](#TODO) you can skip this giude
and go directly to [modules documentation](#TODO).*


## Prerequisites
- This guide.
- An e-commerce site.
- A Twispay® merchant account. If you don't already have one you can create one [here](https://merchant-stage.twispay.com).
- A `siteId` and secret key (aka `apiKey`) you can get from your Twispay® merchant account. See below where to find it.

![](siteID&apiKey.gif)

***Note!** Keep you `apiKey` private on your server. If it is exposed somebody can do malitions operations in your name.*


## How it works?

