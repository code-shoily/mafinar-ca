---
title: "এলিক্সির ও ফাংশনাল প্রোগ্রামিং"
date: 2020-01-18T14:50:11-05:00
draft: true
---
গত ১২ জানুয়ারি, ২০২০ থেকে আমি একটা এলিক্সির ৮ সপ্তাহব্যাপি বুটক্যাম্প চালু করি আমার ফেইসবুক গ্রুপে। এই পোস্ট লিখা অবধি ১ সপ্তাহ অতিক্রান্ত হল। এই পোস্টটি দ্বিমুখী উদ্দেশ্যে লিখিত। প্রথমত, যারা এলিক্সির বুটক্যাম্পে অংশগ্রহণ করছেন তাঁদের দ্বিতীয় সপ্তাহের প্রথম পাঠ এবং দ্বিতীয়ত, এলিক্সির ও ফাংশনাল প্রোগ্রামিং নিয়ে জেনারেল আলোচনা। 

উল্লেখ্য, এই পোস্ট পরে লাভবান হতে হলে এলিক্সির সিনট্যাক্স সম্পর্কে বেসিক ধারনা থাকা লাগবে যা জানতে পারবেন, যা [এলিক্সির স্কুল বেসিক](https://elixirschool.com/en/) সেকশানের প্রথম ৮টি অধ্যায় থেকে দেখে নিতে পারবেন। আর ভিডিও দেখে শিখতে চাইলে [এই ইউটিউব লিঙ্ক](https://www.youtube.com/watch?v=Ni6ykhoVTks) এবং/অথবা [এই লিঙ্ক](https://www.youtube.com/watch?v=pBNOavRoNL0) দেখে নিতে পারেন।

তাহলে আমি ধরে নিচ্ছি এলিক্সির সিনট্যাক্স সম্পর্কে আপনার বেসিক ধারণা আছে, অথবা, অন্তত ১০০ লাইন এলিক্সির দেখলে বড়জোড় ২০ লাইন আপনাকে গুগল করে বুঝতে হবে যে কী হচ্ছে। এই ধারণা থেকে শুরু করছি আজকের টপিক, এলিক্সির ও ফাংশনাল প্রোগ্রামিং। 

## ফাংশন দিয়ে চিন্তা করা

`f(x) = x` হলে `f(10)` এর মান কি হবে? হাই স্কুলে শিখা ধারণা। এই ধারণা থেকেই কিন্তু ফাংশনাল প্রোগ্রামিংয়ের জন্ম। অর্থাৎ আমাদের ফাংশন হবে গাণিতিক ফাংশনের মত। 

তাহলে কি জাভার মেথড অথবা পাইথনের ফাংশন গাণিতিক ফাংশনের মত নয়? হ্যাঁ অথবা না। `x = [-6, 10, 0, -2]; x.sort()` পাইথন ফাংশনটিই দেখে নেই, তা কিন্তু `None` রিটার্ন করবে, কিন্তু `x` পরিবর্তিত হয়ে যাবে। গাণিতিক ফাংশনে কি তা হয়? অর্থাৎ, আপনি যখন- `f(x)` এর প্লট বানাতে যাবেন, তা নিজে কিছু রেজাল্ট না দিয়ে অন্য আরেকটা প্লটের মান পালটিয়ে দিবে, এমন কি হয়? ফাংশনাল প্রোগ্রামিং মূলত এক্সপ্রেশন প্রধান প্রোগ্রামিং। অর্থাৎ, প্রতিটি `LHS` এর জন্যে রয়েছে `RHS`। অর্থাৎ `frame.setSize(400, 400)` যা নিজে কিছু না বলে চুপিসারে `frame` এর মাত্রা নির্ধারণ করে, এহেন কোড ফাংশনাল প্রোগ্রামিং এ চলবে না। 

## বিশেষ্য বনাম ক্রিয়া 

অবজেক্ট ওরিয়েন্টেড প্রোগ্রামিং আমাদের শিখায় বিশেষ্য নিয়ে চিন্তা করতে। আপনি প্রথমে তৈরি করবেন একাধিক অবজেক্ট এবং এরা একে অন্যের মেথড কল করে এবং অবস্থা পরিবর্তন করে প্রোগ্রামের অগ্রগতি সাধন করে। এখানে ক্রিয়া রয়েছে কিন্তু, তবে আমাদের ফোকাস থাকে বিশেষ্যের প্রতি, ক্রিয়া (অর্থাৎ মেথড) ওই বিশেষ্যের অবস্থান্তরে কাজে আসে। 

এর উল্টা হচ্ছে ফাংশনাল প্রোগ্রামিং নীতি। এখানে ক্রিয়া মূল, অর্থাৎ, প্রোগ্রাম কী নিবে এবং কী দিবে তা একটা ফাংশনের মাধ্যমে প্রকাশিত হয় এবং, তা আরও ছোট ছোট ফাংশনের মাধ্যমে ইনপুটকৃত ওই ডাটা-কে পরিবর্তন করে আউটপুট ডাটাতে। এখানেও কিন্তু বিশেষ্য রয়েছে, তবে তাঁদের কাজ ডাটাকে উপস্থাপন যা ফাংশনের ইনপুট। যদি বলি, `f(x, y) = x + y`, এবং আমরা `f(10, 20)` কল করি, তাহলে কিন্তু `x` অথবা `y` পরিবর্তিত হচ্ছে না, তারা কেবলমাত্র `f` এর কাঁচামাল, ফাংশনটি তাঁদের দিয়ে  নতুন মান `30` রিটার্ন করছে। 

## এলিক্সির সিনট্যাক্স ও ডাটা স্ট্রাকচার নীতি

## প্যাটার্ন ম্যাচিং ম্যাজিক

## পাইপ 

## রিকারশন রহস্যভেদ 

## মডিউল ও প্রোগ্রাম গঠন

## কিছু এলিক্সির মডিউল/লাইব্রেরি পরিচিতি

## 


