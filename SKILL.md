---
name: architecture-explorer-3d
description: Research an end-to-end software flow across repositories and render it as an evidence-backed zoomable 3D architecture map with semantic drill-down, concise component summaries, rich hover details, and verified Azure DevOps code links. Use when the user wants to understand, trace, visualize, or explore an E2E flow in 3D and zoom into services, components, libraries, or code.
---

# 3D Architecture Explorer

Turn an architecture-learning request into a durable, source-linked 3D scene and
open it in the `architecture-explorer-3d` canvas.

## Required outcome

1. Trace the requested flow in runtime order across every relevant repository.
2. Build a dense, deeply nested hierarchy from service -> component(s) -> code
   element(s), repeating component and code levels wherever source-backed decomposition
   adds understanding. A multi-service flow should normally contain roughly 50-100
   meaningful nodes and 8-12 useful zoom steps on decisive branches when the source
   supports that depth; a shallow service -> component -> method chain is insufficient.
3. Give every node:
   - a concise `role` stating why the node exists in this specific E2E flow;
   - a one- or two-sentence `summary`, with two useful lines rendered inside the rectangle;
   - richer `details` for hover and selection;
   - concrete responsibilities, input, output, and failure behavior where known;
   - the owning repository;
   - a verified Azure DevOps source link that opens the exact file/range when possible;
   - a verified component-specific telemetry deep link beside Code when the cluster,
     database/resource, table, and component filter are known.
4. Create a durable scene JSON file.
5. Validate the scene, then open the 3D canvas at the service overview.
6. Generate a durable, self-contained HTML version of the completed scene.
7. Publish exactly one direct bookmark for that HTML file under the top-level
   `Imported` folder in every available Microsoft Edge and Google Chrome profile.

Never fabricate components, ownership, call order, or source links. Mark inferred
relationships in `evidenceStatus` and describe the uncertainty.

Every runtime edge must explain the communication, not just name the arrow:

- what payload or state crosses the edge;
- what the destination returns at a high level;
- wire/in-memory format and protocol;
- a compact representative data structure;
- whether it is an in-process call, same-host IPC, inter-process, inter-node,
  cross-service, public ingress, or response path;
- which network/trust boundary it crosses;
- expected errors, status codes, retry/cancellation behavior, and whether failures
  travel out-of-band or inside a streaming response.

## Research workflow

1. Read the user's named bookmarks or learning artifacts when available. Treat them
   as discovery context, not proof; verify claims against current source.
2. Use `list_projects` when the flow may span repositories.
3. Use the `orchestrate` cross-repository research workflow: one read-only research
   session per relevant repository. Investigate the current repository inline.
4. Ask one focused question only when the entry point, scenario, or repository scope
   cannot be established from the request and source.
5. Follow real callers and callees. Capture precise repository-relative paths,
   symbols, line ranges, process boundaries, protocols, request/response shapes,
   authentication, cancellation, telemetry, and failure paths.
6. Derive Azure DevOps links from the actual remote. Prefer immutable commit links
   with exact line ranges. Use `target="_blank"` behavior in the renderer; do not
   rewrite links through a redirector.
7. For every major service, continue through the entry adapter, contract mapping,
   authorization, validation, orchestration, persistence/state transition, outbound
   client, retry/timeout logic, completion path, and rollback/failure path. Give each
   distinct responsibility its own node when source proves it.
8. Do not stop at the first matching class or method. Trace the meaningful callers
   above it and callees below it, including side branches and response handling, until
   another service boundary or decisive persisted state is reached.
9. Actively verify telemetry after source tracing. When source identifies a likely
   resource but access was unavailable, retry the live connection when VPN/auth becomes
   available, inspect the real schema, and prove a component filter before omitting the
   Telemetry action.

## Durable storage

Write scenes under:

`%USERPROFILE%\OneDrive - Microsoft\Documents\Architecture Explorer\scenes\<topic-slug>.json`

If OneDrive Documents is unavailable, use:

`%USERPROFILE%\Documents\Architecture Explorer\scenes\<topic-slug>.json`

Do not store durable scenes in session state, temporary folders, or source
repositories.

## Standalone HTML bookmarks

Publishing the standalone HTML and browser bookmarks is required for every completed
architecture scene; do not wait for a separate user request.

- Generate one self-contained `.html` file that includes the complete scene,
  renderer, styles, and interactions.
- Write it beside the durable scene collection under
  `%USERPROFILE%\OneDrive - Microsoft\Documents\Architecture Explorer\html\<topic-slug>.html`,
  falling back to `%USERPROFILE%\Documents\Architecture Explorer\html\<topic-slug>.html`
  when OneDrive Documents is unavailable.
- Publish the resulting local `file:///` URL to both Google Chrome and Microsoft Edge.
- Inspect every available browser profile (`Default` and `Profile *`) and update each
  profile's bookmarks.
- Add exactly one URL bookmark for that HTML file directly under the browser's
  top-level `Imported` folder. Use the architecture title as the bookmark name.
- Create the top-level `Imported` folder under the bookmarks bar/favorites bar when it
  does not already exist.
- Do not create a topic folder, numbered flow folders, per-component source
  bookmarks, a bookmark manifest, or an embedded source-bookmark tree. The `Code`
  and `Telemetry` actions on each rectangle are the source-navigation experience.
- Republish the same topic by replacing its existing direct bookmark rather than
  creating duplicates.
- Preserve unrelated bookmarks and profile metadata. Verify the bookmark by reparsing
  the saved browser file and confirming exactly one matching direct child exists.
- Keep every `Code` and `Telemetry` link opening in a new browser tab.

## Scene model

Use the sibling `scene-schema.json` as the contract and `scene-template.json` as a
starting point. Important semantics:

- `position` is `[x, y, z]` in world coordinates.
- `parentId` creates semantic zoom hierarchy.
- `level` is one of `service`, `component`, or `code`. The complete E2E flow is
  scene metadata, not a node or hierarchy level.
- Treat `level` as a semantic type, not a maximum depth. A component may contain
  narrower components, and a code node may contain more specific code nodes. For
  example: service -> subsystem component -> runtime component -> module code ->
  class code -> decisive method code.
- Research beyond the first matching file or class. Continue following callers,
  callees, dispatch, transformations, and response handling until the decisive
  implementation and failure behavior are explained. Prefer roughly 8-12 useful zoom
  steps on important branches when source supports them, and decompose contracts,
  guards, state writes, retry loops, callbacks, and cleanup into separate nodes.
- Never manufacture depth for visual effect. Add another layer only when it reveals
  a distinct responsibility, boundary, decision, transformation, protocol adapter,
  state transition, or failure-handling point. Collapse trivial wrappers and
  repetitive pass-through methods into their nearest meaningful node.
- Color each rectangle's fill, border, accent, and minimap marker consistently by
  semantic level: services are blue, components are green, and code is pink.
  Node-specific colors must not override this mapping; architecture category remains
  distinct through its category pill and technology icons.
- `architectureHints` is an optional, conservative set drawn from `UI`, `Backend`,
  `DNS`, and `Database`. Add a hint only when that role is relevant and verified.
- Prefer a verified `runtimeProfile` over a generic architecture hint. Capture category,
  technologies/languages, build system, runtime, hosting substrate, regional/global
  scope, and owning team. Omit unknown fields rather than guessing Kubernetes, VMSS,
  framework, regionality, or ownership.
- `edges` describe runtime flow, not merely static dependency.
- Every substantive edge must populate `payload`, `response`, `format`, `structure`,
  `communication`, `protocol`, `networkBoundary`, `boundaryDetails`, and `errors`.
- Every newly revealed service/component/code view should retain evidence-backed
  runtime arrows that communicate what data or execution context moves between nodes.
  Add an edge only when source proves a real call, message, response, data transfer, or
  operational dependency participating in the flow.
- Never render hierarchy, containment, or drill-down relationships as arrows. In
  particular, do not synthesize labels such as "Into <component>" from `parentId`.
  Parent containment is communicated only by nested rectangles and breadcrumbs.
- Arrow labels must remain readable at the zoom level where the connected nodes first
  appear. Render them as compact plain text without a containing rectangle, close to
  the arrow path. Use concise primary labels and short payload/format/protocol metadata; put
  full structures, boundaries, and error semantics in the clickable edge detail panel.
- Keep only the white primary arrow action visible at rest. Reveal gray
  payload/format/protocol/communication lines only while the pointer is over that
  primary text, and hide them again when it leaves. Reserve the expanded annotation's
  space so hover does not shift the headline or cause flicker.
- Structure revealed arrow details as three explicitly labeled groups:
  `Input: <parameters or payload>`, `Protocol: <protocol and format>`, and
  `Response: <high-level return value>`. Author an edge-level `response` when the
  destination output is not already precise enough; never infer a specific response
  shape from an unrelated component summary.
- Make the primary arrow action unmistakably interactive without adding a surrounding
  rectangle: use a small information/expand glyph, a subtle dotted underline, pointer
  cursor, and blue hover emphasis. The hit target follows the white headline rather
  than the normally hidden gray details.
- Anchor the white primary arrow action immediately above its blue route, with roughly
  6-10 pixels of visual clearance. Reserve hidden detail space below the headline
  rather than pushing the headline upward; hovering must not move it away from the
  route the text explains.
- Apply the same attachment when collision handling places a white primary action
  below the route: keep its first line roughly 6-10 pixels below the blue line, never
  drop it beneath the connected rectangles. Increase the horizontal gap between
  rectangles when the complete above/below labels cannot fit near their shared route.
- Wrap arrow annotations across as many complete lines as needed inside the available
  inter-card gap. Never clip them with canvas `maxWidth`, slice their strings, or hide
  trailing metadata. Prefer line breaks over shrinking text; if the complete label
  cannot coexist with nearby content, move or hide the whole annotation until zoom
  provides enough room.
- Reserve generous horizontal space between connected rectangles and place each arrow
  label inside that gap, centered between its source and destination. Do not float
  labels far above the route or let them overlap either rectangle. Apply the same
  spacing rule to the overview row and DFS child grids.
- Measure the complete arrow label before placement. If it cannot fit in the gap
  without overlapping either rectangle, place it immediately above the connected
  rectangles instead; never shrink it into unreadable text or force it into the gap.
- Keep arrow labels narrow and retain invisible hit targets for selection. Detect
  collisions between those targets. Within each overlapping horizontal
  cluster, alternate placement above and below the flow in order: top, bottom, top,
  bottom. Recalculate hit targets from the final positions.
- Never use ellipses as semantic placeholders or truncation indicators. If an arrow
  annotation still collides after alternate placement, keep it hidden at that zoom
  level and restore the complete plain-text annotation once zoom creates enough space.
- Render each network/trust-zone name as a high-contrast badge above its enclosing
  ellipse, clamped inside the viewport. Do not place low-contrast zone text over the
  line, inside the circle, or behind node/edge content.
- Reserve a dedicated foreground band above node cards and runtime-profile badges for
  network-zone names, VNet/subnet descriptions, and resource lists. Wrap long zone
  text, draw annotations after scene content, and move the architecture row downward
  when necessary so no network text is hidden behind a card, arrow, badge, breadcrumb,
  toolbar, or another annotation.
- When the network annotation cannot sit directly beside the enclosing boundary,
  extend the boundary upward toward it: reshape the ellipse or add a short tapered
  boundary connector so the orange/network outline visibly terminates at the
  explanation. Keep that connector behind text and outside runtime-profile badges.
- Treat semantic-label occlusion as a layout defect everywhere. Measure cards, runtime
  badges, technology icons, arrows, network annotations, E2E markers, and fixed UI
  panels before final placement; reserve space or reposition content rather than
  painting one meaningful item behind another.
- Hovering an arrow's invisible label hit target must highlight the complete arrow path and label
  in blue, matching the visual emphasis used when hovering a connected node.
- Draw all flow lines, animated spines, packets, and arrowheads behind their text
  annotations. A line must never cross through or visually obscure its own label;
  reserve a clear text aperture or repaint the complete annotation in the foreground.
- Define `e2eFlow.nodeIds` as the canonical request flow in runtime order, from the
  user/client entry point through execution to the final consumed result. The renderer
  draws it as a separate thick, high-contrast spine with repeated direction arrowheads,
  forward animation, and explicit START/END markers.
- Preserve the complete response route in `e2eFlow.nodeIds`, including repeated
  services when the result returns through them. Never shortcut Engine directly to
  the UI when the real path returns through Gateway, transport/client, query state,
  or a host callback. Repeated node IDs are valid when runtime order revisits a
  service on the response path.
- The E2E spine must be semantically adaptive: at overview zoom it collapses hidden
  implementation nodes to their visible service ancestors; as component/code
  rows appear, the same spine expands through those newly visible nodes. Keep this
  route continuous and ordered; do not define disconnected per-layer spines.
- Render START and END as foreground callouts outside the first and last rectangles,
  with short connector lines to the actual endpoints. They must remain visible above
  node cards and be clamped inside the viewport rather than hidden behind content.
- `kind: "async"` renders a dashed edge; `flow: false` suppresses the animated packet.
- Assign `networkZone` to nodes that run inside a known network/trust zone and define
  that zone in `networkZones`. The renderer draws a toggleable enclosing ellipse.
- Do not invent network placement. Use `external` or omit `networkZone` when unknown.
- Keep the top level sparse. Place children near their parent so wheel zoom toward a
  location naturally reveals the next layer.
- Use 280-420 world units between top-level flow steps and 70-150 units between a
  parent and its children.
- Use `evidenceStatus: "verified"` only for source-backed facts. Use `"inferred"` or
  `"external"` otherwise and explain why in `details`.

## Composition rules

- Scene title and summary describe the complete user intent without creating a
  synthetic root node.
- Services form the only readable left-to-right E2E spine at initial zoom. Do not
  expose component/code rows in the overview.
- The initial/reset overview must fit the complete E2E flow in the viewport, from
  request START through result END, without requiring pan or zoom.
- Lay all top-level E2E services on one centered horizontal baseline. Never use a
  diagonal low-left to high-right composition for the overview. Compute card widths
  and inter-card gaps from the viewport so the row remains centered and fully visible.
- Component and code nodes require progressively closer zoom or explicit focus.
- Use aggressive semantic-zoom collapse thresholds: when zooming out, lower-level
  rectangles should disappear quickly rather than linger and overcrowd the overview.
- Keep the camera orientation fixed. Users may pan and zoom, but must not yaw, orbit,
  rotate, or tilt the architecture plane; spatial direction must remain stable.
- The initial/reset view alone is fit to the complete E2E route. Wheel input after that
  must visibly scale and pan the whole scene around the pointer, even over empty canvas;
  it is expected that zooming in no longer keeps the full E2E route visible.
- Keep interaction latency low in both hosted and self-contained HTML forms. Target
  smooth 60 FPS on a normal laptop, cap canvas device-pixel scaling when necessary,
  avoid repeating expensive static layout/text work every frame, and keep wheel zoom
  visibly responsive rather than using overly conservative scale factors.
- During pointer dragging, translate the cached static scene bitmap rather than
  rebuilding layout, text, hit targets, and network geometry on every move. Rebuild
  one sharp, exact frame after release; pause flow particles during the drag.
- Keep semantic-navigation debounce short enough to prevent accidental multi-level
  skips without making deliberate zoom feel delayed. Flow particles should move
  slowly enough to study direction, taking roughly 8 seconds to complete the route.
- Use depth-first semantic zoom, never breadth-first expansion. The overview shows the
  top-level services only. Zooming into one node turns only that selected node into
  a large containing rectangle and shows only its direct children. Other branches and
  same-depth descendants stay collapsed.
- Support arbitrary evidence-backed hierarchy depth rather than assuming exactly
  three zoom steps. Repeated component and code levels retain their semantic colors,
  while breadcrumbs and containment communicate how deep the user has drilled.
- Zooming into one of those children replaces the expanded view with that child's
  container and direct children. Continue one selected branch at a time, like DFS;
  never reveal every node at the next depth merely because the camera crossed a zoom
  threshold.
- A parent acting as a container remains labeled but becomes visually subdued
  (translucent/vague) so the active child layer receives emphasis. The parent's border
  and bounds must clearly contain its descendants without obscuring them.
- Zooming back out across the semantic threshold returns exactly one step to the
  focused node's parent and restores that parent's direct-child composition.
- In a fixed-layout DFS view, wheel zoom must produce immediate visible semantic
  navigation rather than changing an invisible camera distance: scroll in over a
  rectangle to open that branch; scroll out to return exactly one parent. Debounce
  level changes briefly so one trackpad gesture cannot skip several hierarchy levels.
- Wheel expansion must also work when the pointer is over empty canvas. Resolve the
  nearest visible expandable rectangle by screen distance; when two candidates are
  both within the close-proximity radius, expand both. If none are close, expand only
  the single nearest candidate.
- Gate each semantic expansion by zoom depth. Do not reveal a child layer until there
  is enough screen scale to preserve non-overlapping sibling rectangles; an attempted
  early expansion should only zoom the scene, signaling that the user must move closer.
- Verify that all visible sibling rectangles have disjoint bounds after every layout.
  Parent containers may enclose descendants, but unrelated or sibling rectangles must
  never be drawn on top of one another.
- Measure floating UI panels after their content is rendered. Position breadcrumbs,
  controls, and supporting panels from actual bounds rather than fixed offsets so
  typography and viewport changes cannot make interface rectangles overlap.
- Make `Reset view` a prominent, comfortably sized primary navigation control rather
  than a small utility button. It must remain easy to find and activate at every zoom.
- Expand selected components locally in the existing composition, never to the full
  viewport. Show only each selected component's immediate children. Do not reveal
  grandchildren unless that child is separately expanded by a later gesture.
- Preserve one continuous horizontal flow lane during semantic expansion. Grow an
  expanded parent sideways around its immediate children and place those children
  left-to-right on the same baseline as the surrounding E2E systems. Never stack newly
  revealed rectangles downward.
- Keep all unexpanded E2E services visible in their original runtime order while a
  branch expands. Push preceding systems farther left and following systems farther
  right; allow normal pan/zoom to inspect an expanded lane that exceeds the viewport.
- Apply the same horizontal containment recursively: expanding a child widens its
  containing branch and pushes neighboring rectangles sideways rather than creating a
  second row.
- Scrolling out collapses the nearest expanded component or the nearest two when both
  are close, including their already-expanded descendant branches.
- Provide a toggleable side minimap that shows the whole architecture, highlights the
  current camera target, and supports click-to-pan navigation.
- Anchor the minimap in the lower-left above the legend. It must not obscure the title,
  breadcrumbs, upper flow labels, or the main horizontal E2E row.
- The minimap must include one-click row controls for the deepest visible semantic
  level and an Overview action that collapses immediately to the main service row.
  Manual row collapse is a hard visibility cap; zoom cannot reveal deeper rows until
  the user raises that cap.
- Give the minimap expanded, compact, and hidden states. Compact mode keeps a small
  labeled restore control while hiding the map, row controls, and helper text.
- Every right-side node or edge detail panel must have an obvious close control. A
  later node or edge selection reopens it normally.
- Render `Telemetry ↗` beside `Code ↗` on a node card when verified telemetry is
  available. Both links open in a new tab with `noopener noreferrer`. Descendants may
  inherit their nearest ancestor's telemetry only when it genuinely scopes the same
  component area.
- Never fabricate telemetry. Record the verified telemetry resource in
  `telemetrySource`, the readable query in `telemetryQuery`, and its deep link in
  `telemetryUrl`. Every telemetry query must end with `| limit 10`, including queries
  that already use a time filter or ordering.
- Services and components revealed during drill-down remain inside their owning
  container on the shared horizontal lane.
- Code nodes identify decisive classes, functions, controllers, clients, or files.
- Do not create a node for every file. Include only nodes that explain a meaningful
  responsibility, transformation, boundary, decision, or failure mode.
- Keep labels under 36 characters and summaries under 260 characters.
- Write summaries so their first two rendered lines independently explain what the
  rectangle does; do not rely on hover for basic comprehension.
- Render a node's hover-detail panel wholly inside that node's rectangle, anchored
  to its lower interior with text wrapped to the available width. Never let node
  hover details float beyond or obscure a neighboring rectangle.
- Render a clearly labeled `ROLE` section inside every non-container rectangle. Show
  the explicit `role` when authored; for older scenes, derive it from the first one or
  two verified responsibilities. Wrap and show the complete role inside the rectangle;
  never cap it to a fixed number of lines. Measure each card independently and make its
  height fit the title, owner, summary, full role, and source/telemetry actions with
  only compact bottom padding. Do not force every sibling to the tallest sibling's
  height, and do not leave dead vertical space after ROLE.
- Render verified `architectureHints` as small, muted badges immediately beside the
  rectangle. Use them sparingly as orientation hints, not as prominent titles. Do not
  tag every descendant, infer DNS merely from an HTTP URL, or add an obvious hint that
  provides no useful architectural distinction.
- Render `runtimeProfile` as two or three compact muted lines beside the rectangle:
  category plus technologies, runtime/hosting/scope, and owner when team ownership is
  explicitly evidenced. Keep full evidence and longer explanations in details.
- Add compact, recognizable technology icons beside each verified runtime profile,
  covering categories and platforms such as UI, React, TypeScript, Backend, C#/.NET,
  Rust/C++, Database, AKS/Kubernetes, and VMSS. Pair every icon with adjacent readable
  text; icons orient the user but never replace the verified platform label.
- Give every technology icon a hover tooltip with the technology's full name and make
  the icon clickable. Clicking opens that technology's official documentation home
  page in a new tab with `noopener noreferrer`; never link to unofficial tutorials or
  search results.
- Emphasize the primary architectural category more strongly than individual
  technologies. Every verified UI, Backend, Database, or DNS node gets a prominent
  high-contrast category pill with icon and full label; framework and hosting icons
  remain smaller supporting cues.
- Network zones must identify the actual verified topology rather than use a generic
  trust-zone label. Include VNet/subnet or AKS networking, public/private ingress,
  load balancer, public IP, NSG, DNS, VMSS/AKS nodes, and regional scope when source
  proves them. Describe alternate deployment topologies conditionally.
- Style response edges distinctly and prefix their labels with `Response` so reverse
  arrows are understood as results, frames, acknowledgements, or callbacks returning
  through the request path rather than accidental backwards execution.
- Preserve important overview API labels. Increase horizontal spacing and collapse
  closely related implementation services behind an evidence-backed parent group when
  necessary; reveal those services only on zoom.
- Use substantially larger, high-contrast typography throughout the canvas and its UI:
  node titles, owner metadata, summaries, roles, arrows, zones, toolbar, minimap,
  breadcrumbs, hover cards, and details. Never preserve fit by shrinking essential text
  below comfortable reading size; widen the horizontal lane or rely on zoom/pan.
- Keep the page title and overview description at normal application-heading scale;
  larger architecture typography applies to working content, not an oversized masthead
  that overlaps breadcrumbs or the flow.
- Use details for the deeper explanation rather than overcrowding the scene.
- Include reverse response edges when they materially improve understanding.

## Validation

Before opening:

1. Parse the JSON.
2. Confirm node IDs are unique.
3. Confirm every `parentId`, edge source, and edge target exists.
4. Confirm every position contains three finite numbers.
5. Confirm every Azure DevOps link was derived from a real remote and points to an
   existing path.
6. Confirm all substantive nodes have a summary, details, repo, and evidence status.
7. Decode or inspect every telemetry deep link and confirm its query ends with
   `| limit 10`.

Open with:

`open_canvas({ canvasId: "architecture-explorer-3d", instanceId: "<topic-slug>", input: { scenePath: "<absolute-scene-path>" } })`

Use `get_scene_summary` after opening. Use `focus_node` when the user asks to jump to
a specific service or component.
