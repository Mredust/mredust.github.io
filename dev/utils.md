# JwtUtil

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
    public static int verifyToken(Claims claims) {
        if (claims == null) {
            return -1;
        }
        try {
            boolean flag = claims.getExpiration().before(new Date());
            if (flag) {
                return 0;
            } else {
                return claims.getExpiration().getTime() - CURRENT_TIME < EXPIRE_SOON_TIME ? 1 : 2;
            }
        } catch (ExpiredJwtException ex) {
            return 0;
        } catch (Exception e) {
            return -1;
        }
    }
    
    /**
     * 解析oken
     *
     * @param token
     * @return Claims
     */
    public static Claims parseToken(String token) {
        try {
            Claims claimsBody = getClaimsBody(token);
            if (verifyToken(claimsBody) > 0) {
                return claimsBody;
            } else {
                return null;
            }
        } catch (Exception e) {
            return null;
        }
    }
}

```





# CaptchaUtil

```java
import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.geom.AffineTransform;
import java.awt.image.BufferedImage;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.Random;

/**
 * 验证码工具类
 *
 * @author Mredust
 */
public class CaptchaUtil {
    private CaptchaUtil() {
    }
    
    private static final String VERIFY_CODES = "123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    private static final Random random = new Random();
    
    /**
     * @param width        图片宽
     * @param height       图片高
     * @param outputStream 输出流
     * @param verifySize   验证码长度
     * @param captchaData  验证码字符源
     * @return 验证码
     * @throws IOException
     */
    public static String outputVerifyImage(int width, int height, OutputStream outputStream, int verifySize, String captchaData) throws IOException {
        String verifyCode = generateVerifyCode(verifySize, captchaData);
        outputImage(width, height, outputStream, verifyCode);
        return verifyCode;
    }
    
    /**
     * 使用指定源生成验证码
     *
     * @param verifySize 验证码长度
     * @param sources    验证码字符源
     * @return 验证码
     */
    private static String generateVerifyCode(int verifySize, String sources) {
        // 未设定展示源的字码，赋默认值大写字母+数字
        sources = (sources == null || sources.isEmpty()) ? VERIFY_CODES : sources;
        int codesLen = sources.length();
        Random rand = new Random(System.currentTimeMillis());
        StringBuilder verifyCode = new StringBuilder(verifySize);
        for (int i = 0; i < verifySize; i++) {
            verifyCode.append(sources.charAt(rand.nextInt(codesLen - 1)));
        }
        return verifyCode.toString();
    }
    
    /**
     * 输出指定验证码图片流
     *
     * @param width
     * @param height
     * @param outputStream
     * @param code
     * @throws IOException
     */
    private static void outputImage(int width, int height, OutputStream outputStream, String code) throws IOException {
        int verifySize = code.length();
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
        Graphics2D graphics = image.createGraphics();
        graphics.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
        
        // 绘制背景
        Color color = drawBackground(width, height, graphics);
        
        // 绘制干扰线
        drawInterfereLine(width, height, graphics);
        
        // 添加噪点
        addNoise(width, height, image);
        
        // 添加图片扭曲
        shear(width, height, graphics, color);
        
        // 绘制验证码
        drawCode(width, height, code, graphics, verifySize);
        
        graphics.dispose();
        ImageIO.write(image, "jpg", outputStream);
    }
    
    /**
     * 绘制背景
     *
     * @param width
     * @param height
     * @param graphics2D
     * @return
     */
    private static Color drawBackground(int width, int height, Graphics2D graphics2D) {
        Color[] colors = new Color[]{Color.WHITE, Color.CYAN, Color.GRAY, Color.LIGHT_GRAY, Color.MAGENTA, Color.ORANGE, Color.PINK, Color.YELLOW};
        List<Color> colorList = Arrays.asList(colors);
        Collections.shuffle(colorList);
        
        graphics2D.setColor(Color.GRAY);
        graphics2D.fillRect(0, 0, width, height);
        Color color = getRandomColor(200, 250);
        graphics2D.setColor(color);
        graphics2D.fillRect(0, 2, width, height - 4);
        return color;
    }
    
    /**
     * 绘制干扰线
     *
     * @param width
     * @param height
     * @param graphics2D
     */
    private static void drawInterfereLine(int width, int height, Graphics2D graphics2D) {
        graphics2D.setColor(getRandomColor(160, 200));
        for (int i = 0; i < 2; i++) { // 难度：2条
            int x = random.nextInt(width - 1);
            int y = random.nextInt(height - 1);
            int xl = random.nextInt(6) + 1;
            int yl = random.nextInt(12) + 1;
            graphics2D.drawLine(x, y, x + xl + 40, y + yl + 20);
        }
    }
    
    /**
     * 添加噪点
     *
     * @param width
     * @param height
     * @param image
     */
    private static void addNoise(int width, int height, BufferedImage image) {
        float yawpRate = 0.01f; // 难度：0.01f
        int area = (int) (yawpRate * width * height);
        for (int i = 0; i < area; i++) {
            int x = random.nextInt(width);
            int y = random.nextInt(height);
            int rgb = getRandomColor(0, 255).getRGB();
            image.setRGB(x, y, rgb);
        }
    }
    
    /**
     * 扭曲方法
     *
     * @param graphics
     * @param width
     * @param height
     * @param color
     */
    private static void shear(int width, int height, Graphics graphics, Color color) {
        shearX(width, height, graphics, color);
        shearY(width, height, graphics, color);
    }
    
    /**
     * x轴扭曲
     *
     * @param graphics
     * @param width
     * @param height
     * @param color
     */
    private static void shearX(int width, int height, Graphics graphics, Color color) {
        int period = random.nextInt(2);
        int phase = random.nextInt(2);
        for (int i = 0; i < height; i++) {
            double d = period / 2.0 * Math.sin((double) i / (double) period + (6.2831853071795862D * phase));
            graphics.copyArea(0, i, width, 1, (int) d, 0);
            graphics.setColor(color);
            graphics.drawLine((int) d, i, 0, i);
            graphics.drawLine((int) d + width, i, width, i);
        }
    }
    
    /**
     * y轴扭曲
     *
     * @param graphics
     * @param width
     * @param height
     * @param color
     */
    private static void shearY(int width, int height, Graphics graphics, Color color) {
        int period = random.nextInt(40) + 10;
        int phase = 7;
        for (int i = 0; i < width; i++) {
            double d = period / 2.0 * Math.sin((double) i / (double) period + (6.2831853071795862D * phase) / 20.0);
            graphics.copyArea(i, 0, 1, height, 0, (int) d);
            graphics.setColor(color);
            graphics.drawLine(i, (int) d, i, 0);
            graphics.drawLine(i, (int) d + height, i, height);
        }
    }
    
    
    /**
     * 绘制验证码
     *
     * @param width
     * @param height
     * @param code
     * @param graphics2D
     * @param verifySize
     */
    private static void drawCode(int width, int height, String code, Graphics2D graphics2D, int verifySize) {
        graphics2D.setColor(getRandomColor(100, 160));
        int fontSize = height - 4;
        Font font = new Font("Algerian", Font.ITALIC, fontSize);
        graphics2D.setFont(font);
        char[] chars = code.toCharArray();
        for (int i = 0; i < verifySize; i++) {
            AffineTransform affine = new AffineTransform();
            affine.setToRotation(Math.PI / 4 * random.nextDouble() * (random.nextBoolean() ? 1 : -1), (((double) width / verifySize) * i) + ((double) fontSize / 2), (double) height / 2);
            graphics2D.setTransform(affine);
            graphics2D.drawChars(chars, i, 1, ((width - 10) / verifySize) * i + 5, height / 2 + fontSize / 2 - 10);
        }
    }
    
    /**
     * 随机颜色
     *
     * @param fc
     * @param bc
     * @return
     */
    private static Color getRandomColor(int fc, int bc) {
        fc = Math.min(fc, 255);
        bc = Math.min(bc, 255);
        int r = fc + random.nextInt(bc - fc);
        int g = fc + random.nextInt(bc - fc);
        int b = fc + random.nextInt(bc - fc);
        return new Color(r, g, b);
    }
}


```







# CodeGenerator

```java
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;
import java.util.Scanner;


/**
 * <p>
 * 依赖包：
 * <p>
 * <dependency>
 * <groupId>com.baomidou</groupId>
 * <artifactId>mybatis-plus-generator</artifactId>
 * <version>3.4.1</version>
 * </dependency>
 * </p>
 * <p>
 * <dependency>
 * <groupId>org.apache.velocity</groupId>
 * <artifactId>velocity-engine-core</artifactId>
 * <version>2.3</version>
 * </dependency>
 * </p>
 * <p>
 *<dependency>
 *<groupId>org.mybatis.spring.boot</groupId>
 *<artifactId>mybatis-spring-boot-starter</artifactId>
 *<version>2.3.1</version>
 *</dependency>
 *</p>
 * <p>
 * <dependency>
 * <groupId>com.baomidou</groupId>
 * <artifactId>mybatis-plus-boot-starter</artifactId>
 * <version>3.4.3</version>
 * </dependency>
 * </p>
 *
 * @author Mredust
 * @Description 代码生成器
 */
public class CodeGenerator {
    public static void main(String[] args) {
        CodeGenerator codeGenerator = new CodeGenerator();
        codeGenerator.run();
        System.out.println("代码生成成功！");
    }
 
    // 数据库配置
    private String driverName = "com.mysql.cj.jdbc.Driver";
    private String ip = "localhost:3306";
    private String database = "javaweb";
    private String username = "root";
    private String password = "motianze4";
    private String conditions = "?serverTimezone=UTC&useUnicode=true&useSSL=false&characterEncoding=utf8";
    private String url = "jdbc:mysql://" + ip + "/" + database + conditions;
    
    // 全局配置
    private String AUTHOR = "Mredust";
    private static final String OUTPUT_DIR = System.getProperty("user.dir") + "/src/main/java";
    
    // 包名配置
    private String PARENT = "com.mredust";
    private static final String CONTROLLER = "controller";
    private static final String SERVICE = "service";
    private static final String SERVICE_IMPL = "service.impl";
    private static final String MAPPER = "mapper";
    private static final String XML = "mapper";
    private static final String ENTITY = "entity.pojos";
    
    // 模板配置
    private static final String CONTROLLER_TEMPLATE = "/templates/controller.java.vm";
    private static final String SERVICE_IMPL_TEMPLATE = "/templates/serviceImpl.java.vm";
    private static final String SERVICE_TEMPLATE = "/templates/service.java.vm";
    private static final String MAPPER_TEMPLATE = "/templates/mapper.java.vm";
    private static final String XML_TEMPLATE = "/templates/mapper.xml.vm";
    private static final String ENTITY_TEMPLATE = "/templates/entity.java.vm";
    
    // 策略配置
    private String[] tables = {"tb_user"};
    private String[] tablePrefix = {"tb_"};
    
    
    // 是否自定义配置
    public boolean flag() {
        Scanner sc = new Scanner(System.in);
        System.out.println("请选择是否自定义配置：0. 否   1. 是");
        return sc.nextInt() == 1;
    }
    
    public String scanner(String tip) {
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入" + tip + ":");
        if (sc.hasNext()) {
            String ipt = sc.next();
            if (ipt != null && !ipt.isEmpty()) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "!");
    }
    
    public void run() {
        // 1.获取代码生成器的对象
        AutoGenerator autoGenerator = new AutoGenerator();
        if (flag()) {
            // 自定义配置
            custom();
        }
        // 2.设置数据源
        autoGenerator.setDataSource(getDataSourceConfig());
        // 3.设置全局配置
        autoGenerator.setGlobalConfig(getGlobalConfig());
        // 4.设置包名相关配置
        PackageConfig packageInfo = getPackageConfig();
        autoGenerator.setPackageInfo(packageInfo);
        // 5.配置xml文件的输出路径
        autoGenerator.setCfg(getInjectionConfig(packageInfo));
        // 6.配置模板
        autoGenerator.setTemplate(getTemplateConfig());
        // 7.策略设置
        autoGenerator.setStrategy(getStrategyConfig());
        // 8.执行生成操作
        autoGenerator.execute();
    }
    
    
    private void custom() {
        this.ip = scanner("数据库服务器地址：");
        this.database = scanner("数据库名：");
        this.username = scanner("数据库用户名：");
        this.password = scanner("数据库密码：");
        this.PARENT = scanner("项目路径的包名(例如：com.demo)：");
        this.tables = scanner("表名，多个表名用英文逗号隔开").split(",");
        this.tablePrefix = scanner("表前缀，多个表前缀用英文逗号隔开").split(",");
    }
    
    
    private DataSourceConfig getDataSourceConfig() {
        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName(driverName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }
    
    private GlobalConfig getGlobalConfig() {
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setAuthor(AUTHOR);
        globalConfig.setOutputDir(OUTPUT_DIR);
        globalConfig.setOpen(false);
        globalConfig.setFileOverride(true);
        globalConfig.setControllerName("%sController");
        globalConfig.setServiceName("%sService");
        globalConfig.setServiceImplName("%sServiceImpl");
        globalConfig.setMapperName("%sMapper");
        globalConfig.setXmlName("%sMapper");
        globalConfig.setEntityName("%s");
        return globalConfig;
    }
    
    private PackageConfig getPackageConfig() {
        PackageConfig packageInfo = new PackageConfig();
        packageInfo.setParent(PARENT);
        packageInfo.setController(CONTROLLER);
        packageInfo.setService(SERVICE);
        packageInfo.setServiceImpl(SERVICE_IMPL);
        packageInfo.setMapper(MAPPER);
        packageInfo.setXml(XML);
        packageInfo.setEntity(ENTITY);
        return packageInfo;
    }
    
    private InjectionConfig getInjectionConfig(PackageConfig packageInfo) {
        InjectionConfig injectionConfig = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };
        ArrayList<FileOutConfig> fileOutConfigs = new ArrayList<>();
        fileOutConfigs.add(new FileOutConfig(XML_TEMPLATE) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                return String.format("%s/src/main/resources/%s/mapper/%sMapper.xml",
                        System.getProperty("user.dir"),
                        packageInfo.getParent().replace(".", "/"),
                        tableInfo.getEntityName());
            }
        });
        injectionConfig.setFileOutConfigList(fileOutConfigs);
        return injectionConfig;
    }
    
    private TemplateConfig getTemplateConfig() {
        TemplateConfig templateConfig = new TemplateConfig();
        templateConfig.setController(CONTROLLER_TEMPLATE);
        templateConfig.setServiceImpl(SERVICE_IMPL_TEMPLATE);
        templateConfig.setService(SERVICE_TEMPLATE);
        templateConfig.setMapper(MAPPER_TEMPLATE);
        templateConfig.setEntity(ENTITY_TEMPLATE);
        templateConfig.setXml(null);
        return templateConfig;
    }
    
    private StrategyConfig getStrategyConfig() {
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setColumnNaming(NamingStrategy.underline_to_camel);
        strategyConfig.setEntityTableFieldAnnotationEnable(true);
        strategyConfig.setRestControllerStyle(true);
        strategyConfig.setEntityLombokModel(true);
        strategyConfig.setInclude(tables);
        strategyConfig.setTablePrefix(tablePrefix);
        return strategyConfig;
    }
}
	
```







# Result

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

import static com.mredust.entity.result.ResultEnums.*;

/**
 * @author Mredust
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Result<T> implements Serializable {
    private Integer code;
    private String msg;
    private T data;
    
    private static <T> Result<T> of(ResultEnums enums) {
        return of(enums, enums.getMsg());
    }
    
    private static <T> Result<T> of(ResultEnums enums, String msg) {
        return of(enums, msg, null);
    }
    
    private static <T> Result<T> of(ResultEnums enums, T data) {
        return of(enums, enums.getMsg(), data);
    }
    
    private static <T> Result<T> of(ResultEnums enums, String msg, T data) {
        return new Result<>(enums.getCode(), msg, data);
    }
    
    /**
     * 操作成功结果集
     *
     * @return
     */
    public static Result<?> success() {
        return of(SUCCESS);
    }
    
    public static Result<?> success(String msg) {
        return of(SUCCESS, msg);
    }
    
    public static <T> Result<T> success(T data) {
        return of(SUCCESS, data);
    }
    
    public static <T> Result<T> success(String msg, T data) {
        return of(SUCCESS, msg, data);
    }
    
    public static Result<?> success(ResultEnums enums) {
        return of(enums);
    }
    
    public static Result<?> success(ResultEnums enums, String msg) {
        return of(enums, msg);
    }
    
    public static <T> Result<T> success(ResultEnums enums, T data) {
        return of(enums, data);
    }
    
    public static <T> Result<T> success(ResultEnums enums, String msg, T data) {
        return of(enums, msg, data);
    }
    
    /**
     * 操作失败结果集
     *
     * @return
     */
    public static Result<?> error() {
        return of(ERROR);
    }
    
    public static Result<?> error(String msg) {
        return of(ERROR, msg);
    }
    
    public static Result<?> error(ResultEnums enums) {
        return of(enums);
    }
    
    public static Result<?> error(ResultEnums enums, String msg) {
        return of(enums, msg);
    }
    
}

```





# ResultEnums

```java
import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor
public enum ResultEnums {
    SUCCESS(200, "操作成功"),
    ERROR(400, "操作失败"),
    UNAUTHORIZED(401, "未授权"),
    FORBIDDEN(403, "禁止访问"),
    NOT_FOUND(404, "资源不存在"),
    SYSTEM_ERROR(500, "系统错误");
    
    private final int code;
    private final String msg;
}

```

