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

### declare missing module

```typescript
declare module '@storybook/addon-links' {
	export function linkTo(s: string): void
}
```
