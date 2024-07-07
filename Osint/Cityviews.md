# Offtheramp

Category: OSINT (Easy)

Points: 100

Solves: 631

>After having to go on the run, I've had to bunker down. Which building did I capture this picture from?

### Solution

![Cityviews](/images/CityviewsChal.jpeg)

We first zoom in on the building name at the bottom: `The G.. Southern ...`
After some careful deducing we get, `The Great Southern Hotel`.

Then if we zoom in on the billboard in the back we see we are in Melbourne.

![Billboard](/images/CityviewsBillboard.png)

Going [there](https://www.google.com/maps/place/The+Great+Southern+Hotel+Melbourne/@-37.8200738,144.9547023,3a,75y,335.16h,105.56t/data=!3m7!1e1!3m5!1si-0FWAkpINClhzgGPIkdUA!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fpanoid%3Di-0FWAkpINClhzgGPIkdUA%26cb_client%3Dmaps_sv.share%26w%3D900%26h%3D600%26yaw%3D335.15625774285877%26pitch%3D-15.564408112051382%26thumbfov%3D90!7i16384!8i8192!4m20!1m10!3m9!1s0x6ad65d51ec7a3043:0xc24c13994bad2d84!2sThe+Great+Southern+Hotel+Melbourne!5m2!4m1!1i2!8m2!3d-37.8197222!4d144.955!16s%2Fg%2F11yx7qgrh!3m8!1s0x6ad65d51ec7a3043:0xc24c13994bad2d84!5m2!4m1!1i2!8m2!3d-37.8197222!4d144.955!16s%2Fg%2F11yx7qgrh?coh=205410&entry=ttu), We can align ourselves with the triangle shaped roof in the image.

![Answer](/images/CityviewsAnswer.png)

So the picture was taken from `Hotel Indigo Melbourne`


### Flag

```DUCTF{Hotel_Indigo_Melbourne}```