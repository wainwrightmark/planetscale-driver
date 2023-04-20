# PlanetScale Serverless Driver for Rust™

<div align="center">
  <!-- Version -->
  <a href="https://crates.io/crates/planetscale-driver">
    <img src="https://img.shields.io/crates/v/planetscale-driver.svg?style=flat-square"
    alt="Crates.io version" />
  </a>
</div>

Rust "version" of [database-js](https://github.com/planetscale/database-js). As stated in database-js it uses PlanetScale HTTP api for database queries.
It will run perfectly run on Cloudflare Workers Or Vercel Edge Functions.

## Usage
NOTE: [Anyhow](https://crates.io/crates/anyhow) crate is required while using deserializer.

## Examples
### Connection and simple SQL execution
```rust
use planetscale_driver::PSConnection;

let mut conn = PSConnection::new(
  "<host>",
  "<user>",
  "<password>",
);
    
let res = conn.execute("SELECT 1").await.unwrap();
```

### Rows deserialization into struct
As you can see, deserialization doesn't use field names (MAYBE IN FUTURE) so remember to write your structs correctly!

```rust
use planetscale_driver::{Database, Deserializer, query};

#[derive(Database, Debug)]
struct TestD {
  val: u32
}

// ...

let res: TestD = query("SELECT 1").fetch_one(&mut conn).await.unwrap();
println!("{:?}", res);
```

### QueryBuilder
If you want to bind safely values into your query, you should use QueryBuilder

Note: now query method is wrapper around QueryBuilder

```rust
// ...

// note: values passed to .bind function must have trait ToString 
let id = 69;
let name = "420";

// res there will be empty result, if you want to get reponse data use "execute_raw"
let res = query("INSERT INTO test(id, name) VALUES($0, \"$1\")")
  .bind(id)
  .bind(name)
  .execute(&mut conn)
  .await
  .unwrap();
```

### More examples in the [examples](examples) folder
If you want to run them:
```bash
PS_HOST=<host> PS_USER=<username> PS_PASS=<pscale_password> cargo run --example <example_name>
```
