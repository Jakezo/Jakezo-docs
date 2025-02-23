#Promise와 jQuery Ajax 명시적 resolve 호출의 중요성

비동기 프로그래밍은 현대 웹 개발에서 필수적인 요소입니다. 특히 서버와의 데이터 통신이나 파일 읽기 같은 작업은 시간이 걸릴 수 있기 때문에, 이를 효율적으로 처리하기 위해 **Promise**가 널리 사용됩니다. 이번 글에서는 **Promise**와 **jQuery Ajax**의 관계를 중심으로, 왜 **resolve**를 명시적으로 호출해야 하는지 자세히 알아보겠습니다.

---

### 1. 비동기 처리와 Promise란?

**비동기 처리**는 코드가 순차적으로 실행되는 대신, 시간이 걸리는 작업(예: 서버 요청)이 끝날 때까지 다른 작업을 멈추지 않고 계속 진행하는 방식을 말합니다. 이때 **Promise** 객체를 사용하면 비동기 작업의 성공 또는 실패를 관리할 수 있습니다.

**Promise**는 자바스크립트에서 비동기 작업을 처리하기 위해 제공되는 객체입니다.

#### **Promise의 구조**

**Promise**는 두 개의 콜백 함수를 인자로 받습니다:

1. **resolve**: 비동기 작업이 성공적으로 완료되었음을 알리는 함수.
2. **reject**: 비동기 작업이 실패했음을 알리는 함수.

**Promise**는 세 가지 상태를 가집니다:

1. **Pending (대기 중)**: 비동기 작업이 진행 중인 상태.
2. **Fulfilled (이행됨)**: 비동기 작업이 성공적으로 완료된 상태. 이때 **resolve**가 호출됩니다.
3. **Rejected (거부됨)**: 비동기 작업이 실패한 상태. 이때 **reject**가 호출됩니다.

Promise는 **.then()**을 통해 성공 시 처리할 코드를 작성하고, **.catch()**로 실패 시의 처리를 담당합니다.

**🔹 예제: 기본 Promise 생성 및 사용**

```javascript
const myPromise = new Promise((resolve, reject) => {
    const success = true;  // 조건에 따라 성공 여부 결정
    
    if (success) {
        resolve("작업이 성공적으로 완료되었습니다!");  // 성공 시 호출
    } else {
        reject("작업이 실패했습니다.");  // 실패 시 호출
    }
});

myPromise
    .then(result => {
        console.log("성공:", result);
    })
    .catch(error => {
        console.error("실패:", error);
    });
```

이 예제에서 **resolve**는 성공 시, **reject**는 실패 시 호출됩니다. 호출된 후에는 각각 **.then()**과 **.catch()** 블록이 실행됩니다.

---

### 2. resolve의 역할과 필요성

**resolve**는 Promise를 **Fulfilled(이행됨)** 상태로 전환하는 함수입니다. 만약 **resolve**를 호출하지 않으면, Promise는 **영원히 Pending(대기 중)** 상태로 남게 되어 **.then()** 블록이 실행되지 않습니다.

#### 🔹 **resolve 호출이 필요한 이유**

- **Promise는 기본적으로 Pending 상태**로 시작하며, 상태 변화를 자동으로 하지 않습니다.
- 비동기 작업이 완료되었음을 **명시적으로 알려줘야** 다음 단계(then)가 실행됩니다.

**예시)**

```javascript
function examplePromise() {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve("작업 성공!");  // 성공시 Promise를 Fulfilled상태로 변경
        }, 2000);
    });
}

examplePromise()
    .then((message) => {
        console.log("성공:", message);  // 2초 후 "작업 성공!"이 표시됩니다.
    })
    .catch((error) => {
        console.error("실패:", error);
    });
```

> 💡 **중요!**  
> **resolve**를 호출하지 않으면, Promise는 어떻한 것도 해주지 않고 **까지 Pending 상태**로 남아있습니다.

---

### 3. jQuery Ajax와 Promise

jQuery가 제공하는 **$.ajax** 함수도 다른 프로그램과 달리, **Promise**를 배우지 않고 **Deferred** 객체를 반환합니다.

무엇을 모두 Promise로 구현하려면 **resolve** 함수를 명시적으로 호출**해야 합니다.

#### **$.ajax 와 Promise 결합하기**

```javascript
function ajaxWithPromise() {
    return new Promise((resolve, reject) => {
        $.ajax({
            url: "/api/success",
            type: "GET",
            success: function(data) {
                resolve(data);  // 성공시 Promise를 Fulfilled 상태로 변경
            },
            error: function(error) {
                reject(error);  // 실패시 Promise를 Rejected 상태로 변경
            }
        });
    });
}

ajaxWithPromise()
    .then((data) => {
        console.log("요청 성공:", data);
    })
    .catch((error) => {
        console.error("요청 실패:", error);
    });
```

> **중요**: **resolve** 호출이 됩으만 해도 `.then()`이 실행됩니다.

---

### 4. resolve를 호출하지 않을 때 발생하는 문제

#### **예시: resolve 호출 안함**

```javascript
function examplePromise() {
    return new Promise((resolve, reject) => {
        // 어떻한 도 해보지 않으면
    });
}

examplePromise()
    .then((message) => {
        console.log("성공:", message);  // 절대 실행되지 않음
    })
    .catch((error) => {
        console.error("실패:", error);  // 절대 실행되지 않음
    });
```

- **결과**: **resolve** 나 **reject**를 호출하지 않으면, Promise는 **영원히 대기 상태**로 남게 됩니다.

---

### 5. 결론: resolve 호출의 중요성

4. **비동기 흐름의 제어**: 비동기 작업의 성공 여부를 명시적으로 알려줘야 합니다.
5. **가독성 및 유지보수성**: 명시적 호출로 비동기 흐름이 더욱 직관적이고 관리하기 쉬워집니다.
6. **체이닝 가능**: Promise를 이용하면 여러 비동기 작업을 순차적으로 연결할 수 있습니다.

**Promise**를 활용한 비동기 처리는 현대 웹 개발의 필수 기술입니다. **resolve**의 역할을 명확히 이해하고 활용하면, 더욱 깔끔하고 유지보수하기 쉬운 코드를 작성할 수 있습니다.

---

**🌟 추가로 궁금한 점이 있다면 댓글로 남겨주세요!**