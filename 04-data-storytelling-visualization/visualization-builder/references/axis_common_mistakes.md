# Axis Common Mistakes

Common axis-related visualization traps and how to fix them. This reference extends the chart type guidance in `chart_selection_guide.md` with axis-specific detail.

## Trap 1: Bar chart y-axis starting at 0 for non-zero-baseline data

**Problem**: matplotlib/seaborn bar charts default to y=0 baseline. For data that
has no meaningful zero point (temperature: 20–35°C, profit margin: 15–25%,
satisfaction scores: 7.0–9.5), ~60% of chart height is wasted whitespace beneath
the bars, compressing visual differences between groups.

**Fix**: Set y-axis lower bound near the data minimum:

```python
ax.set_ylim(data_min - margin, data_max + margin)
```

**Rule of thumb**: If zero is a meaningful reference point (counts, revenue,
error rates), keep 0 baseline. If zero is arbitrary (temperature, scores,
ratios with a narrow range), start from the data range floor.

---

## Trap 2: Mixed positive/negative values in grouped bar charts

**Problem**: When a grouped bar chart includes both positive and negative values,
matplotlib draws bars upward from y=0 for positive values and downward from y=0
for negative values. Bars pointing in opposite directions make comparisons
fundamentally misleading—the eye reads bar length relative to 0, not relative
to the other bars in the group.

**Example**: Winter temperature comparison: max_temp (~+3.8°C) bar goes up,
min_temp (~-6.5°C) bar goes down, mean_temp (~-2.2°C) bar goes down. Three
related metrics pointing in different directions are unreadable as a group.

**Fix**: Replace bar chart with a **dot/scatter plot + error bars**:

```python
ax.errorbar(x + offset, means, yerr=stds, fmt='o', color=color,
            capsize=4, markersize=10, label=label)
```

Always add a zero reference line for mixed-sign data:

```python
ax.axhline(y=0, color='gray', linestyle=':', alpha=0.5, linewidth=1)
```

**Alternative**: Use a floating bar (positive portion only) or a panel plot that
separates positive and negative metrics.

---

## Trap 3: Connecting discontinuous time periods in line charts

**Problem**: When data has a temporal gap (e.g., 2005–2009 and 2020–2024 with
no data for 2010–2019), a single continuous line connects far-apart points,
creating a visually misleading "jump" or "cliff" between periods. The implied
trend across the gap is an artifact, not data.

**Fix**: Plot each period as a separate segment. Add a visual break marker:

```python
# Segment 1
ax.plot(indices[:n1], vals[:n1], marker='o', ...)
# Segment 2
ax.plot(indices[n1:], vals[n1:], marker='o', ...)
# Break marker
ax.plot([n1 - 0.5, n1 - 0.5], [ymin, ymax], ':', color='gray', alpha=0.5)
ax.annotate(f'⏇ {gap_years}年间隔', xy=(n1 - 0.5, ymid),
            fontsize=9, color='gray', ha='center')
```

---

## Trap 4: Y-axis tick density too high

**Problem**: With `MultipleLocator(0.5)` on a narrow data range (e.g., 20.7°C to
23.3°C), the y-axis produces 7+ tick labels on a ~4-inch plot area. This exceeds
readable density, and data-point annotations can collide with axis labels.

**Fix**: Limit ticks to 4–5 per axis:

```python
# Manual ticks
ax.set_yticks([20.0, 21.0, 22.0, 23.0, 24.0])

# Or use MaxNLocator
ax.yaxis.set_major_locator(mticker.MaxNLocator(nbins=4))
```

**Rule of thumb**: If ticks per axis-inch > 3 (8 ticks on a 2.5-inch axis),
reduce tick count. This holds for both x and y axes.

---

## Trap 5: fig.suptitle cut off by tight_layout

**Problem**: `plt.tight_layout()` allocates the entire figure area to the axes
(rect=[0,0,1,1]). `fig.suptitle()` placed at default y≈0.98 ends up outside the
visible figure boundary and gets clipped.

**Fix**: Reserve space at the top before calling suptitle:

```python
plt.tight_layout(rect=[0, 0, 1, 0.90])   # axes occupy bottom 90%
fig.suptitle('Title', fontsize=14, fontweight='bold')
```

**Alternative**: Use `constrained_layout=True` when subplots don't share y-axes.

---

## Verification checklist

After generating any multi-series or custom chart, check:

- [ ] Y-axis baseline is appropriate for the data type (zero for counts/revenue,
      data-min for temperatures/scores)
- [ ] No mixed-sign bars in grouped charts (use dot plot instead)
- [ ] Temporal gaps are marked, not connected with a continuous line
- [ ] Y-axis tick labels are readable (not overlapping)
- [ ] Data annotations do not collide with axis labels or each other
- [ ] suptitle / figure titles are fully visible (not clipped)
- [ ] Chart works in greyscale (color-dependent distinctions remain legible)
