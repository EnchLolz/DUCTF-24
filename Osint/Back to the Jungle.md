# Bridget Lives

Category: OSINT (Easy)

Points: 100

Solves: 460

>Did MC Fat Monke just drop a new track????? 👀👀👀

### Solution

Who is MC Fat Monke? Let's google him!

![Google Search](/images/BackToTheJungleGoogleSearch.png)

Looks like we found his new track. After listening to the most banger track on loop for 30 mins, https://www.youtube.com/watch?v=jmhn3IMLQyM, I realized that I needed to find a flag :skull:.

After trying a bunch of random stuff, audio analysis, yt video meta data, etc, and rewatching it over and over again, I saw something interesting at [2:34](https://youtu.be/jmhn3IMLQyM?t=154), a computer that said free flag :0

![Yt Video](/images/BackToTheJungleYT.png)

There is also a link at the top of this fake webpage:
https://average-primate-th.wixsite.com/mc-fat-monke-appreci

Carefully transcribing it, and visiting the website yields the flag.

### Flag

```DUCTF{wIr_G0iNg_b4K_t00_d3r_jUNgL3_mIt_d15_1!!111!}```