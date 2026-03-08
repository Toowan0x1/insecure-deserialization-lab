# insecure-deserialization-lab

Security research and proof-of-concepts for insecure deserialization vulnerabilities in Java and .NET frameworks.

This repository contains experiments, payloads, and vulnerable applications for studying deserialization attacks in different ecosystems.

- .NET
  - Newtonsoft.Json
  - BinaryFormatter
  - NetDataContractSerializer
  - ObjectStateFormatter

- Java
  - Jackson
  - Java native serialization
  - Commons Collections
  - ysoserial gadget chains

## Purpose

This repository was created to:

- Understand insecure deserialization attacks
- Experiment with gadget chains
- Practice exploitation techniques used in real penetration tests

## Tools used

### ysoserial tool

There are two main versions:
- Java: [ysoserial](https://github.com/pwntester/ysoserial)
- .NET: [ysoserial.net](https://github.com/pwntester/ysoserial.net) Deserialization payload generator for a variety of .NET formatters.

ysoserial is a payload generator. It:
- Uses a known gadget chain
- Crafts a malicious serialized object
- Embeds your command inside it
- Outputs raw serialized bytes

Example logic:
```App deserializes object → gadget chain triggers → command executes```
