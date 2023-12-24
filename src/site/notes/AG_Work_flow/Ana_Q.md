---
{"dg-publish":true,"permalink":"/ag-work-flow/ana-q/","tags":["Syed"]}
---

### Questions

[id1]: ## "Streamlines data integration, simplifies reporting[Fiori and etc..] & supports consistent business logic across applications"

This functionality can’t be achieved by Open SQL and CDS.
- AMDP vs CDS :
  - CDS (Core Data Services) - Data Modeling and Simplification [Hover][id1]
  - AMDP (ABAP Managed Database Procedures): -  Code Pushdown and Performance Optimization
    1. *AMDP* - we can call one function inside the other, it is helpful in returning multiple result set on complex logics. Whereas, CDS is dedicated for single set of logic and return only one result set.
    2. *CDS*  - To read and process for reporting whereas AMDP is to process and modify data at DB layer.  
Migration Scenario [[AG_Work_flow/Ana_Q#\|#]]

### Use Case


### **Role of CDS and AMDP in SAP S/4HANA Migration:**

#### **CDS:**
1. **Analytical Capabilities with Code Pushdown resulting in better Performance and simplified Modeling **
    - Enables the creation of analytical views for real-time analytics and reporting with enhanced performance.
2. **Fiori User Interfaces:**
    - Foundation for developing Fiori applications, enhancing user interfaces.

#### **AMDP:**
1. **Integration with Existing ABAP Code with Code Pushdown for the Complex Logic**
    - Creates optimized database procedures which support integration within existing ABAP applications.
2. **Performance Improvement in Batch Processing:**
    - Significantly improves performance for batch processing and data-intensive tasks.

#### **Alignment with SAP S/4HANA Principles:**

1. **Simplified Data Model:**
    - CDS and AMDP align with the principle of a simplified data model in SAP S/4HANA.
2. **Code Pushdown and In-Memory Processing:**
    - Support principles of code pushdown and in-memory processing for improved performance.
3. **Real-Time Analytics and Reporting:**
    - Contribute to real-time analytics and reporting capabilities in SAP S/4HANA.
4. **Enhanced User Experience with Fiori:**
    - CDS supports Fiori application development, enhancing the overall user experience.

### Security
#### **CDS Views:**

1. **Authorization Checks:**
    - `@AccessControl.authorizationCheck` annotations. Ensure that these checks align with the required security policies.
2. **Data Exposure:**
    - Limit the data fields exposed to users based on their roles and responsibilities.
3. **Client Handling:**
    - When using client-specific CDS Views, ensure that proper client handling is implemented, and access is restricted based on user roles.
4. **Field Level Security:**
    - Leverage field-level security options provided by CDS Views to restrict access to specific fields based on user roles.
5. **Role-Based Access Control (RBAC):**
    - Enforce role-based access control for both CDS Views and AMDP. Define roles and assign them to users based on their responsibilities.

#### **AMDP:**

1. **SQL Injection Prevention:**
    - Implement proper parameterization and input validation in AMDP to prevent SQL injection attacks.
2. **Secure Database Connection:**
    - Ensure secure database connections by using encrypted connections and configuring database user credentials with the principle of least privilege.
3. **Privileges and Roles:**
    - Assign the necessary database privileges and roles to the user executing the AMDP, ensuring they have the required permissions for the operations performed.
4. **Error Handling:**
    - Implement robust error handling in AMDP to avoid exposing sensitive information in case of failures.

### Int 

**What is the role of the Analytical Path Framework (APF) in Embedded Analytics?**
- Describe how APF enables the creation of guided analytics paths for users in SAP S/4HANA.

**Explain the concept of KPIs (Key Performance Indicators) and their role in Embedded Analytics.**
- Discuss how KPIs are defined and used to measure the performance of business processes in Embedded Analytics.

**What are the prerequisites for implementing Embedded Analytics in SAP S/4HANA?**
- Discuss the technical and functional requirements for setting up and utilizing Embedded Analytics in SAP S/4HANA.

**Explain the role of SAP Fiori Elements in simplifying the development of analytical applications.**
- Describe how Fiori Elements streamline the development process and enhance the user experience in Embedded Analytics.

**What are the security considerations for Embedded Analytics, especially in terms of data access and user roles?**
- Discuss how security is managed in Embedded Analytics to ensure appropriate data access and user permissions.


## Managing Security in Embedded Analytics

**Objective:** Discuss how security is managed in Embedded Analytics to ensure appropriate data access and user permissions.

### Security Considerations:

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

### Implementation Strategies:
1. **Collaboration with Security Teams:**
   - Collaborates closely with security teams to align Embedded Analytics security with overall organizational security policies.
   - Ensures adherence to industry standards and compliance requirements.
2. **User Provisioning and Deprovisioning:**
   - Establishes robust processes for user provisioning and deprovisioning.
   - Ensures timely removal of access for users who no longer require analytical privileges.
4. **Encryption and Data Masking:**
   - Implements encryption techniques to secure data in transit and at rest.
   - Utilizes data masking to protect sensitive information from unauthorized access.

### User Permissions Management:
1. **Customized Analytical Apps Permissions:**
   - Customizes permissions for Analytical Apps based on user roles and responsibilities.
   - Ensures that users have access only to the relevant analytics content aligned with their job functions.
2. **Authorization Checks in Fiori Apps:**
   - Implements authorization checks within Fiori Apps to validate user permissions.
   - Ensures that users can only perform actions for which they have the necessary authorization.

# Extras
---
