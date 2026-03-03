---
illustration_id: 05
type: framework
style: sketch
---

基础组件架构 — 多人协作一致性保障 - Sketch Framework

Layout: Two-layer hierarchy, bottom foundation row + top usage row

STRUCTURE: hierarchical — bottom layer is base components (reusable), top layer is slide pages that consume them

NODES:
Bottom layer (Foundation — 基础组件层):
- 6 component boxes in a row: "BaseContentSlide", "BaseCard", "BaseLineChart", "BaseBarChart", "BaseStackedBarChart", "BaseTable"
- Label above entire row: "基础组件 (6个)" with underline
- Small sketch icons inside each: slide icon, card icon, line chart, bar chart, stacked bars, table grid

Top layer (Usage — 幻灯片页面层):
- 4 example page boxes: "CoverSlide", "GDP章节", "生产端章节", "..." (indicating more)
- Label above: "幻灯片页面 (33个独立 .tsx 文件)"
- Dotted boxes to show there are many more

Arrows: Multiple upward arrows from bottom components to top slides — showing each slide page consumes multiple base components

Right side annotation: sketched sticky-note style box saying:
"3人 × 3工具 × 2模型
→ 风格漂移风险
→ 组件保证一致性"

RELATIONSHIPS:
- Arrows from BaseCard → multiple slides (showing reuse)
- Arrows from BaseLineChart → GDP章节, 生产端章节 (specific usage)
- Label on arrows: "复用"

LABELS:
- Component names in boxes (exact names from article)
- "视觉一致性由组件自身保证" as bottom caption
- "Claude Code / Cursor / Trae / Codex" as small handwritten note near top layer indicating different tools used

COLORS: Pure black pencil sketch. Bottom layer boxes with slightly heavier/thicker strokes to indicate "foundation" weight. Top layer boxes with lighter strokes. Arrows with arrowheads. Cross-hatching or dot fill to differentiate layers.

STYLE: Raw pencil notebook sketch. Rough rectangles, freehand arrows, handwritten labels. Like a component architecture diagram sketched during a design discussion. Natural pencil texture, imperfect lines.

ASPECT: 16:9

Clean composition with generous white space. White background. Clear two-layer separation. Text should be large and prominent with handwritten-style fonts. Component names must be legible.
