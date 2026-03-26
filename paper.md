---
title: 'tobler: Spatial interpolation and areal analysis for Python'
tags:
- Python
- geographic information science
- interpolation
- spatial analysis
date: "25 March 2023"
output: pdf_document
authors:
- name: Elijah Knaap
  orcid: "0000-0001-7520-2238"
  equal-contrib: true
  affiliation: 1
- name: Sergio J. Rey
  orcid: "0000-0001-5857-9762"
  equal-contrib: true
  affiliation: 2
- name: Renan X. Cortes
  orcid: "0000-0002-1889-5282"
  equal-contrib: true
  affiliation: 3
bibliography: paper.bib
affiliations:
- name: University of California, Irvine
  index: 1
- name: San Diego State University
  index: 2
- name: Federal University of Rio Grande do Sul
  index: 3
---

<!--

NEW JOSS REQUIREMENTS

Length

* longer paper acceptable (but not necessarily encouraged...)
*  750-1750

Sections

https://joss.readthedocs.io/en/latest/paper.html#:~:text=Your%20paper%20must-,include,-the%20following%20required

* Summary
* Statement of need
* State of the field
* Software design
* Research impact statement
* AI usage disclosure

-->


[@mennis2006IntelligentDasymetric;@molinski2022PyinterpolateSpatial;@goodchild1980areal;@Eicher2001dasy]

# Summary


`tobler` is an open-source Python library for spatial interpolation and areal data transformation, designed to support a wide range of workflows in geographic information science (GIScience), spatial data science, and applied spatial analysis. The package provides a unified, extensible framework for transferring data between incompatible zonal systems—an essential task when working with spatially aggregated data such as census geographies, administrative boundaries, or service areas.

Areal interpolation is a foundational problem in spatial analysis, arising whenever data collected over one set of polygons (source zones) must be reallocated to another (target zones) with different spatial configurations. `tobler` implements a suite of methods for this task, ranging from simple area-weighted interpolation to more advanced dasymetric and model-based approaches. The package is designed to operate natively on GeoPandas GeoDataFrames, enabling seamless integration into modern Python-based geospatial workflows.

As part of the PySAL (Python Spatial Analysis Library [@pysal2007;rey2022pysalecosystem]) ecosystem, `tobler` adheres to shared design principles emphasizing composability, transparency, and reproducibility. It fills a critical gap in the Python geospatial stack by providing robust, well-tested implementations of areal interpolation methods that are both accessible for applied users and extensible for methodological research.

# Statement of need

Spatial data are frequently aggregated to arbitrary or administratively defined units, such as census tracts, ZIP codes, or political boundaries. These units often change over time or differ across datasets, creating substantial challenges for longitudinal analysis, data integration, and cross-scale comparison. Analysts are therefore routinely required to transfer variables from one set of spatial units to another, a process complicated by partial overlaps, mismatched boundaries, and differences in spatial resolution.

Traditional GIS software (e.g., ArcGIS, QGIS) provides tools for areal interpolation, but these implementations are often embedded in graphical interfaces, limiting reproducibility and automation. Moreover, they may not expose the full range of methodological options or allow for easy integration into data science pipelines.

In the Python ecosystem, prior to `tobler`, support for areal interpolation was fragmented or limited. While foundational libraries such as GeoPandas provide data structures and geometric operations, they do not natively implement interpolation methods. As a result, users often relied on ad hoc scripts or external tools, leading to inconsistent workflows and potential methodological errors.

`tobler` addresses these challenges by providing:

* A coherent and well-documented API for areal interpolation
* Native integration with GeoPandas and the broader PySAL ecosystem
* Support for multiple interpolation paradigms, including area-weighted, dasymetric, and model-based approaches
* A focus on reproducibility, allowing interpolation workflows to be scripted, version-controlled, and shared

These capabilities make `tobler` particularly valuable for researchers and practitioners in fields such as geography, urban planning, public health, environmental science, and regional economics, where spatial data integration is a routine requirement.

# Core functionality

`tobler` organizes its functionality around several key interpolation paradigms, each corresponding to different assumptions about how variables are distributed within source zones.

## Area-weighted interpolation

Area-weighted interpolation is the most basic and widely used method for transferring data between polygon layers. It assumes that variables are uniformly distributed within each source zone and allocates values to target zones in proportion to the area of overlap.

`tobler` provides efficient implementations for both **extensive variables** (e.g., population counts) and **intensive variables** (e.g., rates or densities), ensuring appropriate handling of each type. The library also supports pycnophylactic adjustments to preserve totals where required.

## Dasymetric interpolation

Dasymetric interpolation refines area-weighted approaches by incorporating ancillary data—such as land use, land cover, or remotely sensed information—to model the internal heterogeneity of source zones. For example, population may be redistributed only to residential areas rather than uniformly across all land.

`tobler` supports both vector- and raster-based dasymetric workflows, allowing users to integrate a wide range of auxiliary datasets. This is particularly useful in urban and environmental applications where fine-scale heterogeneity is important.

## Model-based interpolation

Beyond deterministic approaches, `tobler` includes model-based methods that use statistical or machine learning techniques to estimate spatial distributions. These approaches can incorporate covariates and capture more complex spatial patterns, providing improved accuracy in many contexts.

The design of `tobler` allows these methods to be extended and customized, making the package a useful platform for methodological research in spatial interpolation.

## Integration with GeoPandas

All core functions in `tobler` operate directly on GeoPandas GeoDataFrames, minimizing friction in typical workflows. Users can pass source and target datasets as GeoDataFrames, specify variables of interest, and obtain interpolated results as new GeoDataFrames. This design leverages the broader geospatial Python stack, including Shapely for geometry operations and pandas for tabular data handling.

## Example workflow

A typical area-weighted interpolation in `tobler` can be implemented as follows:

```python
from tobler.area_weighted import area_interpolate

result = area_interpolate(
    source_df,
    target_df,
    extensive_variables=["population"],
    intensive_variables=["income"]
)
```

This operation transfers population counts and income measures from the source geometries to the target geometries, handling each variable type appropriately.

# Relationship to existing software

`tobler` is a component of the PySAL ecosystem, which provides a comprehensive suite of tools for spatial analysis in Python. Within this ecosystem:

* `libpysal` provides spatial weights, data structures, and foundational utilities
* `esda` supports exploratory spatial data analysis
* `spreg` implements spatial econometric models
* `mapclassify` provides classification schemes for choropleth mapping

`tobler` complements these packages by addressing the specific problem of spatial data transformation between incompatible zonal systems.

Compared to desktop GIS platforms, `tobler` offers several advantages:

* **Reproducibility**: Workflows can be scripted and version-controlled
* **Transparency**: Methods and assumptions are explicit and inspectable
* **Extensibility**: Users can modify or extend algorithms for research purposes
* **Integration**: Interpolation can be embedded within larger data science pipelines, including machine learning and statistical modeling

While similar functionality exists in other ecosystems (e.g., R packages such as `areal` or `sf`-based workflows), `tobler` provides a native solution for Python users, aligning with the growing adoption of Python in geospatial and data science communities.

# Performance and design considerations

`tobler` is designed with attention to both computational efficiency and usability. Spatial overlay operations, which are central to areal interpolation, can be computationally intensive for large datasets. The package leverages vectorized operations and efficient geometric libraries (via GeoPandas and Shapely) to handle these tasks.

The API design emphasizes clarity and consistency, with function signatures that explicitly distinguish between extensive and intensive variables. This reduces the likelihood of common errors in interpolation workflows and encourages best practices in spatial data handling.

Additionally, `tobler` is developed with testing and documentation standards consistent with the Scientific Python ecosystem, ensuring reliability and maintainability.


# Statement of need

...

# State of the field

...

# Software design

...

# Research impact statement

...

# AI usage disclosure

...

# Acknowledgements

`tobler` is developed as part of the PySAL community, which brings together researchers and developers working on spatial analysis methods and software. The project builds on decades of research in areal interpolation, dasymetric mapping, and spatial data science, and benefits from contributions across the open-source geospatial community.

The following acknowledgement applies to James D. Gaboardi:

> This manuscript has been authored in part by UT-Battelle LLC under contract DE-AC05-00OR22725 with the US Department of Energy (DOE). The US government retains and the publisher, by accepting the article for publication, acknowledges that the US government retains a nonexclusive, paid-up, irrevocable worldwide license to publish or reproduce the published form of this manuscript, or allow others to do so, for US government purposes. DOE will provide public access to these results of federally sponsored research in accordance with the DOE Public Access Plan (http://energy.gov/downloads/doe-public-access-plan).

# References

