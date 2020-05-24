# engsure
## Model validation that humans can read
_pronounced like "ensure"_

## Table of Contents
- [How to Use](#how-to-use)
  - [API Docs](#api-docs)
  - [Simple Example](#simple-example)
- [How to contribute](#how-to-contribute)

## How to Use

### API Docs
TODO

### Simple Example
```rust
use engsure::opt::{is_some};
use engsure::num::{is_phone_number};
use engsure::txt::{is_email_address};

struct Person {
  pub first_name: String,
  pub last_name: String,
  pub phone_number: (i8, i8, i8),
  pub email: Option<String>
}

impl Person {
  pub fn name(&self) -> String {
    format!("{} {}", self.first_name, self.last_name)
  }

  pub fn validate(self) -> engsure::Ctx<Person> {
    engsure::that(self)
      .aka(self.name())
      .has(|p| p.phone_number)
      .that(is_phone_number)
      .and()
      .has(|p| p.email)
      .when(is_some)
      .that(is_email_address)
      .done()
  }
}

pub fn main() -> engsure::Result {
  let bob = Person {
    first_name: "Bob",
    last_name: "Builder",
    phone_number: (505, 555, 1234),
    email: Some("bob.t.builder@yeswecan.org")
  }
  
  let sally = Person {
    first_name: "Sally",
    last_name: "Jackson",
    phone_number: (602, 111, 2222),
    email: Some("forgot-dotcom@gmail")
  };

  // The following errors with:
  //   EngsureError: invalid email of "forgot-dotcom@gmail" in "Sally Jackson"
  bob.validate()
    .and_then(|_| sally.validate())
    .done()
}
```
