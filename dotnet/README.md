
# Preparing our ASP.NET Lab to Test Deserialization Attacks on .NET

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

```cat Program.cs``` give us:

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













