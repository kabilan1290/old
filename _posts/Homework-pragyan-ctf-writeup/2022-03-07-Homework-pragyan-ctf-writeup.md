---
layout: post
title:  "Homework pragyan ctf writeup"
date:   2022-03-07 09:29:20 +0700
categories: ctfwriteup
---


### Challenge name: Homework

<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/Screenshot_2022-03-06_19-33-51.png">

<p>- This is the only misc challenge in pragyan CTF !  I managed to solve this with the help of my teammates from TamilCTF! cheers to everyone.</p>

<p>- The challenge gave us an link and the description says something about a traitor submitted home work and we have to find him maybe?lets try!</p>

<p>- Visting the link we are presented with the webpage ! hmm nothing interesting.</p>
  <br>
<img src="https://github.com/kabilan1290/WebCTF/blob/master/pragyan/Screenshot_2022-03-06_19-34-19.png">
  
<p>- The webpage has the response as `Sorry the portal is not accepting new assignments at this time`</p>
  
<p>- Source code does not have anything interesting too! so going on with directory brutefoce we found an interesting directory named `response`.</p>
  
</p>- Upon visiting the endpoint,we get the below response.</p>
  
<br>
<img src="https://github.com/kabilan1290/WebCTF/blob/master/pragyan/Screenshot_2022-03-06_19-34-40.png">
 
<p>- Voilà ! it seems we found the culprit who submitted the assignment! Its dipensu and we have an option to download his assignment.</p>
  
<p>- Downloading the assignment gave us an zip file named pieces and inside the zip we can see lot of image files.</p>
  
<img src="https://github.com/kabilan1290/WebCTF/blob/master/pragyan/Screenshot_2022-03-06_19-37-39.png">
  
<p>- Seems a image was separated into multiple parts and i thought of merging them together btw is it sorted in a order? and there was 90 separate images.</p>
  
<p>- After a initial analysis on the image we were to find there are coordinates on each image metadata !</p>

<img src="https://github.com/kabilan1290/WebCTF/blob/master/pragyan/coordinate.png">

<p>- The total 90 images contains X coordinate ranges from 1 to 9 and y coordinates ranges from 1 to 10 but the images were not sorted!</p>
  
<p>- We can merge the images but first we need to sort them according to the coordinates and better i decided to change the images name according to the coordinates</p>

<p>- I wrote a script to name the images with coordinates and i attached the script below.[Added comments for explanation]</p>

```
import os
#Directory which has images
a = os.listdir()

# Command = exifool -G -Artist filename
# The above command is used for extracting only particular information from exiftool data and we only interested in coordinates.
command = "exiftool -G -Artist "
for i in a:
    x=os.popen('exiftool -G -Artist '+i).read()
    # we getting the cordinates in the variable x
    x =x.replace(' ', '')
    # removing whitespaces
    x =x[20:]
    
    os.system("cp "+i+" "+x)
    #renaming the images with coordinate names
    os.system("rm "+i)
    #removing the orginal file
  ```
  
<p>- With the above script,we get the beautiful output as follows.
  
  <img src="https://github.com/kabilan1290/WebCTF/blob/master/pragyan/sorted.png">
  
<p>- We got the result and to our plus,our images comes sorted and we can easily merge them with x and y coordinates.
  
<p>- Below paperwork explains how to arrange the images!
  
<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/paperwork.jpg">

<p>I used opencv to merge the images and below is the script!</p>

```
import cv2
import numpy as np
import os

a = os.listdir()

a = sorted(a)

im1 = cv2.imread(a[0])
im2 = cv2.imread(a[1])
im3 = cv2.imread(a[2])
im4 = cv2.imread(a[3])
im5 = cv2.imread(a[4])
im6 = cv2.imread(a[5])
im7 = cv2.imread(a[6])
im8 = cv2.imread(a[7])
im9 = cv2.imread(a[8])
im10 = cv2.imread(a[9])

#horizontal image set 1
im_h1 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[10])
im2 = cv2.imread(a[11])
im3 = cv2.imread(a[12])
im4 = cv2.imread(a[13])
im5 = cv2.imread(a[14])
im6 = cv2.imread(a[15])
im7 = cv2.imread(a[16])
im8 = cv2.imread(a[17])
im9 = cv2.imread(a[18])
im10 = cv2.imread(a[19])

#horizontal image set 2
im_h2 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[20])
im2 = cv2.imread(a[21])
im3 = cv2.imread(a[22])
im4 = cv2.imread(a[23])
im5 = cv2.imread(a[24])
im6 = cv2.imread(a[25])
im7 = cv2.imread(a[26])
im8 = cv2.imread(a[27])
im9 = cv2.imread(a[28])
im10 = cv2.imread(a[29])

#horizontal image set 3
im_h3 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[30])
im2 = cv2.imread(a[31])
im3 = cv2.imread(a[32])
im4 = cv2.imread(a[33])
im5 = cv2.imread(a[34])
im6 = cv2.imread(a[35])
im7 = cv2.imread(a[36])
im8 = cv2.imread(a[37])
im9 = cv2.imread(a[38])
im10 = cv2.imread(a[39])

#horizontal image set 4
im_h4 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[40])
im2 = cv2.imread(a[41])
im3 = cv2.imread(a[42])
im4 = cv2.imread(a[43])
im5 = cv2.imread(a[44])
im6 = cv2.imread(a[45])
im7 = cv2.imread(a[46])
im8 = cv2.imread(a[47])
im9 = cv2.imread(a[48])
im10 = cv2.imread(a[49])

#horizontal image set 5
im_h5 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])


im1 = cv2.imread(a[50])
im2 = cv2.imread(a[51])
im3 = cv2.imread(a[52])
im4 = cv2.imread(a[53])
im5 = cv2.imread(a[54])
im6 = cv2.imread(a[55])
im7 = cv2.imread(a[56])
im8 = cv2.imread(a[57])
im9 = cv2.imread(a[58])
im10 = cv2.imread(a[59])

#horizontal image set 6
im_h6 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[60])
im2 = cv2.imread(a[61])
im3 = cv2.imread(a[62])
im4 = cv2.imread(a[63])
im5 = cv2.imread(a[64])
im6 = cv2.imread(a[65])
im7 = cv2.imread(a[66])
im8 = cv2.imread(a[67])
im9 = cv2.imread(a[68])
im10 = cv2.imread(a[69])

#horizontal image set 7
im_h7 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[70])
im2 = cv2.imread(a[71])
im3 = cv2.imread(a[72])
im4 = cv2.imread(a[73])
im5 = cv2.imread(a[74])
im6 = cv2.imread(a[75])
im7 = cv2.imread(a[76])
im8 = cv2.imread(a[77])
im9 = cv2.imread(a[78])
im10 = cv2.imread(a[79])

#horizontal image set 8
im_h8 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

im1 = cv2.imread(a[80])
im2 = cv2.imread(a[81])
im3 = cv2.imread(a[82])
im4 = cv2.imread(a[83])
im5 = cv2.imread(a[84])
im6 = cv2.imread(a[85])
im7 = cv2.imread(a[86])
im8 = cv2.imread(a[87])
im9 = cv2.imread(a[88])
im10 = cv2.imread(a[89])

#horizontal image set 9
im_h9 = cv2.hconcat([im1, im2,im3,im4,im5,im6,im7,im8,im9,im10])

#Connecting everything veritically
im_v1 = cv2.vconcat([im_h1,im_h2,im_h3,im_h4,im_h5,im_h6,im_h7,im_h8,im_h9])

cv2.imwrite('output.jpg', im_v1)
```
<p>- Merging them together! we got our flag : D</p>

<img src="https://raw.githubusercontent.com/kabilan1290/WebCTF/master/pragyan/output.jpg">

### Flag:
<p>p_ctf{w3ll_d0n3_h4ck3r}</p>
----------------------------------------------------------------------------------------------------------------

