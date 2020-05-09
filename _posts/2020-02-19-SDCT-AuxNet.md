---
layout: post
title: SDCT-AuxNet$^{\theta}$
date: 2020-02-19
use_math: true
---

In this post, we discuss our recently published paper: SDCT-AuxNet$^\theta$: DCT Augmented Stain Deconvolutional CNN with Auxiliary Classifier for Cancer Diagnosis. The architecture is developed for acute lymphoblastic leukemia (ALL) cell classification. The architecture employs a small CNN boosted with domain-specific initial layers and a novel ensembling approach. The ensemble approach utilizes two different classifiers coupled with a user-defined variable. The ensembling approach is very generic and can be easily extended to any classification architecture. The dataset used is publicly available at  <a href="
https://doi.org/10.7937/tcia.2019.dc64i46r">
[TCIA]</a>. The article is avialable  <a href="
https://doi.org/10.1016/j.media.2020.101661">
[MedIA]</a>, and the code will be made public soon. 

<!DOCTYPE html>
<html>
<body>

<p>Click on the button to copy the text from the text field. Try to paste the text (e.g. ctrl+v) afterwards in a different window, to see the effect.</p>

<input type="text" value="Hello World" id="myInput">
<button onclick="myFunction()">Copy text</button>

<p>The document.execCommand() method is not supported in IE8 and earlier.</p>

<script>
function myFunction() {
  var copyText = document.getElementById("myInput");
  copyText.select();
  copyText.setSelectionRange(0, 99999)
  document.execCommand("copy");
  alert("Copied the text: " + copyText.value);
}
</script>

</body>
</html>

