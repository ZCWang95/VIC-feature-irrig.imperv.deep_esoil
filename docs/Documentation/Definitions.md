#Definitions

### annual_prec
VIC uses the regression published by Kimball et. al. to improve the estimation of the dew point temperature in dry regions. This regression requires the average annual precipitation in mm for each grid cell. If the value of `annual_prec` is negative, the model will use the daily minimum temperature without modification for the dew point temperature.

### avg_T
This parameter is the temperature of the soil at the [damping depth](#dp). This temperature is often assumed to be the same as the average annual air temperature. This temperature is used as the bottom boundary of all thermal flux calculations made for the soil column.

### b_infilt
The b_infilt parameter is the parameter used to describe the Variable Infiltration Curve. This is typically a value that is adjusted during the calibration of the VIC model. Parameter values range from $10^{-5}$ to 0.4. Higher values will produce more runoff. 0.2 is often used as a starting value.

### bubble
The bubble parameter is the bubbling pressure, h, for the soil texture type (see, e.g., Table 5.3.2 in Rawls, et al (Handbook of Hydrology)). This parameter is necessary for running the VIC model with `FULL_ENERGY==TRUE` or `FROZEN_SOIL==TRUE`. Values must be > 0.0.

If you have a VIC soil parameter file created for water-balance mode runs, in which bubbling pressure has been set to a "nodata" value such as -99, you will not be able to use this soil parameter file for `FULL_ENERGY==TRUE` or `FROZEN_SOIL==TRUE`. However, a quick way to estimate bubbling pressure from the existing soil parameters (namely the _expt_ parameter) is:

`bubble = 0.32*expt + 4.3`

This is illustrated in figure 1, generated by taking the data from table 5.3.2 in Rawls, et al (Handbook of Hydrology), computing _expt_ from _lambda_, and performing a linear regression.

[![Bubbling Pressure vs Expt Link](http://www.hydro.washington.edu/Lettenmaier/Models/VIC/Documentation/Info/BubbleVsExpt_thumb.gif)](http://www.hydro.washington.edu/Lettenmaier/Models/VIC/Documentation/Info/BubbleVsExpt.gif)

Figure courtesy F. Munoz, 2009

Reference: Rawls et al., Infiltration and Soil Water Movement, In: _Handbook of Hydrology_, D. Maidment (ed.), 1993

### bulk_density
The bulk density in layers used in the VIC model can be compiled from data available through Penn State University at [http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/bd/doc.html](http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/bd/doc.html)

This reports bulk density for each of 11 individual layers, which cover a depth of 2.5 meters. The majority of data does not extend beyond 1.5 meters, which includes the first 9 of their 11 layers. The Penn State data is available in gridded form at a resolution of 30 arc-seconds.

The gridded bulk density data is imported into Arc/Info, where it is averaged for the appropriate individual layers and aggregated to the grid cell size.

### cellnum
Cell numbers are assigned to each grid cell beginning with 1 at the upper left corner of the basin, and numbering consecutively by rows. Cell numbers will be referenced in soil files, as well as in the vegetation parameter file.

### depth
Depth is the depth of each layer in meters. Though this can be changed in the calibration process, initially it can be set to the breakdown of layer depths selected by combining the 11 individual soil layers of the Penn State University STATSGO data (for example, the Ksat data).

### dp
This is the soil thermal damping depth. It is defined as the depth in the soil column at which the soil temperature remains nearly constant annually. This is the depth to which soil thermal flux calculations will be made, and is often set to 4m. The constant temperature at this boundary is defined with the parameter [avg_T](#avg_t).

### Ds
The soil parameter `Ds` represents the fraction of the Dsmax parameter at which non-linear baseflow occurs. This is typically a parameter that is adjusted during the calibration of the VIC model. An initial value of 0.001 may be used. Typically this value is small (less than 1).

### Dsmax
The parameter Dsmax is the maximum velocity of baseflow for each grid cell. This can be estimated using the saturated hydraulic conductivity, [Ksat](#ksat), for each grid cell multiplied by the slope of the grid cell. The values for [Ksat](#ksat) can be averaged for the layers for which baseflow will be included. When working in decimal degrees, the elevation data for the basin should be projected to an equal area map projection, in order to have horizontal dimensions in the same units as the vertical dimensions so that the slopes computed in Arc/Info are meaningful values.

### expt
The exponent (`expt`) parameter is the exponent, `n`, from the Brooks-Corey relationship (see, e.g., Table 5.1.1 in Rawls, et al). Values can be estimated from the table of soil hydraulic properties indexed by soil texture [(see table here)](soiltext.md). Values should be > 3.0.

Reference: Rawls et al., Infiltration and Soil Water Movement, In: _Handbook of Hydrology_, D. Maidment (ed.), 1993

### init_moist
Initial moisture content of each layer can be set at any reasonable value. One approach is to use fractional soil moisture content (expressed as a fraction of the maximum soil moisture; max. `soil moisture = porosity * layer depth`) at the critical point, `Wcr`, which can be computed for each layer as a depth in millimeters by multiplying `Wcr` by the thickness of the layer in meters, and then multiplying by 1000.

### Ksat
The parameter `Ksat` is the saturated hydraulic conductivity for each layer used in a VIC model. `Ksat` is approximated using data on soil textures available for the basin.

Soil textures are from the STATSGO database (USDA NRCS), which has been compiled from a state-by-state format into a U.S. database, available through Penn State University at [http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html](http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html)

This reports 16 different soil texture classes for 11 layers, which cover a depth of 2.5 meters. The majority of data does not extend beyond 1.5 meters, which includes the first 9 of their 11 layers. The Penn State data is available in gridded form at a resolution of 30 arc-seconds.

The soil texture data is then imported into Arc/Info, where it is indexed to values of Ksat according to an index table soil hydraulic properties [(see sample table here)](soiltext.md). The data in this table are placed into an INFO file in Arc/Info, and the individual relates are then created to be used in an Arc/Info macro to create the Ksat grid for each layer.

### MIN_WIND_SPEED
`MIN_WIND_SPEED` defines the minimum observable wind speed, which is then used to correct the model forcing data. Anemometers typically have a minimum observable wind speed on the order of 0.5 m/s. When forcing the VIC model with hourly wind speeds, the model's surface flux estimates have been observed to "hiccup" (especially in dry climates), while this does not impact the long term energy balance, it will be very noticeable when looking at daily fluxes. Therefore, VIC will use MIN_WIND_SPEED to restrict the minimum value of wind speed used to force the model.

Default value is set to 0 m/s, which works well when the model is forced with daily or monthly winds.

### NLAYER
`NLAYER` is the number of soil moisture layers to be used by the model. Typically the number of layers is 2 or 3. The model cannot be run in energy balance mode with fewer than 3 layers (the top layer is thin, 5-15cm), but the water balance model used to have only 2 layers. The model has not been tested with more than 3 layers, so problems may occur if you run the model with more than three layers. 

### NODES
`NODES` defines the number of soil thermal nodes used by the model to explicitly solve soil thermal fluxes. At this time the explicit solution of the soil thermal fluxes occurs only when the model is run with the frozen soil algorithm activated. The model uses a node at the surface, a node at the bottom of the thin (5-15cm) top layer, and a node at the damping depth. If more nodes are defined they are distributed evenly between the bottom of the top layer and the damping depth. More nodes improves the accuracy of the soil heat flux solution, but also increases the computational time. We recommend at least 5 nodes.

### off_gmt
`off_gmt` is the number of hours difference from GMT (aka UTC). These are the number of hours to be added to GMT/UTC to obtain local time for each grid cell. Sample offsets are listed in the table below. since UTC does not use any daylight savings adjustment, the gmt offset will decrease by one hour for those areas using daylight savings. For example, Eastern Standard Time (EST) changes from GMT-5 hours to Eastern Daylight Time (EDT) at GMT-4 hours.

**Table of GMT Offsets in hours**

| Zone      | City or Area Zone Crosses     |
|--------   |-----------------------------  |
| GMT-12    | Eniwetok                      |
| GMT-11    | Samoa                         |
| GMT-10    | Hawaii                        |
| GMT-9     | Alaska                        |
| GMT-8     | PST, Pacific US               |
| GMT-7     | MST, Mountain US              |
| GMT-6     | CST, Central US               |
| GMT-5     | EST, Eastern US               |
| GMT-4     | Atlantic, Canada              |
| GMT-3     | Brazilia, Buenos Aries        |
| GMT-2     | Mid-Atlantic                  |
| GMT-1     | Cape Verdes                   |
| GMT       | Greenwich Mean Time, Dublin   |
| GMT+1     | Berlin, Rome                  |
| GMT+2     | Israel, Cairo                 |
| GMT+3     | Moscow, Kuwait                |
| GMT+4     | Abu Dhabi, Muscat             |
| GMT+5     | Islamabad, Karachi            |
| GMT+6     | Almaty, Dhaka                 |
| GMT+7     | Bangkok, Jakarta              |
| GMT+8     | Hong Kong, Beijing            |
| GMT+9     | Tokyo, Osaka                  |
| GMT+10    | Sydney, Melbourne, Guam       |
| GMT+11    | Magadan, Soloman Is.          |
| GMT+12    | Fiji, Wellington, Auckland    |

\* Table copied from Texas A&M University Institute for Scientific Computation Web Page.

### phi_s
The parameter `phi_s` is the unitless soil moisture diffusion coefficient. This parameter is designed for the future inclusion of soil moisture diffusion in the VIC model moisture trasport equations. Currently this feature has not been implemented so a value of -999 can be used as a placeholder.

### resid_moist
Residual moisture content is the amount of soil moisture that cannot be removed from the soil by drainage or evapotranspiration. When residual soil moisture is defined as 0 mm/mm the soil hydraulic conductivity relationship collapses to Campbell (1974), otherwise it follows Brooks and Corey (1964).

Values are provided to the model as soil moisture contents (volume of residual soil moisture content / total volume of soil) [mm/mm]. Note that these units are different from the input units of critical (Wcr_FRACT) and wilting point (Wpwp_FRACT), which are fractions of porosity [mm/mm].

Brooks, R. H. and A. T. Corey, Hydraulic Properties of Porous Media, Hydrology Paper 3, Colorado State University, Fort Collins, Colo, 1964.

Campbell, G. S., A Simple Method for Determining Unsaturated Conductivity from Moisture Retention Data, Soil Sci., vol. 117, pp. 311-314, 1974.

### root_zone
The parameter `root_zone` is the number of root zones used in the vegetation parameter file to define the root distribution. This allows the rooting distribution to be defined independently of both vegetation type and soil layer depth. When soil moisture layer depths are changed during calibration, the model uses linear distribution based on the defined root zones to redistribute root fractions.

No default value is set, this parameter must be defined in the global parameter file.

### root_depth
The parameter `root_depth` describes the thickness of the root zone (the sum of depths is the total depth of root penetration)

### root_fract 
`root_fract` is the fraction of root in the current root zone. 

### rough
Surface roughness of bare soil, expressed in meters, can be set to a value 0.001, and adjusted according to local data.

### snow_rough
The surface roughness of the snowpack, expressed in meters, can be set to an initial value of 0.0005, and can then be adjusted according to local data.

### Wcr_FRACT
The parameter `Wcr_FRACT` (Wcr) is the fractional soil moisture (expressed as a fraction of the maximum soil moisture; max. soil moisture = porosity * layer depth) at the critical point, which is the water content below which hydraulic conductivity begins to fall below saturated values, as does transpiration. This is set at 70% of the field capacity, in accordance with the different soil textures. Field Capacity is defined as the water content at a tension of -33kPa.

Soil textures are from the STATSGO database (USDA NRCS), which has been compiled from a state-by-state format into a U.S. database, available through Penn State University at [http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html](http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html)

This reports 16 different soil texture classes for 11 layers, which cover a depth of 2.5 meters. The majority of data does not extend beyond 1.5 meters, which includes the first 9 of their 11 layers. The Penn State data is available in gridded form at a resolution of 30 arc-seconds.

The soil texture data is then imported into Arc/Info, where it is indexed to values of `Wcr` according to the table [SOILTEXT.](soiltext.md) The data in this table are placed into an INFO file in Arc/Info, and the individual relates are then created to be used in an Arc/Info macro to create the `Wcr` grid.

### wind_h

The measurement wind height is the height above the ground at which wind observations were recorded. Typically wind observations are taken at a height of 2 m in clearings. However, the VIC model assumes wind observations are made above the vegetation cover in each grid cell and that wind speeds through and below the canopy can be found using exponential and logarithmic wind profiles. For short vegetation types (grasses, shrubs, etc.), the wind measurement height should be ~2 m above the top of the vegetation. For tall vegetation types (trees), especially those defined with an overstory, the wind measurement height should be set much higher than the vegetation. This is physically justified because a forest canopy impacts atmospheric stability to a much higher elevation, than shorter vegetation. If the model is required to run with an overstory defined, and the wind height set to just above the vegetation (for example the tree height + 2m) large negative evaporations and sublimations may be found during the winter months, as the snow interception algorithm is forced to use unrealistically strong wind speeds.

**When defining a vegetation type with overstory, it is strongly recommended that wind_h be defined at least 10 m higher than the height of the vegetation.**

### Wpwp_FRACT

The parameter `Wpwp_FRACT` (`wp`) is the fractional soil moisture (expressed as a fraction of the maximum soil moisture; max. soil moisture = porosity * layer depth) at the wilting point. Wilting Point is set at the water content at a tension of 1500 kPa, and is approximated for the different soil textures.

Soil textures are from the STATSGO database (USDA NRCS), which has been compiled from a state-by-state format into a U.S. database, available through Penn State University at [http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html](http://dbwww.essc.psu.edu/geotree/dbtop/amer_n/us_48/data/soilprop/statsgo_geo/soiltext/doc.html)

This reports 16 different soil texture classes for 11 layers, which cover a depth of 2.5 meters. The majority of data does not extend beyond 1.5 meters, which includes the first 9 of their 11 layers. The Penn State data is available in gridded form at a resolution of 30 arc-seconds.

### Ws
The parameter Ws is the fraction of maximum soil moisture where non-linear baseflow occurs. As with the [`Ds`](#ds) parameter, this is generally adjusted during the calibration phase of applying the VIC model. Values for Ws are typically greater than 0.5. An initial value of 0.9 can be used.

### quartz
The parameter `quartz` describes the quartz content of the soil. 

### soil_density
The parameter `soil_density` describes the soil particle density and a value of 2685 kg/m3 is typically used. 

### fs_active
The parameter `fs_active` describes whether or not the frozen soil algorithm is activated for a grid cell. A value of 0 indicates that the frozen soil routine is not run for the grid cell even if soil temperatures fall below 0 C. A value of 1 indicates that the frozen soil routine is run. 

### veg_class 
The parameter `veg_class` refers to the vegetation class identification number. There have typically been 12 vegetation classes in VIC but newer versions of the model allow for more than 12 vegetation classes. 

### veg_descr
The parameter `veg_descr` describes the vegetation class.

### Nveg
The parameter `Nveg` describes the number of active vegetation classes (or types) in a grid cell. 

### Cv
The parameter `Cv` describes the fraction of the grid cell covered by each active vegetation class. 

### LAI 
`LAI` is the monthly leaf area index; 12 monthly values are supplied.

In the classic driver, `LAI` is supplied in the veg library file (one set of monthly values for each land cover class) and optionally can be supplied in the veg parameter file (one set of monthly values for each land cover class in each grid cell). If `LAI` values are included in the veg parameter file, `VEGPARAM_LAI` must be set to `TRUE` in the global parameter file. To tell VIC which values of `LAI` to use, `LAI_SRC` in the global parameter file can be set to `FROM_VEGLIB` (from veg library file), `FROM_VEGPARAM` (from veg parameter file - this is the default), or `FROM_VEGHIST` (from the veg history file).

In the image driver, `LAI` is supplied in the parameter file (one set of monthly values for each land cover class in each grid cell).

### fcanopy 
`fcanopy` is the canopy fraction; 12 monthly values are supplied.

In the classic driver, `fcanopy` is optionally supplied in either the veg library file (one set of monthly values for each land cover class) or the veg parameter file (one set of monthly values for each land cover class in each grid cell). If `fcanopy` values are included in the veg library file, `VEGLIB_FCAN` must be set to `TRUE` in the global parameter file. If `fcanopy` values are included in the veg parameter file, `VEGPARAM_FCAN` must be set to `TRUE` in the global parameter file. To tell VIC which values of `fcanopy` to use, `FCAN_SRC` in the global parameter file can be set to `FROM_DEFAULT` (ignore all supplied values and use a constant value of 1.0 everywhere - this is the default), `FROM_VEGLIB` (from veg library file), `FROM_VEGPARAM` (from veg parameter file), or `FROM_VEGHIST` (from the veg history file).

In the image driver, `fcanopy` is supplied in the parameter file (one set of monthly values for each land cover class in each grid cell). To tell VIC to use these values, `FCAN_SRC` must be set to `FROM_VEGPARAM` in the global parameter file; else set it to `FROM_DEFAULT` in which case VIC will use a constant value of 1.0.

### albedo 
`albedo` refers to the shortwave albedo and is specific to each vegetation type; 12 monthly values are supplied.

In the classic driver, `albedo` is supplied in the veg library file (one set of monthly values for each land cover class) and optionally can be supplied in the veg parameter file (one set of monthly values for each land cover class in each grid cell). If `albedo` values are included in the veg parameter file, `VEGPARAM_ALB` must be set to `TRUE` in the global parameter file. To tell VIC which values of `albedo` to use, `ALB_SRC` in the global parameter file can be set to `FROM_VEGLIB` (from veg library file), `FROM_VEGPARAM` (from veg parameter file - this is the default), or `FROM_VEGHIST` (from the veg history file).

In the image driver, `albedo` is supplied in the parameter file (one set of monthly values for each land cover class in each grid cell).

### fimperv and feffimperv
`fimperv` is the area fraction of each land cover class that is impervious, and `feffimperv` is the fraction of the impervious area that is effective (connected to the channel network).

In the classic driver, `fimperv` and `feffimperv` optionally can be supplied in the veg parameter file (one value of each parameter for each land cover class) or. If `fimperv` and `feffimperv` values are included in the veg parameter file, `VEGPARAM_FIMP` must be set to `TRUE` in the global parameter file. To tell VIC which values of `fimperv` and `feffimperv` to use, `FIMP_SRC` in the global parameter file can be set to `FROM_VEGPARAM` (from veg parameter file - this is the default), or `FROM_DEFAULT` in which case VIC will use a constant value of 0.0 for both.

In the image driver, `fimperv` and `feffimperv` are supplied in the parameter file (one value of each parameter for each land cover class in each grid cell). To tell VIC to use these values, `FIMP_SRC` must be set to `FROM_VEGPARAM` in the global parameter file; else set it to `FROM_DEFAULT` in which case VIC will use a contant value of 0.0 for each.

### overstory
`Overstory` is a flag to indicate if the current vegetation type has an overstory or not. A value of 1 indicates an overstory, a value of 0 indicates no overstory. 

### rarc
The parameter `rarc` represents the architectural resistance of each vegetation type. It is typically set to ~2 s/m. 

### rmin
The parameter `rmin` refers to the minimum stomatal resistance of each vegetation type. It is typically set to ~100 s/m. 

### RGL
`RGL` is the minimum incoming shortwave radiation at which there will be transpiration. It typically has a value of 30 W/m2 for trees and for crops about 100 W/m2. 

### rad_atten
The parameter `rad_atten` is the radiation attenuation factor. It is normally set to 0.5 but may need to be adjusted for high latitudes. 

### wind_atten
The paraemeter `wind_atten` is the wind speed attenuation through the overstory. The default value is typically 0.5. 

### trunk_ratio 
The parameter `trunk_ratio` is the ratio of total tree height that refers to the trunk (does not include branches). The default value is typically 0.2.

### veg_rough 
The parameter `veg_rough` refers to the roughness length of the vegetation type and is typically 0.123 * vegetation height.

### displacement 
The parameter `displacement` is the vegetation displacement height and is typically 0.67 * vegetation height.    
