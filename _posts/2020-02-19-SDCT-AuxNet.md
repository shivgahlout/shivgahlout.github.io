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



<p>Click on the button to copy the text from the text field. Try to paste the text (e.g. ctrl+v) afterwards in a different window, to see the effect.</p>

<input type="text" value="@article{GEHLOT2020101661,
title = "SDCT-AuxNetθ: DCT augmented stain deconvolutional CNN with auxiliary classifier for cancer diagnosis",
journal = "Medical Image Analysis",
volume = "61",
pages = "101661",
year = "2020",
issn = "1361-8415",
doi = "https://doi.org/10.1016/j.media.2020.101661",
url = "http://www.sciencedirect.com/science/article/pii/S136184152030027X",
author = "Shiv Gehlot and Anubha Gupta and Ritu Gupta",
keywords = "Acute lymphoblastic leukemia, ALL diagnosis, Cell classification, Convolutional neural network, Deep learning",
abstract = "Acute lymphoblastic leukemia (ALL) is a pervasive pediatric white blood cell cancer across the globe. With the popularity of convolutional neural networks (CNNs), computer-aided diagnosis of cancer has attracted considerable attention. Such tools are easily deployable and are cost-effective. Hence, these can enable extensive coverage of cancer diagnostic facilities. However, the development of such a tool for ALL cancer was challenging so far due to the non-availability of a large training dataset. The visual similarity between the malignant and normal cells adds to the complexity of the problem. This paper discusses the recent release of a large dataset and presents a novel deep learning architecture for the classification of cell images of ALL cancer. The proposed architecture, namely, SDCT-AuxNetθ is a 2-module framework that utilizes a compact CNN as the main classifier in one module and a Kernel SVM as the auxiliary classifier in the other one. While CNN classifier uses features through bilinear-pooling, spectral-averaged features are used by the auxiliary classifier. Further, this CNN is trained on the stain deconvolved quantity images in the optical density domain instead of the conventional RGB images. A novel test strategy is proposed that exploits both the classifiers for decision making using the confidence scores of their predicted class labels. Elaborate experiments have been carried out on our recently released public dataset of 15114 images of ALL cancer and healthy cells to establish the validity of the proposed methodology that is also robust to subject-level variability. A weighted F1 score of 94.8% is obtained that is best so far on this challenging dataset."
}" id="myInput">
<button onclick="myFunction()">Copy BibTeX</button>



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

