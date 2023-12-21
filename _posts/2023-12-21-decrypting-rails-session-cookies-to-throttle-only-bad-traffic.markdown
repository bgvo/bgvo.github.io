---
layout: post
title: "Decrypting Rails Session Cookies to Throttle Only Bad Traffic"
date: 2023-12-20 19:01:00
categories: rails security encryption
---


# Decrypting Rails Session Cookies to Throttle Only Bad Traffic
## Introduction
In a recent project, our team utilized Rack::Attack to manage and throttle incoming traffic, aiming to mitigate potential harm from malicious entities. Considering our application is accessible only to logged-in users, it seemed logical to apply different throttling rules for authenticated users. However, a challenge arose: the methods like `current_user` from Devise are unavailable at the middleware level where Rack::Attack operates. Our solution was to decrypt the Rails session cookie for identifying registered users. This post details our journey.

## How Rails Session Cookies Work
Rails session cookies are pivotal for maintaining user sessions in a web application. They store session data on the client-side, ensuring a seamless user experience. The session data is encoded and secured with a server-side secret, making it tamper-proof. 

- **Encoding and Security**: The session data is serialized and then encoded with Base64. Rails uses a secret key base for encryption, ensuring that the data cannot be read or altered without the key.
- **Client-Side Storage**: The cookie is stored in the user's browser, allowing Rails to retrieve and decode it with each request, maintaining session continuity.

This mechanism is integral to how users interact with Rails applications, ensuring both security and functionality.

## Decrypting Rails Session Cookie
Deciphering the Rails session cookie, though challenging given the Rails framework's complexity, is possible. The decryption process hinges on understanding the encryption method used.

### Encryption Process
Rails employs a sophisticated encryption process to secure session data. 

- **Step-by-Step Breakdown**:
  1. Serialization: Session data is converted into a string format.
  2. Encryption: This string is encrypted using an AES cipher.
  3. Encoding: The encrypted data is then Base64 encoded for safe transmission via cookies.

Knowing this, we can reverse the process to access the session data.

### Decryption Steps
The decryption process involves several critical steps:

1. **Splitting the Cookie**: The session cookie comprises three parts - an initialization vector, an authentication tag, and the encrypted data. We separate these using Base64 decoding.

   {% highlight ruby linenos %}
   data, iv, auth_tag = session_cookie&.split("--")&.map { |v| Base64.strict_decode64(v) }
   {% endhighlight %}
   
   The authentication tag is 16 bytes long, which aligns with the standard length for AES encryption, ensuring integrity and authenticity.

2. **Setting Up the Cipher**:
   We configure an OpenSSL cipher for decryption, using the same parameters as Rails for encryption.

   {% highlight ruby linenos %}
   cipher = OpenSSL::Cipher.new("aes-256-gcm")
   iteration_count = configuration.iteration_count
   salt = options[:salt] || configuration.salt
   hash_digest_class = options[:hash_digest_class] || configuration.hash_digest_class
   secret = OpenSSL::PKCS5.pbkdf2_hmac(key, salt, iteration_count, cipher.key_len, hash_digest_class.new)
   {% endhighlight %}

   This step includes generating a secret key using the same algorithm Rails uses, ensuring compatibility.

3. **Decrypting the Data**:
   We configure the cipher for decryption and process the encrypted data.

   {% highlight ruby linenos %}
   cipher.decrypt
   cipher.key = secret
   cipher.iv = iv
   cipher.auth_tag = auth_tag
   cipher.auth_data = ""
   {% endhighlight %}

   Finally, we decrypt the data and parse it into a readable format.

   {% highlight ruby linenos %}
   cookie_payload = cipher.update(data)
   cookie_payload << cipher.final
   JSON.parse(cookie_payload)
   {% endhighlight %}

## Integrating Decryption into a Gem
To maximize the utility and security of this decryption process, we encapsulated it into a Ruby gem. This approach offers several advantages:

- **Community Oversight**: Open-sourcing this functionality invites scrutiny and contributions, enhancing security.
- **Ease of Integration**: The gem can be easily integrated into any Rails project.
- **Security through Obscurity**: While the code is accessible, embedding it in a gem makes it less conspicuous in the application.

I encourage you to explore and contribute to the gem repository. Your feedback is invaluable, and if you find it helpful, please star the repo to aid others in discovering it.

[Link to the Gem Repository]

-- END OF THE BLOG POST --
