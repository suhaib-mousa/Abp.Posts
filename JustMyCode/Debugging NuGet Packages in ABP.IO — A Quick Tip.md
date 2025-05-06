Ever had a moment where you're certain something's going wrong inside a NuGet package, but you can’t quite see what’s happening?

Good news: **Visual Studio lets you step directly into those packages**—even ones like `Volo.Abp.Account` or `Volo.Abp.Identity`.

### How to Enable Debugging Inside NuGet Packages

1. **Turn off “Just My Code”**
   Go to `Tools → Options → Debugging`, then uncheck **Enable Just My Code**.

2. **Enable Microsoft Symbol Servers**
   Navigate to `Tools → Options → Debugging → Symbols` and check **Microsoft Symbol Servers**.

3. **Enable Source Link Support**
   Visual Studio will try to fetch actual source files from NuGet packages. Most ABP.IO packages already support Source Link out of the box.

### Real-World Example

Let’s say you're overriding `IdentityUserAppService`, and something isn’t behaving correctly in `CreateAsync`.

You place a breakpoint on `base.CreateAsync(input)` and press **F11**.

Suddenly, you're stepping into the **actual ABP source code**, pulled directly from the NuGet package.

Now you can see how user creation is handled under the hood, how roles are assigned, and what data gets persisted.

### A Few Things to Keep in Mind

* Symbol loading and Source Link support can **slow down your IDE** and debugging experience.
* Once you're done digging through package internals, it’s a good idea to re-enable "Just My Code" and disable symbol servers to keep things fast.

### Final Thought

Whether you're troubleshooting login flows, background jobs, or auditing logic, being able to step inside NuGet packages is a huge time-saver. It’s one of those small Visual Studio tweaks that can make a big difference—especially when working with frameworks like ABP.

---

Would you like a version formatted for Markdown or a shorter summary for social media too?
