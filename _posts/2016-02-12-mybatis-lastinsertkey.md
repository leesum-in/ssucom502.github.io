---
layout: post
title: "MyBatis에서 Insert 된 Id 값 가져 올 때 겪은 시행착오"
date: 2016-02-12 15:46:00
categories: myBatis
---


MyBatis 에서 Insert 한 뒤에 키 값을 가져오려면


```xml
<insert id="insertSample" parameterType="sampleVo">
        INSERT INTO tb_sample (title, content) VALUES ( #{title}, #{content} )
        <selectKey resultType="int" keyProperty="sampleId" order="AFTER">
            SELECT LAST_INSERT_ID() 
        </selectKey>
</insert>
```

위와 같이 `insert` 태그 안에 `selectKey` 를 넣고 `order="AFTER"` 를 설정 해서 하면 된다. 
( `parameterType`은 Vo 객체로 하였다. )

Dao 코드 에서는 위 쿼리를 실행한 리턴 값이 `키값` 일 것이라 예상을 하고 리턴 했다. 

```java
public int insertSample(SampleVo sampleVo) {
        return sqlSessionTemplate.insert("sampleDao.insertSample", sampleVo);       
}
```

그리고 테스트 코드를 돌려 보았더니 내가 예상한 키값이 리턴이 되지 않고 자꾸 `1`이 리턴 되는 것이었다!!!
혹시나 해서 테스트DB 를 조회 해보니 Insert는 잘 수행 되었었다. 
분명 `SELECT LAST_INSERT_ID()` 를 제대로 사용하지 못해서 이런 것이라 생각하고 대삽질들의 향연을 펼쳤다. 

허무하게도 원인은 다른데에서 찾았다. (나의 무지에서 비롯된 삽질이었지만...) myBatis 는 아주 스마트하게도 내가 parameter로 던진 `sampleVo`객체의 `sampleId` 변수에 키값을 세팅해주고 있었다. 결국 수정된 Dao 코드는 다음과 같다.

```java
public int insertSample(SampleVo sampleVo) {
        sqlSessionTemplate.insert("sampleDao.insertSample", sampleVo);
        return sampleVo.getSampleId();
}
```

>결론 : 잘 알고 쓰자..
