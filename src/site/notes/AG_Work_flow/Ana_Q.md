---
{"dg-publish":true,"permalink":"/ag-work-flow/ana-q/","tags":["Syed"]}
---

### Questions

- CDS VS AMDP :
  1. In AMDP, we can call one function inside the other, it is helpful in returning multiple result set on complex logics. Whereas, CDS is dedicated for single set of logic and return only one result set.
  2. CDS views can be created to read and process data at DB layer. Whereas AMDP can be created to process and modify data at DB layer.
  3. AMDP is used to work with stored procedures, which further go to HANA DB layer and execute that. This functionality can’t be achieved by Open SQL and CDS.

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

#### **AMDP:**

1. **SQL Injection Prevention:**
    - Implement proper parameterization and input validation in AMDP to prevent SQL injection attacks.
2. **Secure Database Connection:**
    - Ensure secure database connections by using encrypted connections and configuring database user credentials with the principle of least privilege.
3. **Privileges and Roles:**
    - Assign the necessary database privileges and roles to the user executing the AMDP, ensuring they have the required permissions for the operations performed.
4. **Error Handling:**
    - Implement robust error handling in AMDP to avoid exposing sensitive information in case of failures.

### **General Considerations:**

1. **Transport Security:**
    - Secure the transport layer between the application and the database to prevent unauthorized interception of data.
2. **Authentication Mechanisms:**
    - Use secure authentication mechanisms for both CDS Views and AMDP, such as SAML, X.509 certificates, or other industry-standard methods.
3. **Logging and Auditing:**
    - Implement detailed logging and auditing mechanisms to track access to CDS Views and the execution of AMDP. Regularly review logs for suspicious activities.
4. **Data Encryption:**
    - Consider encrypting sensitive data both in transit and at rest to enhance overall data security.
5. **Role-Based Access Control (RBAC):**
    - Enforce role-based access control for both CDS Views and AMDP. Define roles and assign them to users based on their responsibilities.
6. **Compliance with Policies:**
    - Ensure that security configurations and implementations comply with organizational security policies, industry standards, and regulatory requirements.
7. **Regular Security Audits:**
    - Conduct regular security audits to identify and address any potential vulnerabilities in the CDS Views and AMDP implementations.

By addressing these considerations, you can help ensure that CDS Views and AMDP are implemented securely and adhere to the necessary authorization and security standards within your SAP environment. It's important to stay informed about SAP security best practices and leverage the tools and features provided by SAP to enhance security.