# RUCLSM
The RUC LSM repository, that includes implementations in CCPP (UFS), MPAS and WRF.

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
  - sfctmp - top subroutine to compute evergy and moisture budgets.
  
  - soil - this subroutine calculates energy and moisture budget for vegetated surfaces without snow, and heat diffusion and Richards eqns in soil.
    - it calls soiltemp subroutine to update soil temerature and skin temprature.
    - it calls soilmoist subroutine to compute soil moisture and surface runoff.
         
  - snowsoil - this subroutine is called for snow covered areas of land. It solves energy and moisture budgets on the surface of snow, and on the interface of snow and             soil. 
    - it calls snowtemp subroutine to computes skin temperature, snow temperature, soil tmperature and moisture, surface runoff, snow depth and snow melt.
    - it calls soilmoist subroutine to compute soil moisture and surface runoff
          
  - soilprop - computes thermal diffusivity and diffusional and hydraulic conductivities.
  - tranf - compoutes transpiration function.
  - vilka - this subroutine finds the solution of energy budget at the surface from the pre-computed table of saturated water vapor mixing ratio and estimated surface              temperature. 
  - soilvegin - this subroutine computes effective land and soil parameters in the grid cell from the weighted contribution of soil and land categories represented in              the grid cell. 
  - sice - this subroutine is called for sea ice without accumulated snow on its surface.
     - it solves heat diffusion inside ice and energy budget at the surface of ice, computes skin temperature and temerature inside sea ice.
  - snowseaice - this subroutine is called for sea ice with accumulated snow on its surface.
     - It solves energy budget on the snow interface with atmosphere and snow interface with ice, calculates skin temperature, snow and ice temperatures, snow depth and snow melt. 
  
CCPP argument list:
local_name	standard_name	          long_name	                                            units	      type	   dimensions	kind	intent	optional
iter	      ccpp_loop_counter	      loop counter for subcycling loops in CCPP	            index	     integer	   ()		     in          	False
me	mpi_rank	current MPI-rank	index	integer	()		in	False
master	mpi_root	master MPI-rank	index	integer	()		in	False
kdt	index_of_time_step	current number of time steps	index	integer	()		in	False
im	horizontal_loop_extent	horizontal loop extent	count	integer	()		in	False
nlev	vertical_dimension	number of vertical levels	count	integer	()		in	False
lsoil_ruc	soil_vertical_dimension_for_land_surface_model	number of soil layers internal to land surface model	count	integer	()		in	False
lsoil	soil_vertical_dimension	soil vertical layer dimension	count	integer	()		in	False
zs	depth_of_soil_levels_for_land_surface_model	depth of soil levels for land surface model	m	real	(soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
t1	air_temperature_at_lowest_model_layer	mean temperature at lowest model layer	K	real	(horizontal_dimension)	kind_phys	in	False
q1	water_vapor_specific_humidity_at_lowest_model_layer	water vapor specific humidity at lowest model layer	kg kg-1	real	(horizontal_dimension)	kind_phys	in	False
qc	cloud_condensed_water_mixing_ratio_at_lowest_model_layer	moist (dry+vapor, no condensates) mixing ratio of cloud water at lowest model layer	kg kg-1	real	(horizontal_dimension)	kind_phys	in	False
soiltyp	soil_type_classification	soil type at each grid cell	index	integer	(horizontal_dimension)		inout	False
vegtype	vegetation_type_classification	vegetation type at each grid cell	index	integer	(horizontal_dimension)		inout	False
sigmaf	vegetation_area_fraction	areal fractional cover of green vegetation	frac	real	(horizontal_dimension)	kind_phys	in	False
laixy	leaf_area_index	leaf area index	none	real	(horizontal_dimension)	kind_phys	in	False
sfcemis	surface_longwave_emissivity_over_land_interstitial	surface lw emissivity in fraction over land (temporary use as interstitial)	frac	real	(horizontal_dimension)	kind_phys	inout	False
dlwflx	surface_downwelling_longwave_flux	surface downwelling longwave flux at current time	W m-2	real	(horizontal_dimension)	kind_phys	in	False
dswsfc	surface_downwelling_shortwave_flux	surface downwelling shortwave flux at current time	W m-2	real	(horizontal_dimension)	kind_phys	in	False
snet	surface_net_downwelling_shortwave_flux	surface net downwelling shortwave flux at current time	W m-2	real	(horizontal_dimension)	kind_phys	in	False
delt	time_step_for_dynamics	physics time step	s	real	()	kind_phys	in	False
tg3	deep_soil_temperature	deep soil temperature	K	real	(horizontal_dimension)	kind_phys	in	False
cm	surface_drag_coefficient_for_momentum_in_air_over_land	surface exchange coeff for momentum over land	none	real	(horizontal_dimension)	kind_phys	in	False
ch	surface_drag_coefficient_for_heat_and_moisture_in_air_over_land	surface exchange coeff heat & moisture over land	none	real	(horizontal_dimension)	kind_phys	in	False
prsl1	air_pressure_at_lowest_model_layer	mean pressure at lowest model layer	Pa	real	(horizontal_dimension)	kind_phys	in	False
zf	height_above_ground_at_lowest_model_layer	layer 1 height above ground (not MSL)	m	real	(horizontal_dimension)	kind_phys	in	False
wind	wind_speed_at_lowest_model_layer	wind speed at lowest model level	m s-1	real	(horizontal_dimension)	kind_phys	in	False
shdmin	minimum_vegetation_area_fraction	min fractional coverage of green vegetation	frac	real	(horizontal_dimension)	kind_phys	in	False
shdmax	maximum_vegetation_area_fraction	max fractional coverage of green vegetation	frac	real	(horizontal_dimension)	kind_phys	in	False
alvwf	mean_vis_albedo_with_weak_cosz_dependency	mean vis albedo with weak cosz dependency	frac	real	(horizontal_dimension)	kind_phys	in	False
alnwf	mean_nir_albedo_with_weak_cosz_dependency	mean nir albedo with weak cosz dependency	frac	real	(horizontal_dimension)	kind_phys	in	False
snoalb	upper_bound_on_max_albedo_over_deep_snow	maximum snow albedo	frac	real	(horizontal_dimension)	kind_phys	in	False
sfalb	surface_diffused_shortwave_albedo	mean surface diffused sw albedo	frac	real	(horizontal_dimension)	kind_phys	inout	False
flag_iter	flag_for_iteration	flag for iteration	flag	logical	(horizontal_dimension)		in	False
flag_guess	flag_for_guess_run	flag for guess run	flag	logical	(horizontal_dimension)		in	False
isot	soil_type_dataset_choice	soil type dataset choice	index	integer	()		in	False
ivegsrc	vegetation_type_dataset_choice	land use dataset choice	index	integer	()		in	False
fice	sea_ice_concentration	ice fraction over open water	frac	real	(horizontal_dimension)	kind_phys	inout	False
smc	volume_fraction_of_soil_moisture	total soil moisture	frac	real	(horizontal_dimension, soil_vertical_dimension)	kind_phys	inout	False
stc	soil_temperature	soil temperature	K	real	(horizontal_dimension, soil_vertical_dimension)	kind_phys	inout	False
slc	volume_fraction_of_unfrozen_soil_moisture	liquid soil moisture	frac	real	(horizontal_dimension, soil_vertical_dimension)	kind_phys	inout	False
lsm_ruc	flag_for_ruc_land_surface_scheme	flag for RUC land surface model	flag	integer	()		in	False
lsm	flag_for_land_surface_scheme	flag for land surface model	flag	integer	()		in	False
land	flag_nonzero_land_surface_fraction	flag indicating presence of some land surface area fraction	flag	logical	(horizontal_dimension)		in	False
islimsk	sea_land_ice_mask	sea/land/ice mask (=0/1/2)	flag	integer	(horizontal_dimension)		in	False
rdlai	flag_for_reading_leaf_area_index_from_input	flag for reading leaf area index from initial conditions for RUC LSM	flag	logical	()		in	False
imp_physics	flag_for_microphysics_scheme	choice of microphysics scheme	flag	integer	()		in	False
imp_physics_gfdl	flag_for_gfdl_microphysics_scheme	choice of GFDL microphysics scheme	flag	integer	()		in	False
imp_physics_thompson	flag_for_thompson_microphysics_scheme	choice of Thompson microphysics scheme	flag	integer	()		in	False
smcwlt2	volume_fraction_of_condensed_water_in_soil_at_wilting_point	soil water fraction at wilting point	frac	real	(horizontal_dimension)	kind_phys	inout	False
smcref2	threshold_volume_fraction_of_condensed_water_in_soil	soil moisture threshold	frac	real	(horizontal_dimension)	kind_phys	inout	False
do_mynnsfclay	do_mynnsfclay	flag to activate MYNN surface layer	flag	logical	()		in	False
con_cp	specific_heat_of_dry_air_at_constant_pressure	specific heat !of dry air at constant pressure	J kg-1 K-1	real	()	kind_phys	in	False
con_rv	gas_constant_water_vapor	ideal gas constant for water vapor	J kg-1 K-1	real	()	kind_phys	in	False
con_rd	gas_constant_dry_air	ideal gas constant for dry air	J kg-1 K-1	real	()	kind_phys	in	False
con_g	gravitational_acceleration	gravitational acceleration	m s-2	real	()	kind_phys	in	False
con_pi	pi	ratio of a circle's circumference to its diameter	none	real	()	kind_phys	in	False
con_hvap	latent_heat_of_vaporization_of_water_at_0C	latent heat of vaporization/sublimation (hvap)	J kg-1	real	()	kind_phys	in	False
con_fvirt	ratio_of_vapor_to_dry_air_gas_constants_minus_one	rv/rd - 1 (rv = ideal gas constant for water vapor)	none	real	()	kind_phys	in	False
weasd	water_equivalent_accumulated_snow_depth_over_land	water equiv of acc snow depth over land	mm	real	(horizontal_dimension)	kind_phys	inout	False
snwdph	surface_snow_thickness_water_equivalent_over_land	water equivalent snow depth over land	mm	real	(horizontal_dimension)	kind_phys	inout	False
tskin	surface_skin_temperature_over_land_interstitial	surface skin temperature over land use as interstitial	K	real	(horizontal_dimension)	kind_phys	inout	False
tskin_wat	surface_skin_temperature_over_ocean_interstitial	surface skin temperature over ocean (temporary use as interstitial)	K	real	(horizontal_dimension)	kind_phys	inout	False
rainnc	lwe_thickness_of_explicit_rainfall_amount_from_previous_timestep	explicit rainfall from previous timestep	m	real	(horizontal_dimension)	kind_phys	in	False
rainc	lwe_thickness_of_convective_precipitation_amount_from_previous_timestep	convective_precipitation_amount from previous timestep	m	real	(horizontal_dimension)	kind_phys	in	False
ice	lwe_thickness_of_ice_amount_from_previous_timestep	ice amount from previous timestep	m	real	(horizontal_dimension)	kind_phys	in	False
snow	lwe_thickness_of_snow_amount_from_previous_timestep	snow amount from previous timestep	m	real	(horizontal_dimension)	kind_phys	in	False
graupel	lwe_thickness_of_graupel_amount_from_previous_timestep	graupel amount from previous timestep	m	real	(horizontal_dimension)	kind_phys	in	False
srflag	flag_for_precipitation_type	snow/rain flag for precipitation	flag	real	(horizontal_dimension)	kind_phys	inout	False
smois	volume_fraction_of_soil_moisture_for_land_surface_model	volumetric fraction of soil moisture for lsm	frac	real	(horizontal_dimension, soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
tslb	soil_temperature_for_land_surface_model	soil temperature for land surface model	K	real	(horizontal_dimension, soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
sh2o	volume_fraction_of_unfrozen_soil_moisture_for_land_surface_model	volume fraction of unfrozen soil moisture for lsm	frac	real	(horizontal_dimension, soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
keepfr	flag_for_frozen_soil_physics	flag for frozen soil physics (RUC)	flag	real	(horizontal_dimension, soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
smfrkeep	volume_fraction_of_frozen_soil_moisture_for_land_surface_model	volume fraction of frozen soil moisture for lsm	frac	real	(horizontal_dimension, soil_vertical_dimension_for_land_surface_model)	kind_phys	inout	False
canopy	canopy_water_amount	canopy water amount	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
trans	transpiration_flux	total plant transpiration rate	W m-2	real	(horizontal_dimension)	kind_phys	out	False
tsurf	surface_skin_temperature_after_iteration_over_land	surface skin temperature after iteration over land	K	real	(horizontal_dimension)	kind_phys	inout	False
tsnow	snow_temperature_bottom_first_layer	snow temperature at the bottom of first snow layer	K	real	(horizontal_dimension)	kind_phys	inout	False
zorl	surface_roughness_length_over_land_interstitial	surface roughness length over land (temporary use as interstitial)	cm	real	(horizontal_dimension)	kind_phys	inout	False
sfcqc	cloud_condensed_water_mixing_ratio_at_surface	moist cloud water mixing ratio at surface	kg kg-1	real	(horizontal_dimension)	kind_phys	inout	False
sfcdew	surface_condensation_mass	surface condensation mass	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
tice	sea_ice_temperature_interstitial	sea ice surface skin temperature use as interstitial	K	real	(horizontal_dimension)	kind_phys	inout	False
sfcqv	water_vapor_mixing_ratio_at_surface	water vapor mixing ratio at surface	kg kg-1	real	(horizontal_dimension)	kind_phys	inout	False
sncovr1	surface_snow_area_fraction_over_land	surface snow area fraction	frac	real	(horizontal_dimension)	kind_phys	inout	False
qsurf	surface_specific_humidity_over_land	surface air saturation specific humidity over land	kg kg-1	real	(horizontal_dimension)	kind_phys	inout	False
gflux	upward_heat_flux_in_soil_over_land	soil heat flux over land	W m-2	real	(horizontal_dimension)	kind_phys	out	False
drain	subsurface_runoff_flux	subsurface runoff flux	kg m-2 s-1	real	(horizontal_dimension)	kind_phys	out	False
evap	kinematic_surface_upward_latent_heat_flux_over_land	kinematic surface upward evaporation flux over land	kg kg-1 m s-1	real	(horizontal_dimension)	kind_phys	out	False
hflx	kinematic_surface_upward_sensible_heat_flux_over_land	kinematic surface upward sensible heat flux over land	K m s-1	real	(horizontal_dimension)	kind_phys	out	False
rhosnf	density_of_frozen_precipitation	density of frozen precipitation	kg m-3	real	(horizontal_dimension)	kind_phys	out	False
runof	surface_runoff_flux	surface runoff flux	kg m-2 s-1	real	(horizontal_dimension)	kind_phys	out	False
runoff	total_runoff	total water runoff	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
srunoff	surface_runoff	surface water runoff (from lsm)	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
chh	surface_drag_mass_flux_for_heat_and_moisture_in_air_over_land	thermal exchange coefficient over land	kg m-2 s-1	real	(horizontal_dimension)	kind_phys	inout	False
cmm	surface_drag_wind_speed_for_momentum_in_air_over_land	momentum exchange coefficient over land	m s-1	real	(horizontal_dimension)	kind_phys	inout	False
evbs	soil_upward_latent_heat_flux	soil upward latent heat flux	W m-2	real	(horizontal_dimension)	kind_phys	out	False
evcw	canopy_upward_latent_heat_flux	canopy upward latent heat flux	W m-2	real	(horizontal_dimension)	kind_phys	out	False
sbsno	snow_deposition_sublimation_upward_latent_heat_flux	latent heat flux from snow depo/subl	W m-2	real	(horizontal_dimension)	kind_phys	out	False
stm	soil_moisture_content	soil moisture content	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
wetness	normalized_soil_wetness_for_land_surface_model	normalized soil wetness	frac	real	(horizontal_dimension)	kind_phys	inout	False
acsnow	accumulated_water_equivalent_of_frozen_precip	snow water equivalent of run-total frozen precip	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
snowfallac	total_accumulated_snowfall	run-total snow accumulation on the ground	kg m-2	real	(horizontal_dimension)	kind_phys	inout	False
flag_init	flag_for_first_time_step	flag signaling first time step for time integration loop	flag	logical	()		in	False
flag_restart	flag_for_restart	flag for restart (warmstart) or coldstart	flag	logical	()		in	False
errmsg	ccpp_error_message	error message for error handling in CCPP	none	character	()	len=*	out	False
errflg	ccpp_error_flag	error flag for error handling in CCPP	flag	integer	()  

MPAS
2 June 2025
a5f54275a - do not initialize when restart or cycle
