# They're Making Decoys

Category: OSINT (Medium)

Points: 121

Solves: 190

>The Sergeant from Clarence asked one of the Corporals to patrol for Emus to keep the our fresh troops on their toes and maybe scare some discipline into them. They headed out to an area with lots of sightings reported though they never made it back. Some of the last images uploaded showed what looked to be the enemy, though it turned out to be decoys. These broom heads are getting clever. I'm guessing we're not getting the camera equipment back...

### Solution

![Decoy](/images/DecoysChal.png)

First thing upon inspection, we notice that this is a screen shot from google maps :skull: and has floating branches and heads.

![Watermark](/images/DecoysWatermark.png) There is a water mark :0 ![Reaction](/images/DecoysWatermarkReaction.png)

Anyway, we are obviously somewhere in Australia, and we were able to fixate on New South Wales but couldn't get futher. Looking through local parks and emu concentrations was of no use. However, the metal emu decoys do look very unique.

![Rev Img Search](/images/DecoysRevImgSearch.png)

After reverse image searching and carefully scrolling through all the options I noticed a [facebook post](https://www.facebook.com/groups/156920868310630/posts/761028711233173/?_rdr) that looked shockingly similar.

Reading through the post gives us an approximate location:
`This blind bend just east of Tailem Drive is a well known emu crossing`

![Map](/images/DecoysMap.png)

Going on [street view](https://www.google.com/maps/@-29.5505887,153.277674,3a,75y,157.31h,87.94t/data=!3m7!1e1!3m5!1snW02AeqGuTEngxlC1zlXUA!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fpanoid%3DnW02AeqGuTEngxlC1zlXUA%26cb_client%3Dmaps_sv.share%26w%3D900%26h%3D600%26yaw%3D157.31381717777202%26pitch%3D2.0562890270150262%26thumbfov%3D90!7i16384!8i8192?coh=205410&entry=ttu), we verify the location:

![Street View](/images/DecoysStreetView.png)


This gives us the coords, `-29.5505887,153.277674`, rounded to `-29.5506,153.2777`


### Flag

```DUCTF{-29.5506,153.2777}```