Hello ABP.IO Community,

### What's the Marked Item System?

Imagine being able to easily flag your favorite blog posts or star important products. The Marked Item system does just that. It allows users to mark items with icons or emojis, making it super easy to keep track of what’s important to them. This nifty addition lets users mark any kind of resource, like blog posts or products, as favorites, starred, flagged, or bookmarked.

### Key Features

1. **Marked Toggling Component**:
   - Users can mark items using customizable icons or emojis. This makes the user experience more interactive and fun!
   - Here’s a quick example of how you can use it:
     ```razor
     @await Component.InvokeAsync(typeof (MarkedItemToggleViewComponent), new
     {
         entityId = "...",
         entityType = "product",
         needsConfirmation = true // (optional)
     })
     ```

2. **Simple Configuration**:
   - You can easily set up your own entity types and mark types (like favorite or starred).
   - Just add this configuration in your `YourModule.cs`:
     ```csharp
     Configure<CmsKitMarkedItemOptions>(options =>
     {
         options.EntityTypes.Add(
             new MarkedItemEntityTypeDefinition(
                 "product",
                 StandardMarkedItems.Favorite
             )
         );
     });
     ```

3. **Filtering on Marked Items**:
   - Users can filter their marked items, making it a breeze to find their favorites.
   - Here’s how you can filter marked items in your queries:
     ```csharp
     protected async override Task<IQueryable<YourEntity>> CreateFilteredQueryAsync(YourEntityPagedAndSortedResultDto input)
     {
         IQueryable<YourEntity> query = await base.CreateFilteredQueryAsync(input);

         if (input.FilterOnFavorites && CurrentUser.IsAuthenticated)
         {
             var markedQuery = await UserMarkedItemRepository.GetQueryForUserAsync("entityType", CurrentUser.GetId());
             query = query.Where(e => markedQuery.Any(m => m.EntityId == e.Id));
         }

         return query;
     }
     ```

### How to Enable the Marked Item Feature

By default, the CMS Kit features are turned off. Here’s how to turn on the Marked Item feature in your application:

1. **During Development**: Use the Global Feature system.
2. **At Runtime**: Use the ABP Framework's Feature System.

> Check the ["How to Install" section of the CMS Kit Module documentation](Index.md#how-to-install) to see how to enable/disable CMS Kit features on development time.

### Conclusion

This new feature excites me much, and I hope it adds even more engagement and user experience to your apps. Try it out and see how it can enhance your applications.

Happy coding!
