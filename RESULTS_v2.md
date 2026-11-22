# Catch the Flip — Results and Capstone

*PushQuantum Hackathon · Alice & Bob challenge · 22 November 2026*  
*Times are in units of 1/κ₂; segment duration T = 200.*

## 1. Summary

We investigated whether buffer radiation can herald logical bit flips of a dissipatively stabilized cat qubit. The detector ranks the 400-bin heterodyne records using the most negative three-bin mean of the I quadrature, plus a cat-size correction:

```text
Score = −(minimum 3-bin moving average of I) + 1.0 × (3.5 − n̄)
```

The Q-quadrature feature did not improve our tested training scores. **Validation:** flip suppression **0.55** at 70% yield (AUC **0.768**). **Official hidden-test leaderboard:** **0.53**. Detection is useful for *post-selection*, but the measured false-positive rate makes direct Pauli-frame correction harmful.

## 2. Required results table

| Quantity | Value / finding | How obtained |
|---|---|---|
| Nominal bit-flip rate ΓZ (n̄ = 2, 2.5, 3, 3.5) | 0.00170, 0.000782, 0.000350, 0.000153 | Liouvillian spectrum; device sheet |
| Nominal phase-flip rate ΓX, same sizes | 0.099, 0.123, 0.147, 0.171 | Liouvillian spectrum; device sheet |
| Flip probability per 200-unit segment, nominal | 14.6%, 7.30%, 3.40%, 1.51% | Master equation; device sheet |
| Flip probability per segment, hidden | 24.30%, 14.80%, 8.82%, 4.56% | Observed label fractions on validation |
| Buffer output during flips vs. cat size | Densest 1-unit window: **2.54, 3.51, 4.28, 5.43** clicks in flip-labeled segments; **1.87, 1.94, 2.06, 2.13** in non-flip segments | Tier-A nominal click records; **proxy**, not isolated total photons per flip |
| Detector on validation | AUC **0.768**; **0.55**; yield **0.700**; TPR **0.69**; FPR **0.2668**; correction S **3.46** | `scoring.evaluate`, 30% discarded globally |
| Fraction discarded by cat size | **100%, 80.70%, 0.55%, 8.30%** for n̄ = 2, 2.5, 3, 3.5 | `scoring.by_cat_size` on validation |
| Final hidden-test suppression | **0.53** | Official leaderboard (user-reported) |
| P(flip | n), large bursts | At n̄ = 2.5, rises with maximal 1-unit click count; approximately **0.48 for n ≥ 5** | Tier-A plot, tail grouped; few samples in tail |
| Best oracle correction score S | **0.949 (preliminary)** | `pneg` from 300 counting trajectories; threshold at 0.5; high sampling uncertainty |
| Distance-5 logical bit-flip probability, n̄ = 3.5 | No herald **0.1828**; Pauli-frame correction **0.4928**; post-selected **0.1040** | `repcode` using measured size-3.5 TPR/FPR and residual flip fraction; five-part yield **0.171** |

**Numerical check.** At n̄ = 2.5, ΓZ = 0.0007823042, 0.0007823035, 0.0007823035 for Fock cutoffs N = 20, 24, 28. ΓX ≈ 0.1225995 was unchanged at displayed precision. The rates are converged over the tested range. ΓX is close to the simple photon-loss estimate 2κ₁n̄ = 0.100; thermal excitations also flip parity, whereas the buffer's two-photon dissipation preserves parity.

## 3. Figures and physical interpretation

![Flip probability versus cat size](flip_vs_cat.png)

*Figure 1.* Larger cats flip less often. The hidden device has higher flip rates than the nominal model at each measured size, consistent with its finite-bandwidth buffer and Kerr nonlinearity.

![One-unit burst proxy versus cat size](burst_vs_cat.png)

*Figure 2.* Flip-labeled segments show increasingly strong short buffer bursts as n̄ grows. The non-flip background rises much more slowly. These are **record-level peak-window counts**; they do not yet establish the isolated burst duration τ or validate a precise n̄²κ₂τ scaling law.

In a long trajectory, the largest sudden decrease in ⟨ZL⟩ occurred near t = 913.5, accompanied by **eight tightly clustered buffer clicks** around t = 913.6–914.0 (nine in a wider ±10-unit window). This is a strong example of a disturbance creating buffer activity, although the plotted ⟨ZL⟩ did **not** reach −1, so it cannot by itself establish a completed flip.

## 4. Capstone: What is a herald worth?

**Does a large burst prove a flip?** No. In the nominal click records at n̄ = 2.5, P(flip | n) increases with burst size but remains well below 1 in the measured tail. The buffer reports that the memory left its protected subspace; after stabilization, it may return to the original lobe, reach the other lobe, or remain partially uncertain. A herald is evidence of a disturbance, **not a guaranteed logical X**.

**Post-selection versus correction.** The detector's 70%-yield operation on validation catches about 69% of flips but falsely flags 26.68% of non-flips. Discarding flagged segments reduces the conditional flip rate by 55%. Applying X to all flagged segments instead adds too many errors: the correction score is S = 3.46 (> 1), where smaller is better and S < 1 would be beneficial. The more privileged `pneg` oracle gives a preliminary S ≈ 0.949, suggesting only modest correction benefit even with far more information; more simulation is needed to establish this reliably.

**Effect on error correction.** At cat size 3.5, the provided distance-5 model predicts a logical bit-flip probability of **18.28%** without heralds, **49.28%** with our Pauli-frame correction, and **10.40%** after post-selection. The latter retains only **17.1%** of five-part combinations. Thus post-selection improves the quality of surviving data but is costly in yield and is not a direct substitute for fault-tolerant error correction. A phase-flip repetition code has bit-flip distance 1; using heralds to handle bit flips robustly requires an outer code or decoder designed to exploit located errors, possibly as erasures.

**Conclusion.** Short buffer bursts carry useful *statistical* information about flips, especially for larger cats. Our heterodyne detector reaches the validation full-points threshold (0.55) and scores 0.53 on the official hidden test, but the same heralds do not reliably specify a corrective X. The remaining key physics task is to determine which burst patterns represent completed versus aborted flips and quantify their duration and emitted photon number.

*Outstanding: isolated burst size/duration; systematic two-mode bandwidth comparison. Figures were reconstructed from measured tabular results, not exported directly from the notebook.*
