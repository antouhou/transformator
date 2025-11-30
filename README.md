# transformator

A Rust library for CSS-style 3D transform composition and inheritance. Compose hierarchical transforms with support for perspective, rotations, translations, scaling, and transform origins - just like CSS transforms work in browsers.

## Features

- **CSS-like transform composition**: Chain transforms using familiar patterns (`translate`, `rotate`, `scale`)
- **Hierarchical inheritance**: Child transforms automatically inherit and compose with parent transforms
- **Perspective support**: Apply CSS-style perspective with customizable origin
- **Hit testing**: Project screen coordinates back to local space for accurate hit detection
- **Optional serialization**: Enable `serde` support with the `serialization` feature

## Installation

```toml
[dependencies]
transformator = "0.1"

# With serialization support
transformator = { version = "0.1", features = ["serialization"] }
```

## Usage

### Basic Transform Composition

```rust
use transformator::Transform;

// Create a root transform (identity)
let root = Transform::new();

// Create a parent with position, perspective, origin, and rotation
let parent = Transform::new()
    .with_position_relative_to_parent(350.0, 250.0)
    .with_parent_container_perspective(500.0, 400.0, 300.0)
    .with_origin(50.0, 50.0)  // Rotate around center of 100x100 element
    .then_rotate_x_deg(45.0)
    .compose_2(&root);

// Create a child that inherits parent's transform
let child = Transform::new()
    .with_position_relative_to_parent(10.0, 10.0)
    .compose_2(&parent);

// Transform local points to world coordinates
let world_pos = parent.transform_local_point2d_to_world(0.0, 0.0);
```

### Chaining Multiple Transforms

```rust
let transform = Transform::new()
    .with_position_relative_to_parent(100.0, 100.0)
    .with_origin(50.0, 50.0)
    .then_rotate_y_deg(30.0)
    .then_rotate_x_deg(45.0)
    .then_translate(10.0, 20.0)
    .then_scale(1.5, 1.5)
    .compose_2(&Transform::new());
```

### Hit Testing (Screen to Local Coordinates)

```rust
// Project mouse position to local coordinates for hit testing
if let Some((local_x, local_y)) = transform.project_screen_point_to_local_2d((mouse_x, mouse_y)) {
    // Check if point is inside your shape in local space
    if local_x >= 0.0 && local_x <= 100.0 && local_y >= 0.0 && local_y <= 100.0 {
        println!("Hit!");
    }
}
```

### Available Transform Methods

| Method | Description |
|--------|-------------|
| `translate(x, y)` / `then_translate(x, y)` | 2D translation |
| `translate_3d(x, y, z)` / `then_translate_3d(x, y, z)` | 3D translation |
| `rotate_x_deg(deg)` / `then_rotate_x_deg(deg)` | Rotate around X axis |
| `rotate_y_deg(deg)` / `then_rotate_y_deg(deg)` | Rotate around Y axis |
| `rotate_z_deg(deg)` / `then_rotate_z_deg(deg)` | Rotate around Z axis |
| `scale(sx, sy)` / `then_scale(sx, sy)` | 2D scaling |
| `scale_3d(sx, sy, sz)` / `then_scale_3d(sx, sy, sz)` | 3D scaling |
| `with_origin(x, y)` | Set transform origin (pivot point) |
| `with_position_relative_to_parent(x, y)` | Set position relative to parent |
| `with_parent_container_perspective(dist, ox, oy)` | Set perspective |
| `compose(&parent)` / `compose_2(&parent)` | Compose with parent transform |

## License

Licensed under either of Apache License, Version 2.0 or MIT license at your option.