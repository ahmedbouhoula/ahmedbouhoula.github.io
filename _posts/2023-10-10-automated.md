---
layout: post
title: "Automated Large-Scale Analysis of Cookie Notice Compliance"
abstract: "Privacy regulations such as the General Data Protection Regulation (*GDPR*) require websites to inform EU-based users about non-essential data collection and to request their consent to this practice. Previous studies have documented widespread violation of these regulations. However, these studies provide a limited view of the general compliance picture: they are either restricted to a subset of notice types, detect only simple violations using prescribed patterns, or analyze notices manually. Thus, they are restricted both in their scope and in their ability to analyze violations at scale.

We present the first general, large-scale, automated analysis of cookie notice compliance. Our method is capable of interacting with cookie notices, e.g., by navigating through their settings. It observes declared processing purposes and available consent options using Natural Language Processing and compares the actual use of cookies with the declared usage. By virtue of the generality and scale of our analysis, we correct for the selection bias present in previous studies focusing on specific Consent Management Platforms (*CMP*). We also provide a more general view of the overall compliance picture using a set of 97k websites popular in the EU. We report, in particular, that 65.4% of websites offering a cookie rejection option likely collect user data despite explicit negative consent."
authors: Ahmed Bouhoula, Karel Kubicek, Amit Zac, Carlos Cotrini, and David Basin
publisher: Submitted to USENIX Security, 2023.
categories: Machine Learning for Privacy, GDPR Compliance
keywords: cookies, CMP, GDPR, privacy
---

# Automated Large-Scale Analysis of Cookie Notice Compliance

**Authors: Ahmed Bouhoula, Karel Kubicek, Amit Zac, Carlos Cotrini, and David Basin**

**Abstract:** *Privacy regulations such as the General Data Protection Regulation (*GDPR*) require websites to inform EU-based users about non-essential data collection and to request their consent to this practice. Previous studies have documented widespread violation of these regulations. However, these studies provide a limited view of the general compliance picture: they are either restricted to a subset of notice types, detect only simple violations using prescribed patterns, or analyze notices manually. Thus, they are restricted both in their scope and in their ability to analyze violations at scale.*

*We present the first general, large-scale, automated analysis of cookie notice compliance. Our method is capable of interacting with cookie notices, e.g., by navigating through their settings. It observes declared processing purposes and available consent options using Natural Language Processing and compares the actual use of cookies with the declared usage. By virtue of the generality and scale of our analysis, we correct for the selection bias present in previous studies focusing on specific Consent Management Platforms (*CMP*). We also provide a more general view of the overall compliance picture using a set of 97k websites popular in the EU. We report, in particular, that 65.4% of websites offering a cookie rejection option likely collect user data despite explicit negative consent.*

<!-- * Download author pre-print of the paper: [PDF](https://ahmedbouhoula.github.io/assets/pdf/Generalized_GDPR_Violation_Detection_in_Cookie_Notices_preprint.pdf) -->
* Conference page: [USENIX Security 2024](https://www.usenix.org/conference/usenixsecurity24/presentation/bouhoula)
* Download author pre-print of the paper: [PDF](https://ahmedbouhoula.github.io/assets/pdf/bouhoula2024automated.pdf)
* Source code: [Github](https://github.com/bouhoula/alsacnc)
* Request data access: [Google form](https://forms.gle/4cPPLnkwA241sKbP6)

### BibTex

```latex
@inproceedings{bouhoula2024automated,
  author = {Ahmed Bouhoula and Karel Kubicek and Amit Zac and Carlos Cotrini and David Basin},
  title = {Automated Large-Scale Analysis of Cookie Notice Compliance},
  booktitle = {33st USENIX Security Symposium (USENIX Security 24)},
  year = {2024},
  month = aug,
  pages = {TBA},
  isbn = {TBA},
  publisher = {USENIX Association},
  url = {https://www.usenix.org/conference/usenixsecurity24/presentation/bouhoula},
  address = {Philadelphia, PA},
}
```

### Motivation

To deter privacy-intrusive practices and address the ubiquitous tracking on the internet, the EU introduced privacy regulations such as the General Data Protection Regulation (GDPR) and the ePrivacy Directive. These laws mandate, in particular, that websites inform users about the explicit purposes for which their data is collected. This has led to the global adoption of cookie notices, which are now unavoidable when browsing the web. 

Several studies, including [CookieBlock](https://karelkubicek.github.io/post/cookieblock), showed high levels of non-compliance with the regulations. However, these studies are significantly constrained, leading to biased measurements. They either depend on specific technologies present in a subset of consent notices or they are manual and therefore cannot be scaled up. Our work addresses these constraints using a crawler that interacts with the cookie notice and machine learning models that classify declared and observed practices of the website.


### A crawler that interacts with cookie notices

Previous automated studies relied on the API used by websites that relied on specific Consent Management Platforms (*CMP*). We created a crawler that detects cookie notices using a heuristic based on 1) a crowd-sourced [EasyList Cookie](https://secure.fanboy.co.nz/fanboy-cookiemonster.txt), 2) the [z-index](https://developer.mozilla.org/en-US/docs/Web/CSS/z-index) of the cookie notice, and 3) [sentence segmentation models](https://spacy.io/usage/models). 

The crawler is not limited to the front page of the cookie notice as it navigates the notice with a DFS approach. This allows us to perceive the cookie notices as users do, not as the API specifies them.

The crawler browses the websites after performing several actions: rejecting or accepting the cookies, saving default settings, dismissing the notice with a close button, or not interacting at all. For each action, we extract the set of cookies resulting from interacting with it, which we rely on to predict whether websites honor user choices.

Using machine translation, we support websites in the following languages: Danish, Dutch, English, Finnish, French, German, Italian, Portuguese, Polish, Spanish, and Swedish.

*Below, we show you an interactive cookie notice example. All elements in blue dotted boxes contain descriptions of how our ML models would classify them, which is available when you hover your mouse over them. You need JS to see the demo.*

<!-- interactive notice -->
<div>
<style type="text/css" scoped>
* {
  box-sizing: border-box;
}

#regForm {
  background-color: #f7f7f7;
  margin: 30px auto;
  font-family: Raleway;
  padding: 40px;
  padding-bottom: 80px;
  width: 40%;
  min-width: 520px;
}

h1 {
  text-align: center;  
}

/* Hide all steps by default: */
.tab {
  display: none;
}

button {
  background-color: #04AA6D;
  color: #ffffff;
  border: none;
  padding: 10px 20px;
  font-size: 17px;
  font-family: Raleway;
  cursor: pointer;
}

#close {
  background-color: #f7f7f7;
  color: #000000;
  border: none;
  padding: 10px;
  font-size: 30px;
}

button:hover {
  opacity: 0.8;
}

#rejBtn {
  background-color: #fe8184;
}
#setBtn {
  background-color: #2ab5f6;
}
#savBtn {
  background-color: #2ab5f6;
}
#cloTool {
  margin-top: -40px;
  margin-right: -30px;
  float:right;
}

.tooltip {
  position: relative;
  display: inline-block;
  border: 1px dotted rgb(0, 0, 255);
  padding: 3px;
}

.tooltip .tooltiptext {
  visibility: hidden;
  width: 20em;
  background-color: black;
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 5px 0;
  /* Position the tooltip */
  position: absolute;
  z-index: 1;
  top: 100%;
  left: 50%;
  margin-left: -9em;
}

.tooltip:hover .tooltiptext {
  visibility: visible;
}
</style>

<form id="regForm">
  <span class="tooltip" id="cloTool">
    <button type="button" id="close" onclick="nextPrev(2, 'close')"><span aria-hidden="true">&times;</span></button>
    <span class="tooltiptext">Interactive element prediction: close.</span>
  </span>
  
  <h1>Example cookie notice</h1>
  <div class="tab">
    <p>
      <span class="tooltip">
        We use cookies to enhance your browsing experience. <span class="tooltiptext">NLP prediction: Essential purpose.</span>
      </span>
      <span class="tooltip">
        We also use them to serve personalized ads and to analyze our traffic. <span class="tooltiptext">NLP prediction: Non-essential purpose.</span>
      </span>
    </p>
  </div>
  <div class="tab">
    <p>Choose what types of cookies you consent to. Limitation of our study is that we do not inspect what checkbox/toggle values were selected by users. We however submit the default value, and if we observe cookies, it means that consent was not active.</p>
    
    
    <span class="tooltip">
      <input type="checkbox" checked="true" disabled=""> <label>Essential cookies are required to enable essential functions of the website, such as registration or shopping carts. They are always enabled to allow for a smooth and problem-free browsing experience.</label><span class="tooltiptext">NLP prediction: Essential purpose.</span>
    </span>
    
    <span class="tooltip">
      <input type="checkbox"> <label>Advertising cookies tailor advertisements to the viewer, help track the user and collect sensitive data of the user's browsing behavior, usually across multiple websites.</label><span class="tooltiptext">NLP prediction: Non-essential purpose.</span>
    </span>
    
  </div>
  <div class="tab">
    <p>Now the crawler would randomly browse 5 links, scroll, observing all notice after <span class="consentType" style="font-weight: bold;"></span> consent.</p>
  </div>
  <div style="float:right;">
    <span class="tooltip" id="rejTool">
      <button type="button" id="rejBtn" onclick="nextPrev(2, 'reject')">Reject all</button>
      <span class="tooltiptext">Interactive element prediction: reject.</span>
    </span>
    <span class="tooltip" id="setTool">
      <button type="button" id="setBtn" onclick="nextPrev(1)">Customize</button>
      <span class="tooltiptext">Interactive element prediction: settings.</span>
    </span>
    <span class="tooltip" id="savTool">
      <button type="button" id="savBtn" onclick="nextPrev(1, 'save')">Save your choices</button>
      <span class="tooltiptext">Interactive element prediction: save.</span>
    </span>
    <span class="tooltip" id="accTool">
      <button type="button" id="accBtn" onclick="nextPrev(2, 'accept')">Accept all</button>
      <span class="tooltiptext">Interactive element prediction: accept.</span>
    </span>
    <button type="button" id="resBtn" onclick="nextPrev(-2)">Reset demo</button>
  </div>
</form>

<script>
var currentTab = 0; // Current tab is set to be the first tab (0)
showTab(currentTab); // Display the current tab

function showTab(n) {
  var x = document.getElementsByClassName("tab");
  x[n].style.display = "block";
  if (n == 0) {
    document.getElementById("rejTool").style.display = "inline-block";
    document.getElementById("cloTool").style.display = "inline-block";
    document.getElementById("accTool").style.display = "inline-block";
    document.getElementById("setTool").style.display = "inline-block";
    document.getElementById("savTool").style.display = "none";
    document.getElementById("resBtn").style.display = "none";
  } else if (n == 1) {
    document.getElementById("rejTool").style.display = "none";
    document.getElementById("cloTool").style.display = "inline-block";
    document.getElementById("accTool").style.display = "none";
    document.getElementById("setTool").style.display = "none";
    document.getElementById("savTool").style.display = "inline-block";
    document.getElementById("resBtn").style.display = "none";
  } else {
    currentTab = 2;
    document.getElementById("rejTool").style.display = "none";
    document.getElementById("cloTool").style.display = "none";
    document.getElementById("accTool").style.display = "none";
    document.getElementById("setTool").style.display = "none";
    document.getElementById("savTool").style.display = "none";
    document.getElementById("resBtn").style.display = "inline";
  }
}

function nextPrev(n, consent = "") {
  var x = document.getElementsByClassName("tab");
  x[currentTab].style.display = "none";
  currentTab = currentTab + n;
  document.getElementsByClassName("consentType")[0].innerHTML = consent;
  showTab(currentTab);
}
</script>
</div>

### ML and NLP models

The crawler extracts the declared behavior as described in the cookie notice (its text and interactive elements), as well as the observed behavior (cookies that were used by the website depending on the action performed on the cookie notice). We reason about the collected data using three ML models.

* We predict the declared data collection purposes in the notice text using a BERT model. We trained this model using the dataset by [Santos et al.](https://arxiv.org/abs/2110.02597), where we reduced labels to binary decision whether the sentence declares data processing purpose that requires users' consent. This included the following purposes: profiling, advertising, custom content, analytics, and social media features. This model achieves an accuracy of 97.6%.
* We predict the purpose of interactive elements of the cookie notice using a BERT model trained on a dataset of 2353 interactive elements annotated by us. The labels are accept, reject, close, save, settings, and other. This model achieves 95.1% accuracy.
* We classify whether the website uses cookies that require consent using an XGBoost model. This model is based on the prior work [CookieBlock](https://karelkubicek.github.io/post/cookieblock) and allows us to classify when a website uses Analytics or Advertising cookies (*AA cookies*) with a precision of 98.7% and a recall of 91.9%.


### Violation detection

We crawl 97k websites selected using the Chrome UX report, which better represents real browsing patterns than other lists ([Ruth et al.](https://doi.org/10.1145/3517745.3561444)). We select the EU (+UK) countries that speak one of the supported languages to ensure that the websites target users under the GDPR.

The crawled data allows us to reason about the differences between declared and observed behavior. The outputs of the crawler and machine learning models serve as parameters for a decision tree, which outputs ten privacy violations or dark patterns.

![Decision tree for violations](https://ahmedbouhoula.github.io/assets/images/automated/decision_violations.svg)
*Decision tree that takes as input the outputs of the crawl and ML models. It outputs all types of potential violations present on the website. "AA cookies" stands for Analytics or Advertising cookies. Such cookies require consent under EU regulations.*

![Decision tree dark patterns](https://ahmedbouhoula.github.io/assets/images/automated/decision_darkpatterns.svg){: style="float: left; width:50%"}![Observed violations](https://ahmedbouhoula.github.io/assets/images/automated/violations_bar.svg){: style="float: left;width:50%;"}
*On the left, the decision tree of dark patterns. On the right, observed statistics of potential violations and dark patterns.*

### Aggregated results 

We can parametrize website selection based on the country, popularity rank, or the consent provider used by the website. We list the most important observations, all of them are statistically significant:

* More popular websites have fewer **visible violations** such as missing notice, missing reject button, or undeclared purposes than less popular websites. However, these websites are more likely to ignore rejected consent, or assume the user's consent before they interact with the notice.
* Prior studies suffer from a selection bias stemming from restricting the analysis to websites using specific consent providers. We show this by applying such restrictions to our results. For example, we find that restricting the analysis to websites using the [Transparency & Consent Framework](https://iabeurope.eu/transparency-consent-framework/) (studied by [Matte et al.](https://doi.org/10.1109/SP40000.2020.00076)) leads to results that diverge greatly from those on our overall sample of websites.

![Violations per rank](https://ahmedbouhoula.github.io/assets/images/automated/violations_bar_per_rank.svg){: style="float: left; width:50%"}![Violations bias comparison](https://ahmedbouhoula.github.io/assets/images/automated/violations_bias.svg){: style="float: left;width:50%;margin-bottom:20px;margin-top:20px;"}
*On the left, we present violations per rank. On the right, we compare our results with other studies.*

### Q&A

* **Q:** What is the probability that your violation decision tree produces false positives?

  **A:** We tuned the models to be conservative, so they rather produce false negatives. Our evaluation on 500 random websites quntifies both false positives and negatives, showing that our results are conservative. For more details, check Section 7 of the paper.
