Ever had a moment while working on your code where you *know* something's going wrong inside a NuGet package, but you can’t see what's happening?

Good news: Visual Studio can actually let you step **inside** those packages—even ones like `Volo.Abp.Account` or `Volo.Abp.Identity`.

---

### 🛠️ Here's how to enable it:

1. **Turn off “Just My Code”**  
   `Tools → Options → Debugging → Uncheck "Enable Just My Code"`

2. **Enable Microsoft Symbol Servers**  
   `Tools → Options → Debugging → Symbols → Check "Microsoft Symbol Servers"`

3. **Enable Source Link support**  
   Visual Studio will try to pull down actual source files from NuGet (most ABP.IO packages support this!).

---

### 💡 Real-World Example

Let’s say you’re overriding ABP’s `IdentityUserAppService`, and something’s not working in `CreateAsync`.

You put a breakpoint on `base.CreateAsync(input)`... then hit **F11**.

Boom 💥—you’re now stepping through **the actual ABP.IO source code** from the NuGet package.

This helps you understand how user creation works, how roles are assigned, and what’s getting saved.

---

> ⚠️ **Heads up:**  
> Enabling symbol loading and Source Link might slow down your IDE and debugging sessions.  
> **Tip:** Once you're done debugging NuGet internals, go back and **re-enable "Just My Code"** and disable the symbol servers to speed things up.

---

### 🎯 Final Thought

Whether you're debugging login flows, auditing logic, or background jobs, stepping into NuGet packages can save you hours. Just don’t forget to switch the settings back when you're done!
