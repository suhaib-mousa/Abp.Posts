### Dynamic Widget Communication

In modern web development, ensuring that widgets communicate smoothly and update dynamically based on user actions is crucial. I achieved this in the ABP.IO Widgets by leveraging an event-driven approach, allowing widgets to interact seamlessly without the need for complex dependencies.

#### The Core Idea

Rather than having widgets tightly coupled—where one directly manipulates another—you can have them emit and listen for events. This approach keeps your code clean and your widgets reusable.

For example:

1. **Categories Widget**: Displays categories and triggers an event when a category is selected.
2. **Courses Widget**: Listens for the category selection event and refreshes the course list accordingly.
3. **Courses Number Widget**: Updates the course count based on the refreshed list.

![widgets-communication](https://github.com/user-attachments/assets/1a27279c-4de7-40f9-904c-4c64b8e24efe)

#### Quick Implementation

Here's a brief overview of how it works:

- **Categories Widget** triggers a `category-selected` event:
```javascript
(function ($) {
    abp.widgets.Categories = function ($widget) {
        var widgetManager = new abp.WidgetManager({
            wrapper: '.abp-widget-wrapper[data-widget-name="Categories"]',
            filterCallback: function () {
                return {
                    // returned values here
                };
            }
        });

        async function init() {
            // ...
            // Emit that the category selected
            $(".category-tab").off('click').on('click', function () {
                var categoryName = $(this).attr('category-name');
                $(document).trigger('category-selected', { category: categoryName });
            });
        }

        return {
            init: init,
        };
    };
    
})(jQuery);
```
- **Courses Widget** listens for this event and updates the courses:
```javascript
(function ($) {
    let category = 'All'; // initial category
    abp.widgets.Courses = function ($widget) {
        var widgetManager = new abp.WidgetManager({
            wrapper: '.abp-widget-wrapper[data-widget-name="Courses"]',
            filterCallback: function () {
                return {
                    category: category
                };
            }
        });

        async function init() {
            //...
            $(document).trigger('courses-updated', { courses: filteredCourses });
        }

        function refreshCourses(_category) {
            category = _category;
            widgetManager.refresh();
        }
       
        return {
            init: init,
            refreshCourses: refreshCourses
        };
    }

})(jQuery);
```
- **Courses Number Widget** listens for the `courses-updated` event to update the course count:
```javascript
(function ($) {
    let number = 0;
    abp.widgets.CoursesNumber = function ($widget) {
        var widgetManager = new abp.WidgetManager({
            wrapper: '.abp-widget-wrapper[data-widget-name="CoursesNumber"]',
            filterCallback: function () {
                return {
                    number: number
                };
            }
        });
        async function init() {
            $('#number').text(number);
        }
        async function refreshCoursesNumber($number) {
            number = $number;
            widgetManager.refresh();
        }
        return {
            init: init,
            refreshCoursesNumber: refreshCoursesNumber
        };
    };
})(jQuery);
```

To link the widgets and ensure they refresh based on emitted events, you can hard-code this on the page containing the widgets or use the script field if you’re working within the ABP CMS Kit pages feature.

![widgets-communication-script](https://github.com/user-attachments/assets/305f868e-8d4f-48b3-9dd5-85b2d57d0b62)

This setup makes your widgets flexible and easy to manage. Place them anywhere on the page, and they’ll work together effortlessly.

By using event-driven communication, you simplify your development process and make your application more scalable and easier to maintain. Try it in your next project!

Happy coding 😁❤️
