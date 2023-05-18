# New Fee Model

This is a documentation for a new fee model.

Factors (vault contract new states)
- minMarginFeeBps: default value: 0.0015 (0.15%)
- maxMarginFeeBps: default value: 0.01 (1%)
- maxAdditionalMarginFeeBps: default value: 0.5 (50%)
- minExtraPnlFeeBps: default value: 0.05 (5%)
- maxExtraPnlFeeBps: default value: 0.1 (10%)

## Function
(excluding borrowing fee logic at the moment, we will take it into action after finishing the current marginFee discussion)
```
export const getNewFeesV2 = (realizedPnl, positionSize) => {
  let marginFee = minMarginFeeBps * positionSize; // basic margin fee
  let extraPnlFee = 0;

  const pnl1 = realizedPnl - marginFee;
  if (pnl1 > 0) {
    let additionalMarginFee = pnl1 * maxAdditionalMarginFeeBps // 50% of net Pnl as default

    // ensure total margin fee doesn't exceed maxMarginFeeBps (1%) of position size
    if (additionalMarginFee > positionSize * (maxMarginFeeBps - minMarginFeeBps)) {
      additionalMarginFee = positionSize * (maxMarginFeeBps - minMarginFeeBps);
    }

    marginFee += additionalMarginFee;

    // apply extra PnL fees

    const pnl2 = realizedPnl - marginFee;

    if (pnl2 > 0) {
      const relativePnl = pnl2 / positionSize; // percentage of net Pnl to position size
      let extraPnlFeeBps = minExtraPnlFeeBps + relativePnl;

      // ensure that extraPnLFee not exceed maxExtraPnLBps (10%) of the net PnL
      if (extraPnlFeeBps > maxExtraPnlFeeBps) {
        extraPnlFeeBps = maxExtraPnlFeeBps;
      }

      extraPnlFee = pnl2 * extraPnlFeeBps;
    }
  }

  let totalFee = marginFee + extraPnlFee;

  return totalFee;
}
```

## Logic Explained
- apply basic / min margin fee when closing a position (0.15% as default).
- when the net PnL (basic PnL - basic / min margin fee) is larger than 0, apply additional margin fees. Additional margin fees are set as maxAdditionalMarginFeeBps of net PnL (50% as default) until the total margin fee (basic + additional margin fees) reaches maxMarginFeeBps of position size (1% of position size as default). make sure that total margin fee not exceed maxMarginFeeBps of position size (1% of position size as default).
- when the net PnL (basic PnL - total marginFee) is larger than 0, apply extra PnL fees. extra PnL fees are set as minExtraPnlFeeBps (5%) ~ maxExtraPnlFeeBps (10%) of the net PnL. Which value between min and max values is selected depends on the relative size of the net PnL to the position size.
- total fee will be the sum of total margin fee and extra pnl fee.