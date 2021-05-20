---
layout: post
title:  "Convert images in to a single PDF file"
date:   2021-05-20
categories: Today I Learned
permalink: /blog/:title
---

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/srkc95/sreekanthc.com/blob/master/_posts/2021-05-20-convert-images-into-a-single-pdf-file/convert-images-into-a-single-pdf-file.ipynb)

Sometimes we need to convert figures to pdf and combine them into a single pdf file. We can use [Pillow](https://pypi.org/project/Pillow/) and [PyPDF2](https://pypi.org/project/PyPDF2/) for this task.

Import necessary libraries and create required folders.

``` python
import os
import random
import numpy as np
import seedir as sd
from PIL import Image
import matplotlib.pyplot as plt
from PyPDF2 import PdfFileMerger, PdfFileReader

# Get current folder.
cd = os.getcwd() 
root_fold = os.path.join(cd, "combined-pdf")
if not os.path.exists(root_fold):
    os.mkdir(root_fold)

# Create folder to save figures.
fig_fold = os.path.join(root_fold, "fig")
if not os.path.exists(fig_fold):
    os.mkdir(fig_fold)
    
# Create folder to save PDF files.
pdf_fold = os.path.join(root_fold, "pdf")
if not os.path.exists(pdf_fold):
    os.mkdir(pdf_fold)
```

Create random data using numpy and save to the figure folder.

``` python
for i in range(1,5):
    # plot figure.
    plt.figure(figsize=(15,5))
    plt.xlabel("time", size=15)
    plt.ylabel("data", size=15)
    plt.rcParams['figure.facecolor'] = 'white'
    plt.suptitle("Signal "+ str(i), size=20)
    plt.plot(list(np.random.randint(low=-500, high=500, size=100)))
    
    # save figure.
    fig_f = "signal_"+ str(i) + ".png"
    fig_fpath = os.path.join(fig_fold, fig_f)
    plt.savefig(fig_fpath)
    plt.close() 
```

Get all files ending with a particular file extension.  
``` python
def lst_files(path,extension):
    lst = []
    for _, _, files in os.walk(path):
        for file in files:
            if(file.endswith(extension)):
                lst.append(file)
    return lst
```

Convert each figure to pdf [1].
``` python
# Get list of png files.
lst = lst_files(fig_fold, "png")

for file in lst:
    fig_fpath = os.path.join(fig_fold, file)
    fig = Image.open(fig_fpath)
    
    # Check the file is in the 'RGBA' mode.
    if fig.mode == "RGBA":
        fig = fig.convert("RGB")
        
    # Save PDF.
    pdf_f = file[:-3] + "pdf"
    pdf_fpath = os.path.join(pdf_fold, pdf_f)
    fig.save(pdf_fpath,"PDF")
    fig.close()
```

Combine pdf files [2].
``` python
# Get list of PDF files.
lst = lst_files(pdf_fold, "pdf")
merger = PdfFileMerger()

for file in lst:
    pdf_fpath = os.path.join(pdf_fold, file)
    merger.append(PdfFileReader(pdf_fpath, 'rb'))
    
# Save combined PDF.
comb_fname = "combined.pdf"
comb_fpath = os.path.join(root_fold, comb_fname)
merger.write(comb_fpath)
```

Display folder structure. Click [here](https://github.com/srkc95/sreekanthc.com/blob/master/_posts/2021-05-20-convert-images-into-a-single-pdf-file/combined.pdf) for a sample output file. 

```
os.chdir(root_fold)
sd.seedir(style='lines', itemlimit=10, depthlimit=2, exclude_folders=['.git', '.config', 'sample_data'])
```

```
combined-pdf/
├─combined.pdf
├─fig/
│ ├─signal_1.png
│ ├─signal_2.png
│ ├─signal_3.png
│ └─signal_4.png
└─pdf/
  ├─signal_1.pdf
  ├─signal_2.pdf
  ├─signal_3.pdf
  └─signal_4.pdf
```



#### References

1. How to Convert Image to PDF in Python - CodeSpeedy [Internet]. CodeSpeedy. 2021 [cited 20 May 2021]. Available from: <https://www.codespeedy.com/how-to-convert-image-to-pdf-in-python/>
2. How to append PDF pages using PyPDF2 [Internet]. Stack Overflow. 2021 [cited 20 May 2021]. Available from: <https://stackoverflow.com/a/29871560>
