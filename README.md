# Guard

기본적인 Lazy 를 활용한 Guard 를 만드는 예시입니다.

```ts
const UserGuard = ({ children }: Props) => {
    const dispatch = useAppDispatch();
    const navigate = useNavigate();
    const { isJoin, isToken, token }: initialState =
        useAppSelector((state: RootState) => state.userSlice);

    useEffect(() => {
        if (!isToken || token === '') {
            dispatch(deleteUserToken());
            navigate('/signin', { replace: true });
        }

        if (!isJoin && isToken) {
            navigate(`/signup`, { replace: true });
        }
    }, []);

    return <>{children}</>;
};

export default UserGuard;
```

로그인 했을 때가 아니라면 회원 가입 페이지나, 회원 로그인 페이지로 리다이렉션합니다.

로그인 했을 경우라면 children 이 렌더링 되도록 하게끔 설계합니다.

```ts
 <UserGuard>
    <TemplatesLazy />
</UserGuard>
```

라우트에서 Outlet 으로 들어가는 컴포넌트를 감싸줍니다. 이렇게 할때 Lazy 로 안에 있는     <TemplatesLazy /> 컴포넌트를 Lazy 하게 import 하여 useEffect의 순서를 변경해줘서

가드의 useEffect 실행 후 자식 컴포넌트를 렌더링 하도록 만듭니다.

```ts
const TemplatesLazy = Index(
    lazy(() => import('../pages/explore/Templates'))
);
```

이렇게 Lazy 임포트를 할때 Loading Component 를 생성하여 순간 화면을 커버할 수 있도록 하고

Suspense 와 함께 사용하도록 합니다. 

```ts

import React, { Suspense } from "react";
import Loading from "./Loading";

const Index = (Component: React.ElementType) => {
    return function curringLoadable(props: any) {
        return (
            <Suspense fallback={<Loading />}>
                <Component {...props} />
                {/*    lazy Component*/}
            </Suspense>
        );
    };
};

export default Index;


```
