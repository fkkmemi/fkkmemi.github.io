---
title: "Exception definition"
permalink: /docs/exception-definition/
excerpt: "예외처리 룰"
last_modified_at: 2018-04-25T10:40:00+09:00
redirect_from:
  - /theme-setup/
---

예외처리 작성법 by memi

{% include toc %}

# 개요

if else등의 컨디션 중괄호가 많아지면 가독성이 나빠진다.

가독성은 코드 유지보수에 중요한 역활을 하기때문에 최대한 else보다는 상단에서 예외처리를 해서 탭(tab) 개수를 줄인다.

# 일반

- 예외처리는 최상단에서 걸러낸다.
- else가 최대한 적게 나오게 구성한다.

# 사용 예

## 사용자 로그인

아이디가 'user'고 비밀번호가 '12345'가 맞으면 user정보를 전송하는 예

- case 1: 일반

```javascript
if(userId === 'user') {
    if(userPwd === '12345') {
        User.findOne(userId, (err, u) => {
            if(err) {
                res.send({ success: false, msg: err.message });
            }
            else {
                res.send({ success: true, d: u });
            }
        ));
    }
    else {
        res.send({ success: false, msg: 'pwd wrong' });
    }
}
else {
    res.send({ success: false, msg: 'id wrong' });
}
```

- case 2: return error 처리

```javascript
if(userId !== 'user') return res.send({ success: false, msg: 'id wrong' });
if(userPwd !== '12345') return res.send({ success: false, msg: 'pwd wrong' });

User.findOne(userId, (err, u) => {
    if(err) return res.send({ success: false, msg: err.message });
    res.send({ success: true, d: u });
));
```

> case 1,2는 같은 동작을 하지만 코드 라인이 18->7 로 줄었으며 tab depth는 최대 4에서 1로 줄었다.

## 디비 처리

아이디가 있으면 찾고 비밀번호를 '1111'로 변경 > 소속 그룹 이름을 'A'로 업데이트 > 변경된 계정정보를 읽음

- case 1: 일반

```javascript
User.findOne(userId, (err, u) => {
    if(err) {
        console.error('find err: ' + err.message);
    }
    else {
        if(u) {
            User.findOneAndUpdate({ _id: u._id }, { $set: { pwd: '1111' } }, { new: true, upsert: true } (err, u) => {
                if(err) {
                    console.error('user find err: ' + err.message);
                }
                else {
                    Group.findOneAndUpdate({ u_id: u._id }, { $set: { name: 'A' } }, { new: true, upsert: true } (err, g) => {
                        if(err) {
                            console.error('group find err: ' + err.message);
                        }
                        else {
                            User.findOne({ _id: g.u_id }, (err, u) => {
                                if(err) {
                                    console.error('user find err: ' + err.message);
                                }
                                else {
                                    console.log(u);
                                }
                            });
                        }
                    });
                }
            });
        }
        else {
            console.error('find not user');
        }
    }
});
```

- case 2: return err 처리

```javascript
User.findOne(userId, (err, u) => {
    if(err) return console.error('find err: ' + err.message);
    if(!u) return console.error('find not user');
    User.findOneAndUpdate({ _id: u._id }, { $set: { pwd: '1111' } }, { new: true, upsert: true } (err, u) => {
        if(err) return console.error('user find err: ' + err.message);
        Group.findOneAndUpdate({ u_id: u._id }, { $set: { name: 'A' } }, { new: true, upsert: true } (err, g) => {
            if(err) return console.error('group find err: ' + err.message);
            User.findOne({ _id: g.u_id }, (err, u) => {
                if(err) return console.error('user find err: ' + err.message);
                console.log(u);
            });
        });
    });
});
```

> return으로 나가줘서 코드 라인은 줄었지만 전형적인 콜백지옥의 모습이다.

- case 3: 콜백지옥 해소 by promise

```javascript
User.findOne(userId)
    .then((u) => {
        if(!u) throw new Error('find not user');
        return User.findOneAndUpdate({ _id: u._id }, { $set: { pwd: '1111' } }, { new: true, upsert: true });
    })
    .then((u) => {
        return group.findOneAndUpdate({ u_id: u._id }, { $set: { name: 'A' } }, { new: true, upsert: true });
    })
    .then((g) => {
        return User.findOne({ _id: g.u_id });
    })
    .then((u) => console.log(u));
    .catch(err => console.error(err.message));
```

> error처리는 catch로 전부 들어간다. 콜백헬의 탭이 없어서 가독성이 좋아졌다.
다만 에러 메시지가 일괄 처리라 case1,2와 같이 다양하게 메세지를 변경하려면 다른 처리가 필요하다.
프라미스 체인으로 위아래의 상관 관계가 명확해야하는데 조건절이면 아래와 같은 방법을 쓰는 것이 좋다.


- case 4: await를 이용한 방법

```javascript
try {
    const ufo = await User.findOne(userId);
    const ufoau = await User.findOneAndUpdate({ _id: ufo._id }, { $set: { pwd: '1111' } }, { new: true, upsert: true });
    const gfoau = await group.findOneAndUpdate({ u_id: ufoau._id }, { $set: { name: 'A' } }, { new: true, upsert: true });
    const u = await User.findOne({ _id: gfoau.u_id });
    console.log(u);
}
catch(err) {
    console.error(err.message);
}
```

> await는 조건절에서 효과적이다.

# 의견

상단에 리턴 예외처리는 es-lint에서 허용하지 않기 때문에 예외 설정을 해야한다.

물론 위의 코드가 정답은 아니며 추후 유지보수를 위한 노력이다.

가독성을 위한 코딩이며 당장 돌아가는 코드가 중요한 것이 아닌 유지보수가 편한 코드를 지향해야한다.