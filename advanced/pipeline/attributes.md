---
title: Attributes
category: advanced
caption: Passing information among interceptors
keywords: attributes Passing information among interceptors dependency injector instance container
---

Ktor offers an `Attributes` class that acts as a small typed instance container / dependency injector.

The `ApplicationCall` contains a `attributes` property including an instance of this class.
The lifespan of this container is the call: since the request has started, until the response is completely sent.

You can put as many attributes as required per call from an interceptor, and retrieve them later in another interceptor.

### Basic usage

It is possible to define your own typed attributes, by creating instances of the `AttributeKey` class like this:

```kotlin
// Declared as a global property
val MyAttributeKey = AttributeKey<Int>("MyAttributeKey")
```

You can later set the attributes with:

```kotlin
call.attributes.put(MyAttributeKey, 10)
```

And retrieve them in another interceptor by calling:

```kotlin
call.attributes.get(MyAttributeKey)
```

### API reference

The full interface for this class, looks like:

```kotlin
interface Attributes {
    operator fun <T : Any> get(key: AttributeKey<T>): T
    fun <T : Any> getOrNull(key: AttributeKey<T>): T?
    operator fun contains(key: AttributeKey<*>): Boolean
    fun <T : Any> put(key: AttributeKey<T>, value: T)
    fun <T : Any> remove(key: AttributeKey<T>)
    fun <T : Any> take(key: AttributeKey<T>): T = get(key).also { remove(key) }
    fun <T : Any> takeOrNull(key: AttributeKey<T>): T? = getOrNull(key).also { remove(key) }
    fun <T : Any> computeIfAbsent(key: AttributeKey<T>, block: () -> T): T
    val allKeys: List<AttributeKey<*>>
}
```