---
{"dg-publish":true,"permalink":"/ag-work-flow/ana-q/","tags":["Syed"]}
---

## Questions

[[AG_Work_flow/Ana_Q#Q1. AMDP vs CDS\|#Q1. AMDP vs CDS]] **AMDP vs CDS :**
[[AG_Work_flow/Ana_Q#Q2. Security\|#Q2. Security]] Discuss how security is managed in Embedded Analytics to ensure appropriate data access and user permissions.
	- In terms of security and data privacy, how do you ensure compliance with industry standards and organizational policies when implementing Embedded Analytics solutions?
[[AG_Work_flow/Ana_Q#Q3. Performance\|#Q3. Performance]] Share your experience and strategies for optimizing performance in Embedded Analytics, especially when dealing with extensive datasets.
[[AG_Work_flow/Ana_Q#Q4. KPI\|#Q4. KPI]] KPI - High use of UI Annotations 
[[AG_Work_flow/Ana_Q#Q5. Experience & Challenges\|#Q5. Experience & Challenges]] Describe a scenario where you *successfully implemented* real-time reporting using Embedded Analytics. What challenges did you face, and how did you overcome them?

Q6.
    


You

Share your experience and strategies for optimizing performance in Embedded Analytics, especially when dealing with extensive datasets.


---
### Answers

#### Q1. AMDP vs CDS :
- CDS (Core Data Services) - Data Modeling and Simplification ^[Streamlines data integration, simplifies reporting(Fiori and etc..) & supports consistent business logic across applications]
  - AMDP (ABAP Managed Database Procedures): -  Code Pushdown and Performance Optimization
1. *AMDP* - we can call one function inside the other, it is helpful in returning multiple result set on complex logics. Whereas, CDS is dedicated for single set of logic and return only one result set. ^[This functionality can’t be achieved by Open SQL and CDS.]
	  - Performance Improvement in Batch Processing
2. *CDS*  - To read and process for reporting whereas AMDP is to process and modify data at DB layer.  



#### Q2. Security
In terms of security and data privacy, how do you ensure compliance with industry standards and organizational policies when implementing Embedded Analytics solutions?
 **CDS Views:**
1. **Authorization Checks:**
    - `@AccessControl.authorizationCheck` annotations. Ensure that these checks align with the required security policies.
1. **Field Level Security: orRole-Based Access Control (RBAC)**
    - Leverage field-level security options provided by CDS Views to restrict access to specific fields based on user roles.
    - Enforce role-based access control for both CDS Views and AMDP. Define roles and assign them to users based on their responsibilities.

**AMDP:**
1. **SQL Injection Prevention:**
    - Implement proper parameterization and input validation in AMDP to prevent SQL injection attacks.
2. **Secure Database Connection:**
    - Ensure secure database connections by using encrypted connections and configuring database user credentials with the principle of least privilege.
3. **Privileges and Roles:**
    - Assign the necessary database privileges and roles to the user executing the AMDP, ensuring they have the required permissions for the operations performed.
4. **Error Handling:**
    - Implement robust error handling in AMDP to avoid exposing sensitive information in case of failures.


#### Q3. Performance
Experience in Optimizing Performance in Embedded Analytics
Understanding the Challenge:
- Dealing with extensive datasets in Embedded Analytics often presents challenges related to query execution time, data retrieval efficiency, and overall system responsiveness. My experience involves addressing these challenges to ensure a seamless and performant user experience.
 Strategies Employed:
1. **Optimizing Data Models (CDS Views):**
	- eliminating unnecessary joins and aggregations.
	- thereby creating efficient and simplified data models thus streamlining the retrieval of relevant data for analytics.
	- Performance tuning [[AG_Work_flow/Ana_Q#Prunning\|#Prunning]]
2. **Indexing and Partitioning:**
	- Implementing proper indexing and partitioning strategies on database tables can have a substantial impact on query performance. I worked on optimizing database structures to facilitate faster data access.
3. **Query Caching:**
	- Utilizing query caching mechanisms helps in storing and reusing previously executed queries. This is especially effective for recurring analytical queries on static or slowly changing datasets, reducing redundant computations.
4. **Parallel Processing:**
	- Employing parallel processing techniques for data retrieval and computation can be beneficial. This involves breaking down complex tasks into smaller, parallelizable units, enhancing overall processing speed.
5. **Data Pruning and Archiving:**
	- For historical datasets, implementing data pruning and archiving strategies is essential. This ensures that only relevant and current data is part of analytical processes, preventing unnecessary load on the system.
6. **Incremental Data Loading - external systems**
	- Implementing mechanisms for incremental data loading reduces the amount of data processed during each analytics cycle. This is particularly effective when dealing with large datasets that don't change frequently.
- Lessons Learned:
	- **Balancing Act:**
	    - Striking the right balance between data granularity and performance is crucial.
	- **User Education:**
		- Educating end-users about the impact of data volume on performance and promoting best practices for efficient querying fosters a collaborative approach to performance optimization.
	- **Continuous Improvement:**
	    - Performance optimization is an ongoing process. 
	    - Regularly revisiting and refining optimization strategies based on changing data patterns and business requirements is essential for sustained performance gains.

So this been a multifaceted approach, combining code pushdown principles, data modeling strategies, and continuous monitoring. My experience involves adapting and evolving these strategies to meet the specific challenges posed by large and dynamic datasets in the context of Embedded Analytics

#### Q4. KPI
Defining and Using KPIs in Embedded Analytics, [ALP_YVideo](https://www.youtube.com/watch?v=GeYLVDCIy6Y)
- Defining KPIs: - [YVideo_Link](https://www.youtube.com/watch?v=4PWitfs_-3U)
1. **Data Mapping:**
   - Map KPIs to Core Data Services (CDS) Views, selecting attributes and measures.
2. **Thresholds and Targets:**
   - Set acceptable thresholds and establish target values for KPIs.
- Using KPIs for Measurement:
1. **Visualization Techniques:**
   - Use charts and color coding to visually represent KPIs.
2. **Alerts and Notifications:**
   - Configure alerts triggered by KPI thresholds for real-time monitoring.
3. **Historical Analysis:**
   - Analyze trends and compare KPI values with historical benchmarks.
In summary, the process involves aligning business objectives with data models, integrating KPIs into Analytical Apps, and promoting a culture of continuous improvement through user training.

#### Q5. Experience & Challenges
1. **Fiori Analytical Apps Development:**
	- Designed and developed Fiori Analytical Apps tailored to the manufacturing and supply chain domains. These apps provided a user-friendly interface for real-time reporting.
2. **Smart Business Annotations:**
	- Utilized Smart Business annotations within CDS Views to enable advanced analytics features, including predictive analytics and exception handling. ^[**`@SemanticBridge: ...`**]
3. **User Adoption:** - **Challenge:** Overcoming resistance to change and ensuring users embraced real-time reporting.
	- **Solution:** Engaged users early in the process, highlighting the benefits of instant insights and providing ongoing support and training.



#### Q2.Security
Managing Security in Embedded Analytics
 Security Considerations:
1. **Role-Based Access Control (RBAC):**
   - Utilizes RBAC principles to assign roles to users based on their responsibilities.
   - Defines permissions associated with each role to control access to analytical content.
2. **Analytical Privileges:**
   - Implements analytical privileges to restrict data access for specific users or user groups.
   - Defines privileges based on data dimensions, attributes, or hierarchies.
3. **Data Exposure Control:**
   - Ensures controlled exposure of sensitive data through fine-grained access controls.
   - Limits access to specific data fields or records based on user roles.
4. **Integration with SAP HANA Security Features:**
   - Leverages SAP HANA security features for authentication and authorization.
   - Aligns with HANA database security protocols for secure data handling.
5. **Single Sign-On (SSO):**
   - Implements SSO mechanisms to enhance user authentication and streamline access.
   - Ensures a seamless and secure user experience across SAP S/4HANA and Embedded Analytics.

Implementation Strategies:
1. **Collaboration with Security Teams:**
   - Collaborates closely with security teams to align Embedded Analytics security with overall organizational security policies.
   - Ensures adherence to industry standards and compliance requirements.
2. **Robust processes for User Provisioning and Deprovisioning:**
   - Ensures timely removal of access for users who no longer require analytical privileges.
3. **Encryption and Data Masking:**
   - Utilizes data masking to protect sensitive information from unauthorized access.

# Extras
---

#### Prunning
- **`@SemanticBridge: ...`**
   The `@SemanticBridge` annotation enables you to define semantic bridges between elements. It may indirectly impact how data is pruned or aggregated in analytical scenarios.
	```abap
	element Quantity: Quantity @SemanticBridge: #SUMMARY;
	```  
- **`@Analytics` Annotations (e.g., `@DefaultAggregation: ...`):**
	Annotations within the `@Analytics` namespace, such as `@DefaultAggregation`, allow you to define default aggregations for measures. This can indirectly affect how data is pruned or summarized in analytics scenarios.
	```abap
	element SalesAmount: Amount @DefaultAggregation: #SUM;
	```  
-  **`ObjectModel: { SEMANTICS = #TIMEPRUNING; }`**
		The `@ObjectModel` annotation with the `SEMANTICS = #TIMEPRUNING` setting enables time-based pruning for temporal data models.
	```abap
	@ObjectModel: { SEMANTICS = #TIMEPRUNING; } define view SalesByDate as select from Sales { ... }
	```
- **`@Search: ...`**
	-  The `@Search` annotation allows you to define search behavior, and it includes the `only` parameter, which can be used to exclude certain elements from being searchable.
	```abap
	element Name: String(30) @Search.defaultSearchElement: true; element Description: String(100) 
	@Search.only: #NEVER;
	```
- **`@ClientHandling: ...`**
	- The `@ClientHandling` annotation enables client-specific handling. You can use the `exclusive` option to exclude a field from client handling.
	```abap
	element Customer: BusinessPartner; 
	element SalesAmount: Currency @ClientHandling.exclusive: 
	#CLIENT_DEPENDENT;
	```


