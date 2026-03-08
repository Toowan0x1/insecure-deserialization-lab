## Insecure Deserialization (.NET)


## Insecure Deserialization in .NET – Definition

### 1. Definition

This vulnerability occurs occurs when an application deserializes untrusted data without proper validation. -> when the target application uses vulnerable libraries, framework classes, or built-in platform classes that can be abused during deserialization.

During deserialization, the application reconstructs objects from serialized data. In some cases, special methods (such as constructors or deserialization callbacks like OnDeserialized) may be automatically executed.

If an attacker can control the serialized data, they may craft a malicious object that triggers unintended code execution during the deserialization process.

A vulnerable class that can be abused during deserialization is called a **gadget**.

When multiple gadgets are chained together so their method calls lead to arbitrary code execution, the sequence is known as a **gadget chain**.

### 2. Key Concepts
- Gadget and Gadget Chain
- Serializers / Formatters

#### Gadget:
- A legitimate framework classe that can be abused during deserialization to execute unintended behavior.

#### Gadget chain:
- One gadget may not give RCE directly.
- So attackers chain multiple gadgets:
- Object A → triggers B → calls C → executes Process.Start()
- Tools like [ysoserial.net](https://github.com/pwntester/ysoserial.net) generate these chains automatically.

| File              | Purpose                                                                    |
| ----------------- | -------------------------------------------------------------------------- |
| Gadget            | A specific class (or chain of classes) inside a library that can be abused |
| Gadget chain      | Multiple gadgets combined to reach RCE                                     |

****Where do gadgets come from****

They usually come from:
- Third-party libraries
- Framework classes
- Built-in platform classes

For example:
- commons-collections (Java)
- .NET framework classes
- ASP.NET components
- Logging libraries


### 3. Tools

#### ysoserial.net

A tool used to generate malicious serialized payloads for testing .NET deserialization vulnerabilities.
- Contains a collection of known **gadget chains** (e.g., TypeConfuseDelegate, ActivitySurrogateSelector, ObjectDataProvider).
- Generates serialized objects that embed a command inside the payload.
- Used for **security testing and research** of insecure deserialization.

Key points:
- It generates payloads
- It relies on existing gadget chains
- It relies on existing gadget chains to trigger code execution when deserialized.
- It embeds a command string inside serialized object

See: [https://github.com/pwntester/ysoserial.net](https://github.com/pwntester/ysoserial.net)

### 4. Exploitation (Exploitation Flow / Attack flow)

#### During the deserialization process:
- The application recreates objects from user input
- Some objects execute code automatically during construction / property setting
- That behavior can be abused
- If attacker controls object type + data → gadget executes something dangerous.

#### Example:
A gadget might:
- Accept method name
- Accept parameters
- Invoke method during deserialization
- If attacker sets ```Method = "Start" Parameter = "cmd.exe"```
- Boom → RCE.

#### Why do gadgets exist in large frameworks?

Most large frameworks contain gadgets and that because:
- They were not designed with deserialization attacks in mind
- Many classes have dynamic behaviors

#### When does this attack become possible?

An insecure deserialization attack becomes possible when:
- A **gadget** exists
- Gadget executes something during deserialization
- That something can be influenced by attacker

In old .NET Framework, gadgets such as ```ObjectDataProvider```, ```TypeConfuseDelegate```, ```WindowsIdentity``` were commonly abused.

In modern .NET 6:
- Exploitation is generally harder
- Requires **third-party libraries**
- Or the presence of **custom application code** that performs unsafe deserialization


### 5. Exploitation Conditions & Prerequisites

**Exploit prerequisites:**
- A vulnerable deserialization endpoint
- The right gadget chain matching the target’s libraries


## Preparing our ASP.NET Lab to Test Deserialization Attacks on .NET

ASP.NET is a web framework developed by Microsoft. It is used to build, Web applications, APIs, Enterprise systems, and Internal company portals.

### 1. Installing .NET SDK on linux

First you have to update your system with ```sudo apt update``` and ```sudo apt upgrade```.

Install the SDK:

```bash 
sudo apt install -y dotnet-sdk-6.0
```

Verify installation:
```bash
dotnet --version
```

### 2. Create a Test ASP.NET Web App

Create a project folder:
```bash
mkdir dotnet-test
cd dotnet-test
```

Create a new web app:
```bash
dotnet new webapp
```
This generates an ASP.NET Core Razor application.

### 3. Run the web app:
```bash
dotnet run
```

You should see something like:
```bash
Now listening on: http://localhost:5000
```

Open browser ```http://localhost:5000``` or testing locally from Burpsuite, we can run the server like that:
```bash
dotnet run --urls=http://0.0.0.0:5000
```
Then intercept traffic using burpsuite.

## Testing Deserializations

Now we can create simple vulnerable endpoints in ASP.NET and test using:
- Newtonsoft.Json
- [BinaryFormatter](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.serialization.formatters.binary.binaryformatter?view=net-10.0)
- [DataContractSerializer](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.serialization.datacontractserializer?view=net-10.0)
- [ObjectStateFormatter](https://learn.microsoft.com/en-us/dotnet/api/system.web.ui.objectstateformatter?view=netframework-4.8.1)


### 1. Create minimal ASP.NET web app
Create template for a minimal ASP.NET web application (no Razor pages, just a simple HTTP server):

```bash
dotnet new web -n <dotnet_appName>
```

### 2. Project Structure

```bash
$ ls
appsettings.Development.json
appsettings.json
obj/
Program.cs
Properties/
webapp.csproj
```
Explanation:

| File             | Purpose               |
| ---------------- | --------------------- |
| Program.cs       | Main application code |
| webapp.csproj    | Project configuration |
| appsettings.json | Application settings  |
| Properties/      | launch settings       |
| obj/             | build artifacts       |


### 3. Viewing the main application file

Program.cs:

```cs
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

That's will:
- Creates an endpoint /
- Returns "Hello World!" on that endpoint

Running the application: ```dotnet run --urls=http://0.0.0.0:5000``` and then curl:

```bash
$ curl http://localhost:5250
Hello World!
```

Now we can create endpoints like ```/deserialize```, ```/api/test```, etc. for testing our **.NET deserialization attacks and web vulnerabilities**.

We gonna see **a small vulnerable .NET endpoint** I'll add it to `Program.cs` to test **insecure deserialization**.







