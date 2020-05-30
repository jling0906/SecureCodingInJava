---
layout: post
title:  "Sanitization"
date:   2020-05-28 23:14:50 -0700
categories: jekyll update
---
This process ensures data conforms to the requirements of the subsystem to which it is passed.
- "Subsystem" refers to the components of an application, could be local developer code, third party libraries
- Should be applied to both input and output
- Contains:
    - Ensuring data without sensitive information, for example, user privacy related information
    - Elminating volnerabilies from unwanted characters, by removing, replacing, encoding or escaping those characters
    
#### Regular expressions are widely used to match the string of text. Java comes with java.util.regex package.

#### Example:

Suppose a system log file contains messages output by various system processes.
Some processes produce public messages and some processes produce sensitive messages marked “private”, since some of the private logs contain ssn or other sensitive information.

```
10:47:03 private[423] Successful logout name: usr1 ssn: 111223333 
10:47:04 public[48964] Failed to resolve network service
10:47:04 public[1] (public.message[49367]) Exited with exit code: 255 
10:47:43 private[423] Successful login name: usr2 ssn: 444556666 
10:48:08 public[48964] Backup failed with error: 19
```

Goals:

    - Permit user to search the log file for interesting messages
    - Prevent user from seeing any private messages

Insecure solution:

```
(.*? +public\[\d+\] +.*<SEARCHTEXT>.*)
```
The problem lies in the **\<SEARCHTEXT\>**, attcker can put a **regex injection** with the following text:
```
.*)|(.*
```
The regex becomes
```
(.*? +public\[\d+\] +.*.*)|(.*.*)
```
This regex will match any line in the log file, including the private ones.

### Solution:
- A secure solution is to filter out **non-alphanumeric characters (except space and single quote)** from the search string, which prevents regex injection.
- Another method of mitigating this vulnerability is to filter out the sensitive information prior to matching.
Such a solution would require the filtering to be performed every time the log file is loaded into memory, incurring extra complexity and a performance penalty.
```
public class Keywords {
   // ...
    public static Set<String> suggestSearches(String search) { 
        synchronized (lock) {
            Set<String> searches = new HashSet<String>(); 
            StringBuilder sb = new StringBuilder(search.length()); 
            for (int i = 0; i < search.length(); ++i) {
                char ch = search.charAt(i);
                if (Character.isLetterOrDigit(ch) || ch == ' ' || ch == '\'') {
                    sb.append(ch);
                } 
            }
            search = sb.toString();
            // Construct regex dynamically from user string 
            String regex = "(.*? +public\\[\\d+\\] +.*" + search + ".*)"; 
            // ...
        }   
    }
}...
```
