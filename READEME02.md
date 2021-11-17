## React with TypeScriptをインストール

+ `npx create-react-app {プロジェクト名} --template typescript`<br>

+ `tsconfig.json`を編集<br>

```
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    // "strict": true, // コメントアウト
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx"
  },
  "include": [
    "src"
  ]
}
```

## カスタムフックとは

+ ただの関数である<br>

+ hooksの各機能を使用<br>

+ コンポーネントからロジックを分離<br>

+ 使い回し、テスト容易、見通しが良くなる<br>

+ 自由に作れる！use〜という名前になる<br>

## カスタムフックを使わないデータ取得例

+ `src/components`ディレクトリを作成<br>

+ `src/components/UserCard.tsx`コンポーネントを作成<br>

```
import { VFC } from "react"
import { UserProfile } from "../types/userProfile"

type Props = {
    user: UserProfile;

}

export const UserCard: VFC<Props> = (props) => {
    const { user } = props;
    return (
        <div>
            <dl>
                <dt>名前</dt>
                <dd>{user.name}</dd>
                <dt>メール</dt>
                <dd>{user.email}</dd>
                <dt>住所</dt>
                <dd>{user.address}</dd>
            </dl>
        </div>
    )
}
```

+ `src/types`ディレクトリを作成<br>

+ `src/types/userProfile.ts`ファイルを作成<br>

```
export type userProfile = {
    id: number;
    name: string;
    email: string;
    address: string;
}
```

+ `App.tsx`を編集<br>

```
import React from 'react';
import logo from './logo.svg';
import './App.css';
import { UserCard } from './components/UserCard';

const user = {
  id: 1,
  name: "たかき",
  email: "12314@aaa.com",
  address: "ADDRESS"
}

function App() {
  return (
    <div className="App">
      <UserCard user={user} />
    </div>
  );
}

export default App;
```

+ `App.css`を編集<br>

```
.App {
  font-family: sans-serif;
}
```

+ `src/components/UserCard.tsx`のスタイルを設定<br>

```
import { VFC } from "react"
import { UserProfile } from "../types/userProfile"

type Props = {
    user: UserProfile;

}

export const UserCard: VFC<Props> = (props) => {
    const { user } = props;

    const style = {
        border: "solid 1px #ccc",
        borderRadius: "8px",
        padding: "0 16px",
        margin: "8px",
    }

    return (
        <div style={style}>
            <dl>
                <dt>名前</dt>
                <dd>{user.name}</dd>
                <dt>メール</dt>
                <dd>{user.email}</dd>
                <dt>住所</dt>
                <dd>{user.address}</dd>
            </dl>
        </div>
    )
}
```

+ `$ npm install axios --save or $ yarn add axios --save` axiosをインポート<br>

+ `src/types/api`ディレクトリを作成<br>

+ `src/types/api/user.ts`ファイルを作成<br>

```
export type User = {
    id: number;
    name: string;
    username: string;
    email: string;
    address: {
        street: string;
        suite: string;
        city: string;
        zipcode: string;
        geo: {
            lat: string;
            lng: string;
        }
    },
    phone: string;
    website: string;
    company: {
        name: string;
        catchPhrase: string;
        bs: string;
    }
}
```

+ `App.tsx`を編集<br>

```
import { useState } from "react";
import "./App.css";
import { UserCard } from "./components/UserCard";
import axios from "axios";
import { User } from "./api/user";
import { UserProfile } from "./types/userProfile";

function App() {
  const [userProfiles, setUserProfiles] = useState<Array<UserProfile>>([]);

  const onClickFetchUser = () => {
    axios
      .get<Array<User>>("https://jsonplaceholder.typicode.com/users")
      .then(res => {
        const data = res.data.map(user => ({
          id: user.id,
          name: `${user.name}(${user.username})`,
          email: user.email,
          address: `${user.address.city}${user.address.suite}${user.address
            .street}`
        }));
        setUserProfiles(data);
      });
  };

  return (
    <div className="App">
      <button onClick={onClickFetchUser}>データ取得</button>
      {userProfiles.map(user => <UserCard key={user.id} user={user} />)}
    </div>
  );
}

export default App;
```

## ローディング処理

+ `tsconfig.json`を編集<br>

```
{
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext",
      "ES2018" // 追記
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true
  },
  "include": [
    "src"
  ]
}
```

+ `App.tsx`を編集<br>

```
import { useState } from "react";
import "./App.css";
import { UserCard } from "./components/UserCard";
import axios from "axios";
import { User } from "./api/user";
import { UserProfile } from "./types/userProfile";

function App() {
  const [userProfiles, setUserProfiles] = useState<Array<UserProfile>>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(false);

  const onClickFetchUser = () => {
    setLoading(true);
    setError(false);

    axios
      .get<Array<User>>("https://jsonplaceholder.typicode.com/users")
      .then(res => {
        const data = res.data.map(user => ({
          id: user.id,
          name: `${user.name}(${user.username})`,
          email: user.email,
          address: `${user.address.city}${user.address.suite}${user.address
            .street}`
        }));
        setUserProfiles(data);
      })
      .catch(() => {
        setError(true);
      })
      .finally(() => {
        setLoading(false);
      });
  };

  return (
    <div className="App">
      <button onClick={onClickFetchUser}>データ取得</button>
      <br />
      {error ? (
        <p style={{ color: "red" }}>データの取得に失敗しました</p>
      ) : loading ? (
        <p>Loading...</p>
      ) : (
        <>
          {userProfiles.map(user =>
            <UserCard key={user.id} user={user} />
          )}
        </>
      )}
    </div>
  );
}

export default App;
```

## カスタムフックの作成<br>

+ `src/hooks`ディレクトリを作成<br>

+ `src/hooks/useAllUsers.ts`ファイルを作成<br>

```
import axios from "axios";
import { useState } from "react";
import { User } from "../types/api/user";
import { UserProfile } from "../types/userProfile";

// 全ユーザー一覧を取得するカスタムフック
export const useAllUsers = () => {
    const [userProfiles, setUserProfiles] = useState<Array<UserProfile>>([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(false);

    const getUsers = () => {
        setLoading(true);
        setError(false);

        axios
            .get<Array<User>>("https://jsonplaceholder.typicode.com/users")
            .then(res => {
                const data = res.data.map(user => ({
                    id: user.id,
                    name: `${user.name}(${user.username})`,
                    email: user.email,
                    address: `${user.address.city}${user.address.suite}${user.address
                        .street}`
                }));
                setUserProfiles(data);
            })
            .catch(() => {
                setError(true);
            })
            .finally(() => {
                setLoading(false);
            });
    }

    return { getUsers, userProfiles, loading, error }
}
```