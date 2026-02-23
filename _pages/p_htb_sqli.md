---
layout: single
title: "Hack The Box: SQL Injection Fundamentals - Assessment"
permalink: /p_htb_sqli/
author_profile: true
toc: true
toc_label: "HTB SQL Injection Fundamentals"
---

I enjoy pushing my limits, so when I discovered the "SQL Injection Fundamentals" module on Hack The Box, I decided to document my approach and solutions of its `Skill Assesssment` part in this write-up.

# **Scenario**
You have been contracted by `chattr GmbH` to conduct a penetration test of their web application. In light of a recent breach of one of their main competitors, they are particularly concerned with `SQL injection vulnerabilities` and the damage the discovery and successful exploitation of this attack could do to their public image and bottom line.

They provided a target IP address and no further information about their website. Perform an assessment specifically focused on testing for SQL injection vulnerabilities on the web application from a "black box" approach.
{% include figure image_path="/assets/img/htb_sqli_login.png" caption="Login Page"%}

# **My Approach**
Upon landing in the login page and seeing username and password field, I immediately try injecting “ admin’ or 1=1; — “ on username and password. However, that didn’t work as the page consistently showed “username or password is wrong”. At this point, I can only assume that there is no vulnerability in the login page.
{% include figure image_path="/assets/img/htb_sqli_login_novuln.png"%}
Hence, I moved on by trying to create an account first on the registration page.
The registration page has 4 fields:
- Username
- Password
- Repeat password
- Invitation code
The form looks straightforward with one exception, I don’t have the “Invitation code”.

Inputting random code into the field produced “invalid invitation code” notice, a valid error checking.  {% include figure image_path="/assets/img/htb_sqli_reg_invalid.png"%}

Common SQLi checks (with quote ‘ or double quote “) reveal no vulnerabilities on the first 3 fields, but the invitation code field produced error 500.

I turn to Burp Suite for more detail.

In HTTP History tab, I found several validation rules on /static/js/register.js file  {% include figure image_path="/assets/img/htb_sqli_invitation_rules.png"%}

In particular, the invitation code needs to conform to this format: [4 letters - 4 letters - 4 digits].{% include figure image_path="/assets/img/htb_sqli_invitation_rules_explained.png"%}

Could it be that any random code would work as long as the format matches? I decided to try it. {% include figure image_path="/assets/img/htb_sqli_invitation_format.png"%}
Using BurpSuite, I try to modify the invitation code payload by injecting SQLi payloads. Funnily enough, these payloads still gave me error 500:
- ‘ OR 1=1
- ‘ OR ‘1’ = ‘1
- ‘ OR ‘1’ = ‘1’{% include figure image_path="/assets/img/htb_sqli_reg_burp_pattern1.png"%}
After several more tries, I successfully bypassed the registration by adding closing parentheses ‘)’ in the payload:  )’ OR ‘1’ = ‘1 {% include figure image_path="/assets/img/htb_sqli_reg_burp_pattern2.png"%} {% include figure image_path="/assets/img/htb_sqli_reg_burp_successful.png"%}
