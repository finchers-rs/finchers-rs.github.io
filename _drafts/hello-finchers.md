---
layout: post
title: Hello, Finchers!
---

## Example

```rust
#![feature(rust_2018_preview)]

use finchers::{route, routes};
use finchers::endpoint::EndpointExt;
use finchers::endpoints::body;

fn main() -> finchers::rt::LaunchResult<()> {
    let endpoint = route!(/ "api" / "v1" / "posts").and(routes![
        route!(@get / u32 /)
            .and_then(|id: u32| get_post(id)),

        route!(@post /).and(body::json())
            .and_then(|new_post: NewPost| create_post(new_post)),
    ]);

    finchers::rt::launch(endpoint)
}
```
