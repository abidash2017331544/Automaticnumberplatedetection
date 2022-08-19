# Automatic_number_plate_detection_from_image
Hello.
This project is a basic idea for automatic number plate detection system using easyocr and python.
It is done with Jupyter notebook. If u do not know, how to run that or bring that, you need to go to your cmd and install it. then just use jupyter notebook in the cmd. it will open in a browser.
then u can open the file project.ipynb.
all the needed commands line are given there.
I am again telling u, that may not be the best but its a basic project to learn and understand ANPRS.
Hope you like it.

----
Install and Import Dependencies
----

!pip install easyocr

!pip install imutils



import cv2

from matplotlib import pyplot as plt

import numpy as np

import imutils

import easyocr


 
Pre-processing
----

img = cv2.imread('download1.jpg')

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

plt.imshow(cv2.cvtColor(gray, cv2.COLOR_BGR2RGB))



----
filtering
----

bfilter = cv2.bilateralFilter(gray, 11, 17, 17)

edged = cv2.Canny(bfilter, 30, 200)

plt.imshow(cv2.cvtColor(edged, cv2.COLOR_BGR2RGB))





keypoints = cv2.findContours(edged.copy(), cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)

contours = imutils.grab_contours(keypoints)

contours = sorted(contours, key=cv2.contourArea, reverse=True)[:10]



location = None

for contour in contours:

    approx = cv2.approxPolyDP(contour, 10, True)
    
    if len(approx) == 4:
    
        location = approx
        
        break
        
location

mask = np.zeros(gray.shape, np.uint8)

new_image = cv2.drawContours(mask, [location], 0,255, -1)

new_image = cv2.bitwise_and(img, img, mask=mask)



(x,y) = np.where(mask==255)

(x1, y1) = (np.min(x), np.min(y))

(x2, y2) = (np.max(x), np.max(y))

cropped_image = gray[x1:x2+1, y1:y2+1]



----
Using Easy OCR
----

reader = easyocr.Reader(['en'])

result = reader.readtext(cropped_image)

result




----
Result
----

text = result[0][-2]

font = cv2.FONT_ITALIC

res = cv2.putText(img, text=text, org=(approx[0][0][0], approx[1][0][1]+60), fontFace=font, fontScale=1, color=(0,255,0), thickness=2, lineType=cv2.LINE_AA)

res = cv2.rectangle(img, tuple(approx[0][0]), tuple(approx[2][0]), (0,255,0),3)

plt.imshow(cv2.cvtColor(res, cv2.COLOR_BGR2RGB))


