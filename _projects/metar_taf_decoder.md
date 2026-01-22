---
layout: page
title: Metar Taf Decoder
description: Web application to decode METAR and TAF aviation weather reports
img: assets/img/metar_taf_decoder.png
importance: 2
category: Web Development
---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/metar_taf_decoder.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This is a web application using the [MetarParser](https://github.com/mivek/MetarParser) project to decode and request METAR and TAF messages.
[Quarkus](https://quarkus.io/) is used to create a native image.

URL: [https://metar-taf-decoder.com](https://metar-taf-decoder.com)

## History

| Date          | Event                                                                                  |
|---------------|----------------------------------------------------------------------------------------|
| May 2021      | First release of the web application                                                   |
| October 2020  | First release of the `python-metar-taf-decoder`. A Python version of the `MetarParser` |
| February 2019 | The project `MetarParser` is released on Maven Central repository                      |
| December 2017 | First version of the `MetarParser`                                                     |
