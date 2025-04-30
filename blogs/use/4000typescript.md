---
title: typescript
date: 2025/04/01
tags:
 - js
 - typescript
categories:
 - typescript
---

## typescript 


### tsconfig.json
```json
{
  "compilerOptions": {
    "target": "es6",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": false,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    },
    "typeRoots": ["./customeType/*.d.ts", "node_modules/@types"]
  },
  "include": ["./src/**/*", "./customeType/**/*.d.ts"],
  "exclude": ["node_modules",'xxxx']
}
```
