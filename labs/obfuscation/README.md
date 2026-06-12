# Obfuscation Lab

## Objective

The goal of this lab was to explore common forms of obfuscation using text, images, video, and fake tokens.

The lab focused on how information can be transformed, hidden, blurred, redacted, encoded, or accidentally exposed.

## Tools Used

- Linux terminal
- base64
- tr
- rev
- xxd
- ImageMagick
- exiftool
- steghide
- ffmpeg
- ffprobe
- grep
- sed
- sha256sum
- file

## Part 1: Text Obfuscation

I created a plain text message and transformed it using several techniques:

- Base64 encoding
- ROT13
- Reversed text
- Hex encoding
- Layered obfuscation

### Key Findings

Base64, ROT13, reversed text, and hex encoding are not encryption. They can make text less readable, but they are easy to reverse if the analyst recognizes the method.

Layered obfuscation makes analysis harder because each layer must be identified and reversed in the correct order.

## Part 2: Image Obfuscation

I created an image containing visible sensitive text. Then I tested:

- Blur
- Black-box redaction
- Metadata inspection
- Metadata removal
- Steganography using steghide

### Key Findings

Blurring hides information visually, but it may not be safe for true redaction. Black-box redaction is stronger because it replaces the original pixels.

Metadata can reveal information about a file even when the visible image appears harmless.

Steganography can hide a message inside an image without obvious visual changes.

## Part 3: Video Obfuscation

I created a test video, added visible text to it, then tested:

- Blurring part of the video
- Redacting part of the video
- Inspecting video metadata
- Removing video metadata

### Key Findings

Videos can accidentally expose sensitive data such as names, usernames, passwords, IP addresses, browser tabs, documents, and student information.

Blur is not always safe. Redaction is more reliable when sensitive content must be removed.

## Part 4: Token Obfuscation and Exposure

I created fake tokens to explore how secrets can be accidentally exposed in files, scripts, screenshots, videos, and GitHub repositories.

I tested several token-handling examples:

- A hardcoded fake API token in an `.env` file
- A Base64-encoded token
- A token split into multiple script variables
- A token stored in an environment variable
- Redacted copies of token files
- Basic token searching with `grep`
- A `.gitignore` file to avoid committing secret files

### Key Findings

Tokens should be treated like passwords because they can grant access to systems or services.

Base64 encoding does not protect a token. It only changes how the token looks.

Splitting a token into multiple parts is obfuscation, not real security. The program still reconstructs the token when it runs.

Environment variables are better than hardcoding tokens into scripts, but they can still be exposed through terminal output, logs, screenshots, or video recordings.

Before pushing code to GitHub, it is important to search for exposed secrets and avoid committing `.env` files.

If a real token is accidentally committed, it should be revoked and replaced.

## Evidence Collected

Evidence was saved in:

```text
evidence/obfuscation_evidence.txt
