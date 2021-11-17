+++
categories = ["linux"]
date = "2021-11-17"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
slug = "covert-hiec-to-jpg-in-ubuntu"
title = "Convert HEIC to JPG in Ubuntu"
type = ["posts","post"]
[ author ]
  name = "Captain Snowball"
+++

## Convert HEIC to JPG in Ubuntu
Sometimes I have to share files to an old Android device which is not powerful enough to handle jpg so I follow these steps to convert HEIC to JPG

### Install dependencies
This will install package we need to convert images
```bash
sudo apt install libheif-examples
```

### Convert images
The following command will convert all images in current folder
```bash
for file in *.heic
do
heif-convert $file ${file/%.heic/.jpg}
done
```