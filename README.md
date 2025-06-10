# RUCLSM
The RUC LSM repository, that includes implementations in CCPP (UFS), MPAS standalone and WRF.

This land surface model (LSM) was originally developed as part of the NOAA Rapid Update Cycle (RUC) model development effort; with ongoing modifications, it is now used as an option for the WRF, UFS (ccpp) and MPAS community models. The RUC weather model, its WRF-based NOAA successor, the Rapid Refresh (RAP) and High-Resolution Rapid Refresh (HRRR), and the UFS-vased RRFSv1 are hourly updated systems that have an emphasis on short-range, near-surface forecasts including aviation-impact variables and pre-convective environment. Coupling to this LSM (hereafter the RUC LSM) has proven to be critical to provide accurate lower boundary conditions into the atmospheric boundary layer of these models.

The RUC LSM became operational at the NOAA/National Centers for Environmental Prediction (NCEP) first, as part of the RUC from 1998–2012, and then as part of the RAP from 2012 through the present and as part of HRRR from 2014 through the present. The UFS-based RRFSv1 is going into the operations in 2025. The simple treatments of basic land surface processes in the RUC LSM (Smirnova et al. 1997, 2000, 2016, 2025) have proven to be physically robust and capable of realistically representing the evolution of soil moisture, soil temperature, and snow in cycled models. Extension of the RAP domain to encompass all of North America and adjacent high-latitude ocean areas necessitated further development of the RUC LSM for application in the tundra permafrost regions and over Arctic sea ice (Smirnova et al. 2000). Other modifications include refinements in the snow model and a more accurate specification of albedo, roughness length, and other surface properties. These recent modifications in the RUC LSM are described and evaluated in Smirnova et al. 2016, 2025.

International projects for intercomparison of land surface and snow parameterization schemes were essential in providing the testing environment and afforded an excellent opportunity to evaluate the RUC LSM with different land use and soil types and within a variety of climates. The RUC LSM was included in phase 2(d) of the Project for the Intercomparison of Land Surface Prediction Schemes [PILPS-2(d)], in which tested models performed 18-yr simulations of the land surface state for the Valdai site in Russia (Schlosser et al. 1997; Slater et al. 2001;  Luo et al. 2003). The RUC LSM was also tested during the Snow Models Intercomparison Project (SnowMIP, SnowMIP2, ESM-SnowMIP), with emphasis on snow parameterizations for both grassland and forest locations in different parts of the world (Etchevers et al. 2002, 2004; Essery et al. 2009; Rutter et al. 2009, Krinner et al. 2018). The analysis of RUC LSM performance over 10 reference sites in ESM-SnowMIP rated it on the 5th place among the 26 participating models.

Major characteristics of RUC LSM model include:

1. Implicit solution of energy and moisture budgets in the layer spanning the ground surface
2. 9 soil levels with high vertical resolution near surface RUC LSM has more levels in oil than GFS Noah Land Surface Model model with higher resolution near the interface with the atmosphere
3. Prognostic soil moisture variable ( θ−θr) The prognostic variables for soil moisture is volumetric soil moisture content minus residual value of soil moisture which is tied to soil particles and does not participate in moisture transport.
4. Frozen soil physics algorithm RUC LSM has a different approach to take into account freezing and thawing processes in soil.
5. Treatment of mixed phase precipitation It accounts for mixed phase precipitation provided by Thompson Aerosol-Aware Microphysics Scheme used in RAP and HRRR.
6. Simple treatment of sea ice which solves heat diffusion in sea ice and allows evolving snow cover on top of sea ice
7. Sub-grid-scale heterogeneity of surface parameters in RUC LSM.
8. Simle irrigation scheme for cropkand areas
9. Water/snow interception bu canopy as function of vegetation fraction and leaf area index (LAI)
10. 2-layer snow model: when SWE < 1.6 cm - snow layer is combined with top soil layer
11. Fractional snow cover (SWE < 3 cm):
weighted average of snow-covered and snow-free areas to compute snow paramters (roughness, albedo)
12. "mosaic" approach for patchy snow
  - Seperate treatment of energy and moisture budgets for snow-covered and snow-free portions of the grid cell
  - Aggregate solutions at the end of time step
  - Reduced cold bias for areas with thin snow
13. Iterative snow melting algorithm
14. Density of snow on the ground - a function of compaction parameter and snow depth and temperature
15. Snow albedo - a function of temperature and snow fraction
16. Snow interception by canopy - a function of vegetation fraction and LAI
17. Density of falling snow/graupel/ice precipitation
  - empirical temperature-dependent equations;
  - averaged density of frozen precipitation is defined from weighted contribution of each hydrometeor species
18. The depth of new snow is defined from its liquid equivalent and density of frozen precipitation

With the certain level of confidence in the skill of the model, the next requirement is to provide land static fields and surface parameters with the best possible accuracy. RAP and HRRR use the same datasets as GFS Noah Land Surface Model. But instead of specifying surface parameters for the dominant soil and land-use category in the grid box, RUC LSM takes into account the sub-grid scale heterogeneity in the computation of such parameters as roughness length, emissivity, soil porosity, soil heat capacity and others. The difference in roughness between the mosaic and dominant category presented on figure 2 is positive from contribution of the forests, which helped to reduce high biases of surface wind speeds in these regions. Roughness lenghth has also seasonal variability in the cropland regions, which again helped to improve the wind forecasts during the warm season.

RUC LSM has a land and a sea ice components.
Main module in WRF and CCPP:
  module  	module_sf_ruclsm
 	
  This module contains both land and ice components of the RUC LSM model, which is a soil/veg/snowpack and ice/snowpack land-surface model to update soil moisture, soil/ice temperature, skin temperature, snowpack water content, snowdepth, and all terms of the surface energy balance and surface water balance.

MPAS has separate modules for RUC land and ice components called out of lsm_driver and seaice_driver:
Modules: module_ruc_ice mand module_ruc_land

Subroutines include:
  sfctmp - top subroutine to compute evergy and moisture budgets.
  
  soil - this subroutine calculates energy and moisture budget for vegetated surfaces without snow, and heat diffusion and Richards eqns in soil. 
         - it calls soiltemp subroutine to update soil temerature and skin temprature.
         - it calls soilmoist subroutine to compute soil moisture and surface runoff.
         
  snowsoil - this subroutine is called for snow covered areas of land. It solves energy and moisture budgets on the surface of snow, and on the interface of snow and             soil. 
          - it calls snowtemp subroutine to computes skin temperature, snow temperature, soil tmperature and moisture, surface runoff, snow depth and snow melt.
          - it calls soilmoist subroutine to compute soil moisture and surface runoff
  soilprop - computes thermal diffusivity and diffusional and hydraulic conductivities.
  tranf - compoutes transpiration function.
  vilka - this subroutine finds the solution of energy budget at the surface from the pre-computed table of saturated water vapor mixing ratio and estimated surface              temperature. 
  soilvegin - this subroutine computes effective land and soil parameters in the grid cell from the weighted contribution of soil and land categories represented in              the grid cell. 
  sice - this subroutine is called for sea ice without accumulated snow on its surface. it solves heat diffusion inside ice and energy budget at the surface of ice. It           computes skin temperature and temerature inside sea ice. 
  snowseaice - this subroutine is called for sea ice with accumulated snow on its surface. It solves energy budget on the snow interface with atmosphere and snow                 interface with ice. It calculates skin temperature, snow and ice temperatures, snow depth and snow melt. 
  
  

MPAS
2 June 2025
a5f54275a - do not initialize when restart or cycle
