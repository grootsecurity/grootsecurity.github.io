---
layout: single
author_profile: true

toc: true
toc_label: "목차"
toc_icon: "cog"
title: "OWASP TOP 10 - Broken Access Control"
---


---
layout: single
author_profile: true

toc: true
toc_label: "목차"
toc_icon: "cog"
title: "OWASP TOP 10 - Broken Access Control"
---


# Web Attack - Broken Access Control 1
This WEB Attack series deals with 10 web vulnerabilities and its attacks based on OWASP TOP 10.In order to build a mock hacker career, Web Attack technology is basic, so understand it exactly and use it in the incumbent or interview.

## Broken access control 이란?
** Broken Access Control ** is a web vulnerability that occurs when a web application fails to properly manage user access permissions.For example, even if it is called an authenticated user, if it is possible to access unlicensed data or functions, it generates an access control (Access Control).

In short, if the user is certified as a web service (we call this login), it can be called a broken access control if the user is able to take another accessible approach other than the inherent access authority.
<figure style="text-align:center;">
  <img src="../assets/images/access_control.png" width="700">
  <figcaption style="margin-top: 10px;">After certification, the user is authorized to access the resource.</figcaption>
</figure>


Let's look at the diagram below.

<figure style="text-align:center;">
  <img src="../assets/images/broken_access_control_white.png" width="700">
  <figcaption style="margin-top: 10px;">Examples of "wrong approach" control capable of accessing the administrator panel by a random user</figcaption>
</figure>

A random attacker is an external attacker and attacks the web server located mainly in the DMZ in the internal interior of the target./ADMIN paths that only the Admin user, the manager of the Sub Directory of the target web server, must be seen as an administrator (such as a developer, because of the continuing overtime, or because of the continued night work).Web -vulnerabilities occur by causing a broken access control (http 200) that can be viewed successfully (http 200) of the web server.

In short, it is understood that the stranger passing through the front door of our house is in and out.The wrong approach vulnerability caused by this wrong access control is called Broken Access Control.

## Types of Access Control 
Before we describe the Broken Access Control attack technique, let's look at the types of Access Control.The type of Access Control can be classified into three major permissions.

### Vertical Access Control
Vertical approach authority refers to access to users with vertical authority.Let's look at the diagram below.User A does not have access control permissions for Admin users with administrator authority, but on the contrary, ADMIN is accessible to data of users (A/B).Literally, it is an access control method according to the vertical (user type -normal user vs administrator user).

### Horizontal Access Control
Horizontal approach authority means access to users with horizontal authority. Let's look at the diagram below.Access is also limited between user A and user B, the same level user.That is, it is an access control method that prevents one user from accessing other users' resources.

<figure style="text-align:center;">
  <img src="../assets/images/access_control_type_white.png" width="500
  ">
  <figcaption style="margin-top: 10px;">The difference between vertical access control and horizontal access control</figcaption>
</figure>

Therefore, the vertical approach permission is a approach control method used for the purpose of management and supervision by allowing users with higher permissions to access data of subordinate authority, and horizontal approach permissions are mutual access among users with the same authority.By limiting, it is an access control method that protects data between users.

### Context-Dependent Access Control:

Finally, contextual dependent access control controls the approach according to the user's environment or situation.For example, allow users to access certain resources only in certain positions or only at certain times.For example, User A will not be able to use transfer function after a certain time after logging in.In other words, it is subordinate to the context of time and is conditionally limited.

<figure style="text-align:center;">
  <img src="../assets/images/context_control_white.png" width="500">
  <figcaption style="margin-top: 10px;">Contextual access access authority</figcaption>
</figure>


## Access Control Security Models
Following the type of access control, let's take a look at the access control security model.The access control model simply means how to grant and manage access control between the user and the resource.
### DAC (Discretionary Access Control)
DAC is a control model in which the owner of the file controls the access rights of the file directly.

> For example, user A can control access to files generated by user A.It gives user B to read the file created by user A to read the permissions, and gives user C a writing permissions.


### MAC (Mandatory Access Control)
MAC manages the system as a whole, and the user cannot change this permission.

> 예For access to confidential grade documents, only users with a confidential grade are possible, and users cannot change their security rating.

### RBAC (Role-Based Access Control)
With RBAC, access permission is granted according to the role of the user.

> For example, users who have a 'manager' role in the company IT department are granted all system settings permissions, and users who have a 'general user' are only granted data inquiry.In other words, access control is determined according to the role Role.

### ABAC (Attribute-Based Access Control)
With ABAC, access permissions are determined by attributes for users, resources and environments.

> For example, in the banking system, the user can only transfer accounts between 9 am and 5 pm on weekdays, and the user is rejected if he attempts to transfer accounts at 6 pm.

**True hacker? Wouldn't it be possible to hack if something well understood and used the above characteristics?**

## Discovering vulnerabilities of Access Control
Finding vulnerabilities is simple.Most mock hacking professional jackets are a whitebox testing with source code, so you can take a look at the source code responsible for the access control.Blackbox testing is also used to test the number of attacks in the table and test it one by one.

### Horizes Horizontal Access Control Finding
How does the current user A approach the user A resource? 
If so, can we access the resources of user B as a user A by approaching user A resources?

**Source code vulnerable to horizontal approach**
```
def access_resource(user_id, resource_id):
    user = get_user_by_id(user_id)
    resource = get_resource_by_id(resource_id)
    
    if user_id == resource.owner_id:
        return "successful access"
    else:
        return "no access"
```
Looking at the code above, only the user ID is checked to check the access to the resource.Therefore, it is possible to access the resources of other user B by accessing the resource of user A.

### Find vertical access control vulnerabilities
Is the current user A with ordinary permissions?
Can we access the administrator (ADMIN) authority?
If it is accessible, is the administrator's authority wrong (only user A accessible) or is it wrong RBAC?(Is all users with general authority accessible)

**Source code vulnerable to vertical approach**

```
class User:
    def __init__(self, name, role):
        self.name = name
        self.role = role

class Admin(User):
    def __init__(self, name):
        super().__init__(name, "admin")

def access_sensitive_data(user):
    if user.role == "admin":
        print("you are admin!")
    else:
        print("access denied")
```
Looking at the code above, it is granted administrator (Admin) authority by checking only the role of the user, the parameter.Therefore, if the Role of the user A is changed to admin through the forgery parameters as shown below, the user A approaches the SENSTIVE DATA as an admin.


```
POST /sensitive_data HTTP/2
Host: redraccoon.kr
Content-Length: 38

{"name": "A", "role": "admin"}
```

### Contextual dependency access control vulnerabilities
Can the current user A access only to specific resources only to certain time or Geolocation?
If so, can we approach different resources in that specific time?
Or, can we access that specific resources even in other time zones?

## Access Control Attack Technique Examples

### IDOR modulation
- Purpose: Resources without permission by changing URLs, IDs, etc.
- Attack example:

**HTTP Request**
```
GET /account?userId=<USER_ID> HTTP/2
Host: www.redraccoon.kr
```
**HTTP Response**
- 200 OK when successful: The request is successfully processed, and other user's profile information is exposed.
- Fail 403 forbidden: Rejection of other user profile access

**IDOR related LAB**:
- Mongo IOR:
https://pentesterlab.com/exercises/mongo_idor/course

- IDOR to Shell:
https://pentesterlab.com/exercises/idor_to_shell/course

### UsesUffixpatternmatch Vitalization vulnerable- (Spring Framework <5.3)
If the usesUffixPaternmatch setting is activated by default in the Spring framework of 5.3, the method mapped to `/adminURL 'can detour a URL filter by consistent with'/adminURL [.].
- Purpose: access to resources without permission after attempting a URL bypass through the URL's surfix pattern matching
- Attack example:

**HTTP Request**
```
GET /adminURL.Raccoon HTTP/2
Host: SpringApp
```

**HTTP Response**
- 200 OK when successful: Adminurl access by bypassing the Adminurl panel.
- Fail 403 forbidden: Unacceptable.

### Parameter Tampering 
- Purpose: Parameter manipulation and accessing without permissions
- Attack example:
For web servers that control access through Referer, you can manipulate the referer parameter to access high permissions.For example, the HTTP Request header includes an Admin URL in the Referer header, which can be forged as if the Admin permissions are requested.

**HTTP Request**
```
GET /admin/deleteUser HTTP/1.1
Host: redraccoon.kr
Referer: http://redraccoon.kr/admin
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Cookie: session=ABC123
Connection: close
```

- 200 OK when successful, Admin/deleteuser function access by bypassing the Admin Rights access.
- Fail 403 forbidden: Unacceptable.

**HTTP Response**
```
HTTP/1.1 200 OK
Date: Mon, 07 Jun 2024 12:05:00 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 200
Connection: close

<!DOCTYPE html>
<html>
<head>
  <title>Delete User</title>
</head>
<body>
  <h1>Delete User Page</h1>
  <p>User successfully deleted.</p>
</body>
</html>
```

**Hands-on Lab to practice broken access control via paramter tampering**:
- BurpSuite Academy Referer Parameter Tampering:
https://portswigger.net/web-security/access-control/lab-referer-based-access-control


## Broken Access Control Fix and Recommendations
1. URL and Link Security: In order to prevent IDOR (IDOR) attacks, it is necessary to be careful not to openly expose a link that can access sensitive data or functions through URL or links.In addition, only users with valid permissions should be able to access the value of the parameter or web request. 

2. Session tokens and anti-CSRF tokens use: Session tokens and anti-CSRF tokens are used to prevent client request alteration in advance to block access control attacks. 

3. Security header: blocks forged requests using Security Headers such as Content Security Policy (CSP) and X-Content-Type-Options.

## Broken Access Control Hands-on Lab
- [TryHackMe OWASP Broken Access Control](https://tryhackme.com/r/room/owaspbrokenaccesscontrol)
- [WebGoat Broken Access Control A1](https://github.com/WebGoat/WebGoat)
- [DVWA Brute Force](https://github.com/digininja/DVWA)

## Case stuy of web vulnerabilities using the actual Broken Access Control
1. **Accessing to Data Sources of any Facebook Business account via IDOR in GraphQL**: https://medium.com/@mukundbhuva/accessing-the-data-sources-of-any-facebook-business-account-via-idor-in-graphql-1fc963ad3ecd

2. **WordPress File Sharing Plugin IDOR Vulnerability**: https://www.recordedfuture.com/vulnerability-database/CVE-2023-4836
