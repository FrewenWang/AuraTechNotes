**TS1192: Module '"/Volumes/repos/tc-web-ts/node_modules/@types/react/index"' has no default export.**

解决方法：
把"allowSyntheticDefaultImports": true添加到tsconfig.json






**TS7016: Could not find a declaration file for module '../styledComponents/loginAndRegister'. '/Users/michealye/Desktop/myProduct/shebao520-mobile/src/components/styledComponents/loginAndRegister.jsx' implicitly has an 'any' type.**


解决方法：没有指定类型 Edit your tsconfig.json "noImplicitAny": false
