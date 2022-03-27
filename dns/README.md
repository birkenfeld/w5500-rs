# w5500-dns

DNS client implementation for use with the W5500.

## Warning

Please proceed with caution, and review the code before use in a production
environment.

This code was developed for one-off hobby projects.

## Limitations

* No DNS caching.
* Only supports A queries.
* Only supports a single outstanding query.
* Only supports a single question in a query.

## Example

```rust
use w5500_dns::{hl::block, ll::Sn, servers, Client as DnsClient, Hostname, Response};

const DNS_SOCKET: Sn = Sn::Sn3;
const DNS_SRC_PORT: u16 = 45917;

let mut dns_client: DnsClient =
    DnsClient::new(DNS_SOCKET, DNS_SRC_PORT, servers::CLOUDFLARE, random_number);
let hostname: Hostname = Hostname::new("docs.rs").expect("hostname is invalid");

let mut hostname_buffer: [u8; 16] = [0; 16];

let query_id: u16 = dns_client.a_question(&mut w5500, &hostname)?;
let mut response: Response<_> =
    block!(dns_client.response(&mut w5500, &mut hostname_buffer, query_id))?;

while let Some(answer) = response.next_answer()? {
    println!("name: {:?}", answer.name);
    println!("TTL: {}", answer.ttl);
    println!("IP: {:?}", answer.rdata);
}
```

## Relevant Specifications

* [RFC 1035](https://www.rfc-editor.org/rfc/rfc1035)

## Feature Flags

All features are disabled by default.

* `embedded-hal`: Passthrough to [w5500-hl].
* `std`: Passthrough to [w5500-hl].
* `defmt`: Enable logging with `defmt`.  Mutually exclusive with `log`.
  Also a passthrough to [w5500-hl].
* `log`: Enable logging with `log`.  Mutually exclusive with `defmt`.

[w5500-hl]: https://crates.io/crates/w5500-hl
[`std::net`]: https://doc.rust-lang.org/std/net/index.html
[Wiznet W5500]: https://www.wiznet.io/product-item/w5500/