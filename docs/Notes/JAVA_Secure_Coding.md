# JAVA Secure Coding Guide Note

**This Note is about ways to secure code JAVA against various attacks**

* Types of Secure Coding   
    * Input data verification
    * Security feauture
    * Time and State
    * Error handling
    * Code error
    * Encapsulation
    * API Missuse

## SQLi

> Use of user's input as a query directly.

1. Use `prepared Statements` and `setString()`.
```JAVA
...
String query = "SELECT * FROM ? WHERE Name = ? ";
stmt = con.prepareStatement(query);
stmt.setString(1, tableName);
stmt.setString(2, name);
...
```
2. Use `makeSecureString()`
    1. strlen limit
    2. Keywords blacklisting
    3. use of [^\\p{Alnum}] \\ only use alphabets and digits
    4. Also limit keywords like `IF, CHAR, CONCAT, ASCII, UNION, @, exec, ;, SUBSTRING, BENCHMARK, MD5,SHA1, etc...`

### makeSecureString()
```JAVA
    private String makeSecureString(final String str, int maxLength)
{
    String secureStr = str.substring(0, maxLength);
    Matcher matcher = unsecuredCharPattern.matcher(secureStr);
    return matcher.replaceAll("");
}

```

##  Improper Control of Resource Identifiers, Resource Injection

> Happens when user's input is directly used to identify socket port, file, etc. 

1. Use list(dictionary) to restrict input value to what it's supposed to be.


## XSS

> Availability of inserting script on page.

1. Switch  `<, >, &, "` to `&lt; &gt; &amp; &quot;` using `replaceall()`

2. use OWASP supported API, `ESAPI.encoder().encodeForHTMLAttriute();`

## Commnad Injection

> Use of user's input as part of  system commnad.

1. Must make dictionary so that the user's input doesn't directly go into the command.

## Unrestricted Uploading

> 1. Avalability of uploading script file. 2. Avalability to access them. 

1. Make upload directory outside of Web server document. (Different Domain)
2. Using whitelisting, restirct file types(regardless of capitalization of extention).
    1. File name must not consist `.*%00.*|.*%ZZ.*|.*;.*`, which are indicating `eol`. 
    2. Use `if(extractFileType(file) == EXECUTABLE_FILE_TYPE)` with exeption handling.
3. Block user from executing files directly. If possible, control the file authority.
4. Randomize file name with hash table

## Open Redirect

> Following code is weak against open redirect exploit. Cracker could redirect url and use `phishing` attack.     

```JAVA
...
String query = request.getQueryString();
if (query.contains("url"))
{
    String url = request.getParameter("url");
    response.sendRedirect(url);
}
...
```
1. Use whitelisting. Use the following `indexService()`, `contains` 

```JAVA
public IndexService()
{
    allowedUrls = new HashSet<String>();
    allowedUrls.add("http://www.site.com/subpage1.html");
    allowedUrls.add("http://www.site.com/subpage2.html");
    allowedUrls.add("http://www.site.com/subpage3.html");
...
}
...
if(allowedUrls.contains(shortcutInfo.url) == false)
{
    // Error 
    return;
}
```

## XQuery Injection
> !  Must study more..

1. use `bind...()` functions

```JAVA
String xqueryString = "doc('items.xml')/items/price/selling_price/'$itemId'";
...
String itemId = request.getParameter(ITEM_ID);
xqe.bindString(new QName("itemId"), itemId, null);
...

```

## XPath Injection

> 
>[What is XPath](https://velog.io/@mjhuh263/TIL-23-HTML-XPATH-%EB%AC%B8%EB%B2%95%EA%B3%BC-selenium%EC%97%90-XPATH-%EC%9D%B4%EC%9A%A9%ED%95%98%EA%B8%B0) 

1. Filter `", [, ], /, =, @, etc` and Query keywords
2. Use XQuery class. `XQuery xquery = new XQueryFactory().createXQuery(new File("dologin.xq"));`, `vars.put("loginID", name);`

## LDAP Injection


> LDAP : **(Lightweight Directory Access Protocol)**. Protocol for directory service. `ctx.search()` inside LDAP, so one must consider injections.

- LDAP Authentication Flow
![1](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlFwkV%2FbtqHT1ce8qb%2Fk6KfyzcwwF68hraSlFiWEK%2Fimg.png)

- LDAP Directory struction
![2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbub5ZE%2FbtqH0qB7Iwi%2Fm9aVKNNXYNnEt3sp6UxY0k%2Fimg.png)

### Relative references
- [https://yongho1037.tistory.com/796](https://yongho1037.tistory.com/796)

1. Filtering. Replace all the `\` in DN, and consider special characters`(=, +, <, >, #, ; \, (, ), etc)` as regular letter 


## LDAP Maniupulation

> Similar to LDAP Injection

1. Replace all the `\`s with `str.replaceAll("\\","")`


## CSRF(Cross-Site Request Forgery)

> Unauthorized requests done by attacker

1. Use Post Method?? => What if you can modify it by MITM(Man in the Middle) Attack using `burpsuite`.

## Path Traversal

1. Use `replaceAll()` to filter **(", \, /)**.
2. Check file path with `getAbsolutePath()`
3. Make document lists and get url by dictionary system. 

## HTTP Response Splitting

> Happens on `HTTP`. If the application allowd `CR`(%0d,\r) and `LF`(%0a,\n), HTTLP Response Splitting attack is possible. This known to be fixed in most of the modern JAVA EE Application servers.

### Relative references
- [https://blog.detectify.com/2019/06/14/http-response-splitting-exploitations-and-mitigations/](https://blog.detectify.com/2019/06/14/http-response-splitting-exploitations-and-mitigations/)
- [Cross-User Defacement](https://owasp.org/www-community/attacks/Cross-User_Defacement)
1. Must filter all the header values that are direclty obtained by user. Filter out `\n and \r`

## Integer Overflow

> When receiving value as an integer, if the value is greater than 2147483647, it goes negative (2's complement).

1. Make sure to check if the value is greater than 0
2. Also, make sure value doesn't exceed the maximum value of the data type.

## Reliance on Untrusted Inputs in a Security Decision

> Trusting that the hidden values or the header values wouldn't be manipulated by users. 

1. Store sensetive informations like user session information in the server and do a security checkup inside the server.
    1. Use session information instead of Cookie.
2. Design the program so that the program doesn't not depends on input values.

## JDO(JAVA Data Objects), Persistent API, mybatis Data Map  (SQLi)

> Another way to execute SQLs.
1. Use paramatized query (the one with `?`s)
2. Use [makeSecureString](#makesecurestring)

### Relative Reference
- [Presistent](https://gmlwjd9405.github.io/2018/12/25/difference-jdbc-jpa-mybatis.html)

## mybatis Data Map

> 
1. Include filter.
2. Do not use `($...$)` but use `#<...>#`

### Relative Reference
- [Difference of $$ and ##](https://logical-code.tistory.com/25) 
- [Mybatis](https://mybatis.org/mybatis-3/ko/sqlmap-xml.html)

## External Control of Sys. config.

1. DO NOT use external input as parameter of `Connection.setCatalog()`
2. Use whitelisting

When port is already in use, do not switch to new one 

- Secure Coding Example
```JAVA
if (command.equals(CHANGE_FTP_PORT))
    {
    ...
    String servicePortIndex = request.getParameter(PORT_INDEX_PARM);
    ...
    if(servicePortIndex == DEFAULT)
    {
        servicemanage.changeSerivcePort(FTP_SERVICE, DEFAULT_FTP_PORT);
    }
    else if(servicePortIndex == ALTERNATIVE)
    {
        servicemanage.changeSerivcePort(FTP_SERVICE, ALTERNATIVE_FTP_PORT);
    }
}
```

## XSS, DOM

> When `Document.write()` happens, it could be vulernable to `XSS`.

1. Encode the letters (`<, >, &, ", ', /`) to (`&lt;, &gt;, &amp;, &quot;, &#x27;, &#x2F;`). Use `StringEscapeUntils()` 
2.


```JAVA
<%
String eid = request.getParameter("eid");
String safeEID = ESAPI.encoder().encodeForHTMLAttribute(name); // insecure code doesn't contain this 
%>
...
Employee ID: <%= safeEID %>
...

```

## EVAL

> Watch what goes in to the `eval` parameter. Escape letters like (`<, >, &, (, ), ", '`)

1. Use ESAPI.encoder().encodeForJavaScript() 

## Process Control

> When the process loads library without absolute path, the attacker may change the environment variable and set it to what the attacker wishes.

1. Use absolute path.
2. Use hash table. => limit what the users can do...

## Unsafe Reflection

> Use of input as selection of class.


1. Use whitelist. Control the input.

## Download without Integrity check

> Remote download without checking the integrity of the file.

1. Sever : Encript the file with `private key`. And decript it with `public key` (It's in reverse order because everyone can acess it but noone can deform it.)
2. Check checksum first, then decrypt it and use the file.  
```JAVA
_download(checksumFilePath, checksumURL);
decrypt(checksumFilePath, publicKey);
_download(localPath, remoteURL);
return checkCheckSum(localPath, checksumFilePath);
```

## SQLi : Hibernate
> Similar to Hibernate : TopLink, CoCoBase.


![출처: https://tinkerbellbass.tistory.com/24](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F999ECC33599003FF337D83)

[참조](https://tinkerbellbass.tistory.com/24)

1. Use `setParameter()`

```JAVA
String idValue = props.getProperty("idLow");
if (idValue == null || "".equals(idValue)) idValue = "defaultID";
Query query = session.createSQLQuery("select h from Honey as h where h.id '=:idVal‘");
query.setParameter("idVal", idValue);   
```

## Reliance on Untrusted Inputs

> Believing values like cookies, env. variables and hidden field input values are inmanipulatable.

1. Save system status info in server(use session)
2. If data must be preserved in client side, encrypt it and do integrity check
3. Even after js does filtering do another one in server.

```JAVA
HttpSession ses = new HttpSession(true);
ses.putValue("user",username);
ses.putValue("authenticated","1");
```
- [ ] if(getCookie(USER_TYPE).equals(ADMIN_USER))
- [x] if(((String)request.getSession().getAttribute(USER_TYPE)).equals(ADMIN_USER))
---

!!! note
    1. XSS Injection vs. XSS Manipulation
    2. LDAP 
    3. unsafe reflection
    4. Integer overflow ex.2

!!! note
    helloworld

---

