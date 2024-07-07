# Marketing

Category: OSINT (Beginner) (Least solved OSINT BTW :skull:)

Points: 108

Solves: 187

>We have the best marketing team!
>
>Except for that one monke that looks like they slapped something together...
>
>Maybe the bot should lock away that monke to stopping posting stuff online. The other animals should be free, just not the monke.

### Solution

This has got to be referring to MC Fat Monke, but where does the marketing team even post.

Looking at the [SoundCloud](https://soundcloud.com/mc-fat-monke/back-to-the-jungle) of MC Fat Monke, while I was jamming out, unfortunately did not have the flag.

Looking at [DUCTF's company linkedin](https://www.linkedin.com/company/ductf/) doesn't have any jobs under marketing. Looking at the post history also yields nothing.

We also tried looking up the IRL MC Fat Monke, ghostccamm:

![MC Fat Monke Discord](/images/MarketingMonkey.png)

This also led to nothing.

We then found a 2021 [blog](https://jsur.in/posts/2021-09-27-ductf-2021-reflection) about organizing DUCTF. In this blog there is a part that mentions the marketing team: `Without our amazing marketing team making graphics, writing emails and tweets, chatting with CEOs, registering us as an incorporated association, etc. we wouldn't have had the reach we did nor the financial capability to even run the CTF.`

The only thing public would be Graphics and Tweets. I had already looked at the graphics and found nothing super interesting or related to MC Fat Monke and I definitely was not gonna start throwing random steganography stuff at all the images :skull:. This lead me to the tweets.

Scrolling though the [X account](https://x.com/DownUnderCTF) of DUCTF, I saw a very suspicious post 

![Tweet](/images/MarketingTweet.png)

Watching through the whole trailer lead to nothing, and I was about to give up. Turns out, I am blind and missed the flag in the trailer (top left, 14 seconds in) :skull:

![Flag](/images/MarketingFlag.png)


Personally, I think this challenge should be put in the Hard category \:D

### Flag

```DUCTF{doing_a_bit_of_marketing}```