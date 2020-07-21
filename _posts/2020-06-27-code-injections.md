---
layout: post
title:  "Code Injections"
date:   2020-06-27 22:42:04 -0700
categories: jekyll update
---
The vulnerability of code injection happens when there is dynamically constructed code such as JavaScript code in Java. There is a javax.script package enables Java executing JavaScript code. When those js code is constructed by untrusted input through trusted boundary, there could be an injection.
```
private static void evalScript( String firstName) throws ScriptException{
    ScriptEngineManager manager = new ScriptEngineManager();
    ScriptEngine engine = manager.getEngineByName("javascript");
    engine.eval("print('"+firstName+"')");
}
```
The attacker can inject malicious code in the "firstName":
```
    dummy\'); 
    var bw = new JavaImporter(java.io.BuggerWriter);
    var fw = new JavaImporter(java.io.FileWriter);
    with(fw) with (bw) {
        bwr = new BufferWriter( new FileWriter(\"config/cfg\"));
        bwr.write(\"some text\");
        bwr.close();
    }
    //;
```
The code end up printing the "dummy", and writing some text into the config.cfg file.
### Solution:
To defense against the code injection, as well as other type of injections, we should prevent the input without proper sanitization. Below is to allow only alphabetical characters and underscores.
```
    private static void evalScript( String firstName ) throws ScriptException {
        if ( !firstName.matches("[\\w]*")) {  
            throw new IllegalArgumentException();
        }
        ScriptEngineManager manager = new ScriptEngineManager();
        ScriptEngine engine = manager.getEngineByName("javascript");
        engine.eval("print('"+firstName + "')");
    }
```
Moreover, if some special characters have to be permitted in the input, we must normalize the input before doing sanitization. See [n
ormalization](https://jling0906.github.io/SecureCodingInJava/jekyll/update/2020/05/20/normalization.html).

### Conclusion:
- We must sanitize the untrusted input for dynamically constructed code.
- If special characters are allowed, normalize the untrusted input before sanitization.
![](/SecureCodingInJava/images/codeInjection.png)