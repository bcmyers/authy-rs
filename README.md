# Authy-rs
[![](https://docs.rs/authy/badge.svg)](https://docs.rs/authy) [![](https://img.shields.io/crates/v/authy.svg)](https://crates.io/crates/authy) [![](https://travis-ci.org/lholden/authy-rs.svg?branch=master)](https://travis-ci.org/lholden/authy-rs)

Bindings for the Authy two factor web service

## Usage

Please see the [Documentation](https://docs.rs/authy) for more details.

You will need your API key for your application on [authy.com](https://authy.com).

Be sure to add the authy crate to your `Cargo.toml`:

```toml
[dependencies]
authy = "*"
```


'low-level' Usage example:

```rust
extern crate authy;
use authy::{Client, AuthyError};
use authy::api::user;

fn main() {
    let api_url = "https://sandbox-api.authy.com";
    let api_key = "bf12974d70818a08199d17d5e2bae630";

    let c = Client::new(api_url, api_key);

    let country_code = 1;
    let email = "user@domain.com";
    let phone = "949-555-1234";

    let (_, user) = user::create(&c, email, country_code, phone, true).unwrap();
   
    println!("We have a user: {:#?}", user);

    let code = "000000"; // Pretend user has provided a valid code
    match user::verify(&c, user.id, code) {
        Ok(_) => println!("Congrats on being validated!"),
        Err(AuthyError::UnauthorizedKey(e)) => println!("Token provided by the user was wrong"),
        Err(e) => println!("Some server error: {:?}", e),
    };

    // Lets send out a sms token just for fun
    // Must be using a real API key on the production authy server for this to
    // actually send out anything.
    user::sms(&c, user.id, true, Some("login"), Some("Authy documentation example login")).unwrap();
}
```


'high-level' Usage example:

```rust
extern crate authy;
use authy::{Client, User};

fn main() {
    let api_url = "https://sandbox-api.authy.com";
    let api_key = "bf12974d70818a08199d17d5e2bae630";

    let c = Client::new(api_url, api_key);

    let country_code = 1;
    let email = "user@domain.com";
    let phone = "949-555-1234";

    let mut user = User::create(&c, email, country_code, phone, true).unwrap();

    println!("We have a user: {:#?}", user);

    let code = "000000"; // Pretend user has provided a valid code
    if user.verify(&c, code).unwrap() {
        println!("Congrats on being validated!");
    }

    // Lets send out a sms token just for fun
    // Must be using a real API key on the production authy server for this to
    // actually send out anything.
    user.sms(&c, true, Some("login"), Some("Authy documentation example login")).unwrap();
}
```

## License

Authy-rs is licensed under either of

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or
   http://opensource.org/licenses/MIT)

## Contributing

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall
be dual licensed as above, without any additional terms or conditions.

Please see the [CONTRIBUTING](CONTRIBUTING.md) file for more information.
