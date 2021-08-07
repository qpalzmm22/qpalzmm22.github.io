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
2. 
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

 - Why in path we need "-report"?

## HTTP Response Splitting

> Happens on `HTTP`. If the application allowd `CR`(%0d,\r) and `LF`(%0a,\n), HTTLP Response Splitting attack is possible. This known to be fixed in most of the modern JAVA EE Application servers.

Useful to understand HTTP Response Splitting 
[Ref](https://gracefulsecurity.com/http-header-injection/)

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

```JAVA
// Wrong
if (getCookie(USER_TYPE).equals(ADMIN_USER))
...
// Right
if (((String)request.getSession().getAttribute(USER_TYPE)).equals(ADMIN_USER))
...
```

## Authentification

> Missing Authentification on Critical Functions

1. Disable client from bypassing authentication
2. Important page must be re-authenticated.
    - Use verified to be safe libraries and frameworks like `OpenSSL` & `ESAPI` 

```JAVA
public void enforceAuthorization(Object key, Object runtimeParameter) throws org.owasp.esapi.errors.AccessControlException {
    boolean isAuthorized = false;
    try {
        AccessControlRule rule = (AccessControlRule)ruleMap.get(key);
       if (rule == null) {
            throw new AccessControlException("AccessControlRule was not found for key: " + key, "");
        }
        System.out.println("Enforcing Authorization Rule \"" + key + "\" Using class: "
        + rule.getClass().getCanonicalName());
        isAuthorized = rule.isAuthorized(runtimeParameter);
    } catch (Exception e) {
        throw new AccessControlException("An unhandled Exception was " + "caught,
        so we are recasting it as an " + "AccessControlException.", "", e);
    }
    if (!isAuthorized) {
        throw new AccessControlException("Access Denied for key: " + key + " runtimeParameter: " + runtimeParameter, "");
    }
}
```

## Improper Authorization

> SW not checking all the routes from users to access data.

1. Reduce `attack surface`.
2. Use frameworks like `JAAS`, `ESAPI` 

!!! note "example"
    This is an example of LDAP setting
    ```JAVA
    public void f(String sSingleId, int iFlag, String sServiceProvider, String sUid, String sPwd) {
        env.put(Context.PROVIDER_URL, sServiceProvider);
        env.put(Context.SECURITY_AUTHENTICATION, "simple"); // not "none" !!
        env.put(Context.SECURITY_PRINCIPAL, sUid);
        env.put(Context.SECURITY_CREDENTIALS, sPwd)
    }
    ```

The following example uses `ruleMap` 

```JAVA
public void enforceAuthorization(Object key, Object runtimeParameter) throws org.owasp.esapi.errors.AccessControlException {
    boolean isAuthorized = false;       
    try {
        AccessControlRule rule = (AccessControlRule)ruleMap.get(key);
        if (rule == null) 
            throw new AccessControlException("AccessControlRule was not found for key: " + key, "");
        System.out.println("Enforcing Authorization Rule \"" + key + "\" Using class: "+ rule.getClass().getCanonicalName());
        isAuthorized = rule.isAuthorized(runtimeParameter);
    } catch (Exception e) {
        throw new AccessControlException("An unhandled Exception was " + "caught, so we are recasting it as an " + "AccessControlException.", "", e);
    }
    if (!isAuthorized)
        throw new AccessControlException("Access Denied for key: " + key + " runtimeParameter: " + runtimeParameter, "");
}
```
## Improper Permission Assignment

> Assignment of read or write authority to unauthorized user.

1. Config. files and exec files and libs must be only read and executed by admin.
2. Important files like config. files must checked if others can access to it.

```JAVA
String cmd = "umask 77"; // umask 77 makes it rwx------
File file = new File("/home/report/report.txt");
...
Runtime.getRuntime().exec(cmd);

```

## Use of Broken Cryptographic algorithm

> Use of algorithms like `RC2, RC4, RC5, RC6, MD4, MD5, SHA1,DES`
> Key size should be long enough        

![1](safe_Secure_Coding)

Following code uses `RSA` + `OAEP`, which is known to be strongest cryptophically safe combination.


```JAVA
import java.io.IOException;
import java.sql.Connection;
import java.sql.Statement;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.security.Key;
import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.SecureRandom;
import java.security.Security;
import javax.crypto.Cipher;
/**
* Servlet implementation class SqlInjection
*/
public class Service extends HttpServlet
{
    private final String COMMAND_PARAM = "command";
    // Command Ӯဖ ὆
    private final String CHANGE_PASSWORD_CMD = "get_user_info";
    private final String USER_ID_PARM = "user_id";
    private final String PASSWORD_PARM = "password";
    private final String NEW_PASSWORD_PARM = "new_password";
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws
    ServletException, IOException{
        String command = request.getParameter("command");
        if (command.equals(CHANGE_PASSWORD_CMD)){
            String userId = request.getParameter(USER_ID_PARM);
            String password = request.getParameter(PASSWORD_PARM);
            String newPassword = request.getParameter(NEW_PASSWORD_PARM);
            ...
            SecureRandom random = new SecureRandom();
            KeyPairGenerator generator = KeyPairGenerator.getInstance("RSA");
            generator.initialize(2048, random);
            KeyPair pair = generator.generateKeyPair();
            Key pubKey = pair.getPublic();
            Key privKey = pair.getPrivate();
            Cipher cipher = Cipher.getInstance("RSA/ECB/OAEP");
            cipher.init(Cipher.ENCRYPT_MODE, pubKey, random);
            byte[] cipherText = cipher.doFinal(newPassword.getBytes());
            ChangeUserPassword(userId, String(cipherText));
        }
    ...
    }
...
}

```
## Missing Encryption of sensitive data

> Sending sensitive information without encryption

1. Encrypt all the sensitive infos when sending it across internet
2. Use Secure Cookies(HTTPS only).

## Hard coded Password

> Not good for passwords to be hard coded. It's better to be written in seperate file.

![Cipher Class usuage](https://m.blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=javaking75&logNo=220552245734)
package com.qpalzmm22.test;

Following code is how to use `AES` and `RSA` 

```JAVA
import java.io.UnsupportedEncodingException;
import java.security.InvalidKeyException;
import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.NoSuchAlgorithmException;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;

import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.spec.SecretKeySpec;

public class PractiveEncryption {
	public static void encryptWithAES(String encrypt_key, String plain_text) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, IllegalBlockSizeException, BadPaddingException, UnsupportedEncodingException {
		
		
		Cipher cipher = Cipher.getInstance("AES");
		
		SecretKeySpec secretKeySpec = new SecretKeySpec(encrypt_key.getBytes(),"AES");
		cipher.init(Cipher.ENCRYPT_MODE, secretKeySpec);
		
		byte[] encryptBytes = cipher.doFinal(plain_text.getBytes("UTF-8"));
		System.out.println(new String(encryptBytes));
		
		
		//=======================//
		
		cipher.init(Cipher.DECRYPT_MODE,secretKeySpec);
		byte[] decryptBytes = cipher.doFinal(encryptBytes);
		System.out.println(new String(decryptBytes));
	}
	
	public static void encryptWithRSA(String plain_text) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, IllegalBlockSizeException, BadPaddingException, UnsupportedEncodingException{
		
		KeyPairGenerator keypairgen = KeyPairGenerator.getInstance("RSA");
		KeyPair keyPair = keypairgen.generateKeyPair();
		RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();
		RSAPublicKey publicKey = (RSAPublicKey)keyPair.getPublic();
		
		Cipher cipher = Cipher.getInstance("RSA");
		cipher.init(Cipher.ENCRYPT_MODE, privateKey );
		
		byte[] encryptBytes = cipher.doFinal(plain_text.getBytes());
		System.out.println(new String(encryptBytes));
		
		cipher.init(Cipher.DECRYPT_MODE, publicKey);
		byte[] decryptBytes = cipher.doFinal(encryptBytes);
		System.out.println(new String(decryptBytes));
	}
	
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String encrypt_key = "happyProgrammer!"; // must be 16 letters long.
		String plain_text1 = "Hello World!";
		String plain_text2 = "I want to pass the test";
		
		try {
			encryptWithAES(encrypt_key, plain_text1);
		} catch (InvalidKeyException | NoSuchAlgorithmException | NoSuchPaddingException | IllegalBlockSizeException
				| BadPaddingException | UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		try {
			encryptWithRSA(plain_text2);
		} catch (InvalidKeyException | NoSuchAlgorithmException | NoSuchPaddingException | IllegalBlockSizeException
				| BadPaddingException | UnsupportedEncodingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```
## Not-So-Random

> use of `Math.random()` is not so random due to the lack of seed.

1. Use `java.util.Random` class instead
2. Use `SecureRandom()` for key generation

## Plaintext Storage of Password

> Saving password as is... ex)old passwd file in unix systems.

1. Using `RSA` with `OAEP`

## Hard Coded Keys

> Hard coded keys give away information about the encryption. Attacker may use this to brute force the code.

1. It is recommended to use `AES, ARIA, SEED, 3DES` for symmetric keys and `RSA` that's 2048 bit long for asymmnetric key algorithm. DO NOT USE `MD4, MD5, SHA1`
2. Encrypt the keys in diffrent file.

## Weak Passwords

> Weak Passwords cause user account to be vulnerable.

1. Check for the password and require better passwords from user

## Permanent Cookies
> External input deciding max age of cookies

1. System must check and require user to type stronger password.
2. Something like this...
```JAVA
Cookie c = new Cookie("sessionID", sessionID);
int t = Integer.parseInt(maxAge);
if (t > 3600){
    t = 3600;
}
c.setMaxAge(t);

```
3. Cookie.setMaxAge to negetive value.(only exists when browser is not shutdown).

## Use of One-way Hash Function without Salt && Hardcoded Salt

- Also, do not hard code the salt, but use random values using following code. 

> Without salt, has functions are weak against rainbow table.

```JAVA
SecureRandom prng = SecureRandom.getInstance("SHA256PRNG");
String randomNum = new Integer( prng.nextInt() ).toString();
digest.update(randomNum.getBytes());
```

```JAVA
// Instead of This...
import java.security.MessageDigest;

public btye[] getHash(String password) throws NoSuchAlgorithmException {
    MessageDigest digest = MessageDigest.getInstances("SHA-256");
    digest.reset();
    return input = digest.digest(password.getBytes("UTF-8"));
}

// Use this
public btye[] getHash(String password, byte[] salt) throws NoSuchAlgorithmException {
    MessageDigest digest = MessageDigest.getInstances("SHA-256");
    digest.reset();
    digest.update(salt);
    return input = digest.digest(password.getBytes("UTF-8"));
}
```

!!! note "PS"
    The **KeyPairGenerator** class is used to generate pairs of public and private keys. Key pair generators are constructed using the getInstance factory methods (static methods that return instances of a given class).
    A Key pair generator for a particular algorithm creates a public/private key pair that can be used with this algorithm. It also associates algorithm-specific parameters with each of the generated keys.

    There are two ways to generate a key pair: in an algorithm-independent manner, and in an algorithm-specific manner. The only difference between the two is the initialization of the object:

!!! note "PSS"
    Also interestingly, **KeyPairGenerator** automatically uses highest-priorty installed `secureRandom` as a source of randomness when `initialize()` fucntion is called with parameter of `AlgorithmParameterSpec`. That is not the case for **MessageDigest** classes

## No Integrity Check

> Executing or uploading files without checking the integrity of file.

1.`DNS lookup`

## Inappropriate Session Config.

> `SessionMAxInactiveInterval` shouldn't be -1

1. session.setMaxInactiveInterval(-1);
2. Also in xml
    <session-config>
    <session-timeout>-1</session-timeout>
    </session-config>

## Password Management Heap Inspection

> It's not safe to save infortant data in String class. They always reside in memory until garbage collector in JVM activates. 

1. No saving imfos like this `String str = new String(pass)`;
2. Use local String variable. It will disappear as soon as function disappears.

## Hard-Codede Username

> DO NOT hard-code login infos, it makes software management hard, or cause bug and of course, login info will be exposed when someone is accessible to code(duh).

1. Instead recieve it through parameter.
2. It's safer to `load log infos` and `receive the infos` with structered programming method.

## RSA Padding

> RSA must be used with Padding, must be used with `Cipher cipher = Cipher.getInstnaces("RSA/EBC/OAEPPadding")`. Not `"RSA/**none**/OAEPPadding"`.

(What is `EBC, CBC`)[https://ko.wikipedia.org/wiki/%EB%B8%94%EB%A1%9D_%EC%95%94%ED%98%B8_%EC%9A%B4%EC%9A%A9_%EB%B0%A9%EC%8B%9D]
 
OAEP
![!](https://en.wikipedia.org/wiki/File:Oaep-diagram-20080305.png)

## Anti CSRF Token


## Multiple Binds to Same Port

> Could be weak against `packet snipping`

1. `socket.setReuseAddress(false);`

# Insecurity due to State | Time

## TOUTOC(Time of check, Time of use)

> Parellel threads must be manged with multi-processing safe functions

1. Use `synchronized` modifier for the block to be synchronized with other threads.

EX : (notice `synchronized`)
```JAVA
public synchronized void run()
{
    File f = new File("Test_367.txt");
    if(f.exists())
    {
        f.delete();
    }
}
```
2. This goes same for variable that must be shared among other threads
```JAVA
synchronized(SYNC)
{
    name = hreq.getParameter("name");
    ...
}   
```

[about synchronization](https://www.daleseo.com/synchronization/)


## Infinite Loop

> Always put recursion in `if-else` statement. Always check if file is `symbolic link`. link when traversing directories with `isSymbolicLink()`

```JAVA
public String findFile(String rootDir, String fileName){
    String[] fileList = getSubFiles(rootDir);
    for(int fileIdx = 0; fileIdx < fileList.length; fileIdx++){
        if(isDirectory(fileList[fileIdx]) == true){
            if(isSymbolicLink(fileList[fileIdx] == false)){
                String foundPath = findFile(fileList[fileIdx], fileName);
                if(foundPath != null)
                    return fileList[fileIdx] + foundPath;
            }
        } else {
            if (fileList[fileIdx].equals(fileName) == true)
                return fileName;
        }
    }
}
```

Connection must be done like so because if we only allow `conn` not equal to null, 

```JAVA
public class SearchThread extends Thread{
    static private String jdbc_driver = "oracle.jdbc.driver.OracleDriver";
    static private String jdbc_url = "jdbc:orcle:Thin@123.234.33.22:1521:company"
    private Connection conn;
    static private String dbPasswd;
    private String seachItem;

    public SearchThread(String searchItem){
        this.searchItem = searchItem;
    }  
    public void run(){
        conn = DriverManeger.getConnection(jdbc_url, "123.234.33.22")
    }
}

public class ItemSearcher{
    public void searchItems(ArrayList<String> items){
        ArrayList<SearchThread> threads;
        for(int idx = 0; idx < items.length; idx++){
            SearchThread thread = new SearchThread(items.get(idx));
            threads.add(thread);
            thread.start();
        } 
    }
}
```

## Static Database Connection

> Do not use `Static` keyword for DB connection. This will cause race condition if it's called in multiple places.

```JAVA
// Unclear
public void run()
{
    // if(conn == NULL) <- not needed because we run it in seperate thread
    conn = DriverManager.getConnection(jdbc_url, "123.234.33.22", dbPassword);
}
```

## Race Condtion : Singleton Member Field

> servlet member fields are shared among other threads, so they may be exposed to unauthorized user.

1. Do Not save user input data's in Servlet field, but save in local variable
2. or use `new` to allocate the memory.

1. 
```JAVA
public class RaceCon extends javax.servlet.http.HttpServlet
{ 
// private String name; <= do not use this as memberd field
    protected void doPost(HttpServletRequest req, HttpServletResponse res) throws
ServletException, IOException
    {
        // Instead recieve it on local variable 
        String name = req.getParameter("name");
        if(name == null || "".equals(name)) return;
        ...
    }
    ...
}
---

2. 
```JAVA
String [] loginInfo = new String[2];
loginInfo[1] = request.getParameter(USER_ID_PARM);
loginInfo[0] = request.getParameter(PASSWORD_PARM);
```
Something like this...

## J2EE Bad Practices : Direct Use of Threads (Not in 47 )

> `J2EE` restrictes one from user threads in web applications. Instead, one must use defined framework.

1. No new `Thread(Runnable).start()`;

Safe code looks someting like this
```JAVA
public class runthread extends HttpServlet
{
    protected void doGet(HttpServletRequest request, HttpServlet Response response) throws ServletException, IOExcption
    {
        // New MyClass().main() 
        Runtime.getRuntime().exec("java AsyncClass");
    }
}

class AsyncClass
{
    public static void main(String args[])
    {
        // Process and store request stats

        System.err.println("do something");
    }
}
```

## Symbolic Name not Mapping to Correct Object

> Attackers could try to manipulate what the symbolic name is pointing.

1. Do not call classes by `Class.forName("...");`, but call by default way(use constructor), `new ...`
2. **Do not link** the important file but **read the file directly.**

Do not do this, but use the file directly
![1](img/symbolic_name_0.PNG)

## Double-checked Locking

> Double-checked locking does not work as intended. Method Synchonization is the most secure way to synchronize.

# Error handling

## Information exposure on error messages

> Users could obtain information about system by error messages.

1. Send minimum error messages and deal with exceptions inside the source code.
2. Be careful when using `printStackTrace()`
3. Do not include any system info in the error messages

## Detection of Error Condition without action

> A situation where the error was found but not handled correctly

1. Must do **something** in try-catch block
2. In Try-catch statment, one must make sure after try{}, we must set sensitive values to default values so that the users can't access the value.

## Improper Check for Unusual or Exceptional Conditions

> Do not use broad exceptions (Exception e ), but use specific exceptions

```JAVA
public void readFromFile(String fileName) throws FileNotFoundException,
IOException,MyException
{
    try
    {
    ...
    // check if filename is null 
    if ( fileName == NULL ) throw new MyException("ERROR");
    
    File myFile = new File(fileName);
    FileReader fr = new FileReader(myFile);
    ...
    
    } // catch for all possible exceptions
    catch (FileNotFoundException fe) {...}
    catch (IOException ie) {...}
}

```

Combine to the security rule above(**Detection of Error Condition without action**), one must consider all the possibilities and apply actions to each one of them.

## Strong Passwords

Secure Passwords Guide

1. Passwords should be 9 ~ 15 letters
2. At least one special character.
3. Do not have id as substring of password
4. Do not have same password as one before.

## Null Pointer Reference

> Not dereferencing an object without checking if the value is null.

It's simple yet easy to make mistakes
- should be applied when getting parameter value by `getParameter();`

## Improper Resouce Shutdown or Release

> `Open File Descriptos, heap memory, and sockets` not being closed after usuage.

1. do not close connection on `try` statment but use `finally` to close it

FOR EXAMPLE : 

```JAVA
public void processFile() throws SQLException
{
    Connection conn = null;
    String url = "jdbc:mysql://127.0.0.1/example?user=root&password=1234";
    try
    {
        Class.forName("com.mysql.jdbc.Driver");
        conn = DriverManager.getConnection(url);
    }
    catch (ClassNotFoundException e)
    {
        System.err.print("error");
    }
    catch (SQLException e)
    {
        System.err.print("error");
    }
    finally
    {
        ...
        conn.close();
    ...
```

This goes same for sockets

```JAVA
...
    finally
    {
        if(listenSocket != null)
        {
            listenSocket.close();
            listenSocket = null;
        }
        if(connectionSocket != null)
        {
            connectionSocket.close();
            connectionSocket = null;
        }
    }
```

## Call to Notify()

> `Notify` method is not clear on which thread to wake. Do not use this method.

1. Instead, use `lock1.lock()`, and `lock2.unlock()` with class variable `Lock lock1;`.

## One liners (not vul, but mistakes)

- Do not use `serialPersistentFileds` with `public` modifier, but with `private static final`.
- Use `thread.start()` instead of `thread.run()` for most cases. [details](https://www.tutorialspoint.com/difference-between-thread-start-and-thread-run-in-java)
- Do not override `synchronized method` by `asynchronized method`.
- Use `ServerThreadPool.getInstnace().alloc(), ServerThreadPool.getInstnace().free(this)` to allocate memory to thread.

# Encapsulation

## Exposure of Data Element to Wrong Session

> samse as [](). Check that one.

##

!!! note "Things to Study"
    1. XSS Injection vs. XSS Manipulation
    2. LDAP 
    3. unsafe reflection
    4. Integer overflow ex.2
    5. RuleMap
    6. Cipher class
       1. MessageDigest 
    7. Connection con = DriverManager.getConnection(url, "scott", "tiger"); pg.140
    8. MessageDigest class
    9. 16번 무결성  검사없는 코드 다운로드
    10. page 243 .why set max_length as local variable?
---


