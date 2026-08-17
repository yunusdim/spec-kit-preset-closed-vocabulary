# Evidence

Two things are recorded here, and they are not the same kind of thing.

**Section 1** is a reproducible bench. Three fixtures ship with the preset, they
depend on nothing outside this repository, and anyone can run them.

**Section 2** is a measurement taken on a private corpus. It is the motivation for
the check, not evidence anyone else can verify. It is labelled as such.

---

## 1 · Bench — reproducible

Three fixtures under `fixtures/`. One must fire, two must not. A check that only
demonstrates that it bites has not demonstrated that it stays quiet.

### How to run

```
specify preset install closed-vocabulary
cd evidence/fixtures/<fixture>
/speckit.analyze
```

Run each fixture twice: once without the preset installed, once with it. The
difference between the two outputs is the whole claim.

### 001-divergent — MUST FIRE

`order status` is enumerated twice with member sets that differ in both directions.

```
spec.md   FR-001   pending, paid, shipped, delivered, cancelled
plan.md   §Data model   pending, paid, shipped, delivered, refunded

in spec not in plan    cancelled
in plan not in spec    refunded
```

| | |
|---|---|
| expected | one WARNING for `order status`, with both directions reported, plus the coverage figure |
| observed without preset | *fill in after running* |
| observed with preset | *fill in after running* |

### 002-agreeing — MUST NOT FIRE

`payment method` is enumerated twice with the same three members, in a different
order and with a different separator. This is the formatting-variance control.

```
spec.md   card, transfer, wallet
plan.md   wallet | card | transfer
```

| | |
|---|---|
| expected | no divergence reported. Coverage still reported. |
| observed | *fill in after running* |

### 003-declared-subset — MUST NOT FIRE

`shipment status` is declared as a subset of `order status`. The difference is
stated in the spec, so it is intended.

| | |
|---|---|
| expected | no divergence reported. Coverage still reported. |
| observed | *fill in after running* |

### Result table

| fixture | must fire | fired | coverage reported |
|---|---|---|---|
| 001-divergent | yes | *pending* | *pending* |
| 002-agreeing | no | *pending* | *pending* |
| 003-declared-subset | no | *pending* | *pending* |

**Nothing in this table is filled in yet. It gets filled in from actual runs, not
from expectation.** A preset whose evidence table is populated from what the author
expected to happen is the same defect the check exists to catch.

---

## 2 · Field measurement — not reproducible here

A read-only checker of the same shape, run on 13 August 2026 over six architecture
contracts in a private ecosystem. Reported for motivation. Nobody outside that
device can verify it, and it is not offered as evidence of this preset's behaviour.

```
contract            inspected   recognised      divergences
ocgs_ref                  112      4  ( 4%)               1
ocgs_fab                  122      4  ( 3%)               0
ocgs_pru                  122      4  ( 3%)               0
prueba_fabrica            126      0  ( 0%)               0
operator_engine            21      0  ( 0%)               0
governed_core              50      2  ( 4%)               0
```

**The one divergence found**, previously unreported by any verification in that
ecosystem:

```
struct  State.invariants
        "clase en separador|regulador|cobertura|canonico"          4 members

sig     invariant_taxonomy.clase_de
        "separador, regulador, cobertura, canonico,
         redundante o incompatible"                                6 members
```

It is not documentary. The implementation admits the four and raises on any other
value, so a state carrying one of the other two is legal according to the signature
and fails at runtime.

**Two things from that run shaped this preset, and both are failures rather than
successes.**

*The first extractor reported zero across every contract, including the one above.*
It did not recognise `en` as an introducer or `|` as a separator. A zero produced by
an instrument that parses 4 per cent of what it inspects is indistinguishable from a
zero produced by a clean corpus. That is why coverage reporting is not optional in
the appended pass.

*A sibling check in the same ecosystem reported 72 findings where 7 were real.* All
65 extras came from treating a bare word appearing anywhere in the surrounding text
as a declaration. That is why the pass groups on the name preceding the introducer
and refuses to group on content similarity.

---

## 3 · Declared limits

- The pass compares declarations against each other, inside the spec. It cannot tell
  whether a set that every declaration agrees on is the right set. A specification
  that is internally consistent and uniformly mis-transcribed from its source is
  indistinguishable, to this pass, from a correct one.
- It is judgemental. It finds silence, not error. A divergence it reports may be two
  legitimately different sets sharing a name.
- It reports and continues. It never blocks a run.
