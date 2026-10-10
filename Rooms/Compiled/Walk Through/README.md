
# Compiled — Walkthrough

**Platform:** TryHackMe  
**Difficulty:** Easy  
**OS:** Linux  
**Category:** Reverse Engineering  

## Overview

Analyze a compiled Linux executable to identify the correct password using `strings` and Ghidra.

### Skills Practiced

- String extraction
- Reverse engineering with Ghidra
- C code analysis
- Password validation logic

---

## 1. Analyzing the File

### 1.1 Strings

Extract readable strings from the executable.

**Command:**

```bash
strings Compiled-1688545393558.Compiled
```

**Notable Results:**

```text
...
strcmp
__isoc99_scanf
Password:
DoYouEven%sCTF
__dso_handle
_init
Correct!
Try again!
...
```

The executable contains password validation messages, comparison functions, and potential password values.

### 1.2 Ghidra

Import the executable into **Ghidra**, run analysis, and examine the `main()` function.

**Decompiled Code:**

```c
undefined8 main(void)
{
  int iVar1;
  char local_28[32];

  fwrite("Password: ",1,10,stdout);
  __isoc99_scanf("DoYouEven%sCTF",local_28);

  iVar1 = strcmp(local_28,"__dso_handle");
  if ((-1 < iVar1) &&
      (iVar1 = strcmp(local_28,"__dso_handle"), iVar1 < 1)) {
    printf("Try again!");
    return 0;
  }

  iVar1 = strcmp(local_28,"_init");
  if (iVar1 == 0) {
    printf("Correct!");
  }
  else {
    printf("Try again!");
  }
  return 0;
}
```

**Step 1: Identify the Success Condition**

```c
iVar1 = strcmp(local_28,"_init");
if (iVar1 == 0) {
    printf("Correct!");
}
```

`strcmp()` compares two strings:

| Return | Meaning |
|---|---|
| `0` | Strings match |
| `< 0` | First string comes before second |
| `> 0` | First string comes after second |

To print `Correct!`, `local_28` must equal `_init`.

**Step 2: Examine the First Condition**

```c
iVar1 = strcmp(local_28,"__dso_handle");
if ((-1 < iVar1) &&
    (iVar1 = strcmp(local_28,"__dso_handle"), iVar1 < 1)) {
    printf("Try again!");
    return 0;
}
```

This effectively checks:

```c
if (strcmp(local_28, "__dso_handle") == 0)
```

If input equals `__dso_handle`, the program exits with `Try again!`.

Our expected value `_init` passes this check.

**Step 3: Understand Input Parsing**

```c
__isoc99_scanf("DoYouEven%sCTF",local_28);
```

| Component | Purpose |
|---|---|
| `DoYouEven` | Required prefix |
| `%s` | Captures text until whitespace |
| `CTF` | Expected trailing literal |

Since `%s` reads until whitespace, entering `DoYouEven_initCTF` would store `_initCTF`, causing validation to fail.

The program does not check whether `scanf()` matched the trailing `CTF`.

**Step 4: Identify Password**

The captured value must equal `_init`.

**Input:**

```text
DoYouEven_init
```

Press Enter to terminate input. This stores `_init` in `local_28`.

The final comparison becomes:

```c
strcmp("_init", "_init") == 0
```

This evaluates to true, printing `Correct!`.

### 1.3 Testing Password

**Command:**

```bash
chmod +x Compiled-1688545393558.Compiled
./Compiled-1688545393558.Compiled
```

**Input:**

```text
DoYouEven_init
```

**Expected Output:**

```text
Password: Correct!
```

Alternatively:

```bash
printf 'DoYouEven_init\n' | ./Compiled-1688545393558.Compiled
```

---

## 2. Conclusion

Using `strings` and Ghidra, we identified the hardcoded password comparison and determined the required input.

**Key Findings:**

- `strcmp()` returns `0` when strings match.
- `_init` is the required captured value.
- `scanf()` captures the password using `%s`.
- The trailing `CTF` is not required because the return value of `scanf()` is ignored.

**Correct Input:** `DoYouEven_init`

---

**Disclaimer:** Educational demonstration in an authorized TryHackMe lab.
