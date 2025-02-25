

you can define a type safe form in angular like:
```
type UserForm = {
  [field in keyof Partial<IUser>]: FormControl<IUser[field] | null>
}
```

on formGroup<UserForm> when creating a form group
