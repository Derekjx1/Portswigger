---
description: Learning points about directory traversal
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Path/Directory Traversal

## What is Traversal?

Directory or path traversal is a server-side vulnerability that allows an attacker to access folders and files outside the intended directory. This can lead to the exposure of sensitive information.Traversal makes use of the ( ../ or \\..) sequences to traverse server files in its file directory

{% hint style="info" %}
Backslash & Forward slash may vary depending on Windows or Unix based OS
{% endhint %}

Example payload below shows that we are trying to use the traversal sequence to get the /etc/passwd file in the server.

<figure><img src=".gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

## Absolute path bypass

Sometimes server may be sanitizing the traversal(../) sequences making it unable to move out of the current directory but we may be able to circumvent this by just simply specifying an absolute path to the query parameter value. Usually the query parameter value is referencing files relative to current folder, but supplying an absolute path to it may allow us to view other folders.

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Traversal Sequences Striped Non-recursively

In an attempt to sanitise the input , sometimes web server is configured to strip traversal sequences(e., ../). However if they are stripped non recursively, this vulnerability will still be present.

Consider the input: `....//....//etc/passwd`

1. Non-Recursive Stripping:
   * Input: `....//....//etc/passwd`
   * Output: <mark style="color:yellow;">`../../`</mark>`etc/passwd`
   * Result: <mark style="color:yellow;">Still vulnerable to path traversal</mark>
2. Recursive Stripping:
   * Input: `....//....//etc/passwd`
   * Pass 1: `../../etc/passwd`
   * Pass 2: `/etc/passwd`
   * Result: Fully stripped

<figure><img src=".gitbook/assets/image (100).png" alt=""><figcaption><p>Using double traversal sequence to confuse server</p></figcaption></figure>

## Stripping with superfluous decode

Web server may try to strip path traversal sequences by decoding the encoded input before processing the request. However, this can potentially be bypass by double or even triple encoding the payload. Using non standard encodings might work as well.

<figure><img src=".gitbook/assets/image (101).png" alt=""><figcaption><p>Double encoded path traversal payload</p></figcaption></figure>

## Supplying Absolute Path

The application transmits the full file path via a request parameter, and validates that the supplied path starts with the expected folder.

<figure><img src=".gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

## Validation of file extensions bypass using null byte

Web servers might do backend validation for extensions to a supplied file in the query parameter of the request. We can circumvent this by just adding null bytes(%00) to the end of the path and specifying the extension.  &#x20;

<figure><img src=".gitbook/assets/image (103).png" alt=""><figcaption><p>Bypass using null bytes</p></figcaption></figure>

