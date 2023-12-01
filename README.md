# axum-serde

[![crates.io](https://img.shields.io/crates/v/axum-serde)](https://crates.io/crates/axum-serde)
[![crates.io download](https://img.shields.io/crates/d/axum-serde)](https://crates.io/crates/axum-serde)
[![LICENSE](https://img.shields.io/badge/license-MIT-blue)](https://github.com/gengteng/axum-serde/blob/main/LICENSE)
[![dependency status](https://deps.rs/repo/github/gengteng/axum-serde/status.svg)](https://deps.rs/repo/github/gengteng/axum-serde)
[![GitHub Workflow Status](https://img.shields.io/github/actions/workflow/status/gengteng/axum-serde/.github/workflows/main.yml?branch=main)](https://github.com/gengteng/axum-serde/actions/workflows/ci.yml)
[![Coverage Status](https://coveralls.io/repos/github/gengteng/axum-serde/badge.svg?branch=main)](https://coveralls.io/github/gengteng/axum-serde?branch=main)

## 📑 Overview

**axum-serde** is a library that provides multiple serde-based extractors and responders for the Axum web framework.

## 🚀 Basic usage

* Install

```shell
cargo add axum-serde --features yaml
# Enable features as you need
```

* Example

```rust,ignore
use axum::routing::post;
use axum::Router;
use axum_serde::Yaml;
use serde::Deserialize;
use std::net::SocketAddr;
use tokio::net::TcpListener;

#[derive(Debug, Deserialize)]
pub struct Data {
    pub v0: usize,
    pub v1: usize,
}

pub async fn handler(Yaml(_data): Yaml<Data>) {
    // use _data
}

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let router = Router::new().route("/data", post(handler));
    let listener = TcpListener::bind(&SocketAddr::from(([0u8, 0, 0, 0], 0u16))).await?;
    axum::serve(listener, router.into_make_service()).await?;
    Ok(())
}
```

## 🗂️ Extractors

| Extractor                      | Feature | Backend                                                   |
|--------------------------------|---------|-----------------------------------------------------------|
| `Yaml<T>`                      | yaml    | [serde_yaml](https://crates.io/crates/serde_yaml) v0.9.27 |
| `MsgPack<T>` / `MsgPackRaw<T>` | msgpack | [rmp-serde](https://crates.io/crates/rmp-serde) v1.1.2    |
| `Toml<T>`                      | toml    | [toml](https://crates.io/crates/toml) v0.8.8              |
| `Xml<T>`                       | xml     | [quick-xml](https://crates.io/crates/quick-xml) v0.31.0   |

## 🎁 Custom Extractor

Use the `extractor` macro to create custom extractors with minimal boilerplate:

* Install

```shell
cargo add axum bytes mime serde axum-serde async-trait
```

* Example

```rust,ignore
use axum_serde::extractor;
use serde::de::DeserializeOwned;
use serde::Serialize;

extractor!(
    MyFormat,    // The name of the data format.
    MyFmt,       // The actual type name of the HTTP extractor/response.
    application, // The main type of the Content-Type that this extractor supports.
    myfmt,       // The subtype of the Content-Type that this extractor supports.
    from_slice,  // A function identifier for deserializing data from the HTTP request body.
    String,      // The type of error that can occur when deserializing from the request body.
    to_vec,      // A function identifier for serializing the HTTP response body to bytes.
    myfmt        // The test module name.
);

fn from_slice<T: DeserializeOwned>(_bytes: &[u8]) -> Result<T, String> {
    todo!()
}

fn to_vec<T: Serialize>(_value: &T) -> Result<Vec<u8>, String> {
    todo!()
}
```

## 📜 License

This project is licensed under the MIT License.

## 📚 References

* [axum](https://crates.io/crates/axum)
* [serde](https://crates.io/crates/serde)