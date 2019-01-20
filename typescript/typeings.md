# Typing cheat sheet

### Constructor type

```typescript
class Animal {
	constructor() {}
}

interface IAnimalConstructor {
	new (): Animal
}

type TAnimalConstructor = typeof Animal
```
