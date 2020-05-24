# engsure
## Model validation that humans can read
_pronounced like "ensure"_

## Table of Contents
- [How to Use](#how-to-use)
  - [API Docs](#api-docs)
  - [Best Practices](#best-practices)
  - [Simple Example](#simple-example)
- [How to Contribute](#how-to-contribute)

## How to Use

### API Docs
TODO

### Best Practices
How to get the most out of `engsure`

#### provide descriptive labels with `aka` as much as possible
This makes your error messages significantly more valuable, by having a human-readable name for the item being validated.

#### limit `use`s from `engsure` as much as possible
the API was designed with concise names, that way you can use fully-qualified names rather than bringing them directly into scope. 

#### implement the `engsure::Validate` trait
This has 2 benefits:
  1. it lets you separately define `engsure` functionality from the rest of your struct's functionality
  2. by returning the `engsure::Ctx<MyStruct>`, you have more flexibility on what you can do _after_ validating.
      - _e.g._ chain validation of other instances with `and_then` (see the `main` fn in [Simple Example](#simple-example))

### Simple Example
```rust
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
}

impl engsure::Validate for Person {
  use engsure::opt::{is_some};
  use engsure::tup::{is_phone_number};
  use engsure::txt::{is_email_address};

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
  //   engsure::TextError { msg: "invalid email of 'forgot-dotcom@gmail' in 'Sally Jackson'", .. }
  bob.validate()
    .and_then(|_| sally.validate())
    .done()
}
```

## How to Contribute
TODO
