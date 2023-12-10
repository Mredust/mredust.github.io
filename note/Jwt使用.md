## Jwt使用教程

### 1.设置header信息

​	默认header:

> ```java
>map.put{"alg":"HS256"}
> map.put("typ","JWT");
> ```

​	自定义header

```java
private static HashMap<String, Object> getHeader(Enum<SignatureAlgorithm> algorithmEnum) {
    HashMap<String, Object> headerMap = new HashMap<>();
    headerMap.put("alg", algorithmEnum);
    headerMap.put("typ", "JWT");
    return headerMap;
}
```





### 2.设置claims信息

​	参数说明：<span style="color:red;">(!!!自定义注册的声明会覆盖原有的标准声明)</span>

> ```tex
> iss: jwt签发者
> sub: jwt所面向的用户
> aud: 接收jwt的一方
> exp: jwt的过期时间
> nbf: 定义在什么时间之前，该jwt都是不可用的.
> iat: jwt的签发时间
> jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击
> ```

```java
private static HashMap<String, Object> getClaims(Integer account, String password) {
    HashMap<String, Object> claimsMap = new HashMap<>();
    claimsMap.put("iss", Mredust);
    return claimsMap;
}
```

​	自定义claims

```java
private static HashMap<String, Object> getClaims(Integer account, String password) {
    HashMap<String, Object> claimsMap = new HashMap<>();
    claimsMap.put("account", account);
    claimsMap.put("password", password);
    return claimsMap;
}
```





### 3.生成token令牌

​	如果claims已经设置下面相关参数则不用重复设置

> 1. setHeader:设置头部信息
> 2. <span style="color:red;">setClaims | addClaims </span>：覆盖原有注册 | 原有基础上添加注册
> 3. setId 设置 JWT 的唯一标识符
> 4. setIssuer: 设置发布者
> 5. setIssuedAt: 设置 JWT 的发布时间。
> 6. setSubject: 设置主题，通常为用户的唯一标识。
> 7. setAudience: 设置接收者，即预期的 JWT 使用者。
> 8. setExpiration: 设置过期时间，即 JWT 的有效期截止时间。
> 9. signWith:设置签名：通过签名算法和秘钥生成签名
> 10. compressWith:设置数据压缩方式
> 11. setHeaderParam: 设置头部参数，描述 JWT 的基本信息

​	

​	参数设置

```java
public static String getToken(Enum<SignatureAlgorithm> alg, Integer account, String password) {
    HashMap<String, Object> header = getHeader(alg);
    HashMap<String, Object> claims = getClaims(account, password);

    return Jwts.builder()
            .setHeader(header)
            .addClaims(claims)
            .setId(UUID.randomUUID().toString())
            .setIssuer("Mredust")
            .setIssuedAt(new Date(System.currentTimeMillis()))
            .setSubject("system")
            .setAudience("user")
            .setExpiration(new Date(System.currentTimeMillis() + 24 * 60 * 60 * 1000))
            .signWith(SignatureAlgorithm.HS512, generalKey())
            .compressWith(CompressionCodecs.GZIP)
            .compact();
}
```



### 4.源码

```java
import com.sun.org.apache.bcel.internal.classfile.Signature;
import io.jsonwebtoken.*;

import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;
import java.util.*;

/**
 * JWT工具类
 *
 * @Author: Mredust
 */
public class JwtUtils {
    private JwtUtils() {
    }
    
    // 默认加密方式
    private static final SignatureAlgorithm DEFAULT_ALG = SignatureAlgorithm.HS512;
    // 当前时间
    private static final long CURRENT_TIME = System.currentTimeMillis();
    // 过期时间(一天)
    private static final long EXPIRE_TIME = 24 * 60 * 60 * 1000;
    // 即将过期时间(30分钟)
    private static final long EXPIRE_SOON_TIME = 30 * 60 * 1000;
    // JWT_ID
    private static final String JWT_ID = UUID.randomUUID().toString();
    // 加密KEY
    private static final String SECRET_KEY = "M6#T4#a6@Z3@";
    
    /**
     * 生成Token
     *
     * @param claimsMap
     * @return Token
     */
    public static String getToken(HashMap<String, Object> claimsMap) {
        JwtBuilder builder = Jwts.builder();
        claimsMap.forEach((k, v) -> {
            builder.claim(k, v);
        });
        return builder
                .setHeaderParam("typ", "JWT")
                .setHeaderParam("alg", DEFAULT_ALG)
                .setId(JWT_ID)
                .setIssuer("Mredust")
                .setAudience("consumer")
                .setIssuedAt(new Date(CURRENT_TIME))
                .setExpiration(new Date(CURRENT_TIME + EXPIRE_TIME))
                .signWith(DEFAULT_ALG, generalKey())
                // .compressWith(CompressionCodecs.GZIP)
                .compact();
    }
    
    /**
     * 生成加密KEY
     *
     * @return SecretKey
     */
    private static SecretKey generalKey() {
        byte[] encodedKey = Base64.getEncoder().encode(SECRET_KEY.getBytes());
        return new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
    }
    
    
    private static Jws<Claims> getJws(String token) {
        return Jwts.parser().setSigningKey(generalKey()).parseClaimsJws(token);
    }
    
    /**
     * 获取Token的Header
     *
     * @param token
     * @return
     */
    private static JwsHeader getHeaderBody(String token) {
        return getJws(token).getHeader();
    }
    
    /**
     * 获取Token的Body
     *
     * @param token
     * @return
     */
    private static Claims getClaimsBody(String token) {
        return getJws(token).getBody();
    }
    
    /**
     * 获取Token的Signature
     *
     * @param token
     * @return
     */
    private static String getSignatureBody(String token) {
        return getJws(token).getSignature();
    }
    
    
    /**
     * 验证Token
     *
     * @param claims
     * @return -1: 无效 0: 过期 1: 即将过期 2: 正常
     */
    public static int verifyToken(String token) {
        try {
            Claims claims = getClaimsBody(token);
            if (claims.getExpiration().before(new Date())) {
                return 0;
            } else {
                return claims.getExpiration().getTime() - CURRENT_TIME < EXPIRE_SOON_TIME ? 1 : 2;
            }
        } catch (ExpiredJwtException ex) {
            return 0;
        } catch (JwtException e) {
            return -1;
        }
    }
    
    /**
     * 解析Token
     *
     * @param token
     * @return
     */
    public static Claims parseToken(String token) {
        try {
            if (verifyToken(token) > 0) {
                return getClaimsBody(token);
            } else {
                return null;
            }
        } catch (JwtException e) {
            return null;
        }
    }
}
```

