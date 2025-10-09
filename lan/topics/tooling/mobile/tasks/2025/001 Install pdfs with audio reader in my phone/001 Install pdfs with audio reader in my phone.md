---
status: done
---

# 1 Objective

To get some book pdfs transferred to my phone, and to listen to them on the go.

# 2 Related

[Wk 34 000 Downloading youtube songs on my LineageOS phone over termux](../../weekly/2025/Wk%2034%20000%20Downloading%20youtube%20songs%20on%20my%20LineageOS%20phone%20over%20termux.md)

# 3 Journal

2025-10-02 Wk 40 Thu - 07:20 +03:00

Usually I use eReader Prestigio and I also tried ReadEra or . There should be a general solution for more refined artificial voices, but those apps are free.

2025-10-02 Wk 40 Thu - 07:32 +03:00

Similar to [Wk 34 000 Downloading youtube songs on my LineageOS phone over termux](../../weekly/2025/Wk%2034%20000%20Downloading%20youtube%20songs%20on%20my%20LineageOS%20phone%20over%20termux.md),

In the phone Termux app,

````sh
sshd
su
ip addr | grep 192 # get the ip
````

Ensure there is also a password is set or set one with `passwd`.

Then we can send the pdf over:

````sh
scp -P 8022 {file} {user}@{ip}:/sdcard/Download
````

2025-10-02 Wk 40 Thu - 08:03 +03:00

For speech, it seems eReader Prestigio requires me to download Google TTS though.

App name being called Speech Recognition and Synthesis from Google

2025-10-02 Wk 40 Thu - 08:08 +03:00

Maybe we can find some other text to speech apps?

This [reddit post](https://www.reddit.com/r/degoogle/comments/pdwr60/text_to_speech_on_degoogled_android/) discusses some options like eSpeak, and mentions [f-droid](https://f-droid.org/) which hosts mainly open source apps for android.

Through f-droid, we found [gh foobnix/LibreraReader](https://github.com/foobnix/LibreraReader) which in its settings can integrate with TTS [gh RHVoice/RHVoice](https://github.com/RHVoice/RHVoice).

It works!
