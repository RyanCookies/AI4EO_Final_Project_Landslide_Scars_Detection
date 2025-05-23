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
- [Normalised Difference Vegetation Index (NDVI) Mask](#normalised-difference-vegetation-index-ndvi-mask)
- [Unsupervised Learning](#unsupervised-learning)
  - [Bare Soil Index (BSI)](#bare-soil-index-bsi) 
  - [K-Means](#k-means)
  - [Gaussian Mixture Models (GMM)](#gaussian-mixture-models-gmm)
- [Performance Analysis](#performance-analysis)
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
Sentinel-2 is a satellite mission developed by the European Space Agency (ESA) under the Copernicus Programme, designed to provide high-resolution optical imagery for land monitoring. It captures data in 13 spectral bands ranging from visible to shortwave infrared, with spatial resolutions of 10, 20, and 60 metres depending on the band. This study uses Level-2A (L2A) products, which provide bottom-of-atmosphere (BOA) reflectance data derived from Level-1C top-of-atmosphere (TOA) imagery through atmospheric correction (European Space Agency, n.d.).

To fetch the data, you must create an account on the Copernicus Open Access Hub and input your login credentials in the "Fetching Data" section of the code.

```python
# --- Authenticate with Copernicus Data Space ---
username = "your_username"
password = "your_password"
access_token, refresh_token = get_access_and_refresh_token(username, password)

# --- Define Time Ranges for Pre- and Post-earthquake ---
pre_eq_start_date = "2021-11-05"
pre_eq_end_date = "2021-11-06"
post_eq_start_date = "2022-11-20"
post_eq_end_date = "2022-11-21"

# --- Query Sentinel-2 Products Covering 2022 Luding Earthquake Affected Area ---
pre_eq_sentinel2_data = query_sentinel2_luding_data(
    pre_eq_start_date, pre_eq_end_date, access_token
)

post_eq_sentinel2_data = query_sentinel2_luding_data(
    post_eq_start_date, post_eq_end_date, access_token
)

# --- Download the Selected Sentinel-2 Product for Each Time Range ---
download_dir = "/content/drive/MyDrive/GEOL0069_AI4EO/Final_Project/"  # Define download directory

# Download Pre-Earthquake Product
product_id = pre_eq_sentinel2_data['Id'][0]
file_name = pre_eq_sentinel2_data['Name'][0]
download_single_product(product_id, file_name, access_token, download_dir)

# Download Post-Earthquake Product
product_id = post_eq_sentinel2_data['Id'][0]
file_name = post_eq_sentinel2_data['Name'][0]
download_single_product(product_id, file_name, access_token, download_dir)
```
The downloaded file will be in ZIP format. To access the data, unzip the file in the directory where it was saved.

<p align="right">(<a href="#top">back to top</a>)</p>

## Data Alignment
The Sentinel-2 images used in this study exhibited spatial misalignment, particularly when comparing pre- and post-event scenes. These discrepancies can affect pixel-level correspondence and must be corrected through image registration or coregistration techniques to ensure reliable change detection. Therefore, Enhanced Correlation Coefficient (ECC) alignment was applied to the post-earthquake image, using the pre-earthquake image as the reference. The alignment result is shown below:

<p align="center">
  <img src="Figures/RGB_pre_post_aligned_EQ.jpg" width="800" height="auto"/>
  <figcaption style="text-align:center;">The pre-earthquake, unaligned post-earthquake, and aligned post-earthquake RGB images for the region of interest in this study.</figcaption>
</p>

<p align="right">(<a href="#top">back to top</a>)</p>

## Normalised Difference Vegetation Index (NDVI) Mask
<p align="right">(<a href="#top">back to top</a>)</p>

## Unsupervised Learning
### Bare Soil Index (BSI)
### K-Means
### Gaussian Mixture Models (GMM)
<p align="right">(<a href="#top">back to top</a>)</p>

## Performance Analysis
<p align="right">(<a href="#top">back to top</a>)</p>

## Conclusion
<p align="right">(<a href="#top">back to top</a>)</p>

## Environmental Cost Assessment
<p align="right">(<a href="#top">back to top</a>)</p>

## Video Tutorial
<p align="right">(<a href="#top">back to top</a>)</p>

## References
Ding, Z., & Wang, C. (2025). Coseismic landslides caused by the 2022 Luding earthquake in China: Insights from remote sensing interpretations and machine learning models. Frontiers in Earth Science, 13, 1564744. https://doi.org/10.3389/feart.2025.1564744

European Space Agency. (n.d.). SENTINEL-2 Documents. SentiWiki. https://sentiwiki.copernicus.eu/web/document-library#Library-S2-Documents

Wen, X., Ma, S., Xu, X., & He, Y. (2008). Historical pattern and behavior of earthquake ruptures along the eastern boundary of the Sichuan-Yunnan faulted-block, southwestern China. Physics of the Earth and Planetary Interiors, 168(1), 16–36. https://doi.org/10.1016/j.pepi.2008.04.013

<p align="right">(<a href="#top">back to top</a>)</p>

## Contact
<p align="right">(<a href="#top">back to top</a>)</p>
