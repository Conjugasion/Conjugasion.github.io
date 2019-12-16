---
title: Mybatis使用指南
tag: Mybatis
category: Mybatis
excerpt: 详细教程
cover: /image26.jpg
---
[教程链接](https://blog.csdn.net/hellozpc/article/details/80878563)

## 例程
<div style="text-align:center">
<img src="/Mybatis使用指南/image1.jpg">
</div>

### generatorConfig.xml自动生成接口类、model类和对应的Mapper.xml
```bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
 
<!-- 配置Run As Maven build : Goals 参数 : mybatis-generator:generate -Dmybatis.generator.overwrite=true -->
<!-- 配置 tableName,使用 Run As Maven build 生成 dao model 层 -->
<generatorConfiguration>
    <!-- 配置文件路径
    <properties url="${mybatis.generator.generatorConfig.properties}"/>-->
 
    <!--数据库驱动包路径 -->
    <!--<classPathEntry location="/Users/huangjun10/.m2/repository/mysql/mysql-connector-java/5.1.35/mysql-connector-java-5.1.35.jar"/>-->
    <classPathEntry location="D:\\Work\\repository\\mysql\\mysql-connector-java\\5.1.35\\mysql-connector-java-5.1.35.jar"/>

    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--关闭注释 -->
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
 
        <!--数据库连接信息 -->
        <tdfbcConnection driverClass="com.mysql.tdfbc.Driver" connectionURL="tdfbc:mysql://192.168.177.207:3306/city_activity" userId="city_activity_user"
                        password="city_activity_pwd123">
        </tdfbcConnection>
 
        <!--生成的model 包路径 -->
        <javaModelGenerator targetPackage="com.tdf.interactive.play.raisepacket.model" targetProject="src/main/java">
            <property name="enableSubPackages" value="ture"/>
            <property name="trimStrings" value="true"/>
            <property name="" value=""/>
        </javaModelGenerator>

        <!--生成xml mapper文件 路径 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources/spring-mybatis/raisepacket">
            <property name="enableSubPackages" value="ture"/>
        </sqlMapGenerator>
 
        <!-- 生成的Dao接口 的包路径 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.tdf.interactive.play.raisepacket.mapper" targetProject="src/main/java">
            <property name="enableSubPackages" value="ture"/>
        </javaClientGenerator>
 
        <!--对应数据库表名，多个表，请复制指定 -->

        <!--booking是自己添加的...-->
        <table tableName="booking" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>

        <table tableName="raisepacket_award_user" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="raisepacket_daily_sign" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="raisepacket_lottery_transaction" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="raisepacket_multiple_period_join" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="raisepacket_redpacket_transaction" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="raisepacket_score_transaction" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
        <table tableName="tdfinteractive_common_log" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false" selectByExampleQueryId="false"/>
    </context>
</generatorConfiguration>
```
### 接口类
```bash
package com.tdf.interactive.play.raisepacket.mapper;

import com.tdf.interactive.play.raisepacket.model.Booking;

public interface BookingMapper {
    int deleteByPrimaryKey(Long id);

    int insert(Booking record);

    int insertSelective(Booking record);

    Booking selectByPrimaryKey(Long id);

    int updateByPrimaryKeySelective(Booking record);

    int updateByPrimaryKey(Booking record);
}
```
### model类
```bash
package com.tdf.interactive.play.raisepacket.model;

import java.util.Date;

public class Booking {
    private Long id;

    private String bookingPin;

    private String cityId;

    private Date bookingTime;

    private Byte channelId;

    private Byte isDeleted;

    private Date createTime;

    private Date updateTime;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getBookingPin() {
        return bookingPin;
    }

    public void setBookingPin(String bookingPin) {
        this.bookingPin = bookingPin == null ? null : bookingPin.trim();
    }

    public String getCityId() {
        return cityId;
    }

    public void setCityId(String cityId) {
        this.cityId = cityId == null ? null : cityId.trim();
    }

    public Date getBookingTime() {
        return bookingTime;
    }

    public void setBookingTime(Date bookingTime) {
        this.bookingTime = bookingTime;
    }

    public Byte getChannelId() {
        return channelId;
    }

    public void setChannelId(Byte channelId) {
        this.channelId = channelId;
    }

    public Byte getIsDeleted() {
        return isDeleted;
    }

    public void setIsDeleted(Byte isDeleted) {
        this.isDeleted = isDeleted;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    public Date getUpdateTime() {
        return updateTime;
    }

    public void setUpdateTime(Date updateTime) {
        this.updateTime = updateTime;
    }
}
```
### Mapper.xml
```bash
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.tdf.interactive.play.raisepacket.mapper.BookingMapper" >
  <resultMap id="BaseResultMap" type="com.tdf.interactive.play.raisepacket.model.Booking" >
    <id column="id" property="id" tdfbcType="BIGINT" />
    <result column="booking_pin" property="bookingPin" tdfbcType="VARCHAR" />
    <result column="city_id" property="cityId" tdfbcType="VARCHAR" />
    <result column="booking_time" property="bookingTime" tdfbcType="TIMESTAMP" />
    <result column="channel_id" property="channelId" tdfbcType="TINYINT" />
    <result column="is_deleted" property="isDeleted" tdfbcType="TINYINT" />
    <result column="create_time" property="createTime" tdfbcType="TIMESTAMP" />
    <result column="update_time" property="updateTime" tdfbcType="TIMESTAMP" />
  </resultMap>
  <sql id="Base_Column_List" >
    id, booking_pin, city_id, booking_time, channel_id, is_deleted, create_time, update_time
  </sql>
  <select id="selectByPrimaryKey" resultMap="BaseResultMap" parameterType="java.lang.Long" >
    select 
    <include refid="Base_Column_List" />
    from booking
    where id = #{id,tdfbcType=BIGINT}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Long" >
    delete from booking
    where id = #{id,tdfbcType=BIGINT}
  </delete>
  <insert id="insert" parameterType="com.tdf.interactive.play.raisepacket.model.Booking" >
    insert into booking (id, booking_pin, city_id, 
      booking_time, channel_id, is_deleted, 
      create_time, update_time)
    values (#{id,tdfbcType=BIGINT}, #{bookingPin,tdfbcType=VARCHAR}, #{cityId,tdfbcType=VARCHAR}, 
      #{bookingTime,tdfbcType=TIMESTAMP}, #{channelId,tdfbcType=TINYINT}, #{isDeleted,tdfbcType=TINYINT}, 
      #{createTime,tdfbcType=TIMESTAMP}, #{updateTime,tdfbcType=TIMESTAMP})
  </insert>
  <insert id="insertSelective" parameterType="com.tdf.interactive.play.raisepacket.model.Booking" >
    insert into booking
    <trim prefix="(" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        id,
      </if>
      <if test="bookingPin != null" >
        booking_pin,
      </if>
      <if test="cityId != null" >
        city_id,
      </if>
      <if test="bookingTime != null" >
        booking_time,
      </if>
      <if test="channelId != null" >
        channel_id,
      </if>
      <if test="isDeleted != null" >
        is_deleted,
      </if>
      <if test="createTime != null" >
        create_time,
      </if>
      <if test="updateTime != null" >
        update_time,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        #{id,tdfbcType=BIGINT},
      </if>
      <if test="bookingPin != null" >
        #{bookingPin,tdfbcType=VARCHAR},
      </if>
      <if test="cityId != null" >
        #{cityId,tdfbcType=VARCHAR},
      </if>
      <if test="bookingTime != null" >
        #{bookingTime,tdfbcType=TIMESTAMP},
      </if>
      <if test="channelId != null" >
        #{channelId,tdfbcType=TINYINT},
      </if>
      <if test="isDeleted != null" >
        #{isDeleted,tdfbcType=TINYINT},
      </if>
      <if test="createTime != null" >
        #{createTime,tdfbcType=TIMESTAMP},
      </if>
      <if test="updateTime != null" >
        #{updateTime,tdfbcType=TIMESTAMP},
      </if>
    </trim>
  </insert>
  <update id="updateByPrimaryKeySelective" parameterType="com.tdf.interactive.play.raisepacket.model.Booking" >
    update booking
    <set >
      <if test="bookingPin != null" >
        booking_pin = #{bookingPin,tdfbcType=VARCHAR},
      </if>
      <if test="cityId != null" >
        city_id = #{cityId,tdfbcType=VARCHAR},
      </if>
      <if test="bookingTime != null" >
        booking_time = #{bookingTime,tdfbcType=TIMESTAMP},
      </if>
      <if test="channelId != null" >
        channel_id = #{channelId,tdfbcType=TINYINT},
      </if>
      <if test="isDeleted != null" >
        is_deleted = #{isDeleted,tdfbcType=TINYINT},
      </if>
      <if test="createTime != null" >
        create_time = #{createTime,tdfbcType=TIMESTAMP},
      </if>
      <if test="updateTime != null" >
        update_time = #{updateTime,tdfbcType=TIMESTAMP},
      </if>
    </set>
    where id = #{id,tdfbcType=BIGINT}
  </update>
  <update id="updateByPrimaryKey" parameterType="com.tdf.interactive.play.raisepacket.model.Booking" >
    update booking
    set booking_pin = #{bookingPin,tdfbcType=VARCHAR},
      city_id = #{cityId,tdfbcType=VARCHAR},
      booking_time = #{bookingTime,tdfbcType=TIMESTAMP},
      channel_id = #{channelId,tdfbcType=TINYINT},
      is_deleted = #{isDeleted,tdfbcType=TINYINT},
      create_time = #{createTime,tdfbcType=TIMESTAMP},
      update_time = #{updateTime,tdfbcType=TIMESTAMP}
    where id = #{id,tdfbcType=BIGINT}
  </update>
</mapper>
```
