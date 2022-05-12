# [Apollo Client] Subscription이란?

Query와 다른점: 시간이 지나면서 변한 결과를 ‘구독' 할수 있음 -> Subscription은 오래 지속되는 작업

### 언제 써야 좋을까

- **큰 object에 대한 작고 점진적인 변경사항**
- **low-latency, real-time updates가 필요한 경우(ex) chat application)**
- 새 object 생성이나 중요한 필드의 업데이트같은 백엔드 데이터 변경 사항을 클라에 알려주려고 할때
- 이러한 상황이 아닌 보통의 경우에서는 사용하면 안됨
  - subscription 대신 특정 이벤트가 발생할때 쿼리를 간헐적으로 폴링하거나 다시 실행해야함
  - 큰 object에 대한 반복적인 폴링은 cost가 많이 들기 때문
  - 대신 object의 초기값을 쿼리로 가져오고, 변경사항이 발생하는 각각의 필드에 업데이트를 적용할 수 있음

### 사용법

(websocket 등 환경 설정 과정 생략)

1. server측에서 subscription의 스키마를 정의해준다

```jsx
// typeDefs.js
import { gql } from "@apollo/client";
const typeDefs = gql`
  type Subscription {
    newMessage: [Message]
  }
`;
export default typeDefs;
```

2. client측에서 gql문을 작성한다

```jsx
// blah.js
import { gql } from "@apollo/client";
export const SUBSCRIPTION_NEW_MESSAGE = gql`
  subscription NewMessage {
    newMessage {
      sender
      content
      time
      msgId
    }
  }
`;
```

3. client측에서 subscription 실행하기

```jsx
// blah2.js
const subNewChat = useSubscription(SUBSCRIPTION_NEW_MESSAGE);
```

<hr />

### SubscribeToMore

- 기존 useSubscription만 사용했을 경우의 비효율 개선을 위해 적용

```jsx
// blah2.js
const { subscribeToMore, data: messageHistory } = useQuery(GET_MESSAGE_LOG, {
  variables: { id: itemId },
});

subscribeToMore({
  // subsription gql 이름
  document: SUBSCRIPTION_NEW_MESSAGE,
  // updateQuery: return값으로 해당 쿼리문 값 업데이트
  // 첫번째 인자(prev): 해당 쿼리문의 캐시된 값(업데이트 전 값)
  // subscriptionData: 해당 subscription의 결과로 들어온 값
  updateQuery: (prev, { subscriptionData }) => {
    if (!subscriptionData.data) return prev;
    const prevMessage = prev?.item?.messages;
    const newMessage = subscriptionData?.data?.newMessage;
    return {
      // 이전 메시지 + 새로 들어온 메시지로 배열 업데이트
      item: { messages: [...prevMessage, newMessage] },
    };
  },
});
```
