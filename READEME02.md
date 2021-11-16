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
