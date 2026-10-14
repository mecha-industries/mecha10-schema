# Schema reference

Generated from this repo's JSON Schemas by `cargo run --example export_schemas` (LAB-1829) — do not hand-edit. Regenerate with:

```
cargo run -p mecha10-cli --example export_schemas
```

Descriptions come from the schemas themselves (Rust doc comments on node config structs, via `schemars`, or `get_project_schema()`'s own `description` fields for the project config). See [`docs/validate-configs.md`](../validate-configs.md) for how these schemas are used to validate configs, and the raw `.schema.json` files alongside this doc for the exact JSON Schema.

## Contents

- [Project config (mecha10.json)](#project-config-mecha10json)
- [Node config base (common/reserved fields)](#node-config-base-commonreserved-fields)
- [Node config schemas](#node-config-schemas)
- [Model catalog (model_catalog.toml)](#model-catalog-modelcatalogtoml)
- [Simulation environment (environment.json)](#simulation-environment-environmentjson)
- [Behavior composition (behavior tree config.json)](#behavior-composition-behavior-tree-configjson)
- [RL training job (configs/rl/jobs/*.yml)](#rl-training-job-configsrljobsyml)

## Project config (mecha10.json)

Raw schema: [`mecha10.schema.json`](mecha10.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `actuator` | `ActuatorConfig` | no | Actuator motion limits (linear/angular velocity and acceleration) applied to every node. |
| `behaviors` | one of: `BehaviorsConfig`, null | no |  |
| `docker` | `DockerServicesConfig` | no |  |
| `environments` | `EnvironmentsConfig` | no |  |
| `name` | string | yes | The project's name; lowercase letters, numbers, and hyphens only, 1-64 characters. |
| `nodes` | object | no | Node definitions (NodesConfig in mecha10-cli-core) - typically a flat identifier -> version map, though the legacy `drivers`/`standard`/`custom` arrays below also validate (no `additionalProperties` restriction). Keys: a framework node's full package id ("@mecha10/speaker"), a future registry node ("@<org>/<name>"), or a project node's own unprefixed name ("my-custom"); the legacy "@local/*" prefix is no longer supported. |
| `robot_description` | string or null | no |  |
| `robot_id` | string | no | The robot's unique identifier. |
| `robot_name` | string | no | Human-facing name for the robot. |
| `robot_platform` | string or null | no |  |
| `run_targets` | object | no | Execution targets (robot, remote, dev, etc.) |
| `scripts` | object | no | Script shortcuts (npm scripts-like) |
| `simulation` | one of: `ProjectSimulationConfig`, null | no |  |
| `targets` | `TargetsConfig` | no |  |
| `template_version` | string or null | no | Which published templates release this project's scaffolded files were generated from. |
| `version` | string | yes |  |

### nodes

Node definitions (NodesConfig in mecha10-cli-core) - typically a flat identifier -> version map, though the legacy `drivers`/`standard`/`custom` arrays below also validate (no `additionalProperties` restriction). Keys: a framework node's full package id ("@mecha10/speaker"), a future registry node ("@<org>/<name>"), or a project node's own unprefixed name ("my-custom"); the legacy "@local/*" prefix is no longer supported.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `custom` | array of `node` | no | Custom application nodes (legacy array form - see this property's own description) |
| `drivers` | array of `node` | no | Hardware driver nodes (legacy array form - see this property's own description) |
| `standard` | array of `standardNode` | no | Standard/pre-built nodes from packages (legacy array form - see this property's own description) |

### ActuatorConfig

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `locomotion` | `LocomotionLimitsConfig` | no |  |

### BehaviorsConfig

Behavior tree configuration controlling autonomous robot behavior execution.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `active` | string | yes | Name of the active behavior to run (e.g. "idle-wander", "patrol-simple", "rl"). |

### DockerServicesConfig

Docker services configuration for project-level Docker Compose management

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `auto_start` | boolean | no | Whether to auto-start services in dev mode |
| `compose_file` | string | no | Path to docker-compose.yml relative to project root |
| `remote` | array of string | no | Services needed for remote execution (Redis + databases, etc.) |
| `robot` | array of string | no | Services needed for on-robot execution (typically just Redis for message passing) |

### EnvironmentConfig

Configuration for a specific environment

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `control_plane` | string or null | no | Control plane base URL for this environment (e.g. "https://mecha.industries/api"); defaults to the standard control plane URL when omitted. |
| `logging` | one of: `LoggingConfig`, null | no | Per-environment logging overrides, such as whether disk logging is enabled. |

### EnvironmentsConfig

Per-environment configuration (dev, staging, prod), with the active environment selected via
the `MECHA10_ENV` environment variable or `--env` flag.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `dev` | `EnvironmentConfig` | no | Development environment (local control plane) |
| `prod` | `EnvironmentConfig` | no | Production environment (cloud control plane) |
| `staging` | one of: `EnvironmentConfig`, null | no | Staging environment (optional) |

### LocomotionLimitsConfig

Motion limits for a differential-drive (or similar) locomotion base.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `max_acceleration` | number | no |  |
| `max_angular_vel` | number | no |  |
| `max_linear_vel` | number | no |  |

### LoggingConfig

Logging configuration for a single environment, controlling whether node logs are written
to local disk. Remote/network log shipping is independent of this setting.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `disk_logging_enabled` | boolean | no | Whether node stdout/stderr may be written to local disk, in addition to any remote log shipping. |

### NodeRef

A single entry in a target's node list, either a plain node identifier or a named instance
for a node that needs to run as more than one instance under the same target.

One of the following variants:

- **variant 0**
- **variant 1**

**variant 0**

**variant 1**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `instance_id` | string | yes | Which instance of `name` this is (e.g. `real` or `sim`). |
| `name` | string | yes | Node identifier (e.g. `@mecha10/camera`). |

### NodesConfig

Map of node identifiers (e.g. `@mecha10/listener`, `my-custom`) to their pinned versions.

### ProjectSimulationConfig

Project-level simulation settings, distinct from the runtime simulation config used during
execution.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `environment` | string or null | no | Simulation environment name to use for interactive simulation, overriding the default environment. |
| `model` | string or null | no | Robot model name to use for interactive simulation, overriding the default model. |
| `scenario` | string or null | no | Optional scenario name selecting which simulation config file to load. |

### TargetEntry

A single target's configuration: which nodes run on it, and (for `robot`/`remote`) which
Rust target triple(s) `mecha10 build` compiles for.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `nodes` | array of `NodeRef` | no | Nodes that run on this target, either plain node identifiers or named instances of multi-instance nodes. |
| `platforms` | array or null | no | Rust target triple(s) to build for (e.g. `aarch64-unknown-linux-gnu`); defaults to the host machine's own architecture when unset. Only meaningful for `robot`/`remote`. |

### TargetsConfig

Target execution configuration: where nodes run (locally, in simulation, on the robot, or
remotely), and, for `robot`/`remote`, which Rust target triple(s) builds compile for.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `dev` | one of: `TargetEntry`, null | no | Nodes that run locally on the developer's machine, in-process, via `mecha10 dev` |
| `remote` | one of: `TargetEntry`, null | no | Nodes that run remotely in Docker containers, typically AI/ML nodes with platform-specific dependencies |
| `robot` | one of: `TargetEntry`, null | no | Nodes that run on the robot as local processes, typically hardware drivers and low-latency control nodes |
| `simulation` | one of: `TargetEntry`, null | no | Nodes that run in a simulated environment |

### node

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `auto_restart` | boolean | no | Auto-restart on crash |
| `config` | string | no | Path to node configuration file |
| `depends_on` | array of string | no | Node dependencies (other nodes that must start first) |
| `description` | string | no | Node description |
| `enabled` | boolean | no | Whether node is enabled |
| `name` | string | yes | Node name (snake_case) |
| `path` | string | yes | Path to node source code |
| `run_target` | string | no | Where to run this node - a key of this project's `run_targets` map |

### runTarget

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `description` | string | no | Target description |
| `includes` | array of string | no | Node groups to include |
| `resources` | object | no | Resource requirements |

### standardNode

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `binary` | string | yes | Binary name within package |
| `config` | string | no | Path to configuration file |
| `package` | string | yes | Package identifier (e.g., @mecha10/speaker) |

### resources

Resource requirements

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `cpu_limit` | string | no | CPU limit (e.g., '2.0') |
| `devices` | array of string | no | Required device files (e.g., /dev/video0) |
| `gpu` | one of: boolean, object | no | GPU requirements |
| `memory_limit` | string | no | Memory limit (e.g., '512M', '16G') |
| `model_cache` | string | no | Path to model cache directory |
| `storage_size_gb` | integer | no | Storage size in GB |
| `storage_type` | string | no | Storage type requirement |

## Node config base (common/reserved fields)

Fields common to every node's configuration, reserved across all node types.

Raw schema: [`base.schema.json`](base.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `controller` | any | no | The mock-vs-hardware controller backend selector, for the nodes that have this concept (`imu`, `motor`). Opaque - see the module docs for why this can't be a shared Rust type the way `model`/`topics` are. |
| `model` | one of: `NodeModelConfig`, null | no | Which model this node should load, for the nodes that have this concept (`object-detector`, `image-classifier`, `rl`, `llm-command` - the latter via its `name`/`provider` fields only, see the module docs). `None` for every other node, and for these four when the key is simply absent - see the module docs for why this is never a node-specific default value. |
| `relay_topics` | array or null | no | Which of this node's published topics should be forwarded to the dashboard. |
| `status_rate_hz` | number or null | no | How often, in Hz, to publish node status updates, for the nodes that have this concept (`camera`, `motor`, `teleop`). `None` for every other node, and for these three when the key is simply absent - see the module docs for why this is never a node-specific default value. |
| `topics` | `Topics` | no | Per-role publish/subscribe topic configuration - every built-in node declares one of these. |

### ModelSource

Where a node should resolve a model it references from.

Shared by every built-in node that points at an on-disk model this way (`object-detector`,
`image-classifier`, `rl` - see [`NodeModelConfig`]).

One of the following variants:

- **variant 0**
- **variant 1**

**variant 0**

**variant 1**

### NodeModelConfig

A node's reference to the on-disk model it should load - shared/reserved across every
built-in node that has this concept (`object-detector`, `image-classifier`, `rl`), moved
onto [`crate::node_config_base::NodeConfigBase`] (LAB-4384 follow-up) the same way
`relay_topics` already was, rather than each of those nodes re-declaring its own
near-identical `ModelConfig` struct.

This is deliberately a *different* type from [`ModelConfig`] above: that one is the model
catalog's own on-disk metadata (`models/catalog/<name>/config.json` - task, repo, filename,
preprocessing, labels); this one is a *node's pointer* to a model (which one, where its file
lives, where to resolve it from), a completely different concept that happens to share the
word "model".

`name`/`path` default to the empty string rather than this type baking in any one node's
real default value (object-detector defaults to `"yolov8n"`, rl to `"find-target"`,
image-classifier to `"mobilenet-v2"` - genuinely different per node). Each node resolves its
own real default for an empty value itself (see each node's own `model()` accessor on its
`Config` type) - this mirrors exactly how `#[serde(default)]` on this struct's own fields
already behaved before this type was shared (calling `Default::default()`, not a per-node
default function, when the field - or the whole `model` key - is merely absent).

`confidence_threshold` only has meaning for `image-classifier` today (object-detector/rl
ignore it) - it lives here rather than as a `image-classifier`-only extra field so this
struct stays the single shared shape for the "which model does this node use" concept across
all three nodes rather than splintering into near-identical siblings again.

`provider` only has meaning for `llm-command` (object-detector/image-classifier/rl ignore
it) - it used to be a separate field on `NodeConfigBase` alongside `model`, but "which
backend resolves this model" is part of the same "which model does this node use" concept,
so it consolidated into this one struct instead of two separate `Option` fields on the base.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `confidence_threshold` | number or null | no | Minimum confidence score a classification result must meet to be reported (`image-classifier`-specific; ignored by other nodes using this type). |
| `name` | string | no | Name of the model to load. Empty means "not set" - the owning node resolves its own default in that case. |
| `path` | string | no | Filesystem path to the model file. Empty means "not set" - the owning node resolves its own default in that case. |
| `provider` | one of: `Provider`, null | no | Which LLM backend to query (`llm-command`-specific; ignored by other nodes using this type). `None` means "not set" - the owning node resolves its own default in that case. |
| `source` | `ModelSource` | no | Where to resolve the model from. |
| `version` | integer or null | no | Specific model version to use (registry-sourced models only); latest if omitted. |

### Provider

Which LLM backend a node should query - shared/reserved the same way [`NodeModelConfig`] is,
for `llm-command`'s analogous "which model does this node use" concern (LAB-4384 follow-up).
`llm-command` is the only node with this concept today, so unlike [`NodeModelConfig`] there's
no cross-node shape to reconcile - this just lives in `mecha10-core` (rather than
`mecha10-nodes-llm-command`) so [`crate::node_config_base::NodeConfigBase`] can reference it.

One of the following variants:

- **variant 0**
- **variant 1**
- **variant 2**

**variant 0**

**variant 1**

**variant 2**

### Topics

Per-role publish/subscribe topic configuration shared by node config structs.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `publishes` | array of object | no | Topics this node publishes to, keyed by role (e.g. "output", "status") |
| `subscribes` | array of object | no | Topics this node subscribes to, keyed by role (e.g. "input", "control") |

## Node config schemas

Every node's own config fields -- built-in or custom -- live in that node's own `<name>.schema.json`, composed (`mecha10_core::schema_validation::compose_node_schema`) from [Node config base](#node-config-base-commonreserved-fields)'s common/reserved fields plus that node's own `custom`-nested fields; see [`docs/adr/0001-node-config-base-schema.md`](../docs/adr/0001-node-config-base-schema.md) for the full design. A built-in node's file lives in its own crate directory, written by `cargo run --example export_schemas`'s `export_builtin_node_schema_files`; a custom node ships the same file next to its own source. There is no longer a central copy of these under `schemas/` -- the file in each node's own crate directory is the only copy.

- `behavior-executor`: [`../packages/nodes/behavior-executor/behavior-executor.schema.json`](../packages/nodes/behavior-executor/behavior-executor.schema.json)
- `camera`: [`../packages/nodes/camera/camera.schema.json`](../packages/nodes/camera/camera.schema.json)
- `diagnostics`: [`../packages/nodes/diagnostics/diagnostics.schema.json`](../packages/nodes/diagnostics/diagnostics.schema.json)
- `imu`: [`../packages/nodes/imu/imu.schema.json`](../packages/nodes/imu/imu.schema.json)
- `listener`: [`../packages/nodes/listener/listener.schema.json`](../packages/nodes/listener/listener.schema.json)
- `llm-command`: [`../packages/nodes/llm-command/llm-command.schema.json`](../packages/nodes/llm-command/llm-command.schema.json)
- `motor`: [`../packages/nodes/motor/motor.schema.json`](../packages/nodes/motor/motor.schema.json)
- `simulator`: [`../packages/nodes/simulator/simulator.schema.json`](../packages/nodes/simulator/simulator.schema.json)
- `speaker`: [`../packages/nodes/speaker/speaker.schema.json`](../packages/nodes/speaker/speaker.schema.json)
- `teleop`: [`../packages/nodes/teleop/teleop.schema.json`](../packages/nodes/teleop/teleop.schema.json)
- `video-streamer`: [`../packages/nodes/video-streamer/video-streamer.schema.json`](../packages/nodes/video-streamer/video-streamer.schema.json)
- `websocket-bridge`: [`../packages/nodes/websocket-bridge/websocket-bridge.schema.json`](../packages/nodes/websocket-bridge/websocket-bridge.schema.json)

## Model catalog (model_catalog.toml)

Schema for model_catalog.toml, the mecha10-models model catalog

Raw schema: [`model-catalog.schema.json`](model-catalog.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `models` | array of `ModelCatalogEntry` | yes |  |

### ModelCatalogEntry

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `classes` | array of string | no |  |
| `description` | string | yes |  |
| `filename` | string | yes |  |
| `name` | string | yes |  |
| `preprocessing_preset` | string or null | no |  |
| `quantize` | one of: `QuantizeConfig`, null | no |  |
| `repo` | string | yes |  |
| `task` | string | yes |  |

### QuantizeConfig

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `enabled` | boolean | yes |  |
| `method` | string | yes |  |

## Simulation environment (environment.json)

Schema for a simulation environment configuration file.

Raw schema: [`environment.schema.json`](environment.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `$schema` | string | no |  |
| `description` | string | yes | Brief description of the environment's purpose. |
| `dimensions` | object | yes |  |
| `features` | object | yes |  |
| `id` | string | yes | Unique identifier for the environment (snake_case, no spaces). |
| `name` | string | yes | Human-readable name. |
| `render_quality` | `renderQuality` | no |  |
| `rewards` | object | no | Optional RL reward-shaping configuration. |
| `scene_path` | string | yes | Legacy scene resource path, kept for backward compatibility. |
| `version` | string | yes | Semantic version of the environment. |

### dimensions

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `height` | number | yes |  |
| `length` | number | yes |  |
| `unit` | string | yes |  |
| `width` | number | yes |  |

### features

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `floor` | `floorFeature` | no |  |
| `goals` | `goalsFeature` | no |  |
| `lighting` | `lightingFeature` | no |  |
| `npcs` | `npcsFeature` | no |  |
| `objects` | array of one of: `cubeObject`, `imageObject` | no | Custom objects placed in the scene. Each entry is discriminated by `type`. |
| `obstacles` | `obstaclesFeature` | no |  |
| `spawn_points` | `spawnPointsFeature` | no |  |
| `walls` | `wallsFeature` | no |  |

### cubeObject

A 3D cube placed in the scene. Maps to a MuJoCo box geom (collision geom if `add_collision: true`).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `add_collision` | boolean | no |  |
| `color` | array of number | no | RGBA color, each component 0.0-1.0. |
| `material_type` | string | no |  |
| `metallic` | number | no |  |
| `position` | `vec3` | yes | World position `[x, y, z]` in meters. |
| `roughness` | number | no |  |
| `size` | `vec3` | no | Box half/full extents `[x, y, z]` in meters. Default `[1, 1, 1]`. |
| `type` | any | yes |  |

### floorFeature

Flat-plane floor geometry/material.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `collision` | boolean | yes |  |
| `friction` | number | yes |  |
| `material` | string | yes |  |
| `type` | string | yes |  |

### goalsFeature

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `count` | integer | yes |  |
| `type` | string | yes |  |

### imageObject

An image rendered as a flat plane in the scene.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `interactive` | boolean | no | Whether the object responds to click/interaction. |
| `motion` | object | no | Optional back-and-forth patrol motion along one world axis, applied to this image object over the course of an episode. Declares the contract only -- the actual per-step position update is driven by the mecha10-rl side, not this repo. |
| `position` | `vec3` | yes | World position `[x, y, z]` in meters. |
| `rotation` | `vec3` | no | Rotation in degrees `[x, y, z]`, applied to the plane geom. Default `[0, 0, 0]`. |
| `scale` | `vec3` | no | Per-axis scale `[x, y, z]`. Default `[1, 1, 1]`. |
| `source` | string | yes | Path to the image asset, relative to the project root. |
| `type` | any | yes |  |
| `visible` | boolean | no |  |

### lightingFeature

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ambient` | object | yes |  |
| `directional` | object | yes |  |

### npcsFeature

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `count` | integer | yes |  |
| `enabled` | boolean | yes |  |

### obstaclesFeature

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `count` | integer | yes |  |
| `type` | string | yes |  |

### renderQuality

Render quality settings that trade visual fidelity for performance.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `antialiasing` | string | no | Anti-aliasing quality tier. |
| `mesh_detail` | string | no | Tessellation of non-mesh primitive geoms (sphere/cylinder/capsule). No effect on box/plane geoms. |
| `shadows` | string | no | Shadow map resolution tier. |

### spawnPointsFeature

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `count` | integer | yes |  |
| `default_index` | integer | yes |  |
| `positions` | array of `vec3` | yes |  |

### vec3

### vec4

### wallsFeature

Perimeter wall geometry/material.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `collision` | boolean | yes |  |
| `count` | integer | yes |  |
| `height` | number | yes |  |
| `material` | string | yes |  |
| `thickness` | number | yes |  |

### motion

Optional back-and-forth patrol motion along one world axis, applied to this image object over the course of an episode. Declares the contract only -- the actual per-step position update is driven by the mecha10-rl side, not this repo.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `axis` | enum | yes | World axis the object patrols along. |
| `range_max` | number | yes | Maximum coordinate (meters) along `axis` the object patrols to. |
| `range_min` | number | yes | Minimum coordinate (meters) along `axis` the object patrols to. |
| `speed` | number | no | Patrol speed in meters/second. Default `1.0`. |
| `type` | enum | yes |  |

### ambient

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `color` | `vec4` | yes | RGBA color, each component 0.0-1.0. |
| `enabled` | boolean | yes |  |
| `source` | string | yes |  |

### directional

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `angle` | string | yes |  |
| `enabled` | boolean | yes |  |
| `energy` | number | yes |  |
| `shadows` | boolean | yes |  |

## Behavior composition (behavior tree config.json)

A behavior tree definition loaded from a JSON configuration file.

Raw schema: [`behavior.schema.json`](behavior.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `$schema` | string or null | no | URL of the JSON schema this configuration conforms to. |
| `configs` | object | no | Named configuration values referenced by nodes in the behavior tree via `config_ref`. |
| `description` | string or null | no | Human-readable description of what this behavior does. |
| `name` | string | yes | Name of this behavior. |
| `root` | `CompositionConfig` | yes | The root node of the behavior tree. |

### CompositionConfig

A node in a behavior tree: either a composite control-flow node or a leaf behavior node.

One of the following variants:

- **variant 0**
- **variant 1**
- **variant 2**
- **variant 3**

**variant 0**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `children` | array of `CompositionConfig` | yes | Child nodes to run in order. |
| `name` | string or null | no | Optional name for this node. |
| `type` | string | yes |  |

**variant 1**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `children` | array of `CompositionConfig` | yes | Child nodes to try in order. |
| `name` | string or null | no | Optional name for this node. |
| `type` | string | yes |  |

**variant 2**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `children` | array of `CompositionConfig` | yes | Child nodes to run concurrently. |
| `name` | string or null | no | Optional name for this node. |
| `policy` | `ParallelPolicyConfig` | no | Policy determining when this node succeeds based on its children's outcomes. |
| `type` | string | yes |  |

**variant 3**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `config` | any | no | Inline configuration for this node. |
| `config_ref` | string or null | no | Key into the top-level `configs` map to use as this node's configuration. |
| `name` | string or null | no | Optional name for this node. |
| `node` | string | yes | Name of the behavior node type to run. |
| `type` | string | yes |  |

### ParallelPolicyConfig

Determines when a `Parallel` composition node succeeds, based on its children's outcomes.

One of the following variants:

- **variant 0**
- **variant 1**
- **variant 2**

**variant 0**

**variant 1**

**variant 2**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `require_n` | integer | yes |  |

## RL training job (configs/rl/jobs/*.yml)

Raw schema: [`rl-job.schema.json`](rl-job.schema.json).

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `algorithm` | string or null | no |  |
| `batch_size` | integer or null | no |  |
| `clip_range` | number or null | no |  |
| `description` | string or null | no |  |
| `ent_coef` | number or null | no |  |
| `env_name` | string or null | no |  |
| `environment_map` | string or null | no |  |
| `eval_eps` | integer or null | no |  |
| `flow_type` | `FlowType` | yes |  |
| `gae_lambda` | number or null | no |  |
| `gamma` | number or null | no |  |
| `learning_rate` | number or null | no |  |
| `linear_lr_schedule` | boolean or null | no |  |
| `max_grad_norm` | number or null | no |  |
| `min_delta` | number or null | no |  |
| `n_envs` | integer or null | no |  |
| `n_epochs` | integer or null | no |  |
| `n_steps` | integer or null | no |  |
| `n_trials` | integer or null | no |  |
| `name` | string | yes |  |
| `patience` | integer or null | no |  |
| `policy` | string or null | no |  |
| `search_space` | array or null | no |  |
| `seed` | integer or null | no |  |
| `task_name` | string or null | no |  |
| `total_timesteps` | integer or null | no |  |
| `tune_timesteps` | integer or null | no |  |
| `vf_coef` | number or null | no |  |

### FlowType

Which training pipeline flow a job runs as.

Allowed values: `train`, `tune`, `tune-then-train`, `train-from-tune`, `resume-run`

### SearchSpaceEntry

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `choices` | array or null | no |  |
| `high` | number or null | no |  |
| `log` | boolean or null | no |  |
| `low` | number or null | no |  |
| `name` | string | yes |  |
| `type` | `SearchSpaceType` | yes |  |

### SearchSpaceType

Allowed values: `float`, `int`, `categorical`

