# **Understanding Broken Access Control (BAC)**

## What is **Broken Access Control (BAC)**?

**Broken Access Control (BAC)** occurs when a system does not properly enforce permissions or security rules, allowing users to access more data or perform actions they are not authorized for. 
BAC is one of the most critical security vulnerabilities in web applications and can lead to various issues such as unauthorized data access, privilege escalation, and system manipulation.

> **Key Points:**
> - **BAC is not about hacking**; it is about a system that fails to properly check **who is allowed to do what**.
> - BAC can lead to **unauthorized access**, **data breaches**, **privilege escalation**, and more.

---

## **Common Types of Broken Access Control and Examples**

| **Type of BAC**                         | **Example Scenario**                                                                                                                                           | **Description**                                                                                                                                                                  |
|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Insecure Direct Object References (IDOR)** | **Example:** A user modifies the URL to view other users’ order details. <br> **URL**: `https://example.com/orders?id=1234` → Changing `id=1235` lets them view another user's order. | **Cause:** The system doesn’t check if the user is authorized to view the requested order ID. <br> **Fix:** Check that users can only access their own data. Use secure session tokens. |
| **2. Vertical Privilege Escalation**     | **Example:** A regular user tries to access an admin function by directly visiting an admin URL: <br> `https://cms.example.com/edit-article?id=5678` and gains access to edit articles. | **Cause:** The system does not verify user roles before allowing access to higher-privilege functions. <br> **Fix:** Implement role-based access control (RBAC). |
| **3. Horizontal Privilege Escalation**   | **Example:** A user, manipulates a `user_id` parameter in the URL to view another user’s profile: <br> `https://shop.example.com/profile?user_id=5678` | **Cause:** The system doesn't verify that the user should only access their own data. <br> **Fix:** Check user session and enforce strict permission checks. |
| **4. Missing Function Level Access Control** | **Example:** A regular user finds an unprotected admin API endpoint for transferring funds: <br> `POST /api/admin/transfer` and transfers money without authorization. | **Cause:** The system does not check if the user has the necessary permissions to access sensitive functions. <br> **Fix:** Always authenticate and authorize users before allowing sensitive actions. |

---

## **How to Prevent Broken Access Control (BAC)**

### **1. Enforce Strong Authentication and Authorization**
- Always authenticate users before granting access to any sensitive functionality or data.
- Ensure that users are only allowed to perform actions according to their **roles** and **privileges**.

### **2. Implement Role-Based Access Control (RBAC)**
- Define and enforce **roles** such as `admin`, `user`, `moderator`, etc., and assign permissions accordingly.
- Ensure only users with the appropriate roles can access restricted areas.

### **3. Validate All User Inputs**
- Never trust user-supplied data. Always validate and sanitize inputs, especially those that influence access control decisions (e.g., **URL parameters**, **form inputs**).

### **4. Use Secure URLs and Tokens**
- Avoid exposing sensitive data (e.g., **object IDs** or **user identifiers**) in the URL or request parameters that can be manipulated.
- Implement **session-based authentication** to verify the user before allowing access to sensitive data.

### **5. Conduct Regular Security Testing**
- Perform regular security audits, **penetration tests**, and **code reviews** to identify and fix access control issues.
- Use automated security testing tools to detect potential BAC vulnerabilities.

---

## **Final Thoughts**

**Broken Access Control (BAC)** is a serious vulnerability that can have significant security implications for your application. It occurs when the system fails to properly enforce access controls, allowing users to gain unauthorized access to sensitive data and functions.

To ensure the security of your web application, it is crucial to implement proper access control checks at every layer of your application. By following the best practices and understanding common BAC vulnerabilities, you can significantly reduce the risk of unauthorized access and protect your users’ data.

> **Always remember**: **BAC is about enforcing permissions** and making sure **everyone can only access what they’re allowed to**.

---

