# 农作物需水估算

人类活动与地球系统之间的联系应该被量化反映到模拟地球系统过程的模型中以了解人类活动的影响，这也是Earth system modeling中的重要挑战。人类活动关键要素之一就是 水资源管理，其中，一个重要的环节就是water demand的分析。这里就简单记录下review的water demand，尤其是针对水库的需水的建模方法。

## Monitoring US agriculture: The US department of agriculture, national agricultural statistics service, cropland data layer program（2011）

USDA的农业观测任务NASS制作了CDL图。用的是Landsat、MODIS等遥感图，结合地面观测，利用机器学习方法做的分类。

## Vegetation index-based crop coefficients to estimate evapotranspiration by remote sensing in agricultural and natural ecosystems（2011）

这篇文章综述了用遥感数据获得的VI来估算crop coefficients的方法。常见的指标有NDVI、Soil Adjusted VI和Enhanced VI 以及LAI等。方法基本上回归类型方法，详见原文。

## Estimating crop coefficients using remote sensing-based vegetation index（2013）


## Merging remote sensing data and national agricultural statistics to model change in irrigated agriculture（2014）

这篇文章是推求灌溉图的，这里主要看看关于灌溉的一些简单知识。

文中提到通常对灌溉地区会假设灌溉作物相比非灌溉的作物会有更高的NDVI；不同作物不同地区的生长季节的最高NDVI值不同；越是自然缺水条件，灌溉和非灌溉的NDVI值差别就越大。

## Application of a remote sensing method for estimating monthly blue water evapotranspiration in irrigated agriculture（2014）

这篇介绍的用微波遥感土壤含水量推求灌溉用水的数据是比较早的一篇用改方法计算的文章。

$$ET_b = \triangle ET - bias$$

其中，bias是rain-fed区域里遥感观测和模型计算的差值，这部分反映了两者之间存在的计算偏差。$\triangle ET$是灌溉区域的差值，这样综合反映出实际灌溉用水的情况。

时间尺度月尺度，空间分辨率国内模型用的是0.1度的。用到GLDAS是0.25度。

## Global rain-fed, irrigated, and paddy croplands: A new high resolution map derived from remote sensing, crop inventories and climate data（2015）

注意在remote sensing和irrigation结合的领域里，作物map，irrigation water requirement/use quantification都是不一样的子领域。这篇文章是map irrigated/rain-fed croplands的，属于第一类，就不细看了。

## Calculating crop water requirement satisfaction in the West Africa sahel with remotely sensed soil moisture（2015）

这篇文章是用微波遥感数据的土壤含水量推测crop water requirement的，和后面计算irrigation water use的不太一样，这里暂时没细看。

## On inclusion of water resource management in Earth system models -Part 1: Problem definition and representation of water demand（2015）

这是一篇综述性质的文章，总结了在LSM（land surface model）和GHM（global hydrological model）等大尺度模型中的各类型water demand算法的文献，讨论了他们的优缺点，强调了不确定性以及它们的局限。下面就稍微阐述下。

首先简单明确一些概念：

- GHM更多地关注于water cycle，而LSM也关注其他陆面过程，比如energy and carbon cycles。不论是LSM还是GHM，在模拟预测水文变量方面都有待进一步改善。
- 工业时代之后，人类进入新的地质期：“Anthropocene”，即人类世。陆面系统的自然过程，比如水循环，已深刻被人类活动影响，因此地球系统中也应该包括人类活动，对水循环,即需要 human-water interactions，这也是目前的模型中缺少的
- 人类活动对水循环的影响也有不同方面，比如land use变化，比如水资源管理。水资源方面，随着人口增加，工业发展等，人类用水量过去一个世纪有着显著的增加，这样大量需水对水循环会造成显著影响。
- 比如dam operations会改变自然径流的timing，volume，peak以及age。

然后，这篇文章的主要目的是：考虑围绕人类活动对陆面水循环影响的科学以及数据上的挑战，目前实践的情况以及未来研究的方向，主要关注在水资源管理方面，关注水量，不同人类需求的水资源的存储，抽取和再分配。目前Large scale model中Water resources management 模块还存在一些基本问题。首先，是 the conservation of water，在human-water耦合系统中capture water，但这建模很复杂；其次影响因素多，如何考虑是问题；第三，企业晒reginal 和 global的用水和水资源系统调度数据，比如gao huilin等人的文章中说：“there are no direct observations of reservoir storage”；第四， local水资源调度模型和large-scale的应用与研究是有gap的，局部的研究有更充分的数据，而大尺度的分析是较难用local上的方法的。为了评估水资源管理的影响，需要先用算法来描述需水并将其包含到大尺度模型中。这篇文章具体来说，就是调研 the representation of water demand。

论文将需水分为两类，irrigatve和non-irrigative。讨论了不同的计算算法。算法通常的做法是降尺度（top-down 方法）或直接在grid尺度建模（bottom-up方法）。根据应用的类型，这些算法可以被包含在不同的大尺度模型中。另外，需水的计算融入到模型中有online和offline两类不同模式。offline是不需要需水和自然过程之间的互相影响的，不考虑互馈；online是考虑的。GHM中一般是offline的，因为不考虑energy balance等过程。以下是论文的各部分主要内容。

第一，human demand的类型即其对water cycle的影响。灌溉用水还是占比较大，减少了streamflow volume和地下水位，扰动了自然条件；另外影响土壤含水量，会影响陆面过程。非灌溉用水相对少，但有显著的空间变化性（人口，经济水平等），不过非灌溉用水更多地不是consumptive water use，纯消耗的只占一小部分，更多地是return flow。，对径流的扰动更多地是timing。

第二，irrigative demand in large-scale models 的 available representation。灌溉比非灌溉的demand探索得更深。为简化描述，作者对灌溉需水的表示进行了分类：尺度(区域vs全球)；和/或模拟模式(离线与在线)。原文表1(区域)和表2(全球)总结离线模拟的典型例子。表3提供一些online的例子。离线更多，在线的模拟范围都先对较小。这里把表内容copy过来了。

灌溉的引入一定是增加了grid的异构性的，sub-grid级的异构（additional tile）。估计 irrigation demand 需要 irrigation algorithms。irrigation demand的定义是 water required for ideal crop growth in addition to the available water from precipitation。要想模拟基于网格的irrigation demand，首先需要明确 crop type，the extent of irrigated regions 以及 growing seasons。

- crop type 可以从 regional 和 global 数据集（比如 USDA 数据）或者遥感数据（比如MODIS）中提取。
- 识别 growing seasons 有两种常用的方法，较简单的方法是在满足一定温度和降雨的标准时作物就能生长，这在无能量平衡计算的模型中较常用；另一类相对负责的模型可以根据 biophysical conditions of crop growth and/or soil water, canopy and energy balance conditions 来估计作物生长，这在LSM中更常见一些。两种方式都有较大不确定性。
- 生长season确定后，可以计算 irrigation demand。这里就有两类算法了，top-down 和 bottom-up 。有一系列具体的算法。如果灌溉需求完全满足，则实际蒸散比等于标准条件下作物蒸散比。在离线模式下，灌溉率会扰动irrigated tiles的土壤含水量，蒸散发，深层下渗以及runoff。在线应用中，垂直vapor以及heat fluxes也需要考虑，total fluxes for each grid 能用 the sum of the flux contributions from irrigated and non-irrigated portions of the grid 来算，也被引入气候模型作为耦合边界条件。

下面稍微展开下top-down和bottom-up两类计算方法。

top-down类的方法里，irrigation demand不是直接计算的，而是通过将相对粗糙尺度（比如国家尺度，行政区划尺度）的信息降尺度来估计的。这类信息都是基于普查的类似年鉴之类的文件或socio-economic 模型输出的。top-down 方法受water use 全球数据可用性的影响，比如FAO的Information System on Water and Agriculture (AQUASTAT; http://www.fao.org/nr/water/aquastat/main/index.stm)，AQUASTAT能提供国家或地区级的年用水数据，也已扩展到包括socio-economic模型输出了，比如the Global Change Assessment Model (GCAM)。GCAM基于socio-economic 变量估计了农业production，再基于此，using the water required for each crop per unit of land 间接推算出 irrigation water use。降尺度主要通过land-use，technological and/or socio-economic proxies 来执行。这类方法不确定性很大，所以irrigation demand计算更多还是使用bottom-up的方式。

bottom-up类方法直接在grid内通过模拟irrigated tiles 的作物生长来执行，尽管sub-grid尺度异构性很高，这类方法也是被广泛的使用。这类方法都是围绕理想作物需水估计的，即没有water deficit。requirement是基于potential evapotranspiration （ETo，衡量的是大气的moisture deficit）的。有多种方法来估计ETo，因此这一估计也可能各种方法各不相同。LSMs中通常包括能力平衡计算，能求解日变化，因此能直接计算ETo和实际evaporation。另一类常用的方法是FAO的计算irrigation water requirements，这类方法常用于GHMs中，其 evapotranspiration is calculated for a reference crop，然后再利用 a set of empirical coefficients，以及 a function of crop type and development stage 来校正。计算ETo的方法也很多，比如FAO Penman-Monteith。以下再稍展开记录下bottom-up类的方法，由易到难。

- 最简单的表示是：每一时刻的灌溉需水量是使根部土壤水分达到饱和所需的水量，不过它描述了一个极端的需求条件，并明显高估了实际的灌溉用水需求
- 一个更实际的，不过仍然比较简单的方法是认为土壤在生长季节对水分的需求被认为是田间容量，那么灌溉所需的水是使土壤水分达到田间容量所需的水，不过由于蒸发量往往在土壤达到田间容量之前就已达到potential level，这仍然会高估实际需水。蒸发量达到潜在蒸发量ETo的阈值取决于作物，但在大尺度模型中阈值通常被认为是一个定值，也有实现root growth的算法来避免root zone是定值从而获取变化阈值的方法。
- 更现实的灌溉需水量定义是基于作物的潜在蒸散量crop-dependent potential evapotranspiration和作物可用水available crop water之间的差异，这已经被广泛使用了。作物生长情况用潜在蒸散量的恒定月乘数来描述，而有效降雨量则用作作物可用水的代理表示。这里对此方法有两个限制：一是，FAO对与灌溉需水的定义考虑了作物蒸腾和土壤蒸发，这可能会高估需水；第二，假设作物生长仅仅是可用水的函数。
- 基于potential transpiration 而不是 potential evapotranspiration 来定义irrigation demand。潜在蒸腾作用是指作物在没有water stressed的情况下所产生的蒸腾作用。如果其所在host模型有相关的计算，潜在蒸腾作用能考虑CO2施肥效应，可以代表植物对气候条件和/或作物生长周期的适应。显然这种主要用在LSMs中。

Table 1. Representative examples including **regional irrigation** in large-scale models (**offline mode**)

|Reference |Irrigation data |Irrigation demand |Region |Host model |Forcing |Temporal resolution| Spatial resolution|
|-|-|-|-|-|-|-|-|
|Haddeland et al. (2006)|Döll and Siebert (2002)| Difference between current soil  moisture content and minimum of FAO Penman–Monteith crop-specific evapotranspiration and soil moisture content at field capacity.|Colorado (USA) and Mekong (east Asia)|VIC (Liang et al., 1994) |Adam and Lettenmaier (2003); Maurer et al. (2002)|3 h |0.5° * 0.5°|
|Haddeland et al. (2007)|Siebert et al. (2005)|Haddeland et al. (2006)| North  America and Asia|VIC (Liang et al., 1994) |Maurer et al. (2002)|24 h| 0.5° * 0.5°|
|Gueneau et al. (2012)|GAEZ (IIASA/FAO, 2012);FRIS (USDA, 2008)|Difference between actual and potential evapotranspiration based on Farmer et al. (2011). Crop growth and irrigation losses included.|USA| CLM3.5 (Oleson et al., 2004, 2008)| NCC (Ngo-Duc et al., 2005b)| 6 h |2.5° *2.5°|
|Leng et al. (2013)|MODIS (Ozdogan and Gutman, 2008);NASS (USDA, 2002)|Difference between current and ideal soil moisture content based on CLM4CNcrop crop growth model of CLM4 (Levis and Sacks, 2011; Levis et al., 2012).|Conterminous USA |CLM4 (Lawrence et al., 2011)|NLDAS (Cosgrove et al., 2003)|1 h |0.125° *0.125°|
|Nakayama and Shankman (2013)|Liu et al. (2010)| Difference between current soil moisture content and soil moisture at the field capacity.|Changjing, Yellow River basins (China)| NICE (Nakayama, 2011)|ECMWF (http://www.ecmwf.int/en/forecasts/datasets)| 6 h |10 km*10 km|
|Voisin et al. (2013)|Crop area projections in Chaturvedi et al. (2013a, b).|Downscaling GCAM model estimations (Wise and Calvin, 2011;Wise et al., 2009a) using methods of Hejazi et al. (2013a), Siebert and Döll (2008) and Hanasaki et al. (2013a, b).| US Mid-west |SCLM-MOSART(Lawrence et al.,2011; Li et al.,2013); Tesfa et al. (2014)| CASCaDE(http://cascade.wr.usgs.gov)| 1 h |0.125° *0.125°|

Table 2. Representative examples including **global irrigation** in large-scale models (**offline mode**).

|Reference |Irrigation data |Irrigation demand |Host model |Forcing |Temporal resolution| Spatial resolution|
|-|-|-|-|-|-|-|
|Döll and Siebert (2002) |Döll and Siebert (2000)|Difference between Smith (1992) effective rainfall and Priestley and Taylor (1972) crop-specific potential evapotranspiration and Allen et al. (1998) multipliers.|WaterGAP(Alcamo et al.,2003)| CRU TS 1.0 (New et al., 1999, 2000)| 24 h| 0.5° *0.5°|
|de Rosnay et al. (2003)*| Döll and Siebert (2002)| Difference between effective rainfall and FAO potential evapotranspiration (Allen et al., 1998) without considering irrigation efficiency.| ORCHIDEE(Ducoudré et al., 1993)| ISLSCP-I (Sellers et al., 1996b)|24 h| 1° *1°|
|Hanasaki et al. (2006) | Döll and Siebert(2000)| Similar to Döll and Siebert (2002). Reference evaporation is based on FAO Penman–Monteith.|TRIP (Oki and Sud, 1998)|ISLSCP-I (Sellers et al., 1996b)|24 h |0.5° *0.5°|
|Wisser et al. (2008)| Siebert et al. (2005, 2007);GIAM (Thenkabail et al., 2009) |Similar to Haddeland et al. (2006) using Allen et al. (1998) procedure.| WBM(Vörösmarty et al., 1998)| CRU TS 2.1 (Mitchell and Jones,2005);NCEP (Kalnay et al.,1996)|24 h |0.5° *0.5°|
|Rost et al. (2008, 2009)|Siebert et al. (2007)| Difference between available plant moisture and an updated Priestley and Taylor (1972) potential evaporation based on potential canopy conductance of carbon and water (Sitch et al., 2003).|LPJmL (Bondeau et al., 2007)|CRU TS 2.1(Mitchell and Jones,2005)| 24 h| 0.5° *0.5°|
|Hanasaki et al. (2008a, b) |Döll and Siebert (2000)|Difference between current and 75% of field capacity. Irrigation applied 30 days prior to planting. Detailed crop growth representation based on SWIM (Krysanova et al.,1998). | H08 (Hanasaki  et al., 2008a, b)|NCEP-DOE (Kanamitsu et al.,2002); GSWP-2(Zhao and Dirmeyer,2003)|24 h |1° *1°|
|Siebert and Döll (2010)|MIRCA2000 (Portmann et al.,2010)|Difference between actual and crop-dependent reference evapotranspiration computed according to Priestley and Taylor  (1972). Crop coefficients obtained from Allen et al. (1998).| GCWM (Siebert and Döll, 2008)|CRU TS 2.1 (Mitchell and Jones, 2005)| 24 h |0.08° *0.08°|
|Wada et al.(2011, 2012)| MIRCA2000 (Portmann et al.,2010)|Difference between actual and potential transpiration according to van Beek et al. (2011), using Priestley and Taylor (1972) for calculating crop-specific and transpiration (Allen et al., 1998). |PCR- GLOBWB (van Beek et al., 2011)|CRU TS 1.0 (New et  al., 1999, 2000)|24 h |0.5° *0.5°|
|Pokhrel et al. (2012)|Siebert et al. (2007) |Procedure of Hanasaki et al. (2008a, b). Crop calendar is based on potential evapotranspiration (Allen et al., 1998). | MASTIRO (Takata et al.,2003)|Kim et al. (2009); GPCC (Rudolf et al., 2005)|6 h| 1° *1°|
|Wada et al.(2014)| MIRCA2000 (Portmann et al.,2010)|Constant 50mm surface-water depth for paddy irrigation until 20 days before harvesting. For non-paddy areas, the difference between current and ideal plant available moisture at field capacity with dynamic root zone.|PCR-GLOBWB (van Beek et al., 2011)|ERA-Interim (Dee et al., 2011); MERRA (http://gmao.gsfc.nasa.gov/merra/)|24 h |0.5° *0.5°|

Table 3. Representative examples including irrigation in coupled land-surface models (**online mode**).

|Reference| Irrigation data |Irrigation demand| Region |Host LSM |Climate model|Temporal resolution| Spatial resolution|
|-|-|-|-|-|-|-|-|
|Adegoke et al. (2003)|LandSat(http://landsat.gsfc.nasa.gov/)| Target soil moisture deficit (difference between actual and saturated soil moisture).|High Plains (USA)| LEAF-2 (Walko et al., 2000)| RAMS (Pielke et al., 1992)|30 s nested in 1 min|10 km*10 km nested in 40 km*40 km|
|Sacks et al.(2009)| FAO-AQUASTAT(http://www.fao.org/nr/water/aquastat/main/index.stm)| AQUASTAT irrigated water uses applied at constant rate when LAI exceeds 80% of the maximum annual value. |Global| CLM3.5 (Oleson et al., 2008)|CAM (Collins et al., 2004, 2006)| 20 min| 2.8° *2.8°|
|Sorooshian etal. (2011) | CIMIS-MODIS(http://wwwcimis.water.ca.gov/) | Target soil moisture deficit (irrigation starts when the soil moisture drops below a maximum depletion threshold beyond which the plant in stressed (a percentage of field capacity, depending on the crop) and continues to field capacity)| California Central Valley (USA)|Noah (Ek et al., 2003) | NCAR-MM5(Chen and  Dudhia, 2001a, b)|30 min 1 h |4 km*4 km  12 km*12 km  36 km*36 km|
|Harding and Snyder (2012a, b)|MODIS (Friedl et al., 2002; Ozdogan and Gutman, 2008); NASS (USDA, 2002)| Target soil moisture deficit (difference between actual and saturated soil moisture to depth of 2 m). |Great Plains (USA)| Noah (Ek et al., 2003)|WRF (Skamarock et al., 2005)| 30 and 25 s |10 km*10 km|
|Guimberteau et al. (2012)|Döll and Siebert (2002) |Difference between potential transpiration and the net water amount kept by the soil (i.e., the difference between precipitation reaching the soil and total runoff).| Global| ORCHIDEE (Ducoudré et al., 1993)|LMDZ4(Hourdin et al., 2006)| 30 min |2.5° *1.25°|
|Qian et al. (2013)|MODIS (Ozdogan and Gutman, 2008; Ozdogan et al., 2010) |Similar to Sorooshian et al. (2011). Based on Ozdogan et al. (2010), moisture threshold is fixed at 50% of filed capacity. Roots grow based on| Southern Great Plains (USA)| Noah (Ek et al., 2003)| WRF (Skamarock et al., 2005)|3 h |12 km*12 km|

第三，non-irrigative demand 的 available representation。这部分暂略，对于非灌溉需水，更多的采用的是top-down的方法，将国家或行政区划数据转换到流域或网格尺度。

接下来，第四，介绍了上面的表达在large-scale models中的应用。主要还是offline的模式。online最近也有研究。

- Online representation。在coupled land-surface schemes中包含灌溉能提高气候模拟。灌溉引起的降水已经被研究了很长一段时间，灌溉已经被证明对局部和区域的降水模式有显著的影响。
- Offline representation。offline表达需水量更为普遍，各种GHMs和LSMs结合不同的需求算法已被用于模拟当前和未来条件下的需水量动态。结合水需求计算通常可以得到更真实的河流流量模拟，但是目前的模拟显示，在国家、大陆和全球尺度上，对水需求和使用的估计存在很大差异。这在后面展望部分再补充。

最后是讨论以及对未来的展望。

目前在对水需求进行建模和了解其对陆地水循环和人类活动的在线和离线影响方面仍存在重大gap。造成这些gap的部分原因是地球系统过程建模的复杂性，而耦合模拟模式的复杂性更为突出。除了各种计算障碍外，在线模拟的一个主要挑战是陆地和大气模式耦合的不确定性，当给定一个独特的陆面边界条件时，不同的气候模式得到的模拟结果可能是不同的。耦合灌溉地表气候模拟的另一个主要挑战是选择合适的时间和空间分辨率，在此分辨率下，陆地和大气之间的相关物理过程和反馈应该被表示和描述。理想情况下，最佳的建模分辨率应该基于物理现实主义来确定;尽管如此，耦合仿真中分辨率的选择主要受到计算资源、数据可用性和lsm支持的复杂性的限制。如果这些都不是限制因素，那么更精细的时间和空间分辨率可以改善灌溉的在线表示。

通过增加空间分辨率，需要包括更多的过程，以确保在模型内水量平衡，这可能会使与水可用性有关的问题进一步复杂化。只要蒸发计算与作物需水量的估计一致，并且每种作物都由一个独特的水分库提供，那么在离线运行中，精细建模分辨率的影响似乎一般不那么显著。

巨大的不确定性也与当前和未来条件下的离线模式人类需水量模拟有关。例这些不确定性主要与 (i)可用的数据支持，(ii)需求计算算法和(iii)host模型有关。这些来源是广泛且有联系的，并不能很容易地解决和独立量化。在此，作者简要地讨论了这些来源，并提出了未来发展的几个方向：

首先，执行大规模模型所需的输入和forcing数据存在相当大的不确定性。这导致了重大的不一致，特别是在网格尺度上，来自不同来源的信息往往彼此不匹配(例如，土壤性质不适合土地使用)。不确定性也与执行需求计算算法所需的数据有关。比如在许多地区，灌溉区的位置也不确定，灌溉区内的作物的亚网格变化一般无法得到。数据不确定性的另一个来源是关于灌溉技术的信息普遍稀少。非灌溉数据也有类似情况。由于可以获得不同来源的全球和区域数据，出现了各种质量不同的数据集，这些数据集可能支持需求计算算法。在研究的这一阶段，还没有系统地比较各种数据集的不确定性及其对需求模拟的相关影响。这是未来探索的主要需要。

其次，需求计算算法的不确定性:这包括灌溉和非灌溉需求。现有算法的局限性主要包括在时间和空间上描述作物水分需求的不确定性和限制灌溉对水的可用性。如果灌溉受到网格尺度上可用水的限制，那么host模型描述地表水和地下水资源分配的能力会阻碍模拟的质量；目前的自底向上算法由于缺少土壤和作物多样性，不能在亚网格尺度上适当考虑植物特定的水分需求。这可能导致对灌溉需求的错误估计。在目前的研究阶段，计算灌溉需求的不同方法尚未完全比较，以确定有关地区、气候和作物类型的适当算法。这可以被认为是进一步研究的重要需要。未来发展的另一个途径是使用数据同化和模型校准来改进需求模拟。对非灌溉需求，开发鲁棒的降尺度和投影算法来估计它是未来发展的重要需求。

第三，host模型的不确定性:模型可以为需求模拟增加大量的不确定性，特别是灌溉。如第3节所述，灌溉需求的计算涉及求解每个模拟时间步的土壤水分平衡，这取决于host模型中有关的自然过程，如实际蒸散和土壤水分，如何参数化。考虑灌溉与大气的反馈效应可以显著地改变潜在的蒸发量，因此，基于GHMs的离线灌溉需求模拟可能存在偏差，因为它们固有地忽略了气候反馈。从这个角度来看，在线LSMs在模拟二氧化碳浓度增加和未来水资源压力方面优于GHMs，因为它们通常包含许多调查气候、碳、植被和水循环之间相互作用所需的计算组件。此外，尽管有人认为host模型的不确定性比气候forcing(2013)更显著，但灌溉算法和大规模宿主模型的不确定性尚未完全分解和区分。这就需要多种需求算法与多种host模型的混合匹配，进行系统的相互比较和敏感性分析。

## A land data assimilation system for sub-Saharan Africa food and water security applications（2017）

留意下FLDAS的数据。

## Effects of spatially distributed sectoral water management on the redistribution of water resources in an integrated water model （2017）

先了解下摘要，记住一些基本名词。sectoral water withdrawals，consumptive demands 和 给surface以及地下水资源的分配 的表示对提升水循环整体建模是重要的。这篇文章加强了水资源管理在**区域地球系统earth system（ES）**的表达，基本内容是用一个针对地表地下水的**区域 integrated assessment（IA）模型**模拟 sectoral water demands ，然后将**sectoral water demands** 进行空间分布式分配。通过分析CONUS主要**水文区域里模拟的regulated flow 和 sectoral supply deficit** 来评价这个 **integrated modeling framework（IA-ES）**，这点和地球系统关注water storage 变化有区别的。**historical supply deficit 的减少**被作为评价IA-ES模型 在表示评估未来adaption和mitigation strategies的复杂sectoral 人类活动方面的improvement的 指标。这篇文章还评估了由地下水和return flow modules的**individual 和 combined addtions** 导致的 灌溉和非灌溉sectors的**regulated flow和unmet demands的空间变化**。结果表明 通过减少water supply deficit，地下水有显著的regional和sectoral 效果。水文模式上，sectoral return flow 展示了清晰的水文模式上的东西对比，因此return flow 和 IA sectoral demands 一起成为一个美国重要的水资源和water deficits的空间分布因素。这篇文章的分析点出了在获取水资源以及deficits的跨流域分配的区域差异方面，对水资源管理的空间分布secroal表示的需要。

接下来不完全按照原文顺序解读，先结合 introduction 和 modeling framework 把文章的基本方法了解下。

### 论文方法框架

首先，如前面所示，在分析人类活动-地球系统交互的方面，现在有两类策略，一是 integrated assessment（IA）community，他们把water 包含到其建模框架中；另一类是Earth System（ES）modeling community，他们将人类活动，比如水库调度，灌溉模式，地下水抽取等加入到land surface model中。耦合IA和ES这两种模型以充分利用两类模型各自对人类活动和自然物理过程的丰富表达的实验还不多。这里展示文章methodology部分的主图：

![](QQ截图20200908221205.png)

上面图的左半部分（从GCAM-USA到Partitioning of ... water resources）可以认为就是IA，剩下的就是ES了。ES中包括了反应自然物理过程的降雨径流汇流过程，以及一个反映调度等的水资源管理模块。

#### GCAM water demand

IA 简而言之，就是一种分析人类活动的模型框架，这里主要用来分析 不同 water demand，更具体地就是指原文2.1节的 regional integrated assessment model。区域IA 能用来提供网格化的water demands 估计。这里具体用到的就是 GCAM-USA 模型，即 [GCAM](https://github.com/JGCRI/gcam-core) 这个模型中加入更多美国的区域特点后的版本。根据它，可以计算6类需水组成：irrigation requirements（at ten agroecological units），domestic，manufacturing，electricity generation（at the State level），livestock 以及 primary energy（at natioanl scale），可以跟踪多个空间尺度和年时间尺度的变化。

首先，简单补充下GCAM的基本概念，GCAM（Global Change Assessment Model） 是一个整合对经济，能源，农业，气候，土地等多对象表示的一反映全球动态变化的模型，输入中还包括了人口，劳动生产率，能源、农业技术特点，资源可利用性等。其基本架构及各部分和水系统的联系可参考下图：

![](QQ截图20200908181606.png)

在现在的GCAM中，水没有货币价值，当用水需求不能满足时，多余的供应假设来自地下水。

由于GCAM生成的结果和后面的ES模型的时空尺度都不匹配，所以为了耦合，需要对GCAM结果进行空间时间上的降尺度，简而言之，是做了一个时间和空间上的分解，具体的做法可以参考：[One-way coupling of an integrated assessment model and a water resources model: evaluation and implications of future changes over the US Midwest](https://doi.org/10.5194/hess-17-4555-2013)。空间降尺度方面，主要是要利用了人口，灌溉面积等信息将water demands 分解到小尺度，更详细的过程需要参考文献[Integrated assessment of global water scarcity over the 21st century under multiple climate change mitigation policies](https://doi.org/10.5194/hess-18-2859-2014)的4.2节。然后再在state尺度上和USGS的统计结果比较，根据相关系数来大致判断结果的合理性；时间方面，需要从5年一个值的尺度降到天尺度，首先线性插值5年的数据到一年；然后针对不同的用水类型，按照不同的方式降到月尺度；最后在月尺度上使用均匀分布降到日尺度。

下面稍微展开了解下如何从月尺度降到天尺度。针对6种需水，这里分为了4类分别计算。

第一是灌溉irrigation，使用一个叫做 monthly profile 的东西来帮助将年数据降尺度到月上。 monthly profile就是一个轨迹线，有了它，年数据就可以根据它按比例分配到各个月。monthly profile 是通过Global Crop Water Demand Model（GCWM）来获得的，GCWM提供了全球网格的26种作物的月灌溉需水。这26种作物得和GCAM的12类作物之间建立起一个映射关系来帮助估计作物区域月灌溉分布。这样就能为美国每个AEZ（agro-ecological zone）区域构建monthly profiles。然后根据monthly profile 就可以将年灌溉数据降尺度到月尺度。
$$W_{ij}=W_j * Ratio_{AEZ_{ij}}$$
其中，$W_{ij}$是第j年第i月的灌溉需水。

第二类是电力用水。假定用水和发电是正比的。GCAM中用电有三部分：industry，transportation，和 building。工业用水和交通用水被假定为年内是均匀分布的。所以主要变化来自建筑内电力用水。这部分是认为有季节性波动的，计算基于热天和冷天的概念 -- heating degree days（HDD） 和 cooling degree days（CDD）。具体计算这里暂不赘述了，可以查看原文。

第三是domestic。这类直接使用文献[Global monthly water stress: 2. Water demand and severity of water stress](https://doi.org/10.1029/2010WR009792)中的公式来计算。

第四类是mining，livestock，和manufacturing，直接使用一个均匀分布，即都按年的1/12即可。

回到文章主体上，得到subbasin和日尺度的water demand数据之后，接下来需要把它们分配到地下水和地表水的取用上去，即文中的“partitioning of water demand to withdrawal and consumption from groundwater and surface water sources”或者“the allocation of the demands to surface and groundwater systems”。因为water demands的被满足过程中用地表还是地下对水文模型的计算是有明显影响的，都考虑之后，剩下的就是water deficit，所以才需要这一步。

这里还有个概念要提下，就是 [WITHDRAWAL VS. CONSUMPTION](https://sustainabilityreport.duke-energy.com/2008/water/withdrawal.asp#:~:text=Water%20withdrawn%20is%20the%20total,not%20returned%20to%20its%20source.)。先抄录一小段原话：

- Water withdrawn is the total volume removed from a water source such as a lake or river. Often, a portion of this water is returned to the source and is available to be used again.
- Water consumed is the amount of water removed for use and not returned to its source.

也就是说前者是从湖或河中取走的那部分水量，通常这部分水还会回到水源（回到水源的那部分应该就是文中所谓“return flow”），并且可以再次使用，而后者则是指被用掉的不再回到源的那部分，这里我暂且简单将其翻译为取水和耗水。如图所示是USGS做的一个关于取耗水的统计。

![](water-withdrawal-charts.jpg)

不过根据文中的定义，withdrawal是指从水体中取走的全部水量，而consumption是指不会回到水源的那部分（即蒸发或者被做到生产的物品中了），原文图3（下图）也说明了这点，其中一部分属于return flow，另一部分就是consumption了；原文公式4也说明了这点。

![](QQ截图20200908231708.png)

那么是如何分配water demands的呢？逻辑上应该是demand先按比例分给地表地下，然后再根据实际情况去看看能不能满足，再结合return flow的情况，最后就能明确withdrawal，consumption，return flow的情况了。这里面就涉及 water management系统，以及groundwater use 和 return flow 的处理，所以接下来看看原文2.4，2.5和2.6节的内容。

#### Water management (WM) model, Groundwater use 以及 return flow

这部分代码参考：[IMMM-SFA/wm](https://github.com/IMMM-SFA/wm)。

WM用的是generic的调度规则，就是[Hanasaki文章](https://doi.org/10.1016/j.jhydrol.2005.11.011)的那套方法。在使用中预设有限满足非灌溉用水。有1848个水库显式地考虑在模型中，它们主要被分为了四类：flood control，flood control with irrigation 以及其他。水库release被用来支持和其“相关”的网格单元，这些网格单元不能通过它们自己的供水来满足它们的需求。关于“相关”的数据库将每个水库和它们下游的grid cells（100km）联系起来。每个水库的下泄和库容是基于历史长序列入流和需水提前计算好的。计算的时候需要一些调整，月尺度要调整保证年内变化性，日尺度要调整保证满足最小下泄需求（生态需水）以及溢洪。WM会和Model for Scale Adaptive River Transport（MOSART）模型这一河道演进模型结合来计算出流之后的演进。

GCAM 灌溉和非灌溉需水是基于预定义好的比例来分配的，这个比例由USGS的state-level的历史观测water use数据来确定的：
$$C_{gw_{sector}}(lat,lon,t)=C_{GCAM_{sector}}(lat,lon,t)\cdot [\frac{USGS_{groundwater}}{USGS_{total}}]_{sector}$$
$$C_{sfce_{sector}}(lat,lon,t)=C_{GCAM_{sector}}(lat,lon,t)\cdot [\frac{USGS_{surface\ water}}{USGS_{total}}]_{sector}$$
其中，$C_{gw}$和$C_{sfce}$分别是GCAM的结果分配给地下水系统和地表水系统的comsumptive use water demand的那部分。$USGS_{groundwater}$,$USGS_{surface\ water}$,$USGS_{total}$ 分别是 USGS地下水，地表水和全部freshwater withdrawals。因为针对irrigation和non-irrigation有两个比例，所以分配的时候对irrigation和non-irrigation要分别处理。这篇文章中分配给地下水supply的那部分假定总是能被满足的。

## Remote sensing for agricultural applications: A meta-review（2019）

一篇遥感在农业方面应用的综述，这里仅简单了解下。

从遥感数据获取农业变量信息并不是一件直接的事情，大体上有三种方式。

1. 纯粹的经验型方法，即直接率定目标变量和相关观测值之间的相关关系，包括经验公式和机器学习算法
2. 机理类型的方法，基于radiative transfer理论、麦克斯韦方程，光学几何投影等
3. contextual 方法

## Comparing remote sensing and tabulated crop coefficients to assess irrigation water use（2019）

这篇文章的方法应该是相对比较容易实现的，比较适合非灌溉领域，比如水文建模的一些研究领域快速使用。

方法结合了 tabulated crop coefficient method和remote sensing method。
tabulated crop coefficient method如下。

实际蒸散发使用公式$AET=ET_o*K_c$计算；有效降雨由降雨减去5mm（春夏秋），冬季就是全部降雨，土壤缺水量计算为 $SWD_i=SWD_{i-1}+P_e+Irr-AET$。Irr由一个设置的缺水值激活。

Remote Sensing方法如下。在月尺度计算。假设水量平衡中忽略runoff和深层下渗。

Irr=AET-P

$K_c$值由遥感数据NDVI获取，$K_c=1.37*NDVI-0.086$

结果表明两种方法都会有些高估灌溉。

## Quantification of irrigation water using remote sensing of soil moisture in a semi-arid region（2019）

这个也是用soil moisture来估算irrigation water use的。使用的是来自AMSR2的soil moisture数据。将soil moisture，rainfall 和ET数据输入到一个SM2RAIN的模型中，来推算irrigation water use。

该模型的方法和soil moisture卫星观测和模型计算差值的方式不太一样。它也是基于土壤水平衡公式。

$$nZ\frac{dS(t)}{dt}=I(t)-g(t)-r(t)-e(t)$$

n是土壤porosity，Z是soil layer depth，nZ或者表示为$Z^*$表示soil water capacity，S(t)是relative soil moisture，t是时间，I是total water entering into soil，即降雨+灌溉，g是drainage（deep percolation 和 subsurface runoff），r是surface runoff，e是实际蒸散发。

计算的时候忽略r，令$g(t)=aS(t)^b$，$e(t)=ET_a$

所以有：

$$IR(t)=I(t)-P(t)=\frac{Z^*dS(t)}{dt}+aS(t)^b+ET_a(t)-P(t)$$

根据降雨观测去率定这里面的参数，具体地说，是利用非灌溉期地数据来率定参数，然后再用于灌溉期地irrigation water use计算（因为灌溉数据太缺乏）。灌溉和非灌溉之间地bias估算后用来校正模型。

结果显示在灌溉的pixels上平均相关系数可以达到0.86.不过分辨率低了些月尺度下50km。

## Evaluation of twelve evapotranspiration products from machine learning, remote sensing and land surface models over conterminous United States（2019）

这篇文章在CONUS上评估了12种ET数据。

一种是基于机器学习模型的产品（GFET），三种是基于遥感观测的（SSEBop, MOD16和GLEAM），8种是基于LSM的（NLDAS2和3各4种）。用的评价数据是 AmeriFlux 观测站和水量平衡推测的ET（WBET）

结果显示在流域尺度所有的产品都能展示出和WBET较高的相关性（大于0.83）

## Evaluation of hydrologic impact of an irrigation curtailment program using Landsat satellite data（2019）

看看灌溉对径流的影响。

## Evaluation of global terrestrial evapotranspiration by state-of-the-art approaches in remote sensing , machine learning , and land surface models（2019）

一篇综述ET计算的文章，介绍了估算全球ET的不同方法，包括 基于遥感的物理模型，机器学习算法和LSM。然后运用了6种基于遥感的模型和14种LSMs分析了global 陆面ET的时空变化。

遥感物理模型那部分主要是基于遥感数据利用PM公式和Priestley-Taylor方程；

machine learning的方法主要是将ET和一些变量建立起相关关系。比如 NDVI、enhanced vegetation index（EVI）等ET相关控制变量。还有将站点观测升尺度的研究。

## Detecting winter wheat irrigation signals using SMAP gridded soil moisture data（2019）

关注下 SMAP的作用。

## Estimating irrigation water use over the contiguous United States by combining satellite and reanalysis soil moisture data（2019）

这篇文章基于模型估计的soil moisture和遥感观测的soil moisture来计算irrigation water use（IWU）。基本的思路是模型计算的soil moisture是不考虑灌溉的，而遥感观测的是实际情况包含了灌溉的，所以结合两者可以推求IWU。作者计算的是CONUS上2013-2016年间的月尺度灌溉数据，后面数据部分介绍了都处理成0.25度网格，所以应该是这个分辨率了。这两年SMAP又积累了不少数据，所以不必太担心SMAP的数据问题，16-20年也有5年数据能用了。

作者介绍了关于灌溉areas和water withdrawals以往的研究，指出了比如只在single year有数据，以往数据更多反映地是areas equipped for irrigation而不是areas actually irrigated等问题。

还review了以往应用遥感数据到irrigation water use领域的文献。主要包括两类：Optical and thermal 遥感与 微波遥感。

前者主要是Landsat/MODIS等卫星的应用，主要用来帮助生成irrigated/non-irrigated croplands，做irrigation map。在用水方面，主要是利用thermal 遥感计算蒸散发等来估算用水，但是这估算的是irrigation water requirement。

后者主要是用来估算soil moisture。它的好处是它是直接与irrigation相关的一个观测，并且微波卫星全天气条件下可工作的。第一个用遥感土壤含水量做灌溉mapping的是Kumar等人2015年的一篇文章（但是我上面看到了一篇2014年就有的，其中方法还是引用的，所以就更早了）。用了多个卫星地表土壤含水量产品结合Noah LSM的土壤含水量估计来map irrigated areas in the CONUS。其中的关键假设是灌溉没有被考虑到LSM中，而卫星观测推求的土壤含水量是实际的，可以被认为是反映了灌溉导致的土壤含水量变化的。国内也有人研究了观测和reanalysis产品之间的差别，也发现了在灌溉区域，确实两者的差异会更大。SMAP数据2015年才有，所以研究多是近几年的。

不过以上研究都没有用来估算actual irrigation water use的。此法的假设基础就是 模型是没有考虑人工供水的，而微波遥感数据是考虑了的。作者使用的数据是MERRA-2的reanalysis数据以及SMAP/AMSR2/ASCAT三种卫星的soil moisture数据。

在CONUS上，东部不是特别缺水，所以灌溉并不多，西部则不同，很依赖灌溉。卫星土壤含水量应该是对灌溉效率更低的方式更敏感，对滴灌喷灌等技术应不敏感。

方法思路是这样的。

卫星观测下土壤水量平衡公式为：

$$\frac{d \Theta ^{sat}}{dt}=P(t)+I(t)-ET(t)-R(t)-\triangle S_{rest}$$

模型模拟下则为：

$$\frac{d \Theta ^{mod}}{dt}=P(t)-ET(t)-R(t)-\triangle S_{rest}$$

其中，P是降水，I是灌溉，ET是蒸散发，$\Theta ^{mod}$是模型计算的soil moisture，$\Theta ^{sat}$是卫星观测推算的soil moisture。$\triangle S_{rest}$描述的是surface layer下water storage的变化，包括drainage。

所以:

$$I(t)=\frac{d \Theta ^{sat}}{dt}-\frac{d \Theta ^{mod}}{dt}$$

定义一个灌溉时间发生的时候是 $\frac{d \Theta ^{sat}}{dt}>0$ 且 $\frac{d \Theta ^{mod}}{dt}\leq 0$。这意味着不是降雨引发的soil moisture的增大，而是人工补水的结果。对每次事件，如果卫星上的变化是显著的（高于噪音水平），就计算上述差值来推求irrigation。

$$IWU = \int _{i_{SOS}}^{i_{EOS}}(d\Theta _i^{sat}-d\Theta _i^{mod})dt\approx \sum _{SOS}^{i=EOS}\triangle \Theta _i^{sat-mod}$$
$$\triangle\Theta _i^{sat-mod}=\left\{
\begin{aligned}
\triangle \Theta _i^{sat}-\triangle \Theta _i^{mod}, if \ \triangle \Theta _i^{sat}\geq \Theta _{thresh} \\
0, otherwise
\end{aligned}
\right.$$
$$\triangle \Theta _i^{sat}=\Theta _i^{sat}-\Theta _{i-n}^{sat}$$
$$\triangle \Theta _i^{mod}=\Theta _i^{mod}-\Theta _{i-n}^{mod}$$

其中，IWU是从季节开始到季节结束累计的灌溉用水。根据以往研究，CONUS上4月1日到9月30日能覆盖绝大多数的灌溉生长季节时间范围。$\Theta _i^{sat}$和 $\Theta _i^{mod}$是第i天 卫星和模型的估计值， $\Theta _{i-n}^{sat}$和 $\Theta _{i-n}^{mod}$是前面n天前（n天是观测的gap）的卫星和模型估计值。

更多细节参考原文。

结果上，作者和州一级的统计结果进行了比较。estimated irrigation water use和state-level reference water withdrawals相比，SMAP下推求的R能达到0.80.可以说是相当不错了。

## Estimating Net Irrigation Across the North China Plain Through Dual Modeling of Evapotranspiration（2020）

## Regional crop water use assessment using Landsat-derived evapotranspiration（2020）

## Estimating daily reference evapotranspiration based on limited meteorological data using deep learning and classical machine learning methods (2020)

这篇文章利用了几种DL方法来估算日reference evapotranspiration。结果表明，在有 temperature-based 的变量数据的时候，TCN和LSTM能表现的比其他基于温度的经验模型好，个人认为还是能说明温度数据的时间序列规律对ET的影响还是很大的。当radiation-based或humidity-based数据可用的时候，所有ML的模型都比经验方程强。不过验证的对象是PM公式计算的ET0，所以个人感觉这篇文章主要还是看下方法。LSTM和TCN留意下。

## A meteorological-based crop coefficient model for estimation of daily evapotranspiration (2020)

这篇文章介绍了ETc计算的FAO56方法。

谈到了该方法在regional 尺度的应用上会有的缺点：

1. 首先是该模型在几种常见作物的蒸散发估算上效果并不好
2. FAO56的PM公式估计ETo也有高估
3. 此外，Kc和land cover，canopy dynamics，canopy light absorption，soil properties和soil moisture都有关系，因此不确定性很大。

所以计算crop ET的方法就有人研究只依赖于 meteorological forcing的方法。比如 Bouchet's complementary hypothesis

之前有研究建立ETa与ETo和RH之间的线性您和观测，受此启发，这篇文章参考PM公式，结合蒸散发的物理过程，认为ETa和 soil moisture，RH，作物类型，ETo都有关系。并且其根据测站的观测数据，也证实了ETa，ETo和RH对特定的crop确实是有相互关系的。相比于15分钟的观测尺度，水文学者更关心日尺度上的蒸散发过程，所以这篇文章在日尺度上建立了一个二阶多项式来表达Kc，并根据实际数据来拟合这一关系。

$$K_c=f(RH,ET_o)=\sum_{i=1}^3\sum_{j=1}^3m_{ij}\cdot RH^{i-1}ET_o^{j-1}$$

其中，f函数是作物相关的。系数需要在不同土壤类型下的不同地区依据观测数据来率定。

所以这篇文章利用观测数据率定了参数并验证，图4结果表面，4种作物的R2在0.58-0.86之间，图9表明和FAO56的方法相比更优越一些。

## Long time series of daily evapotranspiration in China based on the SEBAL model and multisource images and validation (2020)

这篇文章提供了国内1km分辨率的ET数据，数据可以在文中提到的地址下载。

## Improved ET assimilation through incorporating SMAP soil moisture observations using a coupled process model : A study of U . S . arid and semiarid regions (2020)

关注下SMAP数据在ET计算中的作用。

这篇文章作者研究了在大尺度上同化SMAP数据在估计ET中的作用。利用$PT-JPL_{SM}$模型建立的soil moisture和ET之间的关系，这样就能利用SMAP数据，然后将其作为“观测”可以来约束另一个模型LPJ-DGVM计算的$ET_{LPJ}$，这样更好地模拟ET。

结果表明同化了SMAP数据的ET更接近实际地面观测值。

此法适合估算global ET。

## Evaluation Framework of Landsat 8-Based Actual Evapotranspiration Estimates in Data-Sparse Catchment（2020）

这篇文章基于Landsat8的无云天观测数据，利用一些能量平衡模型计算了一个区域的ET，并和MODIS的产品以及GLEAM产品比较，说明了能量平衡模型的选择对于ET的计算也是有影响的。

## Irrigation water consumption of irrigated cropland and its dominant factor in China from 1982 to 2015（2020）

这篇文章综合了 灌溉作物图，土壤水平衡，灌溉作物物候，和蒸散发产品，研究了一个irrigated cropland water model来量化国内的高分辨率长时间灌溉耗水。

作者收集了全国840个气象站点的气象数据，包括降雨、气温、风速、日照、相对湿度等，插值为8km网格数据。NDVI数据也插值为日尺度数据。26种作物的月作物网格图也处理为8km数据。作物的参数、最大作物深度和作物系数都要有。

计算作物耗水的方法是提出一种ET分解法。用现存的ET数据乘以IrrigationnWaterConsumption占total water consumptionn的比例作为irrigated cropland的IWC值。

ET数据是通过upscale站点的ET值获得。而比例是通过运行crop model来获取的。所以这篇文章的大部分工作量就在如何运行crop model上。

这个过程不是太简单，复杂程度和github上美国农垦局的water demand程序其实差不太多。

## A review of remote sensing applications in agriculture for food security: Crop growth and yield, irrigation, and crop losses（2020）

这里关注下这篇综述的irrigation的部分。

irrigation我们关注 识别灌溉发生的地方和量化灌溉用水。

第一部分主要就是区分灌溉和非灌溉区，或者查看灌溉的时空模式。

第二部分就是估计灌溉需水（irrigation water requirement，IWR）或者灌溉耗水（irrigation water consumption，IWU）。IWR定义为crop water requirement（CWR）和effective precipitation之间的差值。IWR是降水、土壤含水量、土壤属性以及ETc的函数。CWR就是满足ETc的水量。

IWR的估计中重要的变量之一就是ETc，其受气候、作物生长等的影响。通常使用crop coefficient法来计算。也可根据能量平衡结合遥感数据计算。

IWR是作物的需水，而实际的耗水是IWU。分析土壤含水量变化并考虑降水可以推测灌溉耗水IWU。这里值得查看下作者引用的文献（Estimating irrigation water use over the contiguous United States by combining satellite and reanalysis soil moisture data（2019）），并关注下SMAP和GRACE数据的使用。

## Satellite-based global-scale irrigation water use and its contemporary trends（2020）

这篇是大尺度0.25度分辨率上利用水量平衡做计算。

## The green and blue crop water requirement WATNEEDS model and its global gridded outputs（2020）


## Monitoring irrigation using landsat observations and climate data over regional scales in the Murray-Darling Basin（2020）

利用landsat观测和气候数据在流域尺度来监测灌溉。用的是 Irr=AET-P的思路。从Landsat计算的NDVI可以利用几个简单的回归公式转换为Kc，然后结合ETo来计算ETc。从而推算irrigation。不过这一套其实不算是water use。

几种不同的公式在文中分别对应 CMRSET/IrriSat/Kamble ，结果在表4。

## Connections between the hydrological cycle and crop yield in the rainfed U.S. Corn Belt（2020）

注意下里面的数据量之间的相关性。VPD（vapor pressure deficit）/SSM（surface soil moisture）/fET（ratio of ET and PET）是和CROP FIELD高度相关的。

## Satellite‐Based Monitoring of Irrigation Water Use: Assessing Measurement Errors and Their Implications for Agricultural Water Management Policy（2020）

这篇综述主要是讨论了 Satellite‐Based Monitoring of Irrigation Water Use 的不足，这里暂时不细看了。

## Mapping of 30-meter resolution tile-drained croplands using a geospatial modeling approach（2020）


## Physical versus economic water footprints in crop production: A spatial and temporal analysis for China（2021）

简单了解下footprint，注意其中计算crop water使用的aquacrop模型。
