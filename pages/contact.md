---
layout: page
title: "Contact"
meta_title: "Contact me through the form"
show_meta: false
subheadline: "I want to know from you"
teaser: "Please, fill this form to contact me"
permalink: "/contact/"
---

<form action="https://getsimpleform.com/messages?form_api_token=6afeb70ee4fd12657b95c60f372b8def" method="post">
  <!-- the redirect_to is optional, the form will redirect to the referrer on submission -->
  <!--<input type='hidden' name='redirect_to' value='<the complete return url e.g. http://fooey.com/thank-you.html>' />-->
  <!-- all your input fields here.... -->
  <input class="form-control" type="text" name="name" placeholder="Name" />
  <input class="form-control" type="text" name="email" placeholder="Email" />
  <input class="form-control" type="text" name="message" placeholder="Message" />
  <button class="btn btn-primary" type="submit">Submit form</button>
</form>