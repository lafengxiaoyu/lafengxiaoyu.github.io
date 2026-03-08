---
layout: page
title: Guestbook
permalink: /guestbook/
description: Leave your mark on my site
nav: true
nav_order: 5
---

Welcome to my guestbook! Feel free to leave a message and let me know you stopped by.

---

## Leave a Message

Feel free to share your thoughts, questions, or just say hello! Your message will be sent directly to me via email.

<form
  action="https://formspree.io/f/mzdjzqbw"
  method="POST"
  class="guestbook-form"
>
  <!-- Honeypot field to prevent spam -->
  <input type="text" name="_honey" style="display:none">

  <!-- Disable captcha -->
  <input type="hidden" name="_captcha" value="false">

  <!-- Subject -->
  <input type="hidden" name="_subject" value="Guestbook Message from Website">

  <div class="form-group">
    <label for="name">Name *</label>
    <input
      type="text"
      id="name"
      name="name"
      required
      placeholder="Your name"
      minlength="2"
      maxlength="50"
    >
  </div>

  <div class="form-group">
    <label for="email">Email</label>
    <input
      type="email"
      id="email"
      name="email"
      placeholder="your.email@example.com (optional)"
    >
  </div>

  <div class="form-group">
    <label for="message">Message *</label>
    <textarea
      id="message"
      name="message"
      required
      rows="5"
      placeholder="Write your message here..."
      minlength="10"
      maxlength="1000"
    ></textarea>
  </div>

  <!-- Math challenge for spam prevention -->
  <div class="form-group math-challenge">
    <label for="math-answer">Spam Check: What is 3 + 4? *</label>
    <input
      type="text"
      id="math-answer"
      name="math_answer"
      required
      placeholder="Enter the answer"
      pattern="7"
      title="Please solve: 3 + 4 = 7"
    >
  </div>

  <button type="submit" class="btn btn-primary">Send Message</button>
</form>

<div class="form-note">
  <small>
    * Required fields | Your email will only be used to reply to your message and will not be shared with third parties.
  </small>
</div>

---

## Visitor Map

See where visitors to this site are coming from:

<div class="visitor-map-container">
  <a href='https://clustrmaps.com/site/1c9e6' title='Visit tracker'>
    <img src='//clustrmaps.com/map_v2.png?cl=0e1633&w=600&t=m&d=bh2vz-rgpkICIWVOOPKZ0E5suGy5yCYj_Nbg-9D7oHY&co=0b4975&ct=cdd4d9'/>
  </a>
</div>

<div class="map-note">
  <small>This map shows the locations of visitors to this site. Your visit will be added to the map!</small>
</div>

---

## Connect with Me

You can also connect with me through:

- **LinkedIn**: [Connect with me](https://linkedin.com/in/gaozhenyu/)
- **GitHub**: [Create an issue](https://github.com/lafengxiaoyu/lafengxiaoyu.github.io/issues)

---

## Guestbook Policy

- Please be respectful and keep messages civil
- Spam, advertisements, or inappropriate content will be filtered
- Messages are sent directly to my email: 786424908@qq.com
- Your IP address may be logged for security purposes

Thank you for visiting!
