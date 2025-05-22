# Detection of Earthquake-Induced Landslides Using Unsupervised Learning

<p align="center">
  <img src="Figures/RGB_pre_post_EQ.jpg" width="800" height="auto"/>
</p>

<details>
  <summary><b>Table of Contents</b></summary>
  
- [About The Project](#about-the-project)
  - [Background](#background) 
- [Getting Started](#getting-started)
  - [Prerequisite](#prerequisite)
  - [Sentinel-2 Data](#sentinel-2-data)
- [Data Alignment](#data-alignment)
- [Normalised Difference Vegetation Index (NDVI) Mask](#ndvi-mask)
- [Unsupervised Learning](#unsupervised-learning)
  - [Bare Soil Index (BSI)](#bsi) 
  - [K-Means](#k-mean)
  - [Gaussian Mixture Models (GMM)](#gaussian-mixture-models-gmm)
- [Performance Analysis](#performance-anaylsis)
- [Conclusion](#conclusion)
- [Environmental Cost Assessment](#environmental-cost-assessment)
- [Video Tutorial](#video-tutorial)
- [References](#references)
- [Contact](#contact)
</details>

<p align="right">(<a href="#top">back to top</a>)</p>

## About The Project
This project is the final assignment for GEOL0069: Artificial Intelligence for Earth Observation. It explores the use of unsupervised learning methods, specifically K-means and Gaussian Mixture Models, to identify landslide scars using Sentinel-2 optical imagery acquired before and after an earthquake. The complete code implementation for this project is available in the file Final_Project.ipynb.

### Background
Landslides are significant natural hazards, often triggered by seismic events or heavy rainfall, that can damage infrastructure and cause casualties. Therefore, rapid and reliable detection of landslide-affected areas is critical for post-disaster assessment. Moreover, landslide scars can offer valuable insight for developing hydrogeological models to assess slope stability. Satellite remote sensing enables observation over large-scale and inaccessible regions, with optical sensors providing spectral information useful for detecting vegetation loss and surface disturbance. In particular, data from the Sentinel-2 satellite mission, which offers high-resolution multispectral imagery, is well suited for landslide mapping.

<p align="center">
  <img src="Figures/luding_background.jpg" width="800" height="auto"/>
  <figcaption style="text-align:center;">The location of the 2022 Luding earthquake and the region of interest of this study. The map is retrieved from Ding and Wang (2025).</figcaption>
</p>

On 5 September 2022, an Mw 6.6 shallow left-lateral earthquake struck Luding, China, triggering over 5,000 landslides (Ding and Wang, 2025). Despite the stress release along the Xianshuihe Fault caused by the Luding earthquake, historical records suggest that the southern Anninghe Fault still poses a significant seismic hazard (Wen et al., 2008). Consequently, it is important to assess the landslide hazard in the surrounding region. This project focuses on a mountainside area southeast of the epicentre, where landslides were densely concentrated. The selected site also benefits from minimal cloud coverage in both pre- and post-earthquake Sentinel-2 images, providing favourable conditions for conducting this analysis.

A widely used approach for landslide scar detection via remote sensing is the Normalised Difference Vegetation Index (NDVI). 

$$
\text{NDVI} = \frac{\text{NIR} - \text{Red}}{\text{NIR} + \text{Red}}
$$

Since healthy vegetation strongly reflects near-infrared radiation and absorbs red light, NDVI uses these spectral bands to quantify vegetation density. Analysing changes in NDVI before and after the earthquake enables preliminary identification of landslide-affected areas. However, because vegetation reflectance varies with local conditions, traditional threshold-based NDVI methods require prior knowledge of vegetation characteristics. To address this limitation, this study applies unsupervised learning using K-means and Gaussian Mixture Models as an exploratory approach to evaluate whether artificial intelligence can reliably detect landslide scars without prior knowledge of the local environment.

<p align="right">(<a href="#top">back to top</a>)</p>

## Getting Started
This project was conducted on Google Colab, which provides free GPU access and allows storage via Google Drive. Alternatively, you may run it in a local environment, but this requires installing the necessary packages and ensuring sufficient computational resources.
### Prerequisite
To read, write, and analyse geospatial raster data, the following package must be installed in Google Colab using the code below before beginning the project.
```python
!pip install rasterio
```
The package should then be imported along with the other required libraries. For a complete list of packages, please refer to Final_Project.ipynb. If you are running the code locally, ensure all required packages are properly installed.

### Sentinel-2 Data

<p align="right">(<a href="#top">back to top</a>)</p>

## Data Alignment

<p align="right">(<a href="#top">back to top</a>)</p>

## References
Ding, Z., & Wang, C. (2025). Coseismic landslides caused by the 2022 Luding earthquake in China: Insights from remote sensing interpretations and machine learning models. Frontiers in Earth Science, 13, 1564744. https://doi.org/10.3389/feart.2025.1564744

Wen, X., Ma, S., Xu, X., & He, Y. (2008). Historical pattern and behavior of earthquake ruptures along the eastern boundary of the Sichuan-Yunnan faulted-block, southwestern China. Physics of the Earth and Planetary Interiors, 168(1), 16–36. https://doi.org/10.1016/j.pepi.2008.04.013

<p align="right">(<a href="#top">back to top</a>)</p>


