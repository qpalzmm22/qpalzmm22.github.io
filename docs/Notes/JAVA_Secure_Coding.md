# JAVA Secure Coding Guide Note

**This Note is about ways to secure code JAVA against various attacks**

* Types of Secure Coding   
    * Input data verification
    * Security feauture
    * Time and State
    * Error handling
    * Code error
    * Capsulization
    * API Missuse

## SQLi

> Use of user's input as a query directly.

1. Use `prepared Statements` and `setString()`.
```java
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

```java
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

```java
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

- Ref: https://yongho1037.tistory.com/796

1. Filtering. Replace all the `\` in DN, and consider special characters`(=, +, <, >, #, ; \, etc)` as regular letter 

## CSRF(Cross-Site Request Forgery)

> Unauthorized requests done by attacker

1. Use Post Method?? => What if you can modify it by MITM(Man in the Middle) Attack using `burpsuite`.

## Path Traversal

1. Use `replaceAll()` to filter **(", \, /)**.
2. Check file path with `getAbsolutePath()`
3. Make document lists and get url by dictionary system. 

## HTTP Response Splitting

> Happens on `HTTP`. If the application allowd `CR`(%0d,\r) and `LF`(%0a,\n), HTTLP Response Splitting attack is possible. This known to be fixed in most of the modern JAVA EE Application servers.

- Ref: https://blog.detectify.com/2019/06/14/http-response-splitting-exploitations-and-mitigations/ 

