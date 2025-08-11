# **gRPC – Definition**

> gRPC (**g**oogle **R**emote **P**rocedure **C**all) is a **high-performance, open-source RPC framework** that uses **HTTP/2** as its transport protocol and **Protocol Buffers (protobuf)** as its interface definition language (IDL) for defining APIs and serializing data.

It allows services to **call methods on remote servers as if they were local functions**, but with much **faster performance** and **smaller payloads** than traditional REST over JSON.

---

## **Why gRPC exists**

* REST over JSON is **human-readable** but **not the most efficient** (text-based, larger payloads, parsing overhead).
* Microservices sometimes need **low-latency, high-throughput** communication.
* Google internally replaced many JSON APIs with gRPC for better performance — then open-sourced it.

---

## **Key Concepts & Lingo**

* **RPC (Remote Procedure Call)** → Call a function on another machine like it’s local.
* **HTTP/2** → Enables multiplexing, server push, and binary framing → lower latency than HTTP/1.1.
* **Protocol Buffers (Protobuf)** → Binary serialization format, smaller & faster than JSON/XML.
* **IDL (Interface Definition Language)** → You define your service methods & messages in a `.proto` file.
* **Code Generation** → gRPC generates client & server code in multiple languages from `.proto`.
* **Streaming Modes:**

  * **Unary** → One request, one response.
  * **Server Streaming** → One request, multiple responses.
  * **Client Streaming** → Multiple requests, one response.
  * **Bidirectional Streaming** → Both send multiple messages in real time.

---

## **Example**

Imagine in our **E-commerce microservices** setup:

* Cart Service calls Payment Service’s `ChargeCard` function.
* With gRPC, instead of an HTTP REST call, we define a `.proto` file:

```proto
syntax = "proto3";

service PaymentService {
  rpc ChargeCard (ChargeRequest) returns (ChargeResponse);
}

message ChargeRequest {
  string card_number = 1;
  double amount = 2;
}

message ChargeResponse {
  bool success = 1;
  string transaction_id = 2;
}
```

### Behind the Scenes:

1. **Define contract** in `.proto` file.
2. Run **protoc compiler** → Generates **client stubs** & **server skeletons** in your language.
3. **Client** calls `paymentClient.ChargeCard()` like a normal function.
4. Under the hood:

   * gRPC serializes request to **binary** (protobuf).
   * Sends it over **HTTP/2** to server.
   * Server deserializes and processes.
5. Response is serialized back to protobuf and sent to client.

---

## **Advantages Over REST**

* **Smaller payloads** → Binary data instead of verbose JSON.
* **Faster** → Less parsing, uses HTTP/2.
* **Strong typing** → Schema-first, catches errors at compile time.
* **Streaming support** → Real-time communication is built-in.
* **Multi-language** → Same `.proto` works for Java, Go, Python, Node.js, etc.

---

## **When to Use gRPC**

* **Microservices internal communication** (low latency, high throughput).
* **Real-time APIs** (chat, IoT, stock market feeds).
* **Polyglot environments** (multiple languages).
* **When payload size matters** (mobile, embedded systems).

**When NOT to use:**

* If you need **public APIs** for browsers — gRPC isn’t natively supported by most browsers without gRPC-Web proxy.
* If human-readable responses are important for debugging.

---

If you want, I can now **show you a side-by-side performance & payload comparison of REST vs gRPC** so you can easily explain in an interview why companies prefer gRPC for microservices. Would you like me to make that?
