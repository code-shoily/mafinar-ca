---
title: "ফুল স্ট্যাক গ্রাফকিউএল শূন্য থেকে স্বয়ংসম্পূর্ণ - বেসিক ও কোডবিহীন আলোচনা"
date: 2018-06-02T01:23:42-04:00
draft: false
language: "bn"
tags: ["graphql", "python", "django"]
description: "এই পোস্টটি হল আমার গ্রাফকিউএল সিরিজের প্রথমটি, এই পোস্টটিতে আমি খুবই সংক্ষিপ্তভাবে গ্রাফকিউএল নিয়ে আলোচনা করব যা মোটামোটি ল্যাঙ্গুয়েজ নিরপেক্ষ" 
---
গতবছর পাইকন ক্যানাডাতে গ্রাফকিউএল নিয়ে একটা টিউটোরিয়াল নিয়েছিলাম। অফিসের কলিগদের সাথে আড্ডা/ইন্টারনাল টেকটক বহির্ভুত সেটিই ছিল আমার কমিউনিটির সাথে গ্রাফকিউএল নিয়ে প্রথম কথোপকথন। সেখানে অনেক প্রশ্ন করা হয় আমাকে, আর আমার সেশানে বেশিরভাগই ছিলেন গ্রাফকিউএলে নতুন। আমি সবচেয়ে অবাক হই যে বিষয়ে সেটি হল কত তাড়াতাড়ি তারা কনসেপ্ট বুঝে নিয়েছেন। এমন না যে আমার বোঝানোর ক্ষমতা অসাধারণ, তা মোটেও সত্য নয়, আসলটা ছিল যে গ্রাফকিউএল খুবই কনসেপ্ট বান্ধব একটা কনসেপ্ট যার পিছনে কিছুটা ধৈর্য ধরে সময় ব্যয় করলে এপিআই নিয়ে সম্পূর্ণ ভিন্নভাবে চিন্তা করতে শিখবেন। আর এটি "ভাল" ভিন্ন, "খারাপ" ভিন্ন নয়। তো আমি চিন্তা করলাম, বাংলাতে একটা সিরিজের মত চালু করতে পারি গ্রাফকিউএল নিয়ে। আর সেটি নিয়েই হবে আজকের এই আপাত-ইউসলেস ও অগোছাল এই পোস্ট।

আমি আমার বক্তব্য শুরু করেছি এমনভাবে যেন গ্রাফকিউএল কী তা কমন নলেজ। কিন্তু তা সত্য নয়, রেস্ট এপিআই-ই এখন কমন হতে পারল না, গ্রাফকিউএল তো অনেক দূরের টেকনোলজি, কাজেই, যা দিয়ে আমার পোস্টটি শুরু করা উচিত ছিল তা এখন বলছি/ গ্রাফকিউএল এর একাডেমিক সংজ্ঞা চাইলে উইকিপিডিয়া যথেষ্ট, আমি এখানে আমার ভাষায় ব্যাখ্যা করব। আর তা হল

গ্রাফকিউএল হল দুই সিস্টেমের মধ্যে কথোপকথনের একটা মাধ্যম যার কন্সেপ্টুয়াল কেন্দ্র হল ডাটা-র কাঠামো এবং সেই ডাটা-র উপর দু'ধরণের অপারেশান- কুয়েরী (অথবা কাঠামোর একটা অংশ বের করে এর উপস্থাপন) এবং মিউটেশান (অথবা এর কোন অংশের মান পরিবর্তন অথবা কর্তন)। সাধারণত একপক্ষ ক্লায়েন্ট ও অন্যপক্ষ সার্ভার হয়ে থাকে এবং ডাটা অনায়াসে ক্লায়েন্ট সার্ভারের মধ্যে যাতায়াত করে, আপনার কুয়েরি/মিউটেশান হতে পারে মাধ্যমিয় দূরত্ব নিরপেক্ষ। কাঠামো শব্দটিতে জোর দেয়ার কারণ- যেহেতু প্রোটোকোলটি ডাটা কেন্দ্রিক, ডাটা সম্পর্কে চিন্তা করে অনেক সময় ব্যয় করতে হবে, আর ডাটা নিয়ে চিন্তা করার সবচেয়ে উপকারী টুল হল আবস্ট্রাকশান। অবজেক্ট ওরিয়েন্টেড প্রোগ্রামিং আমাদের শিখায় অবজেক্ট হিসেবে ডাটা-কে দেখতে, ডাটাবেজ (রিলেশনাল) শিখায় টেবিল/রো এবং তাদের মাঝে রিলেশান নিয়ে চিন্তা করতে, নো-সিকিউএল শিখায় ডকিউমেনট হিসেবে দেখতে, ঠিক তেমনি, গ্রাফকিউএল আপনাকে বলবে ডাটাকে দেখতে গ্রাফ (নোড/এজওয়ালা গ্রাফ) হিসেবে দেখতে। এই রিপ্রেজেন্টেশান নিয়ে পরে বিস্তারিত বলব।

আরেকটা এনালজিতে আসি, হয়ত সেটা আরও ক্লিয়ার হবে। ডাটাবেজ নিয়ে যদি কাজ করে থাকেন তাহলে শেল নিয়ে কাজ করেই থাকবেন, অর্থাৎ টার্মিনালে গিয়ে `psql -U postgres` লিখলে যা হয় আরকি, এরপর আপনি ডাটা কুয়েরি করবেন সিকুয়েল স্টেটমেন্ট লিখে এবং রো-সমেত ডাটা চলে আসবে আপনার কাছে। সিলেক্ট দিয়ে কুয়েরি করতে পারবেন, ইন্সার্ট/আপডেট দিয়ে মিউটেট করতে পারবেন।

আরেকটা (খানিকটা পলিটিকালি ইঙ্কারেক্ট হয়ত) উদাহরণ, আপনি যদি টার্মিনালে গিয়ে python manage.py shell ইন্টার করেন তাহলে আপনি চলে যাবেন পাইথনের জ্যাংগো সমৃদ্ধ ইন্টের‍্যাক্টিভ শেলে, সেইখানে ধরুন আপনি লিখলেন `users = list(User.objects.all())`। এর কাজ হবে auth_user ডাটা কুয়েরি করে তার মানকে লিস্ট আকারে সেইভ করে রাখবে users ভেরিয়েবলে। এরপর যদি কখনো আপনি print(users) লিখেন তাহলে কি আবার ওই ডাটাবেজ থেকে পুল করে লিস্ট বানিয়ে দেখাবে? নাকি users ভেরিয়েবলে ক্যাশ করা ভেলু দেখাবে? এখানে বলে রাখছি, আজকের পোস্ট জ্যাঙ্গো নিয়ে না, কাজেই ভাল/খারাপ প্র্যাকটিসের অথবা প্রিন্ট এর কন্টেন্ট এর হুবুহু-তা নিয়ে ভাবছি না, ওগুলো নিয়ে ভাবার জন্য অন্তত দুটি পোস্ট থাকবে এই সিরিজে (হ্যাঁ আমি জ্যাঙ্গো দিয়ে গ্রাফকিউএল প্রোভাইডার বানাব, সৌভাগ্যবশত নোড দিয়ে ব্যাক-এন্ডে কাজ করার দূর্ভাগ্য আমার হয়নি, হবেও না, আর এলিক্সির/ফিনিক্সের উপর আরও বড় সিরিজ লিখছি শীঘ্রই, আর গো নিয়ে লিখতে গেলে আমার কার্পাল টানেল সিন্ড্রোম অথবা মেজাজ খারাপ হতে পারে)। আসল কথা হল, একবার কুয়েরি করে ভেরিয়েবলে রাখলে তা রয়ে যাবে যতক্ষণ পর্যন্ত না আপনি ডাটা মিউটেট (`User.objects.create`) করবেন এবং পুনরায় user ভেরিয়েবলকে রিলোড করবেন।

উপরের দুটি গল্প (আমার ব্যাক-এন্ড রিলেটেড র‍্যান্ট বাদ দিলে) জয়েন করে চলুন একটা প্যাটার্ন বানাই; স্টেপ-বাই-স্টেপ

- ১ আপনার ক্লায়েন্ট (টার্মিনাল?) কানেক্ট করে কোন সার্ভার-সদৃশ বস্তুর সাথে (পোস্টগ্রেস সার্ভার অথবা পাইথন ইন্টারপ্রেটার)।

- ২ এরপর আপনি ওই সার্ভার নির্দেশক ল্যাঙ্গুয়েজে (পোস্টগ্রেসের ক্ষেত্রে সিয়ুএলে আর পাইথনের ক্ষেত্রে পাইথন ল্যাঙ্গুয়েজে) কিছু কমান্ড (স্টেটমেন্ট? এক্সপ্রেশান?) রিকুয়েস্ট করেন।

- ৩ সার্ভার (ইন্টারপ্রেটার?) থেকে আপনি পান ল্যাঙ্গুয়েজ/প্রোটোকল প্রদত্ত উপাত্ত (থুক্কু ডাটা) কাঠামো (থুক্কু স্ট্রাকচার)।

- ৪ একবার ডাটাকে আপনি সেইভ করলে তা ইনভেলিডেট (অর্থাৎ পুনঃকুয়েরি) করার আগ পর্যন্ত জমা হয়ে থাকবে আপনার ক্লায়েন্টে (অর্থাৎ টার্মিনালে)

এইবার চিন্তা করুন, এই ৪ স্টেপ যদি ওয়েবের ক্ষেত্রে হয় তাহলে? অর্থাৎ, ক্লায়েন্ট ব্রাউজার (জাভাস্ক্রিপ্টে প্রোগ্রামিয়) আর সার্ভার হবে জ্যাঙ্গো প্রচলিত ব্যাকেন্ড? কানেকশন, অর্থাৎ সেই `python` অথবা `psql -U postgres` হতে পারে একটা এন্ডপয়েন্ট/ইউআরএল যার সাথে কানেকশান করবে জাভাস্ক্রিপ্ট। একটা এন্ডপয়েন্ট কিন্তু, আপনি python একবারই টার্মিনালে লিখেন, pyhton -m Django একবার, এরপর আরেকটা টার্মিনাল খুলে `python -m request` (এবং প্রতিটা মডিউল নিয়ে কাজ করার জন্যে একটা করে টার্মিনাল ও রেডিসের মাধ্যমে তাদের ডাটা আদানপ্রদান ইত্যাদি) করেন না, নিজেকে ঘৃণা না করে থাকলে। এরপর একটা ল্যাঙ্গুয়েজের মাধ্যমে আপনি সার্ভার থেকে ডাটা নিবেন এবং ক্লায়েন্ট রেন্ডার করবে সেই ডাটা। এমন যেন সার্ভার (ব্যাক-এন্ড) হল ডাটাবেজ, ক্লায়েন্ট হল টার্মিনাল, গ্রাফকিউএল (এটি শুধু রানটাইম/সার্ভার স্পেক না, ল্যাঙ্গুয়েজও) হল এসকিউএল, এবং রিয়াক্ট (অথবা ভিউ অথবা অ্যাংগুলার (৪ ছিল কিছুদিন আগে, এখন ৬/৮ হবে কিছু একটা) হল ডাটা রিপ্রেজেন্টেশানের মাধ্যম। এবং বারবার কুয়েরির ক্ষেত্রে তা পাইথনের ভেরিয়েবল ক্যাশিংয়ের মত কাজ করবে, দরকার না পরলে টানবে না।

উপরের প্যারাগ্রাফ পরলে আপনি বুঝতে পারবেন কিছুটা যে গ্রাফকিউএল করেটা কি। কার্যকারিতা অনুযায়ী এটি তাই করে যা রেস্ট করে থাকে, পার্থক্য হল ডেভেলপার চিন্তাধারায় এবং জটিল ডাটা নিয়ে কাজ করায়। রেস্ট এর মত সার্ভারকে এন্ডপয়েন্ট কেন্দ্রিক সিথিল নিয়মানুবর্তি রিসোর্স/কন্টেন্ট হিসেবে শত ইউআরএলের অধিকারী সিস্টেমে চিন্তা না করে, একবার কানেক্টেড হয়ে, প্রশ্ন-উত্তরের মাধ্যমে সার্ভার থেকে ডাটা টানা (যেখানে প্রশ্নের ভাষা এবং উত্তরের কাঠামো দুটোই কড়া নিয়ম ও টাইপের আওতাধীন) এবং তা নিয়ে কাজ করতে শিখায় গ্রাফকিউএল (আসলে কানেক্টেড একবার লজিকালি হয়, আদতে প্রতিবারই রিকুয়েস্ট পাঠায় তবে প্রোগ্রামার হিসেবে আপনার তা মাথায় রাখতে হবে না; সাবক্স্রিপশান ব্যবহার করলে তাও হবে না)।

আজকে এই পর্যন্তই রাখি কন্সেপ্টুয়াল কথাবার্তা। এইবার প্ল্যানে আসা যাক, আমি ৫/৬ টা টপিক লিখব গ্রাফকিউএলের উপর এবং তা হবে ফুলস্ট্যাক ট্রিটমেন্ট, অর্থাৎ কনসেপ্ট, কিছুটা ব্যাকেন্ড, কিছুটা ফ্রন্টএন্ড এবং কিছুটা বেস্ট প্রাকটিস থাকবে এই সিরিজে)। সপ্তাহে একটা করে ছাড়ব (চেষ্টা করব আজকের মত খাপছাড়া না রাখতে কিন্তু আমার র‍্যান্ট চিরন্তন), আপাতত আউটলাইন হল (পরিবর্তন/পরিবর্ধনীয়)

- ০ কনসেপ্ট ১ - বেসিক ও কোডবিহীন আলোচনা (আজকেরটা!)

- ১ কনসেপ্ট ২ - ডাটা স্ট্রাকচার, টাইপ, কুয়েরি ল্যাঙ্গুয়েজ। কী, কেন, ও কেমনে?

- ২ ব্যাকেন্ড ইন্টিগ্রেশান ১ - জ্যাঙ্গো ও গ্রাফিন হাল্কার উপর ঝাপ্সা, ডাটাবেইস, সিরিয়ালাইজার, টাইপ, কুয়েরি, মিউটেশান ইত্যাদি।

- ৩ ফ্রন্টএন্ড ১ - এপোলো, হাল্কার উপর ঝাপ্সা, উপরের জিনিশগুলিকে রিপ্রেজেন্ট করা, সহজ কিছু কুয়েরি/মিউটেশান।

- ৪ ব্যাকেন্ড ২ - কিছুটা এডভান্স ব্যাকেন্ড। যেমন অথোরাইজেশান, নেস্টেড কাঠামো, জটিল মিউটেশান, একাধিক ডাটাবেইস, টিসিপি সার্ভার, বেস্ট প্র্যাকটিস ইত্যাদি

- ৫ ফ্রন্টএন্ড ২ - কিছুটা এডভান্স ফ্রন্টএন্ড। ক্যাশিং, অথেন্টিকেশান, রিকম্পোজ, কমন প্রব্লেম, বেস্ট প্র্যাকটিস ইত্যাদি।

- ৬ (হয়ত) থিওরি - এতকিছু থাকতে গ্রাফ কেন? ক্যাশিং এলগরিদম, রিলে, গ্রাফকিউএলের চেলাপুলা (এজডিবি, আড়ঙ্গ ডিবি ইত্যাদি), কিছু তাত্ত্বিক সম্ভাবনা, আরও কত কি (অথবা কিছুই না)...

এই কাঠামো থাকল আপাতত। একটা কথা বলে রাখি, অনেকেই বলে থাকে, গ্রাফকিউএল সিকুওর না কারণ তা ডাটাবেজকে উম্মুক্ত করে দেয় ক্লায়েন্টের কাছে, তাদের মাথা থেকে মাঝখানের ব্যাকএন্ড হাওয়া হয়ে যায়, ডাটাবেস প্রদত্ত ডাটা আর এপিআই প্রদত্ত ডাটা এক জিনিস না, আর এপিআই শুধু ডাটাবেস থেকেই ডাটা টানবে না, তা হতে পারে ক্যাশ, টিসিপি সার্ভার, অন্য এপিআই অথবা ওয়েব স্ক্র্যাপ (প্লিজ ডোন্ট) করে। কাজেই, উম্মুক্তভাবে এপিআই প্রদত্ত ডাটা কাঠামো (গ্রাফ) এবং ডাটাবেইজ কাঠামোকে ভিন্ন এবং এদের ম্যাপিং একটু ভিন্ন করার মাধ্যমে গ্রাফকিউএল আপনাকে কিছুটা হলেও এপিআই তৈরির এই ক্ষুদ্র ফিলসফি শিখায়। যাই হোক, আগামী সপ্তাহে আবার কথা হবে। হ্যাপি উইকেন্ডস।
