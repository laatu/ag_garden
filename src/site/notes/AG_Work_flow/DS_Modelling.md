---
{"dg-publish":true,"permalink":"/ag-work-flow/ds-modelling/","tags":["#Ds/Modelling"]}
---



### Business Builder
- [[AG_Work_flow/DS_Modelling#Business-Builder-Image\|Ref. image]]
- artifacts - 4 - Dimensions, Fact, Fact Model, Consumption Model


### Data Builder
- [[AG_Work_flow/DS_Modelling#Data-Builder-Image\|Ref. Image]]
- Table ![DS_Modelling_20231220-1.png|30](/img/user/Images/DS_Modelling_20231220-1.png), View - Graphical ![DS_Modelling_20231220-2.png|30](/img/user/Images/DS_Modelling_20231220-2.png)/SQL ![DS_Modelling_20231220-3.png|30](/img/user/Images/DS_Modelling_20231220-3.png)
- Data Flow ![DS_Modelling_20231220-4.png|30](/img/user/Images/DS_Modelling_20231220-4.png), Replication Flow , Task Chain ![DS_Modelling_20231220-5.png|30](/img/user/Images/DS_Modelling_20231220-5.png)
- Entity Relation Model ![Images/DS_Modelling_20231220.png|30](/img/user/Images/DS_Modelling_20231220.png), Intelligent Lookup ![DS_Modelling_20231220-6.png|30](/img/user/Images/DS_Modelling_20231220-6.png)


<br>

#### The New Analytic Model - [link](https://learning.sap.com/learning-journey/explore-sap-datasphere/introducing-data-modeling-in-the-data-builder_c3a59257-4b17-4e46-b707-ce574a8cc66f)
Serves as the analytical foundation for preparing data for consumption. 
- Creation of multidimensional mutlti models for analytical purposes.
- Predefined measures, hierarchies, filters, and associations for enhanced data exploration.
- [Link](https://learning.sap.com/learning-journey/explore-sap-datasphere/introducing-data-modeling-in-the-data-builder_c3a59257-4b17-4e46-b707-ce574a8cc66f) - can use ==CDS View== as a source - making it's source a virtual.
![DS_Modelling_20231220-7.png|500](/img/user/Images/DS_Modelling_20231220-7.png)


![DS_Modelling_20231220-8.png|500](/img/user/Images/DS_Modelling_20231220-8.png)



#### Remote Table
1. [[AG_Work_flow/DS_Modelling#Remote-Table=Snap-shoot-vs-realtime\|Snapshot/RealTime]] - do not maintain in here, maintain at upper layers.
2. Schedule Option
3. Refresh Replication - will pull delta records if pressed.
4. View Monitor
- Filter Option

#### Views
-  When working on Views - there is an Option to persist the dataset is in view(graphical). - here utilizing the *Virtual* Remote table. #Ds/Persistency 
	![DS_Modelling_20231220 2.png|200](/img/user/Images/DS_Modelling_20231220%202.png)

#### SQL View
- Either Standard SQl or SQL Script <sub>(table Function-return structure)</sub>
- other persistence view things are same as before
<br><br>
### Blogs
- [One](https://blogs.sap.com/2023/04/17/sap-datasphere-analytic-model-series-using-variables-in-analytic-model/)


<br><br>


---
# Images for reference
###### Business-Builder-Image
![DS_Modelling_20231220-9.png|500](/img/user/Images/DS_Modelling_20231220-9.png)
###### Data-Builder-Image

![DS_Modelling_20231220-10.png|500](/img/user/Images/DS_Modelling_20231220-10.png)
###### Remote-Table=Snap-shoot-vs-realtime
![/Images/DS_Modelling_20231220 1.png|300](/img/user/Images/DS_Modelling_20231220%201.png)
