# Unsound


## RUSTSEC-2022-0007: qcell crate 中 `TCell` 或 `TLCell` 的内存能被恶意代码访问

因为生命周期参数型变未使用正确而引起的问题，导致恶意代码可以对同一片内存获取两个可变引用。解决方法是把类型参数的协变（Covariant）改为不变（Invariant）。

```rust
struct Invariant<T>(fn(T) -> T);

pub struct TCellOwner<Q: 'static> {
    // Allow Send and Sync, and Q is invariant
    typ: PhantomData<Invariant<Q>>,
}

pub struct TCell<Q, T: ?Sized> {
    // use Invariant<Q> for invariant parameter, not influencing
    // other auto-traits, e.g. UnwindSafe (unlike other solutions like `*mut Q` or `Cell<Q>`)
    owner: PhantomData<Invariant<Q>>,
    // It's fine to Send a TCell to a different thread if the containted
    // type is Send, because you can only send something if nothing
    // borrows it, so nothing can be accessing its contents.
    //
    // `UnsafeCell` disables `Sync` and already gives the right `Send` implementation.
    // `Sync` is re-enabled below under certain conditions.
    value: UnsafeCell<T>,
}
```

- [https://rustsec.org/advisories/RUSTSEC-2022-0007.html](https://rustsec.org/advisories/RUSTSEC-2022-0007.html)
- [https://github.com/uazu/qcell/issues/20](https://github.com/uazu/qcell/issues/20)


## RUSTSEC-2022-0010：Enum错误实现 trait时 enum_map 宏可能会导致 UB

`enum_map!`受影响版本在使用宏时未正确检查枚举的长度，信任用户提供的长度。

当`Enum` trait 中的 `LENGTH` 与 `EnumArray` trait 中的数组长度不匹配 时，可能会导致枚举映射初始化为未初始化的类型，进而允许攻击者执行任意代码。

这个问题只能在手动实现 Enum trait 时发生，它永远不会发生在使用#[derive(Enum)].

触发此漏洞的示例代码如下所示：

```rust
enum E {
    A,
    B,
    C,
}

impl Enum for E {
    // LENGTH 长度不等于 EnumArray 长度
    const LENGTH: usize = 2;

    fn from_usize(value: usize) -> E {
        match value {
            0 => E::A,
            1 => E::B,
            2 => E::C,
            _ => unimplemented!(),
        }
    }

    fn into_usize(self) -> usize {
        self as usize
    }
}

impl<V> EnumArray<V> for E {
    type Array = [V; 3];
}

let _map: EnumMap<E, String> = enum_map! { _ => "Hello, world!".into() };
```

- [https://rustsec.org/advisories/RUSTSEC-2022-0010.html](https://rustsec.org/advisories/RUSTSEC-2022-0010.html)
- [https://gitlab.com/KonradBorowski/enum-map/-/blob/master/CHANGELOG.md#version-202](https://gitlab.com/KonradBorowski/enum-map/-/blob/master/CHANGELOG.md#version-202)