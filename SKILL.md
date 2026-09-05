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
   Add every source-backed durable storage boundary that participates in the flow,
   including blobs, object stores, queues, dead-letter queues, databases, caches, files,
   and event streams.
3. Give every node:
   - a concise `role` stating why the node exists in this specific E2E flow;
   - a one- or two-sentence `summary`, with two useful lines rendered inside the rectangle;
   - richer `details` for hover and selection;
   - concrete responsibilities, input, output, and failure behavior where known;
   - the owning repository;
   - a verified Azure DevOps source link that opens the exact file/range when possible;
   - a verified component-specific telemetry deep link beside Code when the cluster,
     database/resource, table, and component filter are known.
   Storage nodes must also identify `storageType`, the stored data or message,
   partition/key/queue selection, retention or visibility behavior, acknowledgement
   boundary, read/delete semantics, retry behavior, and dead-letter path where source
   proves them.
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
   Inventory every durable read/write boundary on that path. Model the actual queue,
   blob/container, table/database, cache, file, event stream, and dead-letter store as a
   storage node rather than hiding it inside the producer or consumer.
   Before composing the scene, create an ordered dependency inventory for every
   participating service. Classify each dependency as always-on, cache-miss-only,
   conditional, parallel, post-response, background refresh, retry/cancellation, or
   failure-only. Include authentication/authorization, configuration, metadata,
   placement, cache, throttling/semaphore, execution, audit, billing, validation,
   shadow/fork, cancellation, telemetry, and durable-storage dependencies when source
   proves they participate. Do not stop after the dependencies named by the user.
   Keep the requested scenario boundary strict: an adjacent flow sharing a gateway,
   queue technology, repository, or deployment is not part of the scene unless it
   causally participates in the requested E2E result. For an Analytics-table scene,
   exclude Auxiliary/AuxGateway, export, indexing, backfill, alternate-ingress, and
   unproven downstream branches unless the user explicitly requests them.
8. Do not stop at the first matching class or method. Trace the meaningful callers
   above it and callees below it, including side branches and response handling, until
   another service boundary or decisive persisted state is reached.
9. Actively verify telemetry after source tracing. When source identifies a likely
   resource but access was unavailable, retry the live connection when VPN/auth becomes
   available, inspect the real schema, and prove a component filter before omitting the
   Telemetry action.
10. For every network-addressable service in the runtime flow, extract the actual
    hostname used by the caller and execute live DNS lookups from the current
    environment. At minimum run `nslookup <hostname>` and targeted
    `nslookup -type=CNAME`, `-type=A`, and `-type=AAAA` queries. Walk the hostname's
    parent labels with `nslookup -type=SOA <candidate-zone>` and
    `nslookup -type=NS <candidate-zone>` until the authoritative delegated zone and
    DNS provider are established.
11. Preserve the complete observed alias chain, resolved addresses, authoritative
    zone/name servers, resolver context, and observation time in the service's
    `entryDetails` fields. Resolve public and private-link variants only when they
    genuinely participate in the traced flow; never enumerate unrelated hostnames.
12. Treat `nslookup` as point-in-time external evidence, not proof of the deployment
    implementation behind an IP. Correlate it with source or infrastructure
    configuration before naming Front Door, Traffic Manager, a load balancer, ingress
    controller, region, or compute substrate. If a private name is unreachable because
    VPN/private DNS is unavailable, record the exact lookup failure and `Not proven`
    rather than omitting the service or guessing.

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
- `level` is one of `service`, `component`, `code`, or `storage`. Storage is an
  orthogonal architecture type, not a semantic-zoom depth: place it beside the
  producer/consumer at the hierarchy level where its durable boundary becomes relevant.
  The complete E2E flow is
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
- Render storage nodes in a fourth, unmistakable amber/gold palette. Draw them as
  storage cylinders with elliptical caps, including in the minimap, rather than as
  service/component/code rectangles. Never recolor a service or component to imply
  storage; create an explicit `level: "storage"` node.
- Use `storageType` to distinguish `blob`, `queue`, `database`, `event-stream`,
  `cache`, `file`, `object`, or `other`. Create separate nodes for materially distinct
  stores, regional copies, processing queues, and dead-letter queues. Do not model
  in-memory channels or ordinary method buffers as durable storage.
- A storage node must state what it stores, who writes and reads it, its region or
  scope, addressing/partition key, acknowledgement boundary, visibility or retention,
  deletion/commit behavior, and poison/dead-letter handling when known. Mark unknown
  details `Not proven`; do not infer Azure Storage, Event Hubs, Kusto, or another
  platform merely from generic queue/blob terminology.
- `architectureHints` is an optional, conservative set drawn from `UI`, `Backend`,
  `DNS`, and `Database`. Add a hint only when that role is relevant and verified.
- Prefer a verified `runtimeProfile` over a generic architecture hint. Capture category,
  technologies/languages, build system, runtime, hosting substrate, regional/global
  scope, and owning team. Omit unknown fields rather than guessing Kubernetes, VMSS,
  framework, regionality, or ownership.
- Give every network-addressable top-level service an `entryDetails` object. Research
  the client-visible DNS name, CNAME/A/AAAA/private-link chain, authoritative zone and
  provider, protocol and port, SNI/Host handling, TLS termination when proven, public
  or private ingress resource, traffic-management/load-balancing layer, network/trust
  boundary, region/stamp/cluster routing, and the lower-layer compute target.
- Render `entryDetails` as a compact `DNS · hover` badge attached to the left-hand
  entrance of the blue service card. Hovering the badge must reveal the structured
  DNS, ingress, traffic-routing, boundary, and lower-layer facts without obscuring
  Code or Telemetry actions. The self-contained HTML must use the same interaction.
- Keep the DNS/ingress popup open while the pointer is over either its badge or the
  popup itself. Make the popup pointer-interactive and scrollable, provide a short
  hover bridge/grace period between badge and popup, and close it only after the
  pointer has left both. Do not apply `pointer-events: none` to this popup.
- Do not fill infrastructure gaps with likely Azure products. Explicitly write
  `Not proven` when source and live DNS evidence do not establish Front Door, Traffic
  Manager, Azure Load Balancer, TLS termination, or the final compute substrate.
  Use `entryDetails.evidenceStatus` and an immutable `sourceUrl` for the strongest
  supporting DNS/ingress evidence.
- `edges` describe runtime flow, not merely static dependency.
- Every substantive edge must populate `payload`, `response`, `format`, `structure`,
  `communication`, `protocol`, `networkBoundary`, `boundaryDetails`, and `errors`.
- Every edge to or from storage must name the concrete operation in its primary label:
  enqueue/dequeue, upload/download, put/get, append/consume, query, delete, acknowledge,
  renew visibility, or dead-letter. Its details must explain why the request is made,
  what exact data or message is persisted or retrieved, the key/partition/queue used,
  what the storage acknowledgement proves, and what it does not prove about downstream
  completion. Draw separate read and write edges when both materially affect the flow.
- Treat every durable boundary as an explicit **writer -> store <- reader** handoff,
  never as one link in a generic left-to-right chain. The writer's arrow points to the
  store and uses a write verb (`Enqueue`, `Upload`, `Append`, `Put`, `Publish`). The
  reader's request arrow also points from the reader to the store and uses a read verb
  (`Dequeue`, `Download`, `Consume`, `Get`, `Query`). When the returned bytes/message
  materially clarify the flow, add a separate reverse `Response: Return ...` edge; its
  response styling must make clear that the passive store is returning a requested
  result, not initiating work.
- Never place a storage node in `e2eFlow.nodeIds`, including the final database/table.
  The canonical spine is the ordered sequence of active services/processes. Attach the
  final persisted result to the last writer with an explicit write edge and put the END
  marker on that writer; otherwise the thick spine can visually turn passive stores
  into callers or appear to connect one store directly to another.
- For every overview-visible queue, blob, event stream, or database, show both its
  active writer and active reader in the same view whenever both are source-proven.
  If only one side is known, label the other side `External/Not proven` rather than
  implying it through the canonical spine.
- **Visible storage overrides ordinary edge hiding.** Whenever a storage cylinder is
  rendered, render every authored runtime edge whose source or target is that storage,
  regardless of `overview`, the `All interactions` toggle, semantic depth, or whether
  the exact service/component/code endpoint is currently rendered.
- If the non-storage endpoint is hidden inside a collapsed service family, project
  that endpoint to its nearest visible ancestor card while preserving the original
  edge direction, operation, payload, response, protocol, evidence, and details.
  Prefix the visible primary label with the concrete hidden endpoint name, for example
  `Teleduct Driver · Dequeue notification`, so the ancestor attachment never implies
  that the whole family performs an unspecified storage operation.
- This projection is visual only: never rewrite the scene edge's real source/target,
  invent a family-level call, or merge distinct writer/reader/delete/acknowledgement
  edges. Multiple hidden services touching one visible store must produce multiple
  independently selectable lines attached to their shared visible ancestor.
- Do not project a storage edge when no visible ancestor exists, or when projection
  would make both endpoints the same visible card. Reveal/reflow the relevant ancestor
  instead; never draw a self-loop that hides the actual durable boundary.
- Lay out a durable handoff as a readable triad: keep the writer and reader on the
  main service lane in runtime order, and place the passive store wholly above or
  below the midpoint between them. Do not place the store between them on the service
  baseline. Route the writer and reader operations independently so neither line
  crosses another storage cylinder or resembles a storage-to-storage connection.
- Validate the rendered projection, not only the JSON endpoints. Adaptive ancestor
  collapsing, thick E2E spines, overlapping routes, and hidden labels can make valid
  service-to-storage edges look like storage-to-storage calls. At overview and each
  semantic expansion, confirm visually that every store has a clearly labeled writer
  and reader, no route appears to continue through a store, and no two stores appear
  connected. If the projected view is ambiguous, reflow or hide the secondary route.
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
- Keep network/trust-zone ellipses visually quiet. Attach one compact, high-contrast
  circular `(i)` control to each ellipse and hide the zone name, network type, scope,
  VNet/subnet details, resources, and topology notes at rest.
- Reveal the complete zone annotation only while its own `(i)` control is hovered.
  Keep that hover panel clamped inside the viewport, associated with its ellipse, and
  above scene content; moving away must immediately restore the uncluttered view.
- Do not reserve permanent foreground bands, connector callouts, or always-visible
  paragraphs for zone metadata. The circle plus hover disclosure is the default
  network-zone annotation pattern.
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
- Never place concurrently called dependencies consecutively in `e2eFlow.nodeIds`;
  that falsely claims dependency A calls dependency B. Keep the caller and its
  post-join continuation on the canonical spine, declare the concurrent services in
  `e2eFlow.parallelGroups`, and draw explicit caller-to-dependency request edges plus
  dependency-to-caller/join response edges.
- Each `parallelGroups` entry must name the real `sourceNodeId`, all
  `branchNodeIds`, and the `joinNodeId`, with a summary stating the trigger, what runs
  concurrently, and what must complete before the flow continues. Use `columns` only
  to keep a large fan-out readable; it has no runtime semantics.
- Mark the essential request and response edges of a parallel group with
  `overview: true` so they remain visible without enabling `All interactions`. Use
  separate `lane` values for opposite request/response directions so both arrows are
  visible. Prefix response labels with `Response` and state the fan-in condition.
- Use `e2eFlow.phases` to distinguish strict sequence from concurrency. Give a
  parallel fan-out one numbered phase, list its branches in the phase summary, and
  give the join/continuation the next phase. Never assign sequential phase numbers to
  sibling branches that start concurrently.
- Preserve the complete response route in `e2eFlow.nodeIds`, including repeated
  services when the result returns through them. Never shortcut Engine directly to
  the UI when the real path returns through Gateway, transport/client, query state,
  or a host callback. Repeated node IDs are valid when runtime order revisits a
  service on the response path.
- Define concise, source-backed `e2eFlow.phases` for complex flows. The default
  experience is a numbered, clickable phase rail that explains the main sequence
  without drawing every callback and side effect over the cards.
- Always render one ordered primary E2E arrow through the visible components. Sort
  siblings by their first participation in `e2eFlow.nodeIds` so this main path reads
  left to right rather than crossing itself.
- Route every transition on the shortest clear path. Use a direct segment when it
  does not cross another card; never add long decorative return lanes or staggered
  detours merely because an edge points backward. If the runtime sequence would force
  a route behind intervening cards, reflow the later flow steps into a second row below
  the first and connect the rows with a short flowchart elbow.
- Never place a dependency box on top of another dependency line emitted by the same
  caller, including the caller's canonical continuation to its next E2E service.
  Reserve a clear caller-to-continuation lane and place parallel dependency boxes
  wholly outside it, or route every affected line around the complete dependency grid.
  Validate the final routed segments against every visible non-endpoint card; a line
  crossing through any sibling dependency rectangle is a layout failure.
- Keep secondary runtime arrows hidden by default when the complete interaction
  graph would create visual spaghetti. Provide an explicit `All interactions`
  toggle that restores request, response, callback, retry, and side-effect edges.
  The primary spine and `overview: true` parallel request/response edges remain visible
  in both modes.
- The E2E spine must be semantically adaptive: at overview zoom it collapses hidden
  implementation nodes to their visible service ancestors; as component/code
  rows appear, the same spine expands through those newly visible nodes. Keep this
  route continuous and ordered; do not define disconnected per-layer spines.
- Render START and END as foreground callouts attached to the first and last endpoint
  cards, with short connector lines. They must move with those cards after pan, zoom,
  minimap area selection, or semantic expansion; never pin them to fixed screen edges.
  Reposition a callout above or below its visible endpoint when the side placement
  would leave the viewport, and hide it when its real endpoint card is offscreen.
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

- **Highest-priority layout invariant: boxes must never be on top of other boxes.**
  This rule overrides fitting the whole scene into one viewport, compactness, symmetry,
  and decorative composition. Before publication, project the final rendered bounds of
  every card and storage cylinder at overview and every supported expansion state. Any
  intersection between unrelated boxes is a blocking failure: reflow the scene, add
  rows or columns, move off-chain dependencies outside the expanded caller, or require
  pan/zoom. Never accept, hide, or explain away a box-on-box collision.
- Scene title and summary describe the complete user intent without creating a
  synthetic root node.
- Services and directly participating top-level storage boundaries form the readable
  left-to-right E2E spine at initial zoom. Do not expose component/code rows in the
  overview. Keep secondary or failure-only storage inside its owning branch until
  expanded unless it is necessary to explain the main durable handoff.
- The initial/reset overview must fit the complete E2E flow in the viewport, from
  request START through result END, without requiring pan or zoom.
- Use the full viewport in both dimensions. Do not force a large architecture onto one
  compressed horizontal row when several readable rows can use the available space.
  Keep runtime order unmistakable, but wrap the canonical top-level E2E route into
  two or more explicitly authored left-to-right rows when one row would shrink cards,
  crowd labels, overlap routes, or leave most of the screen empty. Prefer cohesive
  phase rows such as producer/delivery, core processing, and persistence/fallback.
  Continue the canonical spine between rows with a short clear elbow and direction
  arrowheads; never use a diagonal composition whose order is ambiguous.
- Place dependencies that are not part of `e2eFlow.nodeIds` in compact stacks or grids
  above or below the row containing their actual caller. Never place an off-chain
  dependency inside a row's main request/response band or between two consecutive
  main-chain services. Use direct request/response arrows and do not connect sibling
  dependencies as a chain. Compute independent card widths and inter-card gaps per row
  so every row remains centered, legible, and visibly separated from the rows above
  and below it.
- When a scene needs a stable authored overview, define explicit overview rows in scene
  metadata rather than relying on automatic source ordering. Each top-level node must
  appear in exactly one row. Use the authored row order for layout, minimap orientation,
  reset/overview behavior, and initial camera fit; preserve that row structure while
  individual branches expand.
- Choose above versus below independently for each caller fan-out based on available
  space and route collisions. Keep the complete dependency card bounds, labels, badges,
  and network ellipse on that side of the main lane; split a fan-out across top and
  bottom only when one side cannot fit without overlap, and keep each subgroup compact.
- Component and code nodes require progressively closer zoom or explicit focus.
- Zooming out must never collapse services, components, code, storage, or expanded
  hierarchy automatically. Camera zoom and hierarchy collapse are separate actions.
  When an outward gesture reaches a point where the renderer would formerly collapse
  the nearest expanded branch, show a modal confirmation instead and preserve the
  complete expanded state until the user explicitly confirms.
- The confirmation must ask a direct question such as `Collapse expanded services?`
  and provide `Keep expanded` plus `Collapse one level`. `Keep expanded`, `Esc`, modal
  cancellation, clicking outside where supported, and continued zooming must preserve
  all expansion state. Make the non-destructive choice the initial keyboard focus.
- Show at most one collapse confirmation per expansion generation. A successful
  transition that adds at least one node ID to the expanded set arms one future prompt.
  Opening that prompt consumes the arm immediately, regardless of whether the user
  chooses `Keep expanded`, presses `Esc`, cancels, or confirms collapse. Every later
  zoom-out must remain silent until another node is newly expanded.
- Camera scaling, panning, repeated zoom-out, keeping the current hierarchy, closing
  the modal, collapsing a level, and selecting an already-expanded item must never
  re-arm the prompt. A later wheel expansion, minimap marker expansion, area-selection
  expansion, or `Expand all` that actually adds a previously collapsed node may re-arm
  exactly one prompt.
- `Collapse one level` may collapse only the nearest branch or branches identified
  when the prompt opened. Do not collapse unrelated branches, recompute the targets
  while the modal is open, or cascade farther than one semantic step. Explicit
  `Collapse all`/`Overview` controls may still restore the overview immediately because
  their labels communicate the destructive navigation action before activation.
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
- Boxes must never be placed on top of one another. This includes architecture cards
  overlapping other cards and fixed UI such as phase rails, breadcrumbs, titles,
  minimaps, legends, toolbars, network labels, or detail panels. Reflow, reserve
  measured space, reduce parallel-grid rows, or hide optional chrome instead.
- Give connected boxes enough measured separation for the complete primary arrow label
  plus at least 24 pixels of clear padding on both sides. Prefer a wider architecture
  that requires horizontal pan over a compact layout where either endpoint covers the
  arrow text. Recompute this spacing after semantic expansion because container growth
  changes the available inter-card corridor.
- Enforce a zero-overlap invariant for every visible semantic item, not only boxes:
  cards, storage cylinders, runtime-profile text, category/technology badges, DNS
  controls, network ellipses, edge labels, arrows, START/END markers, hover targets,
  and fixed UI must not cover one another. An arrow may touch only its own source and
  target. Text or badges belonging to an off-chain dependency should be placed on the
  dependency's outer side (below a bottom dependency row, above a top row), never in
  the clear arrow corridor between that dependency and its caller.
- Treat a numbered phase rail as optional navigation, not required scene content.
  Hide or remove it when it consumes architecture space or overlaps any box; preserve
  phase order in `e2eFlow.phases` for details and future navigation without painting a
  full-width row over the canvas.
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
- The minimap must show every scene node from the initial overview, including collapsed
  descendants. Lay the complete hierarchy out inside the existing minimap dimensions;
  fit and simplify markers rather than increasing the minimap's width or height.
- Anchor the minimap in the lower-left above the legend. It must not obscure the title,
  breadcrumbs, upper flow labels, or the main horizontal E2E row.
- The minimap must include a compact control for the deepest visible semantic level
  and dedicated `Expand all` and `Collapse all` controls. `Collapse all` immediately
  restores the initial main service-row overview and camera fit.
  Manual row collapse is a hard visibility cap; zoom cannot reveal deeper rows until
  the user raises that cap.
- Minimap node markers must control main-view hierarchy expansion. Clicking an
  expandable marker toggles that rectangle, revealing its ancestor path as needed.
  Also provide rectangle and circle area-selection tools. While either tool is active,
  dragging that shape around minimap markers expands every selected expandable node
  and reveals each selected node's ancestor path. Draw the selection shape live,
  distinguish the active tool clearly, and keep ordinary click-to-toggle behavior
  unchanged when area selection is off.
- Minimap area selection is **visibility-snapshot exact**. At pointer-down, freeze the
  IDs of markers currently visible in the main hierarchy. On release, hit-test only
  those frozen markers, using each marker's center against the completed shape.
  Collapsed descendants remain visible as orientation dots in the minimap but are not
  selectable by that gesture, even when their dots lie inside the rectangle or circle.
- Expand every selected marker by exactly one hierarchy level. A gesture may reveal
  the selected marker's direct children, but those newly revealed children and all
  deeper descendants must remain collapsed. They become eligible only after the user
  begins a later, separate selection gesture while they are already visible.
- Selecting an ancestor and one of its descendants in the same gesture is forbidden
  unless both were independently visible at pointer-down and both marker centers were
  inside the shape. Never recompute eligibility after expanding an ancestor, never
  recursively expand the selected subtree, and never interpret containment inside the
  minimap shape as consent to expand hidden green component or pink code levels.
- After rectangle or circle selection is released, move the main camera to the
  selected architecture area and fit the selected nodes plus their newly revealed
  direct children inside the usable viewport. Do not fit the complete architecture;
  preserve readable card scale and account for the minimap, title, breadcrumbs, and
  open details panel when centering the selected area.
- Make minimap area selection behave like Windows Snipping Tool: rectangle selection
  is chosen by default, the map remains visually unchanged until dragging starts, and
  the pointer is a crosshair. Dragging from any corner reveals the selected region;
  releasing applies it. `Esc` and right-click cancel without changing expansion.
  Suppress the post-release click so it cannot accidentally toggle or pan to an
  underlying marker. Never dim, recolor, blur, or replace the idle minimap background.
  `Expand all` expands every hierarchy branch in the main view without resizing the
  minimap. Never force the complete expanded lane below a readable card width merely
  to fit it on screen: preserve legible cards, align the lane after fixed UI, and rely
  on horizontal pan for off-screen branches. Keep all controls within the existing
  minimap dimensions, and never clip, truncate, or hide labels such as `Components`.
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
- Clip every title, owner line, summary, role, badge, and action to its own card.
  Wrap long words when needed. At scales too small for the complete card content,
  progressively hide summary, role, and actions rather than allowing text or buttons
  to extend outside the rectangle.
- Text inside a box must never be drawn on top of other text in that box. Measure the
  rendered line count and height of the title, owner/repository metadata, summary,
  ROLE label, complete role text, and action strip independently, then stack each
  section below the measured bottom of the previous section with visible padding.
  Never use fixed Y offsets that assume a title or metadata stays on one line. Grow
  the card when text wraps; if readable growth is impossible at the current scale,
  hide lower-priority sections rather than overprinting them. Any glyph-on-glyph or
  line-on-line collision inside a card blocks publication.
- Write summaries so their first two rendered lines independently explain what the
  rectangle does; do not rely on hover for basic comprehension.
- Render a node's hover-detail panel wholly inside that node's rectangle, anchored
  to its lower interior with text wrapped to the available width. Never let node
  hover details float beyond or obscure a neighboring rectangle.
- Keep the node hover compact: show the concise node summary rather than the full
  details text, cap it to the card's usable interior, and reserve the complete bottom
  action strip. A hover panel must never cover `Code`, `Telemetry`, or technology
  actions; full details remain available in the click-open side panel.
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
8. Confirm every durable store discovered in the traced path has a `storage` node,
   every storage node has a valid `storageType`, and each storage read/write edge
   explains the operation, request purpose, payload, acknowledgement, and failure
   semantics.
9. Confirm no `parallelGroups.branchNodeIds` are serialized consecutively into the
   canonical `e2eFlow.nodeIds`, every parallel source/branch/join ID exists, and each
   branch has an explicit `overview: true` request edge and response edge returning to
   the caller or declared join node.
10. Compare phase numbering with the source-backed dependency timeline. Concurrent
    branches must share one phase; cache hits, conditionals, post-response work, and
    failure-only branches must not appear as unconditional sequential steps.
11. Project the final overview routes and confirm no caller-to-dependency edge or
    caller-to-continuation spine segment intersects any non-endpoint dependency card
    from that caller's fan-out.
12. Confirm every top-level node absent from the canonical `e2eFlow.nodeIds` is wholly
    above or below the main-chain vertical band and is visually grouped with its real
    caller rather than inserted between sequential main-chain services.
13. Run a final visual collision pass across cards, cylinders, badges, profile text,
    DNS controls, network boundaries, arrows, edge labels, markers, minimap, and fixed
    UI. Any overlap between unrelated visible items blocks publication.
14. Inspect every visible card at overview and supported expansion scales. Confirm its
    title, owner/repository metadata, summary, ROLE label, role text, and actions have
    disjoint measured vertical bounds; any text-on-text overlap blocks publication.
15. For every storage node at every supported expansion state, enumerate all authored
    incident edges and confirm each is rendered. Hidden non-storage endpoints must land
    on their nearest visible ancestor with the concrete endpoint name in the label;
    no incident edge may disappear because `All interactions` is off.
16. Re-audit every node against the user-named scenario. Remove nodes that are merely
    adjacent, share infrastructure, or represent Auxiliary, export, indexing, backfill,
    alternate-ingress, or an unproven branch. Confirm every remaining node contributes
    to the requested result, its durability, retry, acknowledgement, or failure path.

Open with:

`open_canvas({ canvasId: "architecture-explorer-3d", instanceId: "<topic-slug>", input: { scenePath: "<absolute-scene-path>" } })`

Use `get_scene_summary` after opening. Use `focus_node` when the user asks to jump to
a specific service or component.
