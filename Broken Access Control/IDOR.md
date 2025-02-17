# IDOR

>Insecure Direct Object Reference (IDOR) is an access control vulnerability where an application lets users directly access objects based on user-supplied input, like images, PII, or database records. This allows attackers to bypass authorization and access unauthorized resources by manipulating parameters that point to objects.

## Where all can we test for IDOR?

| **Test Point**         | **Description**                                                                 | **Action**                                                                                                                                 |
|------------------------|---------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| **URL Parameters**      | Look for parameters in the URL (e.g., `id`, `user_id`, `order_id`) that reference objects (e.g., user data, orders, documents). | Modify these parameters and test if you can access data belonging to other users.                                                        |
| **Form Inputs**         | Check for form fields (e.g., hidden fields, file uploads) referencing objects directly, like `user_id` or `file_path`. | Manipulate form inputs and observe if unauthorized resources can be accessed.                                                            |
| **API Endpoints**       | Inspect API requests for direct object references (e.g., `GET /api/user/{user_id}`). | Modify parameters like `user_id` or `file_id` in API requests to test if you can access unauthorized data.                               |


## Types of IDOR you will see in wild:

1. The value of a parameter is used directly to retrieve a database record
    
    ```markdown
    http://foo.bar/somepage?invoice=12345
    ```
    
2. The value of a parameter is used directly to perform an operation in the system
    
    ```markdown
    http://foo.bar/changepassword?user=someuser
    ```
    
3. The value of a parameter is used directly to retrieve a file system resource
    
    ```markdown
    http://foo.bar/showImage?img=img00011
    ```
    
4. The value of a parameter is used directly to access application functionality
    
    ```markdown
    http://foo.bar/accessPage?menuitem=12
    ```
    

# Testing for IDOR - ( Manual-Method ):

## **Base Steps:**

```markdown
1. Create two accounts if possible or else enumerate users first. 
2. Check if the endpoint is private or public and does it contains any kind of id param.
3. Try changing the param value to some other user and see if does anything to their account.
4. Done !!
```

## Testcases for IDOR Vulnerabilities

### 1) **Add IDs to requests that don’t have them:**
| Instead of this  | Try this  |
|------------------|-----------|
| `GET /api/MyPictureList`  | `GET /api/MyPictureList?user_id=<other_user_id>`  |

**Pro Tip:**  
You can find parameter names to try by deleting or editing other objects and seeing the parameter names used.

---

### 2) **Try replacing parameter names:**
| Instead of this  | Try this  |
|------------------|-----------|
| `GET /api/albums?album_id=<album_id>` | `GET /api/albums?account_id=<account_id>` |

**Pro Tip:**  
There is a Burp extension called *Paramalyzer* which will help with this by remembering all the parameters you have passed to a host.

---

### 3) **Supply multiple values for the same parameter:**
| Instead of this  | Try this  |
|------------------|-----------|
| `GET /api/account?id=<your_account_id>` | `GET /api/account?id=<your_account_id>&id=<admin_account_id>` |

**Pro Tip:**  
This is known as HTTP parameter pollution. Something like this might give you access to another user's data, such as an admin’s account.

---

### 4) **Change the HTTP request method:**
| Instead of this  | Try this  |
|------------------|-----------|
| `POST /api/account?id=<your_account_id>` | `PUT /api/account?id=<your_account_id>` |

**Pro Tip:**  
Try switching POST and PUT and see if you can upload something to another user’s profile. For RESTful services, try changing GET to POST/PUT/DELETE to discover create/update/delete actions.

---

### 5) **Change the request’s content type:**
| Instead of this  | Try this  |
|------------------|-----------|
| `POST /api/chat/join/123`  
  `Content-type: application/xml`  
  `<user>test</user>` | `POST /api/chat/join/123`  
  `Content-type: application/json`  
  `{“user”: “test”}` |

**Pro Tip:**  
Access controls may be inconsistently implemented across different content types. Don’t forget to try alternative and less common values like text/xml, text/x-json, and similar.

---

### 6) **Try changing the requested file type:**
| Instead of this  | Try this  |
|------------------|-----------|
| `GET /user_data/2341`  | `GET /user_data/2341.json`  |

**Pro Tip:**  
Experiment by appending different file extensions (e.g. .json, .xml, .config) to the end of requests that reference a document.

---

### 7) **Does the app ask for non-numeric IDs? Use numeric IDs instead:**
| Instead of this  | Try this  |
|------------------|-----------|
| `username=user1`  | `username=1234`  |
| `account_id=7541A92F-0101-4D1E-BBB0-EB5032FE1686`  | `account_id=5678`  |
| `album_id=MyPictures`  | `album_id=12`  |

**Pro Tip:**  
There may be multiple ways of referencing objects in the database and the application only has access controls on one. Try numeric IDs anywhere non-numeric IDs are accepted.

---

### 8) **Try using an array:**
| Instead of this  | Try this  |
|------------------|-----------|
| `{"id":19}`  | `{"id":[19]}`  |

**Pro Tip:**  
If a regular ID replacement isn’t working, try wrapping the ID in an array and see if that does the trick.

---

### 9) **Wildcard ID:**
| Instead of this  | Try this  |
|------------------|-----------|
| `GET /api/users/<user_id>/`  | `GET /api/users/*`  |

**Pro Tip:**  
These can be very exciting bugs to find in the wild and are so simple. Try replacing an ID with a wildcard. You might get lucky!

---

### 10) **Pay attention to new features:**
| Instead of this  | Try this  |
|------------------|-----------|
| `/api/CharityEventFeb2021/user/pp/<ID>`  | N/A  |

**Pro Tip:**  
If you stumble upon a newly added feature within the web app, such as the ability to upload a profile picture for an upcoming charity event, check if access control is properly enforced for this new feature.


# Testing For IDOR - ( Automated Method ):

[Automating BURP to find IDORs](https://medium.com/cyberverse/automating-burp-to-find-idors-2b3dbe9fa0b8)

The "IDOR IN" Scanner tool** works by systematically scanning a target web application and examining various endpoints, parameters, and data access points to identify potential IDOR vulnerabilities. 
It leverages techniques such as parameter fuzzing, payload injection, and response analysis to detect signs of insecure direct object references.

[Finding Broken Access Controls](https://threat.tevora.com/finding-broken-access-controls/)

[PimpMyBurp #1 - PwnFox + Autorize: The perfect combo to find IDOR - Global Bug Bounty Platform](https://blog.yeswehack.com/yeswerhackers/pimpmyburp-pwnfox-autorize-find-idor/)

[Automating BURP to find IDORs](https://medium.com/cyberverse/automating-burp-to-find-idors-2b3dbe9fa0b8)

# Reference:

[Everything You Need to Know About IDOR (Insecure Direct Object References)](https://medium.com/@aysebilgegunduz/everything-you-need-to-know-about-idor-insecure-direct-object-references-375f83e03a87)

[Finding more IDORs - Tips and Tricks | Aon](https://www.aon.com/cyber-solutions/aon_cyber_labs/finding-more-idors-tips-and-tricks/)

[KathanP19/HowToHunt](https://github.com/KathanP19/HowToHunt/blob/master/IDOR/IDOR-Old.md)

[Learn about Insecure Object Reference (IDOR) | BugBountyHunter.com](https://www.bugbountyhunter.com/vulnerability/?type=idor)

[WSTG - v4.2](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References.html)

[IDOR (Insecure Direct Object Reference)](https://notes.mufaddal.info/web/idor)

[What I learnt from reading 220* IDOR bug reports.](https://medium.com/@Sm9l/what-i-learnt-from-reading-220-idor-bug-reports-6efbea44db7)

### Medium:

[Full account takeover worth $1000 Think out of the box](https://mokhansec.medium.com/full-account-takeover-worth-1000-think-out-of-the-box-808f0bdd8ac7)

[All About Getting First Bounty with IDOR](https://infosecwriteups.com/all-about-getting-first-bounty-with-idor-849db2828c8)

[](https://codeburst.io/hunting-insecure-direct-object-reference-vulnerabilities-for-fun-and-profit-part-1-f338c6a52782)

[IDOR that allowed me to takeover any users account.](https://vedanttekale20.medium.com/idor-that-allowed-me-to-takeover-any-users-account-129e55871d8)

[All About IDOR Attacks](https://betterprogramming.pub/all-about-idor-attacks-64c4203b518e)

[Access developer tasks list of any of Facebook Application (GraphQL IDOR)](https://amineaboud.medium.com/access-developer-tasks-list-of-any-of-facebook-application-graphql-idor-62307c5e5b34)
