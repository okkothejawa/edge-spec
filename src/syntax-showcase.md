## Syntax Showcase

```rs
type PrimitiveStruct = {
    a: u8,
    b: u8,
    c: u8,
};

type PackedStruct = packed {
    a: u8,
    b: u8,
};

type GenericStruct<T> = {
    a: T,
    b: T,
};

type PrimitiveTuple = (u8, u8, u8);

type PackedTuple = packed (u8, u8, u8);

type GenericTuple<T> = (T, T, T);

type Enum =
    | Option1
    | Option2;

type PrimitiveUnion =
    | Type1(u8)
    | Type2(MyPrimitiveStruct);

type GenericUnion<T> =
    | Some(T)
    | None;

type PrimitiveFn = u8 -> u8;

type PrimitiveMultiArgFn = (u8, u8) -> (u8, u8);

type GenericFn<T> = T -> T;

trait Add = {
    fn add(lhs: Self, rhs: Self) -> Self;
}

impl PrimitiveStruct = {
    fn default() -> Self {
        return Self { 0, 0, 0 };
    }
}

impl PrimitiveStruct: Add = {
    fn add(lhs: Self, rhs: Self) -> Self {
        return Self {
            a: lhs.a + rhs.a,
            b: lhs.b + rhs.b,
            c: lhs.c + rhs.c,
        };
    }
}

mod module {
    mod nestedModule {
        type A = u256;
    }
    pub use nestedModule::A;
}
use module::A;

abi ERC165 = {
    fn supportsInterface(interfaceId: b4) -> bool;
}

contract MyContract;

impl MyContract: ERC165 = {
    fn supportsInterface(interfaceId: b4) -> bool {
        return true;
    }
}

// `MyContract` de-sugars roughly to:
fn main<Cd: ERC165>(calldata: Cd) {
    if callvalue() > 0 { revert(); }
    match calldata {
        ERC165::supportsInterface(interfaceId) => {
            return true;
        },
        _ => revert(),
    };
}
```

Full sugared ERC20 example:

```rs
abi ERC20 = {
    fn balanceOf(owner: addr) -> u256;
    fn allowance(owner: addr, spender: addr) -> u256;
    fn totalSupply() -> u256;
    fn transfer(receiver: addr, amount: u256) -> bool;
    fn transferFrom(sender: addr, receiver: addr, amount: u256) -> bool;
    fn approve(spender: addr, amount: u256) -> bool;
}

contract MyContract = {
    balances: HashMap<addr, u256>,
    allowances: HashMap<addr, HashMap<addr, u256>>,
    supply: u256,
}

impl MyContract: ERC20 = {
    type Transfer = event {
        sender: indexed<addr>,
        receiver: indexed<addr>,
        amount: u256,
    }

    type Approval = event {
        owner: indexed<addr>,
        spender: indexed<addr>,
        amount: u256,
    }

    fn balanceOf(self: Self, owner: addr) -> u256 {
        return self.balances.get(owner);
    }

    fn allowance(self: Self, owner: addr, spender: addr) -> u256 {
        return self.allowances.get(owner).get(spender);
    }

    fn totalSupply() -> u256 {
        return self.supply;
    }

    fn transfer(mut self: Self, receiver: addr, amount: u256) -> bool {
        self.balances.set(caller(), storage.balances.get(caller()) - amount);
        self.balances.set(receiver, storage.balances.get(receiver) + amount);
        log(Self::Transfer { sender: caller(), receiver, amount });
        return true;
    }

    fn transferFrom(mut self: Self, sender: addr, receiver: addr, amount: u256) -> bool {
        if sender != caller() {
            let senderCallerAllowance = self.allowances.get(sender).get(caller());
            if senderCallerAllowance < max<u256>() {
                self.allowances.get(sender).set(caller(), senderCallerAllowance - amount);
            }
        }
        self.balances.set(sender, self.balances.get(sender) - amount);
        self.balances.set(receiver, self.balances.get(receiver) + amount);
        log(Self::Transfer { sender, receiver, amount });
        return true;
    }
    fn approve(mut self: Self, spender: addr, amount: u256) -> bool {
        self.allowances.get(caller()).set(spender, amount);
        log(Approval { owner: caller(), spender, amount });
        return true;
    }
}
```

Full de-sugared ERC20 example:

```rs
type Transfer = event {
    sender: indexed<addr>,
    receiver: indexed<addr>,
    amount: u256,
}

type Approval = event {
    owner: indexed<addr>,
    spender: indexed<addr>,
    amount: u256,
}

abi ERC20 = {
    fn balanceOf(owner: addr) -> u256;
    fn allowance(owner: addr, spender: addr) -> u256;
    fn totalSupply() -> u256;
    fn transfer(receiver: addr, amount: u256) -> bool;
    fn transferFrom(sender: addr, receiver: addr, amount: u256) -> bool;
    fn approve(spender: addr, amount: u256) -> bool;
}

type Storage = {
    balances: HashMap<addr, u256>,
    allowances: HashMap<addr, HashMap<addr, u256>>,
    supply: u256,
}

const storage = Storage::default();

fn main<Cd: ERC20>(calldata: Cd) {
    if callvalue() > 0 { revert() };
    match calldata {
        ERC20::balanceOf(owner) => {
            return storage.balances.get(owner);
        },
        ERC20::allowance(owner, spender) => {
            return storage.allowances.get(owner).get(spender);
        },
        ERC20::totalSupply() => {
            return storage.supply;
        },
        ERC20::transfer(receiver, amount) => {
            storage.balances.set(caller(), storage.balances.get(caller()) - amount);
            storage.balances.set(receiver, storage.balances.get(receiver) + amount);
            log(Transfer { sender: caller(), receiver, amount });
            return true;
        },
        ERC20::transferFrom(sender, receiver, amount) => {
            if sender != caller() {
                let senderCallerAllowance = storage.allowances.get(sender).get(caller());
                if senderCallerAllowance < max<u256>() {
                    storage.allowances.get(sender).set(caller(),senderCallerAllowance - amount);
                }
            }
            storage.balances.set(sender, storage.balances.get(sender) - amount);
            storage.balances.set(receiver, storage.balances.get(receiver) + amount);
            log(Transfer { sender, receiver, amount });
            return true;
        },
        ERC20::approve(spender, amount) => {
            storage.allowances.get(caller()).set(spender, amount);
            log(Approval { owner: caller(), spender, amount });
            return true;
        },
        _ => revert(),
    };
}
```
