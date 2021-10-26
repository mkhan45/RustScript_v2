<div align=center>
    <img src="assets/logo.png" width="600px">
    <h1>
        RustScript V2
    </h1>
</div>


V2 of <https://github.com/mkhan45/RustScript>

I wrote RustScript originally in Java because it was part of a school project,
ignoring performance/code quality because I only had one night to do it.

This is an improved version of RustScript with improved performance and more features
written to learn OCaml. Still WIP

### Build

```bash
dune build
```

Run a file using:

```bash
dune exec ./bin/rustscript_cli.exe <file>
```

Start a REPL using:

```bash
dune exec ./bin/rustscript_cli.exe
```


### Examples:

#### Quicksort

```ex
let sort = fn(ls) => match ls
    | [] -> []
    | [pivot | tail] -> {
        let higher = filter (fn(x) => x >= pivot, tail)
        let lower = filter(fn(x) => x < pivot, tail)

        sort(lower) + [pivot] + sort(higher)
    }

inspect(sort([5, 3, 7, 9, 10, 4, 6])) # [3, 4, 5, 6, 7, 9, 10]
```

#### Run Length Encode
```ex
let run_len_encode = fn(ls) => match ls
    | [] -> []
    | [x | xs] -> {
        let next = run_len_encode(xs)
        match next
            | [(next_x, cnt) | tl] when x == next_x -> [(x, cnt + 1) | tl]
            | _ -> [(x, 1) | next]
    }

let test_ls = [1, 1, 2, 3, 4, 4, 4, 5, 6, 1, 2, 2]

# [(1., 2.), (2., 1.), (3., 1.), (4., 3.), (5., 1.), (6., 1.), (1., 1.), (2., 2.)]
inspect(run_len_encode(test_ls))
```

#### Two Sum
```ex
let two_sum = fn(nums, target) => {
    let helper = fn(m, ls, target) => match ls
        | [] -> ()
        | [(i, x) | xs] -> {
            let complement = target - x
            match m
                | %{complement: ()} -> helper(%{x: i | m}, xs, target)
                | %{complement: y} -> (y, i)
        }

    helper(%{}, enumerate(nums), target)
}

inspect(two_sum([1,9,13,20,47], 10)) # (0, 1)
inspect(two_sum([3,2,4,1,9], 10)) # (0, 4)
inspect(two_sum([], 10)) # ()
```

##### Project Euler #1
```ex
euler1 = sum([x for x in [1..1000] if x % 3 == 0 || x % 5 == 0])
inspect(euler1) # 233168
```

##### Project Euler #2
```ex
let euler2 = {
    let aux = fn((a, b), acc) =>
        if b < 4000000
            then aux((b, a + 4 * b), acc + b)
            else acc

    aux((0, 2), 0)
}

inspect(euler2) # 4613732
```

#### Euler 3
```ex
let gcd = fn(a, b) => match (a, b)
    | (0, b) -> b
    | (a, 0) -> a
    | (a, b) when a > b -> gcd(b, a)
    | (a, b) -> {
        let remainder = b % a
        if remainder != 0 then (gcd(a, remainder)) else a
    }

let abs = fn(x) => if x < 0 then -x else x

let pollard = fn(n) => match n
    | 1 -> ()
    | n when n % 2 == 0 -> 2
    | n -> {
        let g = fn(x, n) => (x * x + 1) % n
        let iter = fn(x, y, d) => match (x, y, d)
            | (x, y, 1) -> {
                let x = g(x, n)
                let y = g(g(y, n), n)
                let d = gcd(abs(x - y), n)
                iter(x, y, d)
            }
            | (_, _, d) -> if d == n then () else d

        iter(2, 2, 1)
    }

let factor = fn(n) => {
    let d = pollard(n)
    if d == () then () else n / d
}

let euler3 = {
    # repeatedly factors until largest is found
    let aux = fn(n) => match factor(n)
        | () -> n
        | f when n == f -> f
        | f -> aux(f)

    let n = 600851475143
    aux(n)
}

inspect(euler3) # 6857
```

More project euler problems can be found in the [examples folder](https://github.com/mkhan45/RustScript2/tree/main/examples).
