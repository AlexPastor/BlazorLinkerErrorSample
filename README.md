# BlazorLinkerErrorSample

### Describe the bug
When using a Blazor WebAssembly Hosted project template and referencing a F# Class Library on the Client project there's a null pointer exception thrown when building the Client project. 

Even when both WASM Client and F# library target the same framework: ```.NET Standard 2.1```

By disabling linking on build for the Client project seems to fix the issue:
```
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```
https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/blazor/configure-linker?view=aspnetcore-3.1

### To Reproduce
On a Blazor WebAssembly Hosted, create a new F# Standard Library (2.0+) and reference it to the Client project or any project that the Client project references.

When I try to build the client project I get the following build error:
``` 
Severity	Code	Description	Project	File	Line	Suppression State
Error		Unhandled exception. System.NullReferenceException: Object reference not set to an instance of an object.	BlazorSample.Client	C:\Development\Nuget\packages\microsoft.aspnetcore.blazor.build\3.2.0-preview1.20073.1\targets\Blazor.MonoRuntime.targets	258	
Error		ILLink failed with exit code -1073741819.	BlazorSample.Client	C:\Development\Nuget\packages\microsoft.aspnetcore.blazor.build\3.2.0-preview1.20073.1\targets\Blazor.MonoRuntime.targets	258	
```
Build output is:
``` C#
1>------ Rebuild All started: Project: BlazorSample.Shared, Configuration: Debug Any CPU ------
2>------ Rebuild All started: Project: StandardLibrary, Configuration: Debug Any CPU ------
2>StandardLibrary -> C:\Development\BlazorSample\BlazorSample\StandardLibrary\bin\Debug\netstandard2.1\StandardLibrary.dll
1>BlazorSample.Shared -> C:\Development\BlazorSample\BlazorSample\BlazorSample\Shared\bin\Debug\netstandard2.1\BlazorSample.Shared.dll
3>------ Rebuild All started: Project: BlazorSample.Client, Configuration: Debug Any CPU ------
3>BlazorSample.Client -> C:\Development\BlazorSample\BlazorSample\BlazorSample\Client\bin\Debug\netstandard2.1\BlazorSample.Client.dll
3>Fatal error in Mono CIL Linker
3>C:\Development\Nuget\packages\microsoft.aspnetcore.blazor.build\3.2.0-preview1.20073.1\targets\Blazor.MonoRuntime.targets(258,5): error : Unhandled exception. System.NullReferenceException: Object reference not set to an instance of an object.
3>   at Mono.Linker.Steps.RemoveUnreachableBlocksStep.ConstantExpressionMethodAnalyzer.Analyze()
3>   at Mono.Linker.Steps.RemoveUnreachableBlocksStep.FindConstantExpressionsMethods(Collection`1 types)
3>   at Mono.Linker.Steps.RemoveUnreachableBlocksStep.FindConstantExpressionsMethods(Collection`1 types)
3>   at Mono.Linker.Steps.RemoveUnreachableBlocksStep.Process()
3>   at Mono.Linker.Steps.BaseStep.Process(LinkContext context)
3>   at Mono.Linker.Pipeline.ProcessStep(LinkContext context, IStep step)
3>   at Mono.Linker.Pipeline.Process(LinkContext context)
3>   at Mono.Linker.Driver.Run(ILogger customLogger)
3>   at Mono.Linker.Driver.Execute(String[] args, ILogger customLogger)
3>   at Mono.Linker.Driver.Main(String[] args)
3>C:\Development\Nuget\packages\microsoft.aspnetcore.blazor.build\3.2.0-preview1.20073.1\targets\Blazor.MonoRuntime.targets(258,5): error : ILLink failed with exit code -1073741819.
3>Done building project "BlazorSample.Client.csproj" -- FAILED.
4>------ Rebuild All started: Project: BlazorSample.Server, Configuration: Debug Any CPU ------
4>BlazorSample.Server -> C:\Development\BlazorSample\BlazorSample\BlazorSample\Server\bin\Debug\netcoreapp3.1\BlazorSample.Server.dll
========== Rebuild All: 3 succeeded, 1 failed, 0 skipped ==========
```
GitHub repo with a sample: https://github.com/AlexPastor/BlazorLinkerErrorSample

### Further technical details
- Blazor WebAssembly Hosted template version: ```3.2.0-preview1.20073.1```
- ASP.Net Core version: 3.1.101
- Visual Studio 2019 version 16.4.5
- Output of `dotnet --info`
```.NET Core SDK (reflecting any global.json):
 Version:   3.1.101
 Commit:    b377529961

Runtime Environment:
 OS Name:     Windows
 OS Version:  10.0.18363
 OS Platform: Windows
 RID:         win10-x64
 Base Path:   C:\Program Files\dotnet\sdk\3.1.101\

Host (useful for support):
  Version: 3.1.1
  Commit:  a1388f194c
```
