# typescript-snippets

## Ensure a type

In specific use cases where type information is awkward to apply and `as` casts are used, which introduce type vulnerabilities, here is a little function that ensures its input matches the given generic. This is essentially the same as an `as` cast but with stricter type requirements.

```typescript
type NoInfer<T> = [T][T extends any ? 0 : never];

function ensureType<ExpectedType = never>(
    input: NoInfer<ExpectedType>,
): NoInfer<ExpectedType> {
    return input;
}
```

See [this code in the TS playground](https://www.typescriptlang.org/play?#code/PQKhCgAIUgVALAlgZ0gFwJ4AcCml44A2uATpCpAK7I4BmlhktA9mSyQMaIB2A5k5W4c0iZt0i8c3HCUQdUaZpABGeHAA8shOYjSEMkLCWYA3RABMc5gDSQSAQzQEyT+9ygwetGbL7oCALZMxkFOeLyIJlLk3FiUaMgAdB7A4Ji4kAByzACS3N4kADywAHyQALyQANqwALo1kBpoUuaobgYA-JAADJAAXJDSUSS1ANzg4KAQ0HDYeATEMv6OjdzIlCQ4CgQSkdE8cWiQAY4cBNvhe+KS0rIc6HOJcDs3MnLkqDRolFgxHpDrM6QeyQADu9gMriOiFo5GhqGYAV0zXMkAAFKCkECKOk5PZCPpgQTmKCrDFZrgAMocWRYNC2TbrQgiPw8fw4f6vO4qHA8fheHxk2ghdkxQ7IACUtjCmzBiAJwOElHxhNUwIeGR8rCeCAo7A4W0a9jO-2o9kkkGYsKcesEwlE4kUjU02i4egMyFwXFokJeUjeHGl8GouzMfhD6TwyHs3kwTFYooO8WS0FS9CEIjEq3Wm1gc0KAFFNDhhFY8xlKkMZCU0VBIPWk2gBtk8gVC8XS+ZyzgStZwBLm7l8jJ216Ud2ygBvOv1zbfEjiRvjAC+E0mwBizW4me4KoMPBM+Is4CkOZw3cKyDQvl4NYArBLxqeNuf81ebzWAOR3z+PibADceC3Hc9xiQ9tHME81hfC9J0xZgBm4SgAlUEhlxrOD4EcAY72XP9n1zfNMIQwZkNQ9C0WInC8PGddNykECCX3bhwOPDgxCvMFjD4ABBZBkEQXhuACBjELIpZKgI19cEva8+S-Y1vlAkF3z5X9xnYtYjnYkhNmEPiBKEkTtzElCJOzGD8yQsySHvP86KAhiHVAg8j0gzTOK1EgclobIAHF-TuAZBAAa24ElxEk6DCNwNEtGNHB4GYQhLBIR8gA) for more details.

Thanks to [this StackOverflow answer](https://stackoverflow.com/a/56688073) for the `NoInfer` type which forces the generic to be supplied.

## Ensure non-empty string

```typescript
type NonEmptyString<ExactStringGeneric extends string> = ExactStringGeneric extends '' ? never: ExactStringGeneric;

function ensureNonEmpty<StringGeneric extends string>(inputString: NonEmptyString<StringGeneric>): StringGeneric {
    if (!inputString) {
        throw new Error(`Expected a non-empty string.`);
    }
    return inputString;
}
```

[see in playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAcg9gOwKIFsygMrAE4EsEDmAPEgB4CGAxsFnoQOIQIR6VQSnBMAmAzlLxz4CAPigBeKGSo0hDJi1xsOXBHygByDVAD8UZgDcWALikVqtYY2asA3ACh7AMwCuCarkTsEvF9gjwyGigRJbyNkrsnDz8gnSiABT4YC6y8aaBqOggYcS51oqUIgCUpvkKrFAA3vZQdVC4TlAJAITJqbnF1bX1vcAAFthwAO76EKNI2EPYCQAGZJDUENxQ5PqIALQQwSACcgQAdLPFDr0Avj11-sB+CA0IKWnCDhf2lIiCUO8uADbcAEIQLKgUxxYQSTQaBzvHzAKDcCBOfC4Lg-EDwYDAkAARghTF8-kyOwSWhObw+cIRSIQKIgaIxWIATHifH4AogsSSEHA4dtshoTkA)

## Array length checking as a type guard

```typescript
// some new type helpers to start out
export type Tuple<
    ArrayElementGeneric,
    LengthGeneric extends number,
> = LengthGeneric extends LengthGeneric
    ? number extends LengthGeneric
        ? ArrayElementGeneric[]
        : _TupleOf<ArrayElementGeneric, LengthGeneric, []>
    : never;
type _TupleOf<
    ArrayElementGeneric,
    LengthGeneric extends number,
    FullArrayGeneric extends unknown[],
> = FullArrayGeneric['length'] extends LengthGeneric
    ? FullArrayGeneric
    : _TupleOf<ArrayElementGeneric, LengthGeneric, [ArrayElementGeneric, ...FullArrayGeneric]>;

// and now the type guard
export function isLengthAtLeast<ArrayElementGeneric, LengthGeneric extends number>(
    array: ArrayElementGeneric[],
    length: LengthGeneric,
): array is [...Tuple<ArrayElementGeneric, LengthGeneric>, ...ArrayElementGeneric[]] {
    return array.length >= length;
}
```

[see in playground](https://www.typescriptlang.org/play?noUncheckedIndexedAccess=true#code/C4TwDgpgBAKgrmANhAPAKCpqBBATrgQxAFFkBbCAO2AHEqJcBLAYwBoMsAZKgc2AAs6lBiygQAHsCoATAM5RKcMgCMG7AHxQAvFG6U+g+k2ZjJM+XoNCRzDpgD8CpatymplObt4Drxu1gccfCJSCApqXxYAbQBdfwCALigAfXgkCAB5ADMUPEIScipaIxZWL30fErYoWPV-JOEANwYAbjRQSBS05Gz0ALyQwoiq9gDLSuFjN3MnFTV-ADE4REQBkEiTCXdPOEoAa0oAewB3SliNbSgllbWNqIByZAr+e5jpjwtvQ0mWf0dr1bBdZVepdBA9HJrULhYo-arjb42MpRKFDWFIqAAOmxANuVRi6jaaAkYEOuGAUCyu2YwEYh0oUEYsgR2GA3AIsmAuSB0KKGzKCI2708ijmuHUAAp-AQgUlUWE+VVzv4ngYkoKRmgAJRJGX5RnyKLYzHdVDymH88pWKrqMrG82KuGxN4Ab38uAgwDguAZeqImNVAig6h0gf4bQAvmg0AB6GNQY78RjIKACJlQZiHZbSKCHZSNOmyRAgKCqVPgCA5jlQAip8EQMqyRiUZjQRjAe7yWuIdsMAiIMrHaBZMmtxkU4CHUvQRoEHhwaDHdv8WYuWJoTOUTlQHhVNZJTlMfSxS4Pe5le7nqCXi9Xy8xImMLJQCVMllsiAc4AS3dwtZlABWLUtSgN0Ak3bdpAgLJm17YsAGVgCPHgAEYDyQ5seEuX8bDWKIAAYH38CCKSgmDKDgkBEOQgAmdDkOwvcgSiAAWIjiPpbcyCIVQADlDmAajMLQqBD0wxi-2YgCiIjMREFkaAwKwEioG4kA+IEoT9Do0SMP0CTcOYwi2nAziKTUjTBL0ngAGZ6PEnQcOMPC2MjIA)

Thanks to ["Typescript: Can I define an n-length tuple type?"](https://stackoverflow.com/a/52490977) for tuple help.
