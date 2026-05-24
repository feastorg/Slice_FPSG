# Slice_FPSG TODO

## CI Results (Pass 1)

- [x] DRC: FAIL — 4 errors (2x shorting_items: nets shorting I2C_CLK and I2C_DAT, 2x solder_mask_bridge: front mask bridges different nets)
- [x] ERC: FAIL (blocked by DRC errors)
- [x] Fab: FAIL (blocked by DRC errors)
- [ ] gen-kibot-index: SKIPPED (upstream failed)
- [ ] deploy-pages: SKIPPED (upstream failed)

**Note: 4-layer board (In1.Cu/In2.Cu enabled in kibot config)**

## Pass 2 – Pre-fab Review

- [ ] Fix 2x shorting items (I2C_CLK, I2C_DAT shorted to unnamed net)
- [ ] Fix 2x solder mask bridge (front mask aperture bridges different nets)
- [ ] Verify BOM completeness
- [ ] Confirm board outline and mounting holes
- [ ] Update README.md (still says "Slice Template")
