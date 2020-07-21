---
layout: post
title:  "Trust Boundaries"
date:   2020-05-17 23:10:04 -0700
categories: jekyll update
---
#### `Java program can execute, contain, or depend on both trusted and untrusted code.`
- JVM --- necessarily trusted
- Locally developed code --- could be trusted or untrusted depending on the programming practice.
- Third-party code.
     - Java runtime, JDK libraries --- trusted to some extent
     - Dynamically loaded code --- trusted level varies
        - System- or user-provided plug-ins
        - System-provided or downloaded libraries
        - Malicious attacker’s classes — obviously untrusted
     - Other third-party libraries --- could be trusted or untrusted.

### Trust Boundaries
Software often contains multiple components and libraries. Each component may operate in one or more trusted domains. For example:

    - Component A can access file-systems, but lacks access to any network
    - Component B has general network access, but lacks access to the file-system
    - Component C can access a secure network, but lacks the access to the file-system and general network.


#### Key idea 1: Distrustful Decomposition
Components have limited trust to each other.

Consequence: Must manage interactions between components with care:
    - Canonicalize, sanitize, normalize and validate inputs
        - Goal: limit potential attacks
    - Sanitize outputs
        - Goal: prevent information and capability leaks
    - Address rules for different code components to access system resources 
![](/SecureCodingInJava/images/distrustfulDecomposiotion.png)
#### Key idea 2: Privilege Separation
To limit impact of errors and of successful attacks
- During the design, decompose the system into separate components
- Each component possesses ONLY those privileges required for it to function

Consequence: 
- Component cannot perform other privileged operations

#### Key idea 3: Privilege Minimization
Privileges are not available all the time
- Be disabled most of the time
- Be enabled ONLY when required.

Consequence:
- Reduce the time of review
- Reduce the amount of privilege code
- Much easier to get it right
- Reduce the opportunities of possible attacks

#### `Good Coding Practices with Trust Boundaries`:
During the design of application, remember to define trust boundaries, do distrustful decomposition, privilege seperation and privilege minimization. Here are some guidelines:
- Trusted and untrusted code are never found in the same domain
    - This means trusted code and untrusted code should not be treated with the same security level, unless both of them are being treated as untrusted code. But we know security check is expensive, it is unnecessary to overpay for code we already know it is trusted.
-  Separately deployable components usually in different trust domains.
-  The previous guideline result in the fact that, you will find similar structure of trust domain map as the architecture diagram.
-  Operations limited to users with particular trust levels (or privileges) may help you find code that should be in separate trust domains
- If you feel you cannot trust something as much as your own program, that means new trust boundaries. Here are possible clues:
    - File system
    - System operations and administrators
    - Various kinds of users, management, etc
    - Input data from various source
    - Log files
    - Cloud service providers

