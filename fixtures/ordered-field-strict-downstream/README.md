# Ordered-field strict downstream smoke

This fixture consumes only source-free `npa-mathlib v0.2.2` certificate
artifacts for `Mathlib.Algebra.Ring.Basic`, `Mathlib.Algebra.Field.Basic`, and
`Mathlib.Algebra.OrderedField.Strict`, plus the pinned `npa-std` equality
certificate. Its local theorems apply `ordered_field_one_sub_pos_of_lt_one`,
`ordered_field_square_lt_one_of_pos_lt_one`,
`ordered_field_one_sub_square_pos`, `ordered_field_two_ne_zero`, and
`ordered_field_four_ne_zero`. No `npa-mathlib` source or metadata is part of
the proof boundary.
