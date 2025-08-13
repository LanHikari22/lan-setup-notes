\#lan #issue #LI000 #resolved

2025-06-17 Wk 25 Tue - 13:31

# 1 Issue

![Pasted image 20250617132953.png](../../../../attachments/Pasted%20image%2020250617132953.png)

![Pasted image 20250617133617.png](../../../../attachments/Pasted%20image%2020250617133617.png)

This is on Ubuntu Linux. GNOME/Wayland.

Why does this happen? It's intermittent.

I've also had to run

````sh
sudo systemctl restart gdm
````

from another machine because my system froze while running this game and switching windows via Alt+Tab.

2025-06-17 Wk 25 Tue - 18:07

I was able to run the game again without a reboot. I just had to kill all processes with "windows" on them. Wine processes hanging around from when I did the gdm restart likely.

# 2 References
