# Stop Renaming Fields — Make Your IDE Suggest `_camelCase` Automatically

As developers, we value consistency and saving time. So when you're injecting dependencies and use `Alt + Enter` to generate a private field, you'd expect your IDE to follow your naming conventions.

But out of the box, it often suggests this:

```csharp
private readonly ICdnProvider cdnProvider;
```

When what you actually want is:

```csharp
private readonly ICdnProvider _cdnProvider;
```

Good news: **Visual Studio** can be configured to do exactly that.

---

## 🛠 Visual Studio: Setting the Naming Rule

To make Quick Actions (`Alt + Enter`) suggest `_camelCase` for private fields:

1. Open **Tools > Options > Text Editor > C# > Code Style > Naming**.
2. Click **“Manage naming styles”**.
3. Create a new naming style:
   - **Name Style Title**: startWithUnderscore
   - **Required Prefix:**: `_`
   - **Required Suffix**: 
   - **Word Separator**: 
   - **Capitalization:** `camel Case Name`
5. Add a new naming rule:
   - Specification: Private or Internal field
   - Required Style: startWithUnderscore (The one you just created)
   - Severity: Suggestion or Warning
6. Save and restart Visual Studio.

Now when you generate a field, Visual Studio will follow your style.

> **Note:**  
> If you want to enforce naming conventions across your whole project or team, consider adding a `.editorconfig` file with naming rules.  
>  
> Both Visual Studio and Rider support `.editorconfig`, and it helps keep things consistent regardless of individual IDE settings.  
>  
> For full details, refer to official documentation from [Microsoft](https://docs.microsoft.com/en-us/visualstudio/ide/editorconfig-code-style-settings-reference).

---

## ✅ Final Result

Say you have this constructor:

```csharp
public ComponentManager(ICdnProvider cdnProvider)
{
    // Alt + Enter here
}
```

Now the generated field will be:

```csharp
private readonly ICdnProvider _cdnProvider;
```

Clean, consistent, and no manual renaming needed.

---

## 👍 Wrap-up

This is a small setup step that removes repetitive cleanup from your workflow. Whether you're working solo or with a team, it's worth taking the time to set it up once and let your IDE do the right thing from then on.

Happy coding!
