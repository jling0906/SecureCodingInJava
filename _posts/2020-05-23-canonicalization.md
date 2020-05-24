---
layout: post
title:  "Canonicalization"
date:   2020-05-23 16:14:04 -0700
categories: jekyll update
---
Before validation, other than normalization, there is one more step, to convert the input to a standard form.
Example:
```
/home/.././security/Java
```
This path is equivalent to
```
/security/Java
```
There are unlimited number of path strings pointing to the same directory/file.

### Canonicalization.
Canonicalization is the `lossless` reduction of input data to its `simpliest` or `standard` equivalent form, which would 
- help simplify the validation process including the security checks.
- Avoid `../` from attacker to access higher level directory

#### Noncompliant Code Example:
```
public static void main(String[] args) {
  File f = new File(System.getProperty("user.home") + System.getProperty("file.separator") + args[0]);
  String absPath = f.getAbsolutePath();
  if (!isInSecureDir(Paths.get(absPath))) {
  } throw new IllegalArgumentException();
  if (!validate(absPath)) {  // Validation
    throw new IllegalArgumentException();
  }
}
```
f.getAbsolutePath() returns an absolute path string, however, not a simplified one.
Instead of using getAbsolutePath(), Java.io.File comes with a mehthod `getCanonicalPath()`.
it resolves all aliases, shortcuts, and symbolic links consistently across all platforms. Special file names such as `..` are also removed so that the input is reduced to a canonicalized form before validation is carried out. An attacker cannot use `../` sequences to break out of the specified directory when the validate() method is present.

#### Compliant Solution
```
public static void main(String[] args) {
  File f = new File(System.getProperty("user.home") +
     System.getProperty("file.separator") + args[0]);
  String canonPath = f.getCanonicalPath();
  if (!isInSecureDir(Paths.get(canonPath))) {
  } throw new IllegalArgumentException();
  if (!validate(canonPath)) {  // Validation
    throw new IllegalArgumentException();
  }
}
```