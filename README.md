# groundfailure

Configuration
-------------

There will be a configuration file found in ~/.groundfailure/config.ini, with the format described below.

The config file format is a modified version of the "INI" format.  It is described in detail here:

http://configobj.readthedocs.org/en/latest/configobj.html#config-files

<pre>
[logistic_models]

  #default_landslide and default_liquefaction parameters below must refer to named models in this file
  default_landslide = nowicki_2014
  default_liquefaction = zhu_2014

  # this can be any string, but it must be a unique and descriptive name of a logistic regression model.
  [[nowicki_2014]]
    #Detailed description of the model, its inputs, etc.
    description = 'This is the Nowicki Model of 2014, which uses cohesion and slope max as input.'
    
    #which type of ground failure model is this? Options are landslide or liquefaction.
    gfetype = landslide

    #what is the grid to which all other grids in this model will be resampled?
    baselayer = cohesion 

    #these layer files can be any Grid2D-subclass supported format
    #These include, but may not be limited to:
    #https://github.com/usgs/MapIO/blob/master/mapio/gdal.py
    #https://github.com/usgs/MapIO/blob/master/mapio/gmt.py
    [[[layers]]]
      cohesion = /Users/mhearne/secondary/data/cohesion_10i.grd
      slope = /Users/mhearne/secondary/data/slope_max.grd

    [[[terms]]]
      #These terms must be named as b1-bN, where N is the number of coefficients
      #in a logistic regression, which takes the form:
      #1/(1 + e^-eqn)
      #where eqn is a linear equation of the form:
      #b0 + b1*t1 + b2*t2 + ... + bN*tN
      #where t1, t2, ... tN are the right hand side of the parameters below.
      b1 = PGA
      b2 = slope
      b3 = cohesion/10.0
      b4 = PGA*slope

    [[[coefficients]]]
      #These coefficients must be named as b1-bN, where N is the number of coefficients
      #in a logistic regression, which takes the form:
      #1/(1 + e^-eqn)
      #where eqn is a linear equation of the form:
      #b0 + b1*t1 + b2*t2 + ... + bN*tN
      #where t1, t2, ... tN are the right hand side of the parameters below.
      b0 = -7.15
      b1 = 0.0604
      b2 = 0.000825
      b3 = 0.0201
      b4 = 1.45e-05

  [[zhu_2014]]
  
    #Detailed description of the model, its inputs, etc.
    description = 'This is the Zhu model of 2014, where we use vs30 and CTI'

    gfetype = liquefaction
  
    baselayer = vs30

    #these layer files can be any Grid2D-subclass supported format
    #These include, but may not be limited to:
    #https://github.com/usgs/MapIO/blob/master/mapio/gdal.py
    #https://github.com/usgs/MapIO/blob/master/mapio/gmt.py
    [[[layers]]]
      vs30 = /Users/mhearne/secondary/data/global_vs30.grd
      cti = /Users/mhearne/secondary/data/globalcti.grd 

    [[[terms]]]
      b1 = 'log((PGA/100.0)*(power(MW,2.56)/power(10,2.24)))'
      b2 = 'cti/100.0'
      b3 = 'log(vs30)'

    [[[coefficients]]]
      b0 = 24.10
      b1 = 2.067
      b2 = 0.355
      b3 = -4.784

    [[[colormaps]]]
      vs30_colormap = jet_r

</pre>