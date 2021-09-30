---
title: "Malaria Visualizations"
author: Malcolm Smith Fraser
date: 2021-09-30T18:15:11Z
tags:
series:
draft: true
plotly: true
---

{{< plotly json="deaths_by_age_all_years.json" height="700">}} 

![alt](/images/average_deaths_by_quantiles.png)

{{< rawhtml >}}
<body>
  <style type="text/css">
    html, body, #container {
      height: 100%;
    }
    body, #container {
      overflow: hidden;
      margin: 0;
    }
    #iframe {
      width: 100%;
      height: 100%;
      border: none;
    }
  </style>
  <div id="container">
    <iframe id="iframe" sandbox="allow-scripts" src="/files/bios-823-2021/homework/deaths_by_age_all_years.html"></iframe>
  </div>
</body>
{{< /rawhtml >}}