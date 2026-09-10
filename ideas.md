# Potential article topics.

## L0 compiler and language work

### Making multiple compilers agree on diagnostics

The diagnostic parity work is probably the most broadly interesting. The hook is
clear: when a language has a reference compiler and a self-hosted compiler,
“same program, same error” becomes part of language quality.

Good angle:

- Why diagnostic codes matter beyond nicer error messages
- How drift appears between compiler implementations
- Using a shared catalog and oracle tests to keep stages aligned
- What parity means for users, tests, and future compiler work

Possible title:

- `When Two Compilers Disagree About an Error`
- `Keeping Compiler Diagnostics Stable Across Implementations`

### Porting compiler tooling from shell scripts to Python

This has a practical engineering audience. It is less language-theory-heavy and
easier to connect to CI portability problems.

Good angle:

- Why shell runner scripts became a maintenance problem
- Cross-platform behavior, especially Windows/MSYS2
- What improved after the port: testability, quiet progress, runner reuse,
  diagnostics
- The regressions and follow-up fixes the port exposed

Possible title:

- `Why We Replaced Compiler Test Shell Scripts With Python`
- `Making Compiler Tooling Boring Across Linux, macOS, and Windows`

### Automatic reference counting bug fixes as a window into ownership

The ARC fixes look like good material if there is enough concrete detail in the
commits. Bugs around reassignment, borrowed parameters, null propagation, and
top-level cleanup can become a strong “ownership rules meet code generation”
article.

Good angle:

- L0’s ownership/runtime model in plain language
- Why reassignment is tricky when values need cleanup
- A minimal failing example and the generated behavior before/after
- How trace tooling helps verify memory behavior

Possible title:

- `The Hidden Complexity of Reassigning a String`
- `Debugging ARC Cleanup in a Small Compiled Language`

### Adding strings as a real language surface

Native string operators and string concatenation are accessible to readers, but
the article becomes interesting only if it explains what had to change under the
surface.

Good angle:

- Moving from helper calls to operators
- Parser/type-checker/codegen/runtime consequences
- Ownership and cleanup implications of `"a" + "b"`
- Migration of compiler and stdlib code away from old helpers

Possible title:

- `What It Takes to Add "a" + "b" to a Language`
- `Turning String Helpers Into Language Operators`

### From prototype language repo to maintainable monorepo

The repo/workflow reorganization, ADRs, shared workspace, release workflow, and
project skills could form a process article. This is useful, but less
distinctive unless the audience cares about language-project operations.

Good angle:

- Why a language family needs level-local ownership and shared infrastructure
- Separating stable docs, plans, ADRs, releases, and level-specific workflows
- Keeping shared work visible without blurring L0/L1 boundaries

Possible title:

- `Organizing a Growing Language Project Without Losing Its Levels`

**Ranking**

1. Diagnostic parity across compilers
2. ARC cleanup and ownership bugs
3. String operators and concatenation
4. Shell-to-Python compiler tooling
5. Monorepo/process evolution

**Why the diagnostic story is the best lead**

It has the best combination of:

- A concrete user problem: inconsistent compiler errors
- A deeper compiler-engineering problem: implementation drift
- A clean solution shape: catalog, policy, oracle tests
- A broader lesson: error behavior is part of a language contract

It also reads well for people who do not know Dea yet. You can introduce L0 in
one paragraph and spend the rest of the post on a problem compiler engineers
recognize.

**A good short series instead of one post**

1. `Keeping Diagnostics Stable Across Compiler Stages`
2. `What It Takes to Add Native String Operators`
3. `Debugging ARC Cleanup With Trace Tests`

That sequence moves from compiler UX, to language features, to runtime
correctness.

## L1 compiler and language work

L1 has better blog material than a plain “new features” recap because its
beginning already contains a clear engineering thesis:

**Do not start the next language level from zero. Bootstrap it from the mature
previous level, make the boundary explicit, then diverge deliberately.**

That gives several good post ideas.

**Best L1 Blog Ideas:**

### Bootstrapping L1 from L0 without pretending it is self-hosted

This is the strongest origin-story post. L1 starts on April 2, 2026 with a
runnable Stage 1 compiler copied and retargeted from L0 Stage 2, written in L0,
compiling `.l1` programs, while `stage2_l1` is explicitly only a placeholder.
That is a concrete compiler-engineering story with a useful lesson: bootstrap
progress comes from choosing which layers must change immediately and which
layers can stay inherited.
See [design-decisions.md](/Users/guglielmo/devel/googlielmo/DEA/l1/docs/reference/design-decisions.md:63)
and [project-status.md](/Users/guglielmo/devel/googlielmo/DEA/l1/docs/project-status.md:33).

Good angle:

- Why L1 exists while L0 is still the release line
- Why the first L1 compiler is implemented in L0
- What was copied, what was renamed, what was kept intentionally historical
- Why `.l0` implementation sources and `.l1` user programs coexist
- What “bootstrap scaffold” means in practice

Possible titles:

- `Bootstrapping Dea/L1 From a Working Compiler`
- `A New Language Level Without a Greenfield Compiler`
- `How L1 Started: Retarget First, Self-Host Later`

### The first day of a new language is mostly boundary work

The first L1 commit is not “add a fancy syntax feature.” It is subtree layout,
compiler naming, source extensions, stdlib copying, runtime surfaces, launchers,
docs, tests, examples, and explicit upstream compiler selection.

That is interesting because it counters a common assumption that language work
starts with grammar changes.

Good angle:

- A language level needs its own file surface, compiler identity, examples,
  stdlib, runtime, docs, tests, and build contract
- What was separated from L0 immediately
- What stayed shared or upstream-dependent
- How this prevents later confusion between L0 and L1

Possible titles:

- `What We Had to Build Before L1 Had Its First New Feature`
- `The Unseen Work of Starting a New Compiler Line`

### Putting compiler intrinsics into a virtual prelude module

This is a very strong technical post from L1’s first days. The `dea` virtual
prelude work is not just syntax sugar. It moves `sizeof` and `ord` out of “magic
bare names” and into normal symbol/module resolution, with shadowing and a
qualified escape hatch. Later `is(...)` joins that model.
See [design-decisions.md](/Users/guglielmo/devel/googlielmo/DEA/l1/docs/reference/design-decisions.md:202).

Good angle:

- The problem with recognizing intrinsics by bare callee name
- Why `sizeof` should not hijack a user-defined `sizeof`
- Why a compiler-synthesized module can be cleaner than a hidden source file
- Import precedence and qualified forms like `dea::sizeof`
- How this lays groundwork for future compiler-owned symbols

Possible titles:

- `Intrinsics Without Magic Names: L1's Virtual Prelude`
- `Why We Made sizeof a Symbol`
- `Designing a Compiler-Owned Prelude Module`

### Designing L1 as a post-L0 experiment line

L1 quickly becomes the place where post-L0 surface decisions land: prefixed
integer literals, wider integer types, `float` and `double`, bitwise operators,
function pointers, `unsafe`, fixed-size arrays, imports/export manifests, symbol
mangling.

This can be framed as a language evolution post:

- L0 is stable enough to release
- L1 is where design pressure goes
- A level split lets features evolve without destabilizing the active release
  line

Possible titles:

- `Why Dea Needed L1 Before L0 Was Done Evolving`
- `Language Levels as a Way to Keep Shipping and Keep Experimenting`

### Defining integers before adding many integers

L1’s numeric story starts early and has depth:

- Binary/octal/hex literals
- Lexer groundwork for large integer literals
- `tiny`, `short`, `ushort`
- `uint`, `long`, `ulong`
- Bigint payloads used contextually during compilation
- Defined behavior instead of inheriting vague host-C behavior

That is a solid technical article if the implementation details are ready to
show. The interesting part is not “we added `0xff`.” It is “we extended integer
syntax and widths while keeping semantics defined through a C99 backend.”
See [design-decisions.md](/Users/guglielmo/devel/googlielmo/DEA/l1/docs/reference/design-decisions.md:236).

Possible titles:

- `Adding Wider Integers to a C-Backed Language Without Handing Semantics to C`
- `From 0xff to ulong: L1's First Numeric Expansion`

### Renaming the ABI before it becomes permanent

Very early L1 work migrates public generated/runtime C names from historical
`l0_*` to `dea_*`. That sounds small, but it is a good “names become interfaces”
post.

Good angle:

- Bootstrap copying leaves historical names everywhere
- Internal legacy names can survive temporarily; public ABI names should not
- Generated C and runtime headers need namespace policy before external code
  relies on them
- Mangling has to reserve both old and new prefixes

Possible titles:

- `The ABI Rename We Did Before Anyone Could Depend on the Old One`
- `Bootstrap Debt at the C Boundary`

### How separate compilation starts before separate compilation exists

Later in the early L1 arc, work appears around export manifests, alias/selective
imports, symbol mangling, interface fingerprints, runtime archive splitting, and
link-order planning. This is a good post if you want to show long-range compiler
design.

Hook:

- The compiler still emits one C translation unit today
- But module visibility, names, exports, runtime archives, and interfaces
  already constrain the route to multiple translation units

Possible titles:

- `Preparing a Single-Translation-Unit Compiler for Separate Compilation`
- `The Work Before .o Files: Exports, Names, and Interfaces in L1`

### Recap of L1 post ideas

**Ranking**

1. **Bootstrapping L1 from L0**
2. **Virtual `dea` prelude and non-magic intrinsics**
3. **Numeric expansion with defined semantics through C**
4. **Starting separate compilation before multi-CU output**
5. **Early ABI renaming from `l0_*` to `dea_*`**

**Best first post**
I would start with:

`Bootstrapping Dea/L1 From a Working Compiler`

It is the most natural “from its beginnings” article. It lets you introduce:

- what L0 and L1 are,
- why L1 was created,
- why L1 Stage 1 is written in L0,
- how a copied compiler becomes a new language level,
- why self-hosting is a later milestone, not the starting gun.

A likely outline:

1. **L0 was the upstream, not the enemy**
2. **The first L1 compiler is an L0 program**
3. **Separating implementation language from user language**
4. **What changed on day one**

- compiler identity
- `.l1` source surface
- stdlib/runtime tree
- bootstrap launcher/build contract
- tests and docs

5. **What we refused to claim**

- no self-hosted L1 yet
- no L1 release line yet
- no blank-slate redesign

6. **Why this is a better starting point for language evolution**

**A strong short series**

1. `Bootstrapping Dea/L1 From L0`
2. `Intrinsics Without Magic Names: The dea Prelude`
3. `Defined Numerics in a C99-Lowered Language`
4. `Preparing L1 for Separate Compilation`

That series follows L1’s real arc: birth, symbol model, language growth,
compiler architecture.
