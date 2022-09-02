# typescript-snippets

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

[example](https://www.typescriptlang.org/play?noUncheckedIndexedAccess=true#code/C4TwDgpgBAKgrmANhAPAKCpqBBATrgQxAFFkBbCAO2AHEqJcBLAYwBoMsAZKgc2AAs6lBiygQAHsCoATAM5RKcMgCMG7AHxQAvFG6U+g+k2ZjJM+XoNCRzDpgD8CpatymplObt4Drxu1gccfCJSCApqXxYAbQBdfwCALigAfXgkCAB5ADMUPEIScipaIxZWL30fErYoWPV-JOEANwYAbjRQSBS05Gz0ALyQwoiq9gDLSuFjN3MnFTV-ADE4REQBkEiTCXdPOEoAa0oAewB3SliNbSgllbWNqIByZAr+e5jpjwtvQ0mWf0dr1bBdZVepdBA9HJrULhYo-arjb42MpRKFDWFIqAAOmxANuVRi6jaaAkYEOuGAUCyu2YwEYh0oUEYsgR2GA3AIsmAuSB0KKGzKCI2708ijmuHUAAp-AQgUlUWE+VVzv4ngYkoKRmgAJRJGX5RnyKLYzHdVDymH88pWKrqMrG82KuGxN4Ab38uAgwDguAZeqImNVAig6h0gf4bQAvmg0AB6GNQY78RjIKACJlQZiHZbSKCHZSNOmyRAgKCqVPgCA5jlQAip8EQMqyRiUZjQRjAe7yWuIdsMAiIMrHaBZMmtxkU4CHUvQRoEHhwaDHdv8WYuWJoTOUTlQHhVNZJTlMfSxS4Pe5le7nqCXi9Xy8xImMLJQCVMllsiAc4AS3dwtZlABWLUtSgN0Ak3bdpAgLJm17YsAGVgCPHgAEYDyQ5seEuX8bDWKIAAYH38CCKSgmDKDgkBEOQgAmdDkOwvcgSiAAWIjiPpbcyCIVQADlDmAajMLQqBD0wxi-2YgCiIjMREFkaAwKwEioG4kA+IEoT9Do0SMP0CTcOYwi2nAziKTUjTBL0ngAGZ6PEnQcOMPC2MjIA)

Thanks to ["Typescript: Can I define an n-length tuple type?"](https://stackoverflow.com/a/52490977) for tuple help.
