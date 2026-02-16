Noticing how quickly things are changing lately?

We were just getting used to .NET 10. Now, Microsoft has already released **.NET 11 Preview 1**. This release is significant, and the emphasis is clearly on improving performance. [`Microsoft for Developers`][1]

Here are the key points that caught my attention from the release notes:

1. Native AI-friendly numeric support (BFloat16):  
   .NET 11 Preview 1 introduces support for **BFloat16**. [`GitHub`][2]  
   Why does this matter? BFloat16 is popular in AI training and inference because it lowers memory usage and increases speed while maintaining acceptable precision.

2. Compression leveled up (Zstandard):  
   There is now native support for **Zstandard** in `System.IO.Compression`, including types like `ZstandardStream` and related options. [`heise online`][3]  
   Why does this matter? Zstandard strikes a great balance between compression ratio and speed, which effectively reduces network traffic and cloud storage costs.

3. Runtime async improvements (real work under the hood):  
   This preview includes significant updates for async execution and diagnostics at runtime.  
   [`Microsoft for Developers`][1]  
   Why does this matter? Async paths can operate more efficiently with less overhead, and debugging is getting easier, especially for deep async call chains and handling exceptions.

4. Smarter JIT for loops (Induction Variable analysis):  
   The JIT now performs deeper analysis of loops, allowing for more automatic optimizations.  
   [`Microsoft for Developers`][1]  
   Why does this matter? This can speed up code that involves heavy loops and computations without needing you to make changes—especially useful in numeric and processing tasks.

5. The CLI became interactive (better daily dev experience):  
   `dotnet run` now supports selecting target frameworks and devices interactively for multi-target projects, with MAUI being a prime example. [`GitHub`][4]  
   Why does this matter? It speeds up your workflow and cuts down on the flags you must remember and type each time.

6. `System.Net.Mime.MediaTypeMap` (finally!):  
   There is now an official API to map file extensions to MIME types and back, for example, `.png` to `image/png`. [`Microsoft Learn`][5]  
   Why does this matter? For years, we either created our own solutions or relied on third-party libraries for something that should have been standard. Now it’s built-in.

7. EF Core improvements and faster migrations workflow:  
   The EF Core tools and JSON mapping are continuing to improve, with enhancements to migration workflows and better JSON support for complex scenarios. [`Microsoft for Developers`][1]

8. MAUI got faster (XAML source generation):  
   XAML source generation is now the default in MAUI for .NET 11. [`Microsoft for Developers`][1]  
   Why does this matter? It leads to faster builds, smoother debugging, and better runtime performance as more tasks move from runtime to compile time.

If you want to try it:  
.NET 11 Preview 1 is available now, and using preview tooling is recommended. [`Microsoft for Developers`][1]

In summary, this update demonstrates that .NET is not "just for the web" anymore. It is increasingly evolving into a platform suited for high-performance workloads and AI-ready situations.

[1]: https://devblogs.microsoft.com/dotnet/dotnet-11-preview-1 ".NET 11 Preview 1 is now available!"  
[2]: https://github.com/dotnet/core/blob/main/release-notes/11.0/preview/preview1/libraries.md "core/release-notes/11.0/preview/preview1/libraries.md at ..."  
[3]: https://www.heise.de/en/news/Microsoft-starts-with-previews-for-NET-11-0-11173628.html "Microsoft starts with previews for .NET 11.0"  
[4]: https://github.com/dotnet/core/blob/main/release-notes/11.0/preview/preview1/sdk.md "core/release-notes/11.0/preview/preview1/sdk.md at main · ..."  
[5]: https://learn.microsoft.com/en-us/dotnet/api/system.net.mime.mediatypemap?view=net-11.0&viewFallbackFrom=net-7.0 "MediaTypeMap Class (System.Net.Mime)"
