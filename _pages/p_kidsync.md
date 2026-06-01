---
layout: single
title: "KidSync: How a Wrong Uniform Turned Into a Weekend Project"
excerpt: "My son wore his PE kit on Sports Day. My daughter submitted homework a day late. The teacher group chat had warned us both times — we just never caught it. So I built something."
author_profile: true
permalink: /p_kidsync/
date: 2026-01-01
last_modified_at: 2026-01-01
toc: true
toc_label: "Table of Contents"
toc_icon: "scroll"
toc_sticky: true
tags:
  - automation
  - homelab
  - ai
  - google-apps-script
  - project
  - parenting
categories:
  - Projects
header:
  teaser: /assets/img/kidsync_banner.png
---

My son walked into school on Sports Day in his regular uniform. Every other kid was in PE kit. The teacher *had* posted a reminder — buried somewhere between a forwarded meme and seventeen "👍" replies in the parent WhatsApp group.

That same week, my daughter missed a homework deadline. Same reason.

Two incidents. One week. Same root cause.

---

## The Problem Wasn't Forgetfulness

My first instinct was *"we need to read the group chat more carefully."* That's the wrong diagnosis.

The real problem was information format mismatch. School reminders arrive in a noisy, unstructured stream — urgent notices mixed with chitchat, memes, and canteen photos. Our brains can't triage that reliably at 7 AM while making breakfast.

We had a shared family calendar. The problem was that *putting things into it* required someone to read the message, understand it, find the date, open Google Calendar, and type it all in. Four steps too many. So it rarely happened.

{% include figure class="kidsync-figure" popup=true image_path="/assets/img/kidsync_chaotic_whatsapp.jpg" alt="A chaotic parent WhatsApp group chat" caption="The kind of reminder stream that kept burying important school notices." %}

---

## The Fix: Make the Calendar Fill Itself

Before writing any code, I mapped it as a flow problem:

```
Teacher posts in WhatsApp group
        ↓
Parent copies and emails it to a shared family Gmail
  (subject: kidsync/child-name)
        ↓
AI reads the message, extracts what matters
        ↓
Calendar event created automatically
```

The key design decision was using **email as the input**. Not a bot, not a custom app — email. Because it's the one thing that required zero behavior change from my partner. Tools that add friction die within a week.

The message body goes to **Gemini**, Google's AI model. Teacher messages are messy — Indonesian, English, abbreviations, no standard format. Gemini reads them like a person would: it infers dates from words like *"besok"* (tomorrow), pulls out each individual item if the message mentions multiple things, and returns structured data the script can act on.

If Gemini isn't confident — no date found, content too vague — it doesn't guess. It sends both parents an alert to review manually. A wrong calendar event is worse than no calendar event.

{% include figure class="kidsync-figure" popup=true image_path="/assets/img/kidsync_flowchart.png" alt="KidSync automation flowchart" caption="The simplified flow: WhatsApp message in, calendar event out." %}

---

## What It Looks Like Now

A teacher sends a reminder. One of us copies it, sends it to the family Gmail with `kidsync/[child]` as the subject. Takes 20 seconds.

Within 30 minutes there's a calendar event — with details, reminders, and the right child's color. Both kids can see their own schedule on the shared tablet in the living room. My son checks it before school. My daughter uses it to pack her bag.

No missed events since. Uniforms correct. And honestly — the bigger surprise was the kids becoming more self-sufficient, because the information was finally in a format they could actually use.

{% include figure class="kidsync-figure" popup=true image_path="/assets/img/kidsync_tablet_calendar.png" alt="Google Calendar with color-coded events" caption="The shared tablet view makes each child's schedule easy to check at a glance." %}

---

## What I Took Away From This

The hardest part wasn't the code — it was diagnosing the problem correctly. Once I stopped blaming forgetfulness and asked *why* information wasn't reaching us reliably, the solution became obvious.

The other thing that stuck: **AI is most useful when you constrain it well.** Gemini's output quality was almost entirely determined by how clearly I defined the task — which is the same skill as writing a good requirements brief. Different context, same thinking.

---

## The Stack

Entirely free. No servers. No monthly bills.

| Component | Tool |
|-----------|------|
| Automation engine | Google Apps Script |
| AI extraction | Gemini 2.5 Flash (free tier) |
| Input | Gmail |
| Output | Google Calendar |
| Logging | Google Sheets |

The whole thing runs in a single script file. Setup takes about 30 minutes.

{% include figure class="kidsync-figure" popup=true image_path="/assets/img/kidsync_log.png" alt="Google Sheets processed log" caption="The processed log keeps track of what was created and what still needs review." %}

---

The full code and setup guide are on GitHub — written so a non-developer can follow it.

[**View KidSync on GitHub →**](https://github.com/bpsghub/kidsync){: .btn .btn--primary}

---

*The wrong uniform was annoying. Building the thing that prevented the next one was kind of fun.*
