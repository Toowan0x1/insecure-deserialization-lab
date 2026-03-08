
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

Open browser:
```
http://localhost:5000
```

Testing Locally from Burp
run the server like:
```bash
dotnet run --urls=http://0.0.0.0:5000
```
Then intercept traffic using burpsuite.




