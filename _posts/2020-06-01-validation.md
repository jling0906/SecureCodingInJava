---
layout: post
title:  "Validation"
date:   2020-06-01 10:45:50 -0700
categories: jekyll update
---
After we complete **normalization**, **canonicalization** and **sanitization**, it is the right time to do validation on the data.

#### **What is validation?**
The process of checking data to ensure it is landing in the intended domain of the receiver.
The domain depends the requirement of receiver, such as
- Check if the data fall in particular numeric range
- Unix `sudo` commands requires authentication, unless authentication was applied 30 seconds ago.
- System invariant checking: only files that are open can be read

#### **Where should validation occur?**
- **Caller validation**: the receiver side implements the validation
    - Can be faster, because the receiver knows what they want, which can help omit some unnecessary validation
    - Cause code bloat: Every caller must do the validation
    - May rely on some malicious callers to perform the validation

- **Callee validation**: the data sender implements the validation
    - Validation code can be encapsulated in a single location, which reduces the code size and the likelihood of incorrect validation
    - It is hard to forsee all the needs of callee, which may still need to do extra validation
- Both
- No validation
    - Fastest but vulnerable

**Note**: More validations means more oppotunities to make mistakes. It is unsafe and speedy-consuming to perform redundant validations. 

#### **Recommendation from CERT:**
```
- Inter-trustboundary: 
    When data crosses the trust boundary, do callee validation for all data
- Intra-trustboundary: 
    When data transfer within the trust boundary, choose from caller and callee based on the needs of the system.
```
#### **Code example:**
```
private Object myState = null;

void setState(Object state) {
  myState = state;      // problem: No validation at all, state could be null
}

void useState() {
...
}
```
#### **Compliant solution:**
```
void setState(Object state) {
    if (state == null) {
        // Handle null state
    }
    if (isInvalidState(state)) {
        // Handle invalid state
    }
} 
myState = state;
void useState() {
    if (myState == null) {
        // Handle missing state (e.g. null) condition
    }
    ...
}  
```
The compliant code validate the data when assigning the value, as well as when using the value.

#### **Assertions:**
Never do assertions to validate the method arguments:
- Reason: When assertions are turned off, validation is turned off.

**Rule MET01-J:** Never use assertions to validate method arguments.
#### **More rules on validation:**
**MET00-EX0:**When method contract requires caller validation.

**MET00-EX1:**When argument type adequately constrains the state of the argument, or when invalid arguments are correctly handled by the body of the method.

**MET00-EX2:**Validating all data everywhere may be too expensive, or too complex, or may exceed its overall value. In this case, consider validation only at API boundaries or only when crossing trust boundaries.