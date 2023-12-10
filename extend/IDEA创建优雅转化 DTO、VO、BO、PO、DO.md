# 一、POJO 的定义

无规则简单的对象，在日常的代码分层中 pojo 会被分为VO、BO、 PO、 DTO

## 1.VO （View Object/Value Object） 表示层对象

- 前端展示的数据，在接口数据返回给前端的时候需要转成VO

- 个人理解使用场景，接口层服务中，将DTO转成VO,返回给前台

  

## 2.B0（Bussines Object）业务层对象

 - 主要在服务内部使用的业务对象

 - 可以包含多个对象，可以用于对象的聚合操作

 - 个人理解使用场景，在服务层服务中，由DTO转成BO然后进行业务处理后，转成DTO返回到接口层

   

## 3.PO（persistent object）持久对象

 - 出现位置为数据库数据，用来存储数据库提取的数据

 - 只存储数据，不包含数据操作

 - 个人理解使用场景，在数据库层中，获取的数据库数据存储到PO中，然后转为DTO返回到服务层中

   


## 4.DTO（Data Transfer Object）数据传输对象

 - 在服务间的调用中，传输的数据对象

 - 个人理解，DTO是可以存在于各层服务中（接口、服务、数据库等等）服务间的交互使用DTO来解耦

   


## 5.DO（domain object）领域实体对象

​	DO 现在主要有两个版本：

- ①阿里巴巴的开发手册中的定义，DO（ Data Object）这个等同于上面的PO


- ②DDD（Domain-Driven Design）领域驱动设计中，DO（Domain Object）这个等同于上面的BO






# 二、插件名称：Simple Object Copy

- 定义方法出入参
- 光标定位方法内，使用快捷键ALT+INSERT(WIN) 、 command + N(mac) ，或者右键鼠标选择Generate，弹出生成选项框后，选择genCopyMethod，代码就生成好了

**需要手写的代码**

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbueuDwcrguo7OUQ0HLibyicMJQwv9ASyYv7FryicslohNiaHX7rpI0iasbLXHg2VHU0XEH5tOdgDMEf9IRw/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

**一键生成后的展示**

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbueuDwcrguo7OUQ0HLibyicMJQzzyDoVdBmf8TqtzRHoHqWre8yzibicUYsJuyuic418H9DWrvYOhGxQZfg/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

**复杂对象转化展示（一键生成后的展示）**

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbueuDwcrguo7OUQ0HLibyicMJQ3YUCPxz4PFPyBrItqb2EwGVOtEnQunF1OadtlqphXwCLUsicEeDwQsw/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

**复杂对象转化源码示例：**

```java 
@Data
public class UserVO {
    private String name;
    private Date entryDate;
    private String userId;
    private List<RoleVO> roleList;
    private RoomVO room;
    public static UserVO convertToUserVO(UserDTO item) {
        if (item == null) {
            return null;
        }
        UserVO result = new UserVO();
        result.setName(item.getName());
        result.setEntryDate(item.getEntryDate());
        result.setUserId(item.getUserId());
        List<RoleDTO> roleList = item.getRoleList();
        if (roleList == null) {
            result.setRoleList(null);
        } else {
     result.setRoleList(roleList.stream().map(UserVO::convertToRoleVO).collect(Collectors.toList());
        }
        result.setRoom(convertToRoomVO(item.getRoom()));
        return result;
    }

    public static RoomVO convertToRoomVO(RoomDTO item) {
        if (item == null) {
            return null;
        }
        RoomVO result = new RoomVO();
        result.setRoomId(item.getRoomId());
        result.setBuildingId(item.getBuildingId());
        result.setRoomName();
        result.setBuildingName();
        return result;
    }

    public static RoleVO convertToRoleVO(RoleDTO item) {
        if (item == null) {
            return null;
        }
        RoleVO result = new RoleVO();
        result.setRoleId(item.getRoleId());
        result.setRoleName(item.getRoleName());
        result.setCreateTime(item.getCreateTime());
        return result;
    }
}
@Data
public class UserDTO {
    private String name;
    private Date entryDate;
    private String userId;
    private List<RoleDTO> roleList;
    private RoomDTO room;
}

@Data
public class RoleVO {
    private String roleId;
    private String roleName;
    private LocalDateTime createTime;
}

@Data
public class RoleDTO {
    private String roleId;
    private String roleName;
    private LocalDateTime createTime;
}

@Data
public class RoomVO {
    private String roomId;
    private String buildingId;
    private String roomName;
    private String buildingName;

}

@Data
public class RoomDTO {
    private String roomId;
    private String buildingId;
}
```

**无入侵市面上有很多类似的工具类，比较常用的有**

- Spring BeanUtils （copyProperties）
- Cglib  BeanCopier （copyProperties）
- Apache BeanUtils （copyProperties）
- Apache PropertyUtils （copyProperties）
- Dozer
- mapstruct
- JSON 序列化 再反序列化

这些工具，不仅要引入相应的依赖jar包,而且对代码有入侵，要调用对应得api方法才能进行转化，一旦遇到类型不一致，字段名稍有变动，就需要另写java代码补全字段，整体代码非常丑陋。

举例：

> ❝
>
> mapstruct
>
> ❞

同样的代码，，不仅要引入依赖、写如下**转化mapper，还要，在对应地方调用对应api(代码入侵验证)**，然而Simple Object Copy 只需要一键生成。

RoomDTO中不存在的roomName、buildingName还要mapstruct另写方法，很容易忽略。源实体中不存在的属性，没有提示，小心前端总是问为啥都是null。

在Simple Object Copy 插件代码生成后，不存在的字段也生成了空方法，直接编译提示补充，不容易忽略

需要手写的代码：

```java
@Mapper(componentModel = "spring",uses = {RoleVOMapper.class,RoomVOMapper.class})
public interface UserMapper {
    UserConverter INSTANCE = Mappers.getMapper(UserConverter.class);
    
    UserVO toUserVO(UserDTO userDTO);
}

@Mapper(componentModel = "spring")
public interface RoleMapper {
    RoleVO toRoleVO(RoleDTO roleDTO);
}

@Mapper(componentModel = "spring")
public interface RoomMapper {
    RoomVO toRoomVO(RoomDTO roomDTO);
}


public class Main {
    public static void main(String[] args) {
        UserDTO user = ;
        UserVO userVO = UserMapper.INSTANCE.toUserVO(user);
        userVO.getRoomVO().setRoomName("大厅1");
        userVO.getRoomVO().setBuildingName("尚德楼");
    }
}
```

> ❝
>
> BeanUtils
>
> ❞

性能稍差。

不支持复杂对象还是要写大量代码，代码字段不清晰不易理解，别人接手难。RoomDTO中不存在的roomName、buildingName还要BeanUtils另写方法，很容易忽略。源实体中不存在的属性，没有提示，小心前端总是问为啥都是null。

需要手写的代码

```java
@Data
public class UserVO {
    private String name;
    private Date entryDate;
    private String userId;
    private List<RoleVO> roleList;
    private RoomVO room;
    public static UserVO convertToUserVO(UserDTO item) {
        if (item == null) {
            return null;
        }
        UserVO result = new UserVO();
        BeanUtils.copyProperties(item,result);
        List<RoleDTO> roleList = item.getRoleList();
        if (roleList == null) {
            result.setRoleList(null);
        } else {
     result.setRoleList(roleList.stream().map(UserVO::convertToRoleVO).collect(Collectors.toList());
        }
        result.setRoom(convertToRoomVO(item.getRoom()));
        return result;
    }

    public static RoomVO convertToRoomVO(RoomDTO item) {
        if (item == null) {
            return null;
        }
        RoomVO result = new RoomVO();
        BeanUtils.copyProperties(item,result);
        
        result.setRoomName();
        result.setBuildingName();
        return result;
    }

    public static RoleVO convertToRoleVO(RoleDTO item) {
        if (item == null) {
            return null;
        }
        RoleVO result = new RoleVO();
        BeanUtils.copyProperties(item,result);
        return result;
    }
}
```





# 三、性能优势

相比上面的工具类，不是使用反射、就是是用代理、序列化操作。相比于纯正的set方法去转化，差距不是一个量级。此次不赘述。



# 四、灵活性、兼容性

跟上述工具类相比插件有很大优势，不再赘述，下面我们比较一下，我之前常用的idea插件generateO2O

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbueuDwcrguo7OUQ0HLibyicMJQzEL124kewE0jdpOj47hjDKI6OM7ibPNlqsibDm3KxKg9aHNBGibuhO3xg/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)

在此推荐其他一个我常用插件：generateAllSetter，搭配食用更佳，



# 五、下载

打开idea plugins，切market place 搜索：Simple Object Copy

![图片](https://mmbiz.qpic.cn/mmbiz_png/eQPyBffYbueuDwcrguo7OUQ0HLibyicMJQrv2esccWvok4Iq8xAXBWo1aQgmE07vbaQjTktLHIWA3UwicVw5VtOlQ/640?wx_fmt=png&tp=wxpic&wxfrom=5&wx_lazy=1&wx_co=1)
