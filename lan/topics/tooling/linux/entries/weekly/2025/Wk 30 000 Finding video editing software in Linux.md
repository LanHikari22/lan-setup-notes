---
status: watch0
---

2025-08-08 Wk 32 Fri - 04:112025-08-08 Wk 32 Fri - 04:47

# 1 Objective

I need to find some video editing software, and also some video playing software. For some reason Videos is not able to open my screencasts...

# 2 Journal

## 2.1 Searching for Video editing software in linux

2025-07-27 Wk 30 Sun - 00:53

Recommendations from [itsfoss blog](https://itsfoss.com/best-video-editing-software-linux/),

[OpenShot](https://www.openshot.org/user-guide/).

They have [AppImage Installations Instructions](https://github.com/OpenShot/openshot-qt/wiki/AppImage-Installation).

2025-07-27 Wk 30 Sun - 01:04

Seems to freeze in the preview on my 18-minute video... This happens when opening it in Firefox too for some reason. It's 3GB. See [4.2 Skipping forward in Video Players in GNOME/Wayland causes them to freeze](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#42-skipping-forward-in-video-players-in-gnomewayland-causes-them-to-freeze).

2025-07-27 Wk 30 Sun - 05:20

Found [gitlab GNOME/pitivi](https://gitlab.gnome.org/GNOME/pitivi) while looking through their gitlab projects.

### 2.1.1 Pend

## 2.2 Searching for video players in linux

2025-07-27 Wk 30 Sun - 01:05

Recommendations from [itsfloss blog for video players](https://itsfoss.com/video-players-linux/),

````sh
flatpak install flathub org.kde.haruna
````

But this requires KDE, and I'm on GNOME/Wayland.

We are able to get `totem` working with `GDK_GL=gles` on Wayland according to [3.1 GNOME Videos OpenGL Support](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#31-gnome-videos-opengl-support). So that's good enough.

# 3 Tasks

## 3.1 Reporting video seek issue

* [ ] Watch0

See issue [\#643](https://gitlab.gnome.org/GNOME/totem/-/issues/643).

2025-07-27 Wk 30 Sun - 05:09

I'm struggling to find this issue online, and I do have sufficient logs on the matter from [4.2 Skipping forward in Video Players in GNOME/Wayland causes them to freeze](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#42-skipping-forward-in-video-players-in-gnomewayland-causes-them-to-freeze).

We should find out where to report this issue.

The most causal correspondence to video unfreeze reporting we have, is the time reported from [gh GStreamer/gst-plugins-base](https://github.com/GStreamer/gst-plugins-base/). But this is a public mirror and I cannot submit issues there. But we can see in the [TODO file](https://github.com/GStreamer/gst-plugins-base/blob/ce937bcb21412d7b3539a2da0509cc96260562f8/gst-libs/gst/video/TODO) that they use bugzilla bugs like [\#664127](https://bugzilla.gnome.org/show_bug.cgi?id=664127).

There they explain

 > 
 > **No new issues can be reported in GNOME Bugzilla anymore.**  
 > **To report an issue in a GNOME project, [go to GNOME GitLab](https://gitlab.gnome.org/GNOME)**.

The [gstreamer topic](https://gitlab.gnome.org/explore/projects/topics/gstreamer) does not seem to reveal any central projects for this issue.

There is the [gitlab GNOME/totem](https://gitlab.gnome.org/GNOME/totem) project. This could be a good place to report, noting that the issue is likely more general than totem.

2025-08-07 Wk 32 Thu - 04:36

Reported [\#643](https://gitlab.gnome.org/GNOME/totem/-/issues/643).

### 3.1.1 Watch0

# 4 Issues

## 4.1 GNOME Videos OpenGL Support

* [x] 

2025-07-27 Wk 30 Sun - 01:10

When I try to open the screencast in GNOME Videos I get

````
An error occured
Could not initialise OpenGL support
````

In Issue [totem #616](https://gitlab.gnome.org/GNOME/totem/-/issues/616) they seem to suggest this is specifically a Wayland problem.

They mention that `GDK_GL=gles` can resolve this.

````sh
cd ~/Videos/Screencasts/expedition33
GDK_GL=gles totem Screencast\ From\ 2025-07-02\ 16-49-00.webm
````

This does in fact open the video.

But we're still having issues skipping forward. This seems to be a shared problem across many video viewers here...

More on [3.2 Skipping forward in Video Players in GNOME/Wayland causes them to freeze](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#32-skipping-forward-in-video-players-in-gnomewayland-causes-them-to-freeze).

## 4.2 Skipping forward in Video Players in GNOME/Wayland causes them to freeze

* [ ] 

2025-07-27 Wk 30 Sun - 05:13

Issue being reported in [3.1 Reporting video seek issue](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#31-reporting-video-seek-issue).

2025-07-27 Wk 30 Sun - 03:05

Many GNOME issues are documented in the [archlinux wiki here](https://wiki.archlinux.org/title/GNOME/Troubleshooting).

### 4.2.1 Logs

2025-07-27 Wk 30 Sun - 04:28

When skipping ahead in the video, it hangs. Some logs on running

````sh
GDK_GL=gles totem Screencast\ From\ 2025-07-02\ 16-49-00\ cut.webm --gst-debug-level=[N]
````

````sh
# --gst-debug-level=2

0:00:15.729361325 1977499 0x5bde2b0f9f20 WARN               structure gststructure.c:3100:priv_gst_structure_append_to_gstring: No value transform to serialize field 'event' of type 'GstEvent'

# then many of
0:01:35.868665064 1960157 0x5b316769ef20 WARN                   totem bacon-video-widget.c:1276:bvw_handle_element_message: Unhandled element message GstNavigationMessage from sink: element message: 0x5b31688937a0, time 99:99:99.999999999, seq-num 2397, element 'sink', GstNavigationMessage, type=(string)event, event=(GstEvent)NULL;
0:01:36.058690943 1960157 0x5b316769ef20 WARN               structure gststructure.c:3100:priv_gst_structure_append_to_gstring: No value transform to serialize field 'event' of type 'GstEvent'
````

````sh
# --gst-debug-level=4
0:00:35.088310287 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstelement.c:2723:_priv_gst_element_state_changed:<play> notifying about state-changed PAUSED to PAUSED (PAUSED pending)
0:00:35.088329663 2013827 0x7a05380014f0 INFO                    task gsttask.c:383:gst_task_func:<multiqueue0:src_0> Task going to paused
0:00:35.088352011 2013827 0x7a0538001260 INFO                    task gsttask.c:383:gst_task_func:<matroskademux0:sink> Task going to paused
0:00:35.088376880 2013827 0x57ec40fcaeb0 INFO               GST_EVENT gstevent.c:605:gst_event_new_flush_stop: creating flush stop 1
0:00:35.089020758 2013827 0x7a05380014f0 INFO                    task gsttask.c:385:gst_task_func:<multiqueue0:src_0> Task resume from paused
0:00:35.089027637 2013827 0x57ec40fcaeb0 INFO               GST_EVENT gstevent.c:990:gst_event_new_segment: creating segment event time segment start=0:06:35.291000000, offset=0:00:00.000000000, stop=0:18:18.719000000, rate=1.000000, applied_rate=1.000000, flags=0x01, time=0:06:35.291000000, base=0:00:00.000000000, position 0:00:00.000000000, duration 0:18:18.719000000
0:00:35.089022155 2013827 0x7a0538001780 INFO                    task gsttask.c:385:gst_task_func:<vqueue:src> Task resume from paused
0:00:35.089067936 2013827 0x7a0538001260 INFO                    task gsttask.c:385:gst_task_func:<matroskademux0:sink> Task resume from paused
0:00:35.089405512 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2070:gst_bin_get_state_func:<play> getting state
0:00:35.089416544 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstelement.c:2539:gst_element_get_state_func:<play> waiting for element to commit state
0:00:35.089425273 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstelement.c:2552:gst_element_get_state_func:<play> timed out
0:00:35.089757626 2013827 0x7a0538001780 INFO               structure gststructure.c:3961:gst_structure_get_valist: Expected field 'gl-allocation-params' in structure: GstBufferPoolConfig, caps=(GstCaps)"video/x-raw\,\ format\=\(string\)I420\,\ width\=\(int\)1920\,\ height\=\(int\)1080\,\ interlace-mode\=\(string\)progressive\,\ multiview-mode\=\(string\)mono\,\ multiview-flags\=\(GstVideoMultiviewFlagsSet\)0:ffffffff:/right-view-first/left-flipped/left-flopped/right-flipped/right-flopped/half-aspect/mixed-mono\,\ pixel-aspect-ratio\=\(fraction\)1/1\,\ colorimetry\=\(string\)bt709\,\ framerate\=\(fraction\)91/3", size=(uint)3110400, min-buffers=(uint)0, max-buffers=(uint)0, allocator=(GstAllocator)"NULL", params=(GstAllocationParams)"GstAllocationParams\,\ flags\=\(GstMemoryFlags\)0\,\ align\=\(guint64\)0\,\ prefix\=\(guint64\)0\,\ padding\=\(guint64\)0\;", gl-min-free-queue-size=(uint)1, options=(string)< GstBufferPoolOptionGLSyncMeta, GstBufferPoolOptionGLTextureTarget2D >;
0:00:35.089942260 2013827 0x7a05380014f0 INFO               structure gststructure.c:3961:gst_structure_get_valist: Expected field 'gl-allocation-params' in structure: GstBufferPoolConfig, caps=(GstCaps)"video/x-raw\,\ format\=\(string\)I420\,\ width\=\(int\)1920\,\ height\=\(int\)1080\,\ interlace-mode\=\(string\)progressive\,\ multiview-mode\=\(string\)mono\,\ multiview-flags\=\(GstVideoMultiviewFlagsSet\)0:ffffffff:/right-view-first/left-flipped/left-flopped/right-flipped/right-flopped/half-aspect/mixed-mono\,\ pixel-aspect-ratio\=\(fraction\)1/1\,\ colorimetry\=\(string\)bt709\,\ framerate\=\(fraction\)91/3", size=(uint)3110400, min-buffers=(uint)1, max-buffers=(uint)0, allocator=(GstAllocator)"NULL", params=(GstAllocationParams)"GstAllocationParams\,\ flags\=\(GstMemoryFlags\)0\,\ align\=\(guint64\)0\,\ prefix\=\(guint64\)0\,\ padding\=\(guint64\)0\;", gl-min-free-queue-size=(uint)1, options=(string)< GstBufferPoolOptionGLSyncMeta, GstBufferPoolOptionGLTextureTarget2D, GstBufferPoolOptionVideoMeta >;
0:00:35.116814609 2013827 0x7a05380014f0 INFO               GST_EVENT gstevent.c:990:gst_event_new_segment: creating segment event time segment start=0:06:35.291000000, offset=0:00:00.000000000, stop=0:18:18.719000000, rate=1.000000, applied_rate=1.000000, flags=0x01, time=0:06:35.291000000, base=0:00:00.000000000, position 0:00:00.000000000, duration 0:18:18.719000000

````

Beginning of the freeze:

````sh
# --gst-debug-level=4
0:03:54.085742951 2013827 0x57ec40fcaeb0 WARN                   totem bacon-video-widget.c:1276:bvw_handle_element_message: Unhandled element message GstNavigationMessage from sink: element message: 0x7a0501060fc0, time 99:99:99.999999999, seq-num 5585, element 'sink', GstNavigationMessage, type=(string)event, event=(GstEvent)NULL;
0:03:54.124119183 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2480:gst_bin_element_set_state:<playsink> current PLAYING pending VOID_PENDING, desired next PAUSED
0:03:54.124174063 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2480:gst_bin_element_set_state:<vbin> current PLAYING pending VOID_PENDING, desired next PAUSED
0:03:54.124207527 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2480:gst_bin_element_set_state:<glsinkbin> current PLAYING pending VOID_PENDING, desired next PAUSED
0:03:54.124237550 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2480:gst_bin_element_set_state:<sink> current PLAYING pending VOID_PENDING, desired next PAUSED
0:03:54.124275628 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2946:gst_bin_change_state_func:<glsinkbin> child 'sink' is changing state asynchronously to PAUSED
0:03:54.124295660 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstbin.c:2480:gst_bin_element_set_state:<glcolorbalance0> current PLAYING pending VOID_PENDING, desired next PAUSED
0:03:54.124346515 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstelement.c:2823:gst_element_continue_state:<glcolorbalance0> completed state change to PAUSED
0:03:54.124364893 2013827 0x57ec40fcaeb0 INFO              GST_STATES gstelement.c:2723:_priv_gst_element_state_changed:<glcolorbalance0> notifying about state-changed PLAYING to PAUSED (VOID_PENDING pending)
````

Waiting for unfreeze after seek:

````sh
# --gst-debug-level=4

0:03:54.605562731 2013827 0x57ec40fcaeb0 WARN                   totem bacon-video-widget.c:1276:bvw_handle_element_message: Unhandled element message GstNavigationMessage from sink: element message: 0x57ec421a1eb0, time 99:99:99.999999999, seq-num 5670, element 'sink', GstNavigationMessage, type=(string)event, event=(GstEvent)NULL;


0:05:46.932625687 2013827 0x57ec40fcaeb0 WARN               structure gststructure.c:3100:priv_gst_structure_append_to_gstring: No value transform to serialize field 'event' of type 'GstEvent'
0:05:46.932657369 2013827 0x57ec40fcaeb0 WARN                   totem bacon-video-widget.c:1276:bvw_handle_element_message: Unhandled element message GstNavigationMessage from sink: element message: 0x57ec42316770, time 99:99:99.999999999, seq-num 5673, element 'sink', GstNavigationMessage, type=(string)event, event=(GstEvent)NULL;
0:05:46.949995432 2013827 0x57ec40fcaeb0 WARN               structure gststructure.c:3100:priv_gst_structure_append_to_gstring: No value transform to serialize field 'event' of type 'GstEvent'


0:07:26.677650247 2013827 0x7a05380014f0 INFO            videodecoder gstvideodecoder.c:3724:gst_video_decoder_clip_and_push_buf:<vp8dec0> First buffer since flush took 0:03:32.550615001 to produce
0:07:26.678515958 2013827 0x7a0538001780 INFO              GST_STATES gstbin.c:3408:bin_handle_async_done:<glsinkbin> committing state from PAUSED to PAUSED, old pending PAUSED
0:07:26.678536183 2013827 0x7a0538001780 INFO              GST_STATES gstbin.c:3431:bin_handle_async_done:<glsinkbin> completed state change, pending VOID
0:07:26.678549866 2013827 0x7a0538001780 INFO              GST_STATES gstelement.c:2723:_priv_gst_element_state_changed:<glsinkbin> notifying about state-changed PAUSED to PAUSED (VOID_PENDING pending)

````

### 4.2.2 Tracing the logs

2025-07-27 Wk 30 Sun - 04:57

The error where it took 3 minutes to unfreeze leads to this [GStreamer/gst-plugins-base source file](https://github.com/GStreamer/gst-plugins-base/blob/ce937bcb21412d7b3539a2da0509cc96260562f8/gst-libs/gst/video/gstvideodecoder.c#L3724).

### 4.2.3 Reproducing the issue on a webm file of varying lengths

2025-07-27 Wk 30 Sun - 07:38

So we press `PS`. We select `Record Screen`. And we start recording for 15 seconds and then we stop. This produces the file `Screencast From 2025-07-27 07-37-09.webm` in `/home/lan/Videos/Screencasts`.

We open this using

````sh
GDK_GL=gles totem Screencast\ From\ 2025-07-27\ 07-37-09.webm
````

where `GDK_GL=gles` is needed for [totem #616](https://gitlab.gnome.org/GNOME/totem/-/issues/616) as discussed in [4.1 GNOME Videos OpenGL Support](Wk%2030%20000%20Finding%20video%20editing%20software%20in%20Linux.md#41-gnome-videos-opengl-support).

We are not able to reproduce the problem. Within a 15 second window, video seek works fine.

The video we were originally testing on was 18 minutes long. Let's next try a 1 minute long screencast. This time not a selection but a whole screen screencast.

It still works fine for 1 minute. Let's go 5 minutes.

Behavior reproduces under 5 minutes.

So between 1 minute and 5 minutes. Let's try 3 minutes next.

It reproduces for 3 minutes. Let's try 1.5 minutes.

Still reproduces for 1.5 minutes.

Issue reproduces for 1 minute. Even though 1 minute has succeeded before... Do the content of the video matter here?

It does not take as long to unhang.

#### 4.2.3.1 Pend

### 4.2.4 See if issue reproduces outside webm

* [ ] Issue reproduces when changing screencast file from webm -> mp4? Yes/No
* [ ] Issue reproduces when done on some other downloaded mp4 video? Yes/No

#### 4.2.4.1 Pend

### 4.2.5 Pend

# 5 HowTos

## 5.1 Cut a video with ffmpeg in terminal

* [ ] 

2025-07-27 Wk 30 Sun - 02:22

Similar to this [answer](https://stackoverflow.com/a/42827058/6944447),

we can do

````sh
ffmpeg -ss 00:00:00 -to 18:12:00 -i input.webm -c copy output.webm
````

Specifically for my use case:

````sh
cd ~/Videos/Screencasts/expedition33
ffmpeg -ss 00:00:00 -to 18:10:00 -i Screencast\ From\ 2025-07-02\ 16-49-00.webm -c copy Screencast\ From\ 2025-07-02\ 16-49-00\ cut.webm
````

We can verify this in a Video Player

````sh
GDK_GL=gles totem Screencast\ From\ 2025-07-02\ 16-49-00\ cut.webm
````

2025-07-27 Wk 30 Sun - 03:04

It doesn't seem like it's cut. And neither including/not of `-ss 00:00:00` seems to help.

### 5.1.1 Pend

## 5.2 Find out my nvidia driver details in linux terminal

* [x] 

There's `nvidia-smi`.

````sh
nvidia-smi   

# out
Sun Jul 27 03:29:47 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.169                Driver Version: 570.169        CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA TITAN RTX               Off |   00000000:01:00.0  On |                  N/A |
| 42%   64C    P2            145W /  280W |   15643MiB /  24576MiB |     56%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+

[...]
````

According to this [answer](https://stackoverflow.com/a/13127714/6944447),

we can also do

````sh
cat /proc/driver/nvidia/version
````

In my case,

````sh
cat /proc/driver/nvidia/version

# out
NVRM version: NVIDIA UNIX Open Kernel Module for x86_64  570.169  Release Build  (dvs-builder@U22-I3-AE18-31-4)  Thu Jun 12 19:38:21 UTC 2025
GCC version:  gcc version 14.2.0 (Ubuntu 14.2.0-19ubuntu2) 
````

And according to this [answer](https://stackoverflow.com/a/29328598/6944447),

````sh
modinfo nvidia | grep version

# out
version:        570.169
srcversion:     C464FE9A9C1E2C3ED7C7A0F
vermagic:       6.14.0-24-generic SMP preempt mod_unload modversions 
````

And according to this [answer](https://stackoverflow.com/a/70456465/6944447),

the version itself can be fetched from `nvidia-smi` with

````sh
nvidia-smi --query-gpu=driver_version --format=csv,noheader --id=0

# out
570.169
````

## 5.3 Get relevant OS/distro information to report

* [ ] 

2025-07-27 Wk 30 Sun - 05:31

````sh
lsb_release -a

# out
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 25.04
Release:	25.04
Codename:	plucky
````

````sh
uname -a

# out
Linux lan-proart 6.14.0-24-generic #24-Ubuntu SMP PREEMPT_DYNAMIC Sun Jun 15 11:18:07 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
````

2025-07-27 Wk 30 Sun - 07:15

Similar to Issue [totem #599](https://gitlab.gnome.org/GNOME/totem/-/issues/599),

````sh
$ totem --gst-version
GStreamer Core Library version 1.26.0

$ totem --version
totem 43.1

$ uname -a
Linux lan-proart 6.14.0-24-generic #24-Ubuntu SMP PREEMPT_DYNAMIC Sun Jun 15 11:18:07 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
````

Similar to issue [totem #616](https://gitlab.gnome.org/GNOME/totem/-/issues/616),

````sh
sudo apt-get install vainfo

vainfo

sudo apt-get remove vainfo

# out
Trying display: wayland
libva info: VA-API version 1.22.0
libva info: Trying to open /usr/lib/x86_64-linux-gnu/dri/nvidia_drv_video.so
libva info: va_openDriver() returns -1
vaInitialize failed with error code -1 (unknown libva error),exit
````

````sh
echo $VDPAU_DRIVER
echo $LIBVA_DRIVER_NAME
echo $GBM_BACKEND
echo $__GLX_VENDOR_LIBRARY_NAME

# out
[nothing]
````

I guess all these are for Arch maybe?

# 6 Investigations

## 6.1 On Desktop environments and compositors

2025-07-27 Wk 30 Sun - 05:37

This [answer](https://stackoverflow.com/a/67595277/6944447) states examples:

* `xorg` is a display server
* `i3` is a window manager
* `picom` is a compositor
* but `sway` is all 3 at once.

They also link to the [wayland architecture docs](https://wayland.freedesktop.org/architecture.html) and state that they all become one program under it.

They explain the compositor purpose there:

 > 
 > The compositor is responsible for rendering the entire screen contents based on its scenegraph and the contents of the X windows.

2025-07-27 Wk 30 Sun - 06:05

Under wayland, the client does the render using libraries like OpenGL:

 > 
 > The client links to a rendering library such as OpenGL that knows how to program the hardware and renders directly into the buffer.

In [the main wayland page](https://wayland.freedesktop.org/),

 > 
 > A Wayland server is called a "compositor". Applications are Wayland clients.

 > 
 > There is no single common Wayland server like Xorg is for X11, but every graphical environment brings with it one of many compositor implementations. Window management and the end user experience are often tied to the compositor rather than swappable components.

In [gnome wiki Wayland Initiative](https://wiki.gnome.org/Initiatives/Wayland),

 > 
 > Some problematic parts of the X protocol, such as grabs, are simply not present under Wayland, which avoids a whole class of problems.

 > 
 > The driver situation for Wayland is as follows:
 > \[...\]
 > 
 > * Nvidia binary drivers do not work currently

 > 
 > For X-based desktops, the ICCCM and EWMH specifications (together with a number of other X and freedesktop specifications) have provided a reasonable basis for interoperability of applications across different environments. \[...\] gnome-shell as a Wayland compositor will implement these interfaces, and the GTK+ Wayland backend will use them.

 > 
 > 3rd party applications will continue to work in GNOME, via xwayland.

Is this still the case? What third party applications make use of wayland instead I wonder...

This documentation seems outdated. They are linking to [this](https://bugzilla.gnome.org/show_bug.cgi?id=wayland) which no longer works.

The timestamp reads `2024-10-23 11:17`.

Oh it does also say

 > 
 > This site has been retired. For up to date information, see [handbook.gnome.org](https://handbook.gnome.org/) or [gitlab.gnome.org](https://gitlab.gnome.org/).

They have [issue reporting guidelines](https://handbook.gnome.org/issues/reporting.html).

### 6.1.1 Pend

## 6.2 Dependency graph of Ubuntu

2025-07-27 Wk 30 Sun - 08:43

Where is what? What's a linux distribution anyway? How do we causally trace where issues occur? There are many unknowns with the linux OS to uncover.

But we can start with Ubuntu. What is it?

[Ubuntu website](https://ubuntu.com/). [Ubuntu documentation](https://help.ubuntu.com/stable/ubuntu-help/index.html)

[howtogeek post: What Is a Linux Distro, and How Are They Different from One Another?](https://www.howtogeek.com/132624/htg-explains-whats-a-linux-distro-and-how-are-they-different/) has some overview on different components but it's very light.

There is [Linux from scratch](https://www.linuxfromscratch.org/). Here is the online development branch of the [book](https://www.linuxfromscratch.org/lfs/view/development/).

### 6.2.1 Pend

# 7 References
