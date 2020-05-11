---
title: Mysql排名函数
tag: [Sql]
category: Sql
excerpt: 取第几名的数据，RANK并列跳跃排名、DENSE_RANK并列连续排序、ROW_NUMBER连续排名
cover: /image6.jpg
---
**`RANK`**并列跳跃排名，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，跳跃到总共的排名。
**`DENSE_RANK`**DENSE_RANK并列连续排序，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，依然按照连续数字排名。
**`ROW_NUMBER`**连续排名，即使相同的值，依旧按照连续数字进行排名。

### 数据库表
```bash
/*
 Navicat Premium Data Transfer

 Source Server         : 小段的数据库
 Source Server Type    : MySQL
 Source Server Version : 50725
 Source Host           : 10.18.2.160:14000
 Source Schema         : djl

 Target Server Type    : MySQL
 Target Server Version : 50725
 File Encoding         : 65001

 Date: 20/11/2019 09:34:50
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for tdf_SHIPSIZE
-- ----------------------------
DROP TABLE IF EXISTS `tdf_SHIPSIZE`;
CREATE TABLE `tdf_SHIPSIZE`  (
  `COMPANY_CODE` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT NULL,
  `COMPANY_NAME` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT NULL,
  `AMOUNT` int(255) NULL DEFAULT NULL,
  `GRADE` int(255) NULL DEFAULT NULL
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_bin ROW_FORMAT = Compact;

-- ----------------------------
-- Records of tdf_SHIPSIZE
-- ----------------------------
INSERT INTO `tdf_SHIPSIZE` VALUES ('66011600', '天津中散船舶管理有限公司', 9, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53100000', '中国船舶燃料连云港有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62030000', '大连华昌船务有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62070000', '海洋石油（洋浦）船务有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08011500', '中海散货运输(香港)有限公司', 23, 6);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62000000', '大连中远海运油品运输有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66020000', '青岛远洋运输有限公司', 54, 8);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08010800', '天津中海华润航运有限公司', 13, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('080200000000', '中远海运能源运输股份有限公司', 116, 10);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53030000', '中燃航运（大连）有限责任公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('3100000011', '天津中散船舶管理有限公司', 9, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08260000', '大连中石油海运有限公司', 8, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53000000', '中国船舶燃料有限责任公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('88000000', '东方海外货柜航运有限公司', 99, 8);
INSERT INTO `tdf_SHIPSIZE` VALUES ('68930000', '上海浦海航运有限公司', 9, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('13000000', '中远海运（上海）有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53040000', '中国船舶燃料河北有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53020000', '中国船舶燃料大连有限公司', 5, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('71010000', 'COSCO SHIPPING (SINGAPORE) PTE LTD', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62210000', 'Pan Cosmos Shipping & Enterprises Co.,Limited', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('68281100', '上海国际轮渡有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08011200', '中海散货运输(上海)有限公司', 28, 6);
INSERT INTO `tdf_SHIPSIZE` VALUES ('63000000', '中远海运（厦门）有限公司', 11, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('65000000', '中远海运特种运输股份有限公司', 98, 8);
INSERT INTO `tdf_SHIPSIZE` VALUES ('65050000', '海南中远海运沥青运输有限公司', 17, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('11170000', '广州海运物资供应公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('01000000', '中远海运发展股份有限公司', 200, 10);
INSERT INTO `tdf_SHIPSIZE` VALUES ('99090000', '南京丝路达航运有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62090000', '深圳中远龙鹏液化气运输有限公司', 5, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08030101', '中国能源船舶管理有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('64000000', '中波轮船股份有限公司', 7, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('01170000', '中海集装箱运输(洋浦)有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('03090000', '中海海盛香港船务有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08020000', '上海中远海运油品运输有限公司', 16, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('68280000', '上海远洋运输有限公司', 219, 10);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08110000', '深圳市三鼎油运贸易有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('04000000', '中远海运船员管理有限公司', 44, 6);
INSERT INTO `tdf_SHIPSIZE` VALUES ('02000000', '中远海运客运有限公司', 10, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08010700', '神华中海航运有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('04220000', '上海时代航运有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('68280600', '上海远洋船舶管理有限公司', 24, 6);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66030000', '深圳远洋运输股份有限公司', 174, 10);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08270000', '台州中油海运有限公司', 6, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('99070000', '福建省海运集团有限责任公司', 11, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66000000', '中远海运散货运输有限公司', 254, 10);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66040000', '中远(香港)航运有限公司', 27, 6);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08040000', '华海石油运销有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08150000', '上海北海船务股份有限公司', 13, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08015100', '中海散运(香港)维利有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('62160000', '大连华洋船务有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08010000', '中海散货运输有限公司', 85, 8);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08010500', '广州京海航运有限公司', 12, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('19190600', '中国能源船舶管理有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53280000', '上海中燃船舶燃料有限公司', 6, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('01220000', '鑫海航运有限公司', 8, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53170000', '舟山中燃船舶燃料有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53140000', '南京中燃船舶燃料有限责任公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('64060000', '上海中波国际船舶管理有限公司', 6, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66010000', '中远散货运输有限公司', 82, 8);
INSERT INTO `tdf_SHIPSIZE` VALUES ('04100000', '中海液化气船舶管理（上海）有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08011100', '上海时代航运有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('68282100', '中日国际轮渡有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('72000000', '中远航务(英国)公司', 7, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('06090000', '广州振兴船务有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('11000000', '中石化中海船舶燃料供应有限公司', 7, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('05000000', '中海工业有限公司', 4, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08010300', '上海银桦航运有限公司', 1, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('03020100', '深圳市三鼎油运贸易有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('03020200', '广东海电船务有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('91020000', '海南港航拖轮有限公司', 8, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('66190000', '海南海盛航运有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('06160000', '广东海电船务有限公司', 2, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53190000', '中国船舶燃料广州有限公司', 7, 2);
INSERT INTO `tdf_SHIPSIZE` VALUES ('53090000', '中国船舶燃料青岛有限公司', 3, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('08100000', '中国矿运有限公司', 4, 0);
INSERT INTO `tdf_SHIPSIZE` VALUES ('91010000', '海南海峡航运股份有限公司', 19, 4);
INSERT INTO `tdf_SHIPSIZE` VALUES ('3300000005', '大连中远海运船舶管理有限公司', 2, 0);

SET FOREIGN_KEY_CHECKS = 1;
```
```bash
# 找Grade第二名
select * from tdf_SHIPSIZE where GRADE = (select max(GRADE) from 
(select * from tdf_SHIPSIZE where GRADE < (SELECT MAX(GRADE) from tdf_SHIPSIZE)) 
as temp)

# 找Grade第三名
select * from tdf_SHIPSIZE where GRADE = 
(select max(GRADE) from 
(select * from tdf_SHIPSIZE where GRADE < 
(select max(GRADE) from tdf_SHIPSIZE where GRADE = (select max(GRADE) from 
(select * from tdf_SHIPSIZE where GRADE < (SELECT MAX(GRADE) from tdf_SHIPSIZE)) 
as temp1)))
as temp2)

# RANK并列跳跃排名，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，跳跃到总共的排名。
# DENSE_RANK并列连续排序，相同的值保留重复名次，遇到下一个不同值时，依然按照连续数字排名。
# ROW_NUMBER连续排名，即使相同的值，依旧按照连续数字进行排名。
SELECT GRADE,RANK() OVER (ORDER BY GRADE DESC) ranking FROM tdf_SHIPSIZE;
SELECT GRADE,DENSE_RANK() OVER (ORDER BY GRADE DESC) ranking FROM tdf_SHIPSIZE;
SELECT GRADE,ROW_NUMBER() OVER (ORDER BY GRADE DESC) ranking FROM tdf_SHIPSIZE;

# 找Grade第二名
SELECT t1.* from tdf_SHIPSIZE t1 join 
(SELECT COMPANY_CODE,GRADE,DENSE_RANK() OVER (ORDER BY GRADE DESC) ranking 
FROM tdf_SHIPSIZE) t2 on t1.COMPANY_CODE = t2.COMPANY_CODE 
where t1.GRADE = t2.GRADE and t2.ranking = 2
```

