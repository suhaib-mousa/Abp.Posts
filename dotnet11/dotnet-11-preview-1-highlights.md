Noticing how insanely fast things are moving lately?

We were *just* getting used to .NET 10, and Microsoft already shipped **.NET 11 Preview 1**. This release is a **big one**, and the focus is clearly on **core performance**. [`Microsoft for Developers`][1]

Here are the highlights that stood out to me from the release notes: 💡

1. Native AI-friendly numeric support (BFloat16):
   .NET 11 Preview 1 introduces **BFloat16** support. [`GitHub`][2]
   Why does this matter? BFloat16 is widely used in training and inference for modern AI workloads because it reduces memory usage and speeds things up while keeping “good enough” precision.

2. Compression leveled up (Zstandard):
   There’s now first-class **Zstandard** support in `System.IO.Compression` (including types like `ZstandardStream` and related options). [`heise online`][3] <br />
   Why does this matter? Zstandard is known for a strong balance between compression ratio and speed, which makes a real difference for network traffic and cloud storage costs.

3. Runtime async improvements (real work under the hood):
   There are notable runtime improvements around async execution and diagnostics in this preview.
   <br />[`Microsoft for Developers`][1] <br />
   Why does this matter? Async-heavy paths can get leaner (less overhead), and the debugging/diagnostic story keeps getting better—especially when you’re dealing with deep async call chains and exceptions.

4. Smarter JIT for loops (Induction Variable analysis):
   The JIT is doing deeper loop analysis (IV analysis), which enables more optimizations automatically.
   <br />[`Microsoft for Developers`][1] <br />
   Why does this matter? It can speed up loop-heavy / compute-heavy code without you touching a single line—especially in numeric and processing workloads.

5. The CLI became interactive (better daily dev experience):
   `dotnet run` now supports interactive target framework and device selection for multi-target projects (MAUI is a great example). [`GitHub`][4] <br />
   Why does this matter? It makes the workflow faster and reduces the amount of flags you have to remember and type every time.

6. `System.Net.Mime.MediaTypeMap` (finally!):
   There’s now an official API to map file extensions to MIME types and back (e.g., `.png` → `image/png`). [`Microsoft Learn`][5]
   <br />
   Why does this matter? For years we either hand-rolled this or pulled in third-party libraries for something that should be standard. Now it is.

7. EF Core improvements + faster migrations workflow:
   EF Core tooling and JSON mapping keep improving, including workflow enhancements around migrations and better JSON support for more complex scenarios. [`Microsoft for Developers`][1]

8. MAUI got faster (XAML source generation):
   XAML source generation is now the default approach for MAUI in .NET 11. [`Microsoft for Developers`][1]<br />
   Why does this matter? Faster builds, smoother debugging, and better runtime performance because more work shifts from runtime to compile time.

🏷If you want to try it:
.NET 11 Preview 1 is available now (preview tooling is recommended). [`Microsoft for Developers`][1]

Bottom line: this update makes it clear that .NET isn’t “just for the web” anymore—it’s increasingly shaping up as a platform for **high-performance workloads** and **AI-ready** scenarios.

[1]: https://devblogs.microsoft.com/dotnet/dotnet-11-preview-1 ".NET 11 Preview 1 is now available!"
[2]: https://github.com/dotnet/core/blob/main/release-notes/11.0/preview/preview1/libraries.md "core/release-notes/11.0/preview/preview1/libraries.md at ..."
[3]: https://www.heise.de/en/news/Microsoft-starts-with-previews-for-NET-11-0-11173628.html "Microsoft starts with previews for .NET 11.0"
[4]: https://github.com/dotnet/core/blob/main/release-notes/11.0/preview/preview1/sdk.md "core/release-notes/11.0/preview/preview1/sdk.md at main · ..."
[5]: https://learn.microsoft.com/en-us/dotnet/api/system.net.mime.mediatypemap?view=net-11.0&viewFallbackFrom=net-7.0 "MediaTypeMap Class (System.Net.Mime)"
