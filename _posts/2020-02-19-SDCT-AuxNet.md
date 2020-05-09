---
layout: post
title: SDCT-AuxNet$^{\theta}$
date: 2020-02-19
use_math: true
---

SDCT-AuxNet$^\theta$: DCT Augmented Stain Deconvolutional CNN with Auxiliary Classifier for Cancer Diagnosis is an architecture developed for acute lymphoblastic leukemia (ALL) cell classification. The architecture employs a small CNN boosted with domain-specific initial layers and a novel ensembling approach. The ensemble approach utilizes two different classifiers coupled with a user-defined variable. The ensembling approach is very generic and can be easily extended to any classification architecture. The dataset used is publicly available at  <a href="
https://doi.org/10.7937/tcia.2019.dc64i46r">
[TCIA]</a>. The article is avialable <a href="
https://doi.org/10.1016/j.media.2020.101661">
[here]</a>, and the code will be made public soon. 



function CopyToClipboard (containerid) {
  // Create a new textarea element and give it id='temp_element'
  var textarea = document.createElement('textarea')
  textarea.id = 'temp_element'
  // Optional step to make less noise on the page, if any!
  textarea.style.height = 0
  // Now append it to your page somewhere, I chose <body>
  document.body.appendChild(textarea)
  // Give our textarea a value of whatever inside the div of id=containerid
  textarea.value = document.getElementById(containerid).innerText
  // Now copy whatever inside the textarea to clipboard
  var selector = document.querySelector('#temp_element')
  selector.select()
  document.execCommand('copy')
  // Remove the textarea
  document.body.removeChild(textarea)
}
  
<div id="to-copy">
  "This text will be copied to your clipboard when you click the button!"
</div>
<button onClick="CopyToClipboard('to-copy')">Copy</button>
