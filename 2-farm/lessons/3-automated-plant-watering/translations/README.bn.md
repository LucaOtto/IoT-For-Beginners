# স্বয়ংক্রিয় সেচকার্য

![A sketchnote overview of this lesson](../../../../sketchnotes/lesson-7.jpg)

> স্কেচনোটটি তৈরী করেছেন [Nitya Narasimhan](https://github.com/nitya)। বড় সংস্করণে দেখার জন্য ছবিটিতে ক্লিক করতে হবে।

## লেকচার-পূর্ববর্তী কুইজ

[লেকচার-পূর্ববর্তী কুইজ](https://brave-island-0b7c7f50f.azurestaticapps.net/quiz/13)

## সূচনা

গত পাঠে আমরা কীভাবে মাটির আর্দ্রতা নিরীক্ষণ করব তা শিখেছি। এই পাঠে জানবো কীভাবে একটি স্বয়ংক্রিয় পানি সরবরাহ ব্যবস্থার মূল উপাদানগুলি তৈরি করতে হবে যা মাটির আর্দ্রতার উপর ভিত্তি করে কাজ করবে। এছাড়াও সময় সংক্রান্ত বিষয়য়াদিও জানবো - সেন্সরগুলি তাদের অবস্থার পরিবর্তনের প্রতিক্রিয়া জানাতে কীভাবে সময় নিতে পারে এবং সেন্সরগুলির দ্বারা পরিমাপ করা বৈশিষ্ট্যগুলি পরিবর্তনে একচুয়েটরের কাজের জন্য কতটা সময় নেয়, তা জানতে পারবো।

এই পাঠে আমরা যেসকল বিষয় শিখবো:

* [লো পাওয়ার আইওটি ডিভাইস থেকে হাই পাওয়ার ডিভাইস নিয়ন্ত্রণ](#লো-পাওয়ার-আইওটি-ডিভাইস-থেকে-হাই-পাওয়ার-ডিভাইস-নিয়ন্ত্রণ)
* [রিলে নিয়ন্ত্রণ](#রিলে-নিয়ন্ত্রণ)
* [MQTT দ্বারা উদ্ভিদ নিয়ন্ত্রণ](#MQTT-দ্বারা-উদ্ভিদ-নিয়ন্ত্রণ)
* [সেন্সর এবং অ্যাকচুয়েটরের টাইমিং](#সেন্সর-এবং-অ্যাকচুয়েটরের-টাইমিং)
* [উদ্ভিদ নিয়ন্ত্রণে টাইমিং যুক্ত করা](#উদ্ভিদ-নিয়ন্ত্রণ-সার্ভারে-টাইমিং-যুক্ত-করা)

## লো পাওয়ার আইওটি ডিভাইস থেকে হাই পাওয়ার ডিভাইস নিয়ন্ত্রণ

আইওটি ডিভাইসগুলি কম ভোল্টেজ ব্যবহার করে। যদিও এটি সেন্সর এবং এলইডি এর মতো লো-পাওয়ার যন্ত্রের পক্ষে যথেষ্ট, কিন্তু বড় হার্ডওয়্যার যেমন সেচের জন্য ব্যবহৃত পানির পাম্প নিয়ন্ত্রণ করতে এটি খুব কম ভোল্টেজ। এমনকি আমাদের বাসাবাড়ির ছোট চারাগাছের জন্য যে ছোট পাম্পগুলি ব্যবহার করা হয়, সেগুলিও আইওটি ডেভ কিটের সহ্যসীমার তুলনামূলক খুব বেশি কারেন্ট নিয়ে নেয় এবং বোর্ডটি পুড়ে যেতে পারে।

> 🎓 তড়িৎ প্রবাহ বা কারেন্ট, পরিমাপ করা হয় অ্যাম্পিয়ার (A) এককে, একটি সার্কিট মাধ্যমে প্রবাহিত বিদ্যুতের পরিমাণ। ভোল্টেজ এখানে ধাক্কা তৈরী করে আর কারেন্ট হলো সেই ধাক্কার পরিমাণ। [তড়িৎ প্রবাহ এর Wikipedia পেইজ](https://wikipedia.org/wiki/Electric_current) পড়লে এ সম্পর্কে আরো বিস্তারিত জানা যাবে।

এই সমস্যার সমাধানটি হল পাম্পকে একটি বাহ্যিক বিদ্যুৎ সরবরাহের সাথে সংযুক্ত করা এবং পাম্পটি চালু করার জন্য একটি অ্যাকচুয়েটর ব্যবহার করা যা অনেকটা বাতির স্যুইচ জ্বালানোর মতো। আমাদের আঙুল একটি স্যুইচ ফ্লিপ করতে খুব সামান্য পরিমাণ শক্তি (দেহের শক্তির আকারে) লাগে এবং এটি 110v বা 240v এ চলমান বিদ্যুতের সাথে বাতিকে সংযুক্ত করে।

![A light switch turns power on to a light](../../../../images/light-switch.png)

> 🎓 [Mains electricity](https://wikipedia.org/wiki/Mains_electricity) বলতে বোঝানো হয়, জাতীয় অবকাঠামো থেকে বাসাবাড়িতে সরবরাহকৃত কারেন্ট। 

✅ আইওটি ডিভাইসগুলি সাধারণত 3.3V বা 5V সরবরাহ করতে পারে ১ এম্পিয়ার কারেন্টে। যদি আমরা মেইন কারেন্টের সাথে এর তুলনা করি, সেক্ষেত্রে আমরা দেখি মেইনে প্রায় ২৩০ভোল্ট সরবরাহ করা হয় ((উত্তর আমেরিকাতে 120V এবং জাপানে 100 V), যার দ্বারা ৩০ এম্পিয়ারের মেশিনও চালানো যায়।

এমন অনেকগুলি অ্যাকচুয়েটর রয়েছে যা এই স্যুইচিং করতে পারে - যান্ত্রিক ডিভাইস সহ বিদ্যমান স্বাভাবিক সুইচগুলিতেও এটি সংযুক্ত করা যাবে যা কোন আঙুল এর মতই স্যুইচ নিয়ন্ত্রণ  করে। এই কাজের প্রচলিত উপায়গুলোর মধ্যে সর্বাধিক জনপ্রিয় হলো রিলে।

### রিলে

রিলে একটি ইলেক্ট্রোমেকানিকাল সুইচ যা বৈদ্যুতিক সংকেতকে যান্ত্রিক সংকেতে রূপান্তর করে এবং একটি স্যুইচ চালু করে। রিলে মূলত একটি বৈদ্যুতিক চৌম্বক।

> 🎓 [ইলেক্ট্রোম্যাগনেট](https://wikipedia.org/wiki/Electromagnet) হলো এমন চৌম্বক যেগুলো তার প্যাঁচানো কয়েল দিয়ে বিদ্যুৎ পরিবহন করানোর মাধ্যমে  তৈরী করা হয়। এখানে বিদ্যুৎ চালু করা হলে, কুণ্ডলী চৌম্বকিত হয়ে যায়। বিদ্যুৎ বন্ধ হয়ে গেলে, তা চুম্বকত্ব হারিয়ে ফেলে।

![When on, the electromagnet creates a magnetic field, turning on the switch for the output circuit](../../../../images/relay-on.png)

রিলেতে একটি কন্ট্রোল সার্কিট বৈদ্যুতিক চৌম্বককে পাওয়ার সরবরাহ করে । যখন বৈদ্যুতিক চৌম্বকটি চালু থাকে, তখন এটি লিভার টেনে একটি স্যুইচকে নিয়ন্ত্রণ করে এবং এত্র একটি আউটপুট সার্কিট সম্পূর্ণ হয়।

![When off, the electromagnet doesn't create a magnetic field, turning off the switch for the output circuit](../../../../images/relay-off.png)

কন্ট্রোল সার্কিট অফ হয়ে গেলে, তড়িৎ চৌম্বকটি বন্ধ হয়ে যায়। এতে করে তা লিভারটি ছেড়ে দেয় এবং আউটপুট সার্কিটটি বন্ধ হয়ে যায়। রিলেগুলি হল ডিজিটাল অ্যাকচুয়েটর - রিলেতে একটি উচ্চতর সংকেত High সিগন্যাল এটি চালু করে, আবার Low সিগন্যাল এটি বন্ধ করে দেয়।

আউটপুট সার্কিটটি সেচকার্যের মতো অতিরিক্ত হার্ডওয়্যারকে পাওয়ার সরবরাহ করতে ব্যবহৃত হতে পারে। আইওটি ডিভাইস রিলে এর স্যুইচ অন করতে পারে যার ফলে সেচকার্য চালু করা যায় এবং গাছপালার পানির ব্যবস্থা হবে। আইওটি ডিভাইসটি তারপরে রিলেটি বন্ধ করে, এতে করে সেচ ব্যবস্থাতে পাওয়ার সরবরাহ বন্ধ হয়ে যায় এবং পানি দেয়ার কাজটি শেষ হয়।

![A relay turning on, turning on a pump sending water to a plant](../../../../images/strawberry-pump.gif)

উপরের ভিডিওতে, একটি রিলে চালু আছে। রিলে এর উপরে একটি এলইডি রয়েছে যা আমাদেরকে জানান দেয় এটি চালু রয়েছে কিনা (কিছু রিলে বোর্ডগুলি রিলে চালু বা বন্ধ আছে কিনা তা বোঝানোর জন্য এলইডি রয়েছে), এবং পাম্পে এনার্জি সরবরাহ করা হয়। ফলে এটি চালু হয়ে উদ্ভিদের জন্য পানি পাম্প করে।

> 💁 রিলেগুলি চালু এবং বন্ধ না করে বরং দুটি আউটপুট সার্কিটের মধ্যে এনার্জি সরবরাহ পরিবর্তন করতেও ব্যবহার করা যেতে পারে। যেমন, লিভারটি হয়তো প্রথমে  একটি আউটপুট সার্কিট সম্পূর্ণ করলো, পরে স্যুইচ করে অন্য আউটপুট সার্কিটটি সম্পূর্ণ করলো। এতে একটি সাধারণ পাওয়ার সংযোগ বা কমন গ্রাউন্ড থাকে।

✅ কিছু গবেষণা করা যাক: বিভিন্ন ধরণের রিলে রয়েছে। এদের পার্থক্য করলে দেখা যায়, কন্ট্রোল সার্কিটে পাওয়ার প্রয়োগ করা হলে রিলেটি চালু বা বন্ধ হয়ে যায়, বা একাধিক আউটপুট সার্কিটে সুইচ করে। এরকম বিভিন্ন ধরণের পার্থক্য নিয়ে আরো অনুসন্ধান করি।

যখন লিভারটি স্থান পরিবর্তন করে, তখন সাধারণত একটি শব্দ শুনতে পাওয়া যায়। এটি একটি ক্লিকের শব্দ, যা দিয়ে বৈদ্যুতিক চৌম্বকটির সাথে যোগাযোগ করা হয়।

> 💁 একটি রিলেকে তারের দ্বারা যুক্ত করা যেতে পারে যাতে বৈদ্যুতিক সংযোগ তৈরি করা হলে, রিলেতে এনার্জি সাপ্লাই বন্ধ হয়ে গিয়ে তা অফ হয়ে যায়। পাওয়ার সরবরাহ করলে, আবার একইভাবে রিলে অন হয়ে কাজ করতে পারবে। এর অর্থ রিলে ক্লিক করলে অবিশ্বাস্যভাবে দ্রুত একটি শব্দ তৈরী করে। এইভাবেই প্রথমদিকে আমাদের দরজার বৈদ্যুতি্ক কলিংবেল তৈরীতে buzzer ব্যবহৃত হতো ।

### রিলে পাওয়ার

ইলেক্ট্রোম্যাগনেট দ্বারা লিভারটি সক্রিয় করতে এবং তার কাজ করতে খুব বেশি পাওয়ারের প্রয়োজন হয় না। এটি আইওটি ডেভ কিট থেকে 3.3V বা 5V আউটপুট ব্যবহার করে নিয়ন্ত্রণ করা যায়। ইন্ডাস্ট্রিয়াল ব্যবহারের জন্য মেইন ভোল্টেজ বা এমনকি উচ্চতর পাওয়ার লেভেলসহ রিলের উপর নির্ভর করে, আউটপুট সার্কিট আরও অনেক বেশি পাওয়ার বহন করতে পারে। এইভাবেই মূলত একটি আইওটি ডেভ কিট কোন একটি গাছের জন্য একটি ছোট পাম্প থেকে শুরু করে পুরো বাণিজ্যিক খামারের বিশাল শিল্প ব্যবস্থার সেচকার্য সমগ্রভাবে নিয়ন্ত্রণ করতে পারে।

![A grove relay with the control circuit, output circuit and relay labelled](../../../../images/grove-relay-labelled.png)

উপরের চিত্রটিতে একটি গ্রোভ রিলে দেখা যাচ্ছে। কন্ট্রোল সার্কিটটি একটি আইওটি ডিভাইসের সাথে সংযোগ স্থাপন করে এবং 3.3V বা 5V ব্যবহার করে রিলেটি চালু বা বন্ধ করে দেয়। আউটপুট সার্কিটের দুটি টার্মিনাল রয়েছে, যেকোন ১টি পাওয়ার বা গ্রাউন্ড হতে পারে। আউটপুট সার্কিট 10A এ 250V পর্যন্ত হ্যান্ডেল করতে পারে, যা বেশ কয়েকটি মেইন-কারেন্ট চালিত ডিভাইসের জন্য যথেষ্ট। চাইলে উচ্চ শক্তি সহ্যক্ষমতা সম্পন্ন রিলে পাওয়া যায় ।

![A pump wired through a relay](../../../../images/pump-wired-to-relay.png)

উপরের চিত্রটিতে, একটি রিলে এর মাধ্যমে পাম্পে পাওয়ার সরবরাহ করা হয়। রিলে এর আউটপুট সার্কিটের এক টার্মিনালে ইউএসবি পাওয়ার সাপ্লাইয়ের +5V টার্মিনাল সংযোগকারী একটি লাল তার রয়েছে এবং আউটপুট সার্কিটের অন্য টার্মিনালটিকে পাম্পের সাথে সংযুক্ত করে অন্য একটি লাল তারের সংযুক্ত করে। একটি কালো তার ইউএসবি পাওয়ার সাপ্লাইয়ের সাথে যুক্ত পাম্পটিকে গ্রাউন্ডে সংযুক্ত করে। রিলে চালু হয়ে গেলে, এটি সার্কিটটি সম্পূর্ণ করে, পাম্পে 5V প্রেরণ করে, পাম্পটি চালু করে।

## রিলে নিয়ন্ত্রণ

আমাদের আইওটি ডেভ কিট থেকে রিলে নিয়ন্ত্রণ করা যাবে।

### কাজ - রিলে নিয়ন্ত্রণ

সঠিক হার্ডওয়্যার পছন্দ অনুসারে নিচের যেকোন একটি গাইডের মাধ্যমে কাজ শুরু করতে হবে।

* [Arduino - Wio Terminal](wio-terminal-relay.md)
* [Single-board computer - Raspberry Pi](pi-relay.md)
* [Single-board computer - Virtual device](virtual-device-relay.md)

## MQTT দ্বারা উদ্ভিদ নিয়ন্ত্রণ

এখনো অবধি আমাদের রিলেটি আইওটি ডিভাইস দ্বারা নিয়ন্ত্রিত হয়েছে, মাটির আর্দ্রতার পরিমাপের উপর ভিত্তি করে। একটি বাণিজ্যিক সেচ ব্যবস্থায়, এই নিয়ন্ত্রণের যুক্তিটিকে আরো বাস্তবধর্মী করা হবে যা একাধিক সেন্সর থেকে প্রাপ্ত ডেটা ব্যবহার করে পানি সরবরাহ করার সিদ্ধান্ত গ্রহণ করবে এবং এতগুলো মানদন্ডের কোনটি কনফিগারেশন বা পরিবর্তন করতে হলে, এক জায়গা থেকেই তা পরিবর্তন করার সুযোগ রাখতে হবে। এটির জন্য, MQTT দ্বারা রিলে নিয়ন্ত্রণ করতে হবে।

### কাজ - MQTT দ্বারা রিলে নিয়ন্ত্রণ

1. MQTT তে সংযোগের জন্য `soil-moisture-sensor` প্রজেক্টে প্রাসঙ্গিক MQTT লাইব্রেরি / pip প্যাকেজ এবং কোড যুক্ত করতে হবে।  ক্লায়েন্ট আইডির নাম হবে `soilmoisturesensor_client` যেখানে শুরুতে আমাদের আইডি লিখতে হবে।

    > ⚠️ এক্ষেত্রে [প্রজেক্ট-১, লেসন-৪ এ MQTT এর সাথে সংযোগ অংশটিতে বিস্তারিত নির্দেশাবলী রয়েছে](../../../../1-getting-started/lessons/4-connect-internet/README.md#connect-your-iot-device-to-mqtt) যা প্রয়োজনে দেখা যেতে পারে।

1. মাটির আর্দ্রতা সহ টেলিমেট্রি ডেটা পাঠানোর জন্য প্রয়োজনীয় কোড সংযোজন করতে হবে। টেলিমেট্রি ম্যাসেজের জন্য, প্রপার্টি এর নাম হবে `soil_moisture` ।

    > ⚠️ এক্ষেত্রে [প্রজেক্ট-১, লেসন-৪ এ টেলিমেট্রি ডেটা পাঠানো অংশটিতে বিস্তারিত নির্দেশাবলী রয়েছে](../../../../1-getting-started/lessons/4-connect-internet/README.md#send-telemetry-from-your-iot-device) ।

1. টেলিমেট্রিতে সাবস্ক্রাইব করার জন্য লোকাল সার্ভার কোড লিখতে হবে এবং `soil-moisture-sensor-server` নামক ফোল্ডারে রিলে নিয়ন্ত্রণ করার জন্য একটি কমান্ড প্রেরণ করতে হবে। কমান্ড ম্যাসেজে এই প্রপারটির নাম হবে `relay_on` এবং ক্লায়েন্ট আইডি `soilmoisturesensor_server` যেখানে শুরুতে আমাদের আইডি লিখতে হবে। প্রজেক্ট-১, লেসন-৪ এর জন্য যে সার্ভার কোডটি লিখেছিলাম, সেই একই কাঠামো রাখতে হবে এবং পরে এই কোডটিতে আরো কিছু যুক্ত হবে।

    > ⚠️ এক্ষেত্রে [প্রজেক্ট-১, লেসন-৪ এর সার্ভার কোড লেখার অংশটি](../../../../1-getting-started/lessons/4-connect-internet/README.md#write-the-server-code) এবং সেই একই পাঠের [MQTT এর মাধ্যমে কমান্ড পাঠানো সংক্রান্ত নির্দেশনা](../../../1-getting-started/lessons/4-connect-internet/README.md#send-commands-to-the-mqtt-broker) প্রয়োজনে দেখা যেতে পারে।

1. প্রাপ্ত কমান্ড থেকে রিলে নিয়ন্ত্রণের জন্য রিলে লিখতে হবে,  ম্যাসেজ থেকে  `relay_on` প্রপার্টি ব্যবহার করতে হবে। `soil_moisture` যদি ৪৫০ এর বেশি হয়, তবে `relay_on` এর জন্য TRUE হবে অন্যথায় FALSE ।

    > ⚠️ [প্রজেক্ট-১, লেসন-৪ এ MQTT এর কমান্ডগুলোর প্রতিক্রিয়া জানানোর অংশে বিস্তারিত নির্দেশাবলী রয়েছে](../../../../1-getting-started/lessons/4-connect-internet/README.md#handle-commands-on-the-iot-device)।

> 💁 এই কাজের সকল কোড [code-mqtt](./code-mqtt) ফোল্ডারে রয়েছে।

ডিভাইস এবং লোকাল সার্ভারে কোডটি চলছে কিনা তা আমাদেরকে নিশ্চিত করতে হবে। এজন্য ভার্চুয়াল সেন্সর দ্বারা প্রেরিত মানগুলি পরিবর্তন করে বা মাটির আর্দ্রতা স্তর পরিবর্তন করে এটি আমাদেরকে পরীক্ষা করতে হবে।

## সেন্সর এবং অ্যাকচুয়েটরের টাইমিং

লেসন-৩ এ আমরা একটি নাইটলাইট তৈরি করেছিলাম - একটি এলইডি যা আলোক সেন্সর দ্বারা নিম্ন স্তরের আলো সনাক্ত হওয়ার সাথে সাথেই জ্বলে উঠে। আলোক সেন্সরটি তাত্ক্ষণিকভাবে আলোর স্তরের পরিবর্তন সনাক্ত করে এবং ডিভাইসটি দ্রুত প্রতিক্রিয়া জানায় যা অবশ্য `loop` বা `while True:` ফাংশনে প্রদত্ত Delay এর সময় এর দ্বারা প্রভাবিত হয়। আইওটি ডেভলাপার হিসাবে আমরা সর্বদা এই জাতীয় দ্রুত প্রতিক্রিয়া লুপের উপর নির্ভর করতে পারব না।

### মাটির আর্দ্রতার জন্য টাইমিং

যদি আমরা কোন হার্ডওয়্যার সেন্সর ব্যবহার করে মাটির আর্দ্রতা বিষয়ক গত লেসনের কাজগুলো করি, তবে এটি তো নিশ্চয়ই আমরা খেয়াল করেছি যে, গাছটিতে জল দেওয়ার পরে মাটির আর্দ্রতা রিডিং আসতে কয়েক সেকেন্ড সময় নিয়েছিল। এটি সেন্সরটি ধীর হওয়ার কারণে নয়, বরং পানি দ্বারা মাটি ভিজতে সময় লাগে বলে।

> 💁 আমরা যদি সেন্সরটির খুব কাছাকাছি পানি সরবরাহ করি, তবে আমরা দেখবো আর্দ্রতার মান খুব বেড়ে যাওয়ার পর দ্রুতই কমছে, আবার আস্তে আস্তে বাড়ছে- এটি সেন্সরটির নিকটবর্তী পানি পুরো মাটি জুড়ে ছড়িয়ে পড়ার প্রক্রিয়ার কারণেই হচ্ছে ।

![A soil moisture measurement of 658 doesn't change during watering, it only drops to 320 after watering when water has soaked through the soil](../../../../images/soil-moisture-travel.png)

উপরের ডায়াগ্রামে মাটির আর্দ্রতা পাঠ 655 দেখায় । উদ্ভিদটিতে জল সরবরাহ করা হয়েছে, তবে এই রিডিং তৎক্ষণাৎ পরিবর্তন হয় না, কারণ পানি তখনও সেন্সরে পৌঁছেনি। সেন্সরে পৌঁছানোর আগেই পানি শেষ হয়ে যেতে পারে এবং এতে নতুন আর্দ্রতার মান কমে যায়।

আমরা যদি মাটির আর্দ্রতার মাত্রা ভিত্তিতে, রিলে এর মাধ্যমে কোন সেচ ব্যবস্থা নিয়ন্ত্রণের কোড লিখি, তবে এই বিলম্বকে বিবেচনায় নেওয়া উচিত এবং আইওটি ডিভাইসে স্মার্টভাবে সময় নির্ধারণ করতে হবে।

✅  কীভাবে এটি করা যাবে, তা কিছুক্ষণ সময় নিয়ে চিন্তা করি।

### সেন্সর এবং অ্যাকচুয়েটরের টাইমিং নিয়ন্ত্রণ

কল্পনা করি যে, আমাদেরকে একটি খামারের জন্য সেচ ব্যবস্থা তৈরি করার দায়িত্ব দেওয়া হয়েছে। মাটির ধরণের উপর ভিত্তি করে, উদ্ভিদের আদর্শ মাটির আর্দ্রতার মান 400-450 এর অ্যানালগ ভোল্টেজের সমান।

আমরা ডিভাইসটি নাইটলাইটের মতো একইভাবে প্রোগ্রাম করতে পারি - সেন্সর মান 450 এর জন্য, পাম্প চালু করতে রিলে চালু করতে হবে। সমস্যাটি হল পাম্প থেকে মাটির ভেতর দিয়ে সেন্সরে পানি যেতে কিছুটা সময় নেয়। সেন্সরটি 450 এর একটি আর্দ্রতা স্তর সনাক্ত করে যখন পানি থামিয়ে দেবে, তখনও কিন্তু পাম্পযুক্ত জল মাটিতে শোষিত হতে থাকায় পানির স্তর কমতে থাকবে। ফলস্বরূপ, এই পুরো প্রক্রিয়ায় পানি নষ্ট হয় এবং শিকড়ের ক্ষতির ঝুঁকি থাকে।

✅ মনে রাখতে হবে - অত্যধিক পানি গাছের পক্ষে ক্ষতিকর এবং এটি একটি মূল্যবান প্রাকৃতিক সম্পদ নষ্ট করে।

এক্ষেত্রে আগে এটি বুঝতে হবে যে একচুয়েটরের কাজ শুরু করা এবং সেন্সর এর কাছ থেকে ডেটা আসা, এর মধ্যে একটু পারষ্পরিক বিলম্ব বা delay হচ্ছে। এর অর্থ সেন্সরটির মান পরিমাপ করার আগে কিছুক্ষণ অপেক্ষা করা উচিত এবং পরবর্তী পরিমাপ নেয়ার আগে একচুয়েটর কিছু সময়ের জন্য বন্ধ রাখতে হবে।

প্রতিবার রিলে কতক্ষণ হওয়া উচিত? এক্ষেত্রে সতর্ক থাকা উচিত এবং কেবল অল্প সময়ের জন্য রিলে চালু করা ভাল। তারপরে মাটি ভালমতো ভিজতে অপেক্ষা করতে হবে এবং তখন আর্দ্রতার স্তরটি আবার যাচাই করতে হবে। সবথেকে গুরুত্বপূর্ণ বিষয়টি হলো, আমরা চাইলেই আরও পানি যুক্ত করতে সর্বদা এটি চালু করতে পারি, কিন্তু মাটি থেকে আমরা পানি সরাতে পারবো না।

> 💁 এই ধরণের টাইমিং কন্ট্রোল সংক্রান্ত বৈশিষ্ট্যগুলো সুনির্দিষ্টভাবে নির্ভর করে - আমরা যে আইওটি ডিভাইসটি তৈরি করছি তার উপর, যে বৈশিষ্ট্য পরিমাপ করছি এবং যেধরণের সেন্সর ও অ্যাকচুয়েটর ব্যবহার করছি তার উপর।
> 
![A strawberry plant connected to water via a pump, with the pump connected to a relay. The relay and a soil moisture sensor in the plant are both connected to a Raspberry Pi](../../../../images/strawberry-with-pump.png)

মনে করি, আমাদের কাছে মাটির আর্দ্রতা সেন্সর এবং একটি রিলে দ্বারা নিয়ন্ত্রিত একটি পাম্প সহ একটি স্ট্রবেরি গাছ রয়েছে। আমরা পর্যবেক্ষণ করেছি যে যখনই পানি দেয়া হয়, মাটির আর্দ্রতা রিডিং স্থির হতে প্রায় 20 সেকেন্ড সময় লাগে। এর অর্থ হল রিলেটি বন্ধ করতে হবে এবং আর্দ্রতার মাত্রা পরীক্ষা করার আগে 20 সেকেন্ড অপেক্ষা করতে হবে। অত্যধিক পানির তুলনায় বরং পানি একটু একটু করে দেয়াই ভালো -আমরা চাইলেই আরও পানি যুক্ত করতে সর্বদা পাম্প চালু করতে পারি, কিন্তু মাটি থেকে আমরা পানি সরাতে পারবো না।

![Step 1, take measurement. Step 2, add water. Step 3, wait for water to soak through the soil. Step 4, retake measurement](../../../../images/soil-moisture-delay.png)

অর্থাৎ সেচ দেয়ার জন্য সর্বোত্তম ধাপগুলি হবে

* 5 সেকেন্ডের জন্য পাম্পটি চালু করা
* 20 সেকেন্ড অপেক্ষা 
* মাটির আর্দ্রতা পরীক্ষা করা
* যদি আর্দ্রতা স্তর প্রয়োজনের তুলনায় এখনও বেশি থাকে, তবে উপরের পদক্ষেপগুলি পুনরাবৃত্তি করুন

পাম্পের জন্য 5 সেকেন্ড খুব দীর্ঘ হতে পারে, বিশেষত যদি আর্দ্রতার মাত্রা প্রয়োজনীয় স্তরের থেকে কিছুটা উপরে থাকে। কতটা সময় এখানে ব্যবহার করতে হবে, তা জানার সর্বোত্তম উপায় হল বারবার কাজটি করে প্রতিক্রিয়া দেখা এবং সে অনুযায়ী সময় নির্দিষ্ট করা। এটি আরও বেশি সুনির্দিষ্ট করা যেতে পারে যেমন, স্থির 5 সেকেন্ডের পরিবর্তে প্রয়োজনীয় মাটির আর্দ্রতার মান প্রতি 100 অতিরিক্ত এর জন্য 1 সেকেন্ড সময় পাম্পটি চালু রাখা।

✅ ছোট একটি গবেষণাঃ এখানে অন্য কোনভাবে কী সময়কে পর্যালোচনা করা যাবে? আর্দ্রতা কমে গেলেই কী গাছে পানি দেয়া উচিত নাকি এখানে গাছে পানি দেয়ার জন্য ভাল এবং খারাপ সময় থাকে - এরকম কিছুও বিবেচনা করতে হবে?

> 💁 ঘরের বহিরাঙ্গনে উদ্ভিদের বৃদ্ধির জন্য স্বয়ংক্রিয় সেচব্যবস্থা নিয়ন্ত্রণ করার সময় আবহাওয়ার পূর্বাভাসগুলিও বিবেচনায় নেওয়া উচিত। যদি বৃষ্টিপাত হওয়ার সম্ভাবনা থাকে, তবে বৃষ্টি শেষ হওয়ার পর্যন্ত পেক্ষা করা যেতে পারে। তখন হয়তো মাটি পর্যাপ্ত পরিমাণে আর্দ্র হতে পারে যেকারণে এটির আর পানির প্রয়োজন হয় না।

## উদ্ভিদ নিয়ন্ত্রণ সার্ভারে টাইমিং যুক্ত করা

সেচকার্যের সময়কালের নিয়ন্ত্রণ যুক্ত করতে এবং মাটির আর্দ্রতার মাত্রা পরিবর্তনের জন্য অপেক্ষা করতে সার্ভার কোডটি সংশোধন করা যেতে পারে। রিলে সময় নিয়ন্ত্রণের জন্য এলগরিদম হবে -

1. টেলিমেট্রি বার্তা গৃহীত
1. মাটির আর্দ্রতার স্তরটি পরীক্ষা করা
1. যদি ঠিক থাকে তবে কিছুই করতে হবেনা। তবে রিডিং যদি খুব বেশি হয় (যার অর্থ মাটির আর্দ্রতা খুব কম) তবে:
    1. রিলে চালু করার নির্দেশ প্রেরণ
    1. 5 সেকেন্ড অপেক্ষা করতে হবে
    1. রিলে বন্ধ করার নির্দেশ প্রেরণ
    1. মাটির আর্দ্রতা স্তর স্থিতিশীল হওয়ার জন্য 20 সেকেন্ড অপেক্ষা করতে হবে

সেচকার্যের পুরো প্রক্রিয়ায়, টেলিমেট্রি বার্তা গ্রহণ থেকে শুরু করে মাটির আর্দ্রতা স্তরকে আবার প্রক্রিয়া করার জন্য প্রস্তুত হওয়া প্রক্রিয়াটি প্রায় 25 সেকেন্ড সময় নেয়। কিন্তু এদিকে আমরা প্রতি 10 সেকেন্ডে মাটির আর্দ্রতা স্তর সংক্রান্ত বার্তা প্রেরণ করছি, যা এখানে ওভারল্যাপ করছে । এক্ষেত্রে সমস্যা তৈরী হতে পারে।

.২ভাবে এটির সমাধান করা যায়ঃ

* আইওটি ডিভাইস কোডটি পরিবর্তন করতে হবে যেন প্রতি মিনিটে কেবল ১টি টেলিমেট্রি প্রেরণ হয়, এভাবে এখানে সামঞ্জস্য বজায় রাখা যাবে।
* সেচকার্য চলার সময়ে টেলিমেট্রি ম্যাসেজে আন-সাবস্ক্রাইব করা।

প্রথম অপশনটি সর্বদা বড় খামারগুলির জন্য ভাল সমাধান নয়। কৃষকের কাছে সেচ চলমান সময়ে মাটির আর্দ্রতার মান দরকার হতে পারে, তা পরে বিশ্লেষণের জন্য, উদাহরণস্বরূপ খামারের বিভিন্ন অঞ্চলে পানির প্রবাহ সম্পর্কে জানতে হলে এই তথ্য দরকার। দ্বিতীয় বিকল্পটি এক্ষেত্রে ভাল - কোডটি টেলিমেট্রিটিকে যখন ব্যবহার করতে  পারে না কেবল তখনই তা উপেক্ষা করছে, তবে টেলিমেট্রি এখনও অন্যান্য সার্ভিসের জন্য থাকবে এবং পরে ব্যবহার করা যাবে।

> 💁 আইওটি ডেটা কেবলমাত্র একটি ডিভাইস থেকে কেবল একটি সার্ভিসে প্রেরণ করা হয় না, বরং অনেক ডিভাইস কোন ব্রোকারের কাছে ডেটা প্রেরণ করতে পারে এবং অনেক সার্ভিস তখন সেই ব্রোকারের কাছ থেকে ডেটা নিতে পারে। উদাহরণস্বরূপ, একটি সার্ভিস মাটির আর্দ্রতার ডেটা নিতে পারে এবং এটি পরবর্তী তারিখে বিশ্লেষণের জন্য একটি ডাটাবেসে সংরক্ষণ করতে পারে। একটি সেচ ব্যবস্থা নিয়ন্ত্রণ করতে অন্য সার্ভিসও একই টেলিমেট্রি ডেটা ব্যবহার করতে পারে।

### কাজ - উদ্ভিদ নিয়ন্ত্রণ সার্ভারে সময় যুক্ত করা

5 সেকেন্ডের জন্য রিলে চালিয়ে, 20 সেকেন্ড অপেক্ষা করার জন্য সার্ভার কোড আপডেট করতে হবে।

1. ভিএস কোডে  `soil-moisture-sensor-server` ফোল্ডার খুলতে হবে (যদি আগে থেকে খোলা না থাকে)। ভার্চুয়াল এনভায়রনমেন্ট একটিভ কিনা তা নিশ্চিত করতে হবে।

1.  `app.py` ফাইল ওপেন করি।

1.  `app.py` ফাইলে নিচের কোড সংযুক্ত করি।

    ```python
    import threading
    ```

    এই স্টেটমেন্টটি পাইথন লাইব্রেরি থেকে `threading` ইম্পোর্ট করে, যা অপেক্ষমান সময়েও পাইথনকে কোড এক্সেকিউট করার সুযোগ দেয়।

1. `handle_telemetry` ফাংশনের উপরে নিম্নের কোড যুক্ত করি  যাতে সার্ভার কোড থেকে আসা টেলিমেট্রি ম্যাসেজ রিসিভ করা যায়ঃ

    ```python
    water_time = 5
    wait_time = 20
    ```

    এটি নির্দিষ্ট করে যে কতক্ষণ রিলে চলবে (`water_time`) এবং তারপর মাটির আর্দ্রতার জন্য কতক্ষণ অপেক্ষা করতে হবে (`wait_time`) সেই বিষয়।

1. তারপর কোডে নিচের অংশ যুক্ত করি

    ```python
    def send_relay_command(client, state):
        command = { 'relay_on' : state }
        print("Sending message:", command)
        client.publish(server_command_topic, json.dumps(command))
    ```

    এটি `send_relay_command` ফাংশনকে ডিফাইন করে যা  MQTT এর মাধ্যমে  রিলেতে নির্দেশ পাঠায়। এই টেলিমেট্রি ডিকশনারি হিসেবে তৈরী করা হলেও পরে এটিকে JSON এ রূপান্তর করা হয়।  `state` এ যে ভ্যালু পাঠানো হয় তা নির্ধারণ  করে যে, রিলে অন নাকি অফ থাকবে।

1.  `send_relay_code` ফাংশনের পর, কোডে নিচের অংশ যুক্ত করি।

    ```python
    def control_relay(client):
        print("Unsubscribing from telemetry")
        mqtt_client.unsubscribe(client_telemetry_topic)
    
        send_relay_command(client, True)
        time.sleep(water_time)
        send_relay_command(client, False)
    
        time.sleep(wait_time)
    
        print("Subscribing to telemetry")
        mqtt_client.subscribe(client_telemetry_topic)
    ```

    এটি প্রয়োজনীয় সময় এর পরিমাণের ভিত্তিতে রিলে নিয়ন্ত্রণ করতে একটি ফাংশনকে সংজ্ঞায়িত করে। এটি টেলিমেট্রি থেকে আন-সাবস্ক্রাইব করে শুরু হয় যাতে মাটির আর্দ্রতা বার্তাগুলি প্রক্রিয়াজাত না হয় যখন সেচ দেয়া হচ্ছে। এরপরে এটি রিলে চালু করার জন্য একটি নির্দেশ পাঠায়। এরপরে এটি `water_time` এর জন্য অপেক্ষা করে এবং তারপর রিলে বন্ধ করার জন্য নির্দেশ দেয় । অবশেষে এটি মাটির আর্দ্রতার মাত্রা `wait_time` সময় স্থিতিশীল হওয়ার জন্য অপেক্ষা করে। এরপরে এটি টেলিমেট্রিতে পুনরায় সাবস্ক্রাইব করে।


1. `handle_telemetry`ফাংশনটি এভাবে পরিবর্তন করিঃ

    ```python
    def handle_telemetry(client, userdata, message):
        payload = json.loads(message.payload.decode())
        print("Message received:", payload)
    
        if payload['soil_moisture'] > 450:
            threading.Thread(target=control_relay, args=(client,)).start()
    ```

    এই কোডটি মাটির আর্দ্রতার স্তর পরীক্ষা করে। যদি এটি 450 এর বেশি হয় তবে মাটিতে জল দেওয়া দরকার, তাই এটি `control_relay` ফাংশন কল করে। এই ফাংশনটি পৃথক থ্রেডে চালানো হয়, যা ব্যাকগ্রাউন্ডে কাজ করে।

1. আমাদের আইওটি ডিভাইসটি চলছে কিনা তা নিশ্চিত করে তারপরে এই কোডটি চালাতে হবে। মাটির আর্দ্রতার স্তর পরিবর্তন করি এবং রিলতে কী ঘটে তা পর্যবেক্ষণ করি - এটি 5 সেকেন্ডের জন্য চালু হওয়া উচিত এবং কমপক্ষে 20 সেকেন্ডের জন্য বন্ধ থাকা উচিত। কেবলমাত্র মাটির আর্দ্রতার মাত্রা পর্যাপ্ত না হলেই বরং আবার চালু হবে।

    ```output
    (.venv) ➜  soil-moisture-sensor-server ✗ python app.py
    Message received: {'soil_moisture': 457}
    Unsubscribing from telemetry
    Sending message: {'relay_on': True}
    Sending message: {'relay_on': False}
    Subscribing to telemetry
    Message received: {'soil_moisture': 302}
    ```

    সিমুলেটেড সেচ ব্যবস্থায় এটি পরীক্ষা করার একটি ভাল উপায় হল শুকনো মাটি ব্যবহার করা। তারপরে রিলে চালু থাকা অবস্থায় ম্যানুয়ালি পানি ঢালা, রিলে বন্ধ হয়ে গেলে তা বন্ধ করে দিতে হবে।
    
> 💁 এসকল কোড [code-timing](./code-timing) ফোল্ডারে পাওয়া যাবে।

> 💁 পাম্প দিয়ে সত্যিকারের সেচ ব্যবস্থা নির্মাণের জন্য,  [6V পাম্প](https://www.seeedstudio.com/6V-Mini-Water-Pump-p-1945.html) ব্যবহার করা এতে পারে যার থেকে একটি  [USB টার্মিনাল পাওয়ার সাপ্লাই](https://www.adafruit.com/product/3628) থাকবে। পাম্পে আসা বা এটি থেকে নির্গত পাওয়ার যেন রিলে দিয়ে সংযুক্ত থাকে - তা নিশ্চিত করতে হবে।

---

## 🚀 চ্যালেঞ্জ

এমন কি আরো কোন আইওটি বা অন্যান্য বৈদ্যুতিক ডিভাইস রয়েছে যেখানে একইরকম সমস্যা আছে - যেখানে অ্যাক্চুয়েটরের ফলাফল সেন্সরে পৌঁছাতে একটু সময় লাগে ? আমাদের বাড়িতে বা প্রতিষ্ঠানে এমন অনেক যন্ত্রই হয়তো আছে - একটু চিন্তা করি 

* তারা কোন বৈশিষ্ট্য পরিমাপ করে?
* অ্যাকচুয়েটর ব্যবহারের পরে বৈশিষ্ট্যটি পরিবর্তন হতে কত সময় লাগবে?
* বৈশিষ্ট্যটির প্রয়োজনীয় মান বা Required Value পরিবর্তন ঠিক আছে কি?
* এটি কীভাবে প্রয়োজনীয় মান এ ফিরে আসবে?

## লেকচার পরবর্তী কুইজ

[লেকচার পরবর্তী কুইজ](https://brave-island-0b7c7f50f.azurestaticapps.net/quiz/14)

## রিভিউ ও স্ব-অধ্যয়ন

টেলিফোন এক্সচেঞ্জসহ আরো বিভিন্ন ক্ষেত্রে রিলে এর ব্যবহার সম্পর্কে জানতে [উইকিপিডিয়া থেকে রিলে সংক্রান্ত আর্টিকেল](https://wikipedia.org/wiki/Relay) পড়া যায়।

## এসাইনমেন্ট

[অধিক কার্যকর সেচব্যবস্থা নির্মাণ](assignment.bn.md)