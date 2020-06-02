---
layout: post
title:  "Before validation - step 1: normalization"
date:   2020-05-19 17:12:04 -0700
categories: jekyll update
---
When data crosses the trust boundaries, we must make sure all data is non-malicious and appropriate. For input, limit the possibilities of attacks; for output, avoid leaking any sensitive informatioon. How to achieve that? Validate the data. 

#### Data validation
One common attack on web application is XSS. Part of the stratege to avoid it is to forbid the "\<script\>" tag in the input. Here is an example of how to do validation. 
```
Pattern pattern = Pattern.compile("[<>]");
Matcher matcher = pattern.matcher(input);
if ( matcher.find() ) {
    throw new IllegalStateException();
} else {
    // ...
}
```

What if the input is 
```
String input = "\uFE64" + "script" + "\uFE65";
```
Java uses unicode for Characters

Java version | Release date       |  Unicode version
------------ |------------------- | ----------------
Java 11      |  March 2018        |   Unicode 10.0
Java 10      |  September 2018	  |   Unicode 8.0
Java 9       |  September 2017    |   Unicode 8.0
Java 8       |  March 2014	      |   Unicode 6.2
Java SE 7    |  July 28, 2011     |   Unicode 6.0
Java SE 6    |  December 11, 2006 |   Unicode 4.0

"\uFE64" repensents "<", "\uFE65" represents ">".

In this case, after validation, the input may still contain \<script\> tags.
Here is one Java secure coding rule for string validation:
```
IDS01-J. Normalize strings before validating them.
```
Below is the Java Normalizer class, where there are four major nomalization forms, NFC, NFD, NFKC and NFKD.

```
public final class Normalizer {

    private Normalizer() {};

    /**
    * This enum provides constants of the four Unicode normalization forms
    * that are described in
    * <a href="http://www.unicode.org/unicode/reports/tr15/tr15-23.html">
    * Unicode Standard Annex #15 — Unicode Normalization Forms</a>
    * and two methods to access them.
    *
    * @since 1.6
    */
    
    public static enum Form {

        /**
        * Canonical decomposition.
        */
        NFD,
 
        /**
        * Canonical decomposition, followed by canonical composition.
        */
        NFC,
 
        /**
        * Compatibility decomposition.
        */
        NFKD,
 
        /**
        * Compatibility decomposition, followed by canonical composition.
        */
        NFKC
    }
 
    /**
    * Normalize a sequence of char values.
    * The sequence will be normalized according to the specified normalization
    * from.
    * @param src        The sequence of char values to normalize.
    * @param form       The normalization form; one of
    *                   {@link java.text.Normalizer.Form#NFC},
    *                   {@link java.text.Normalizer.Form#NFD},
    *                   {@link java.text.Normalizer.Form#NFKC},
    *                   {@link java.text.Normalizer.Form#NFKD}
    * @return The normalized String
    * @throws NullPointerException If <code>src</code> or <code>form</code>
    * is null.
    */
    public static String normalize(CharSequence src, Form form) {
	    return NormalizerBase.normalize(src.toString(), form);
    }
 
    /**
    * Determines if the given sequence of char values is normalized.
    * @param src        The sequence of char values to be checked.
    * @param form       The normalization form; one of
    *                   {@link java.text.Normalizer.Form#NFC},
    *                   {@link java.text.Normalizer.Form#NFD},
    *                   {@link java.text.Normalizer.Form#NFKC},
    *                   {@link java.text.Normalizer.Form#NFKD}
    * @return true if the sequence of char values is normalized;
    * false otherwise.
    * @throws NullPointerException If <code>src</code> or <code>form</code>
    * is null.
    */
    public static boolean isNormalized(CharSequence src, Form form) {
	    return NormalizerBase.isNormalized(src.toString(), form);
    }
}
```
Note:
- There is information loss during conversion. Should be used only when necessary.

#### Compliant Solution:
```
input = Normalizer.normalize(input, Form.NFKC);

Pattern pattern = Pattern.compile("[<>]");
Matcher matcher = pattern.matcher(input);
if ( matcher.find() ) {
    throw new IllegalStateException();
} else {
    // ...
}
```

