## Roslyn Analyzers do not surface information in the build

This repo exists to demonstrate the situation experienced in this GitHub issue: https://github.com/dotnet/roslyn/issues/60620

This repo contains a devcontainer which should allow you replicate simply in VS Code.

When using VS Code I experience informational diagnostics in the problems pane of VS Code.

![screenshot of the problems pane in VS Code displaying "Private member 'WeatherForecastController._logger' can be removed as the value assigned to it is never read [AnalyseThis, AnalyseThis] csharp(IDE0052) [Ln 14, Col 57]"](screenshot-of-the-information-i-would-like-in-my-build.png)

But if we run `dotnet build`:

```shell
Microsoft (R) Build Engine version 17.1.0+ae57d105c for .NET
Copyright (C) Microsoft Corporation. All rights reserved.

  Determining projects to restore...
  All projects are up-to-date for restore.
  AnalyseThis -> /workspaces/roslyn-analyzers-reproduction/bin/Debug/net6.0/AnalyseThis.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:08.20
```

This information is not present. 

If I added the following to my `.editorconfig`:

```ini
dotnet_diagnostic.IDE0052.severity = warning
```

I could dial up each individual `IDE****` to a `warning` or an `error`, but I wouldn't get the VS Code problems pane experience of just seeing that information by default.

So there's this manual syncing effort require to spy on the information diagnostics showing up in VS Code, make a note of their codes, and manually add them to the `.editorconfig` as a warning or error.

The problem I'm trying to solve is this; I'd like to find a way to either:
- surface those IDE information diagnostics in the build directly - so the build would have the same information as the problems panel **OR**
- upgrade *all* those information diagnostics to warnings - or errors - so I can use them to fail a build. A category upgrade working the same way that `dotnet_analyzer_diagnostic.category-Style.severity = error` does **OR**
- failing that it would be great if there was a reference somewhere of what IDE information diagnostics that the Roslyn Analysers are looking for and surfacing by default