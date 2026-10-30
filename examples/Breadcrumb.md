# Breadcrumb Class

[[_TOC_]]

The Breadcrumb class is used to construct and manage breadcrumb trails for use with the [wc-breadcrumb](/Components/Breadcrumb) component.  A breadcrumb trail consists of a sequence of pages representing the user's current location within an application's navigation hierarchy.

A breadcrumb trail can contain two types of entry:
- **placeholder**:
  A piece of text that is included in the trail, but cannot be selected.  They are useful for representing actions that are not accessible through a standard link, such as a Post action.
- **link**:
  A breadcrumb with a name displayed in the trail, with a link that can be selected by the user.

## Creating Breadcrumbs

To create a breadcrumb trail, construct a new Breadcrumb object:
```csharp
new Breadcrumb();
```
This creates a breadcrumb trail capable of holding up to 10 items, which should be sufficient for most applications.

A second constructor is provided to allow for a larger breadcrumb trail:
```csharp
new Breadcrumb(10);
```
If you add more items than this upper limit to the breadcrumb trail an Invalid Operation Exception is thrown with the message "Too many breadcrumbs".

## Fluent Interface
All methods that modify the breadcrumb trail return the current breadcrumb instance.  This allows operations to be chained together.

```csharp
user.Breadcrumbs
  .Add("Home", "~/Home/Index")
  .Add("Users", "~/User/Index");
```

## Link Matching

Several methods identify a breadcrumb by its target page using a
controller and action name.

Only the controller and action are considered when comparing links. Additional route values, such as parameters, are ignored.

The parameter order is intentionally (controller, action) to encourage developers to think about the target page being identified. This differs from the order used by some ASP.NET helper methods.

In these methods the `nameof` operator can be used to provide the `controller` parameter as the `Controller` suffix is automatically removed.

See:
- [HasLink(*controller*, *action*)](#haslink(*controller*,-*action*))
- [RemoveLink(*controller*, *action*)](#removelink(*controller*,-*action*))
- [ClearLink(*controller*, *action*)](#clearlink(*controller*,-*action*))

## Properties

The following properties provide information about the current breadcrumb trail.

| Property |   Type   | Description |
|----------|----------|-------------|
| `Length` | `int`    | The number of items currently in the trail. |
| `Last`   | `Crumb?` | The last entry in the trail or `null` if the trail is empty. |


## Adding Pages
<div style="maring-left: 1em;">

There are several ways to add a page to the trail.

### Add(*name*, *url*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb Add(string name, string? url)
```

Add a new breadcrumb to the trail.

If an item with the same `name` exists, its URL is updated.  Otherwise a new crumb is appended to the end of the trail.  If the `name` identifies a placeholder, it is converted into a link.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `name`    | string  | Text displayed to the user.|
| `url`     | string? | URL to navigate to when the user selects the breadcrumb.|

**Remarks**

- `url` is nullable to allow the result of `Url.Action()` to be passed directly.

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Argument Null     | If either `name` or `url` are `null`, empty or whitespace. |
| Invalid Operation | The breadcrumb trail has exceeded its limit. |

**Example**

To append the "Example" breadcrumb link that will take the user to the Index action of the Example controller when selected:
```csharp
user.Breadcrumbs.Add(
  "Example",
  Url.Action("Index", "Example")
);
```
</div>

### AddMissing(*name*, *url*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb AddMissing(string name, string? url)
```

Add a new breadcrumb with the `name`, with the `url` only if the `name` is not already being used.  If the `name` is being used by an existing item an entry do nothing.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `name`    | string  | Text displayed to the user.|
| `url`     | string? | URL to navigate to when the user selects the breadcrumb.|

**Remarks**

- `url` is nullable to allow the result of `Url.Action()` to be passed directly.

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Argument Null     | If either `name` or `url` are `null`, empty or whitespace. |
| Invalid Operation | The breadcrumb trail has exceeded its limit. |

**Example**

To append the "Example" breadcrumb link that will take the user to the controller's Index action, only if it does not already exist:
```csharp
user.Breadcrumbs.AddMissing(
  "Example",
  Url.Action("Index", "Example")
);
```
</div>

### Add(*name*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb Add(string name)
```
Append the placeholder with the display `name` to the end of the trail.  If a link already exists in the trail with the `name` it is converted into a placeholder.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `name`    | string  | Text displayed to the user. |

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Argument Null     | If either `name` is `null`, empty or whitespace. |
| Invalid Operation | The breadcrumb trail has exceeded its limit. |

**Example**

Append the placeholder "Placeholder" to the end of the trail:
```csharp
user.Breadcrumbs.Add("Placeholder")
```
</div>

### AddMissing(*name*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb AddMissing(string name)
```

Append the placeholder with the `name` to the end of the trial.  Do nothing if a link with that name already exists in the trail.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `name`    | string  | Text displayed to the user.|
| `url`     | string? | URL to navigate to when the user selects the breadcrumb.|

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Argument Null     | If either `name` is `null`, empty or whitespace. |
| Invalid Operation | The breadcrumb trail has exceeded its limit. |

**Example**

Append the placeholder "Placeholder" to the end of the trail, unless an item already exists with that name:
```csharp
user.Breadcrumbs.AddMissing("Example");
```
</div>
</div>

## Inspecting the Trail
<div style="margin-left: 1em;">

These methods all you to determine if a specific breadcrumb is currently in the trail.  This can be used in deciding where to take the user after a method has completed.

### Length
<div style="margin-left: 1em;">

The `Length` property reports the number of items in the breadcrumb trail.

**Example**  

Redirect the user back to the application home page if the breadcrumb trail is empty:
```Csharp
if ( user.Breadcrumbs.Length == 0 ) {
  // Breadcrumbs is empty - redirect users to the application home page.
  return RedirectToAction("Index", "Home");
}
```
</div>

### Last
<div style="margin-left: 1em;">

The property is the most recently added item in the breadcrumb trail or `null` if the trail is empty.

**Example**

Redirect the user back to the current action:
```CSharp
return RedirectToAction(user.Breadcrumbs.Last.Url);
```

</div>

### HasName(*name*)
<div style="margin-left: 1em;">

```CSharp
bool HasName(string name)
```
Return `true` is a breadcrumb link or placeholder with the given `name` exists in the breadcrumb trail.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `name`    | string  | Display name of the breadcrumb item.|

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Null Reference | If either `name` is `null`. |

**Example**

To determine whether the "Example" breadcrumb is present:
```csharp
if ( user.Breadcrumbs.HasName("Example") ) {
  // Breadcrumb is present
} else {
  // Breadcrumb is not present
}
```
</div>

### HasLink(*controller*, *action*)
<div style="margin-left: 1em;">

```CSharp
bool HasLink(string controller, string action)
```
Return `true` if a breadcrumb with a link to the given `action` within the given `controller` is in the breadcrumb trail, otherwise it will return a `false`.

**Parameters**

|   Parameter  |  Type  | Description |
|--------------|--------|-------------|
| `controller` | string | Name of the controller. |
| `action`     | string | Name of the action. |

**Remarks**

See [Link Matching](#link-matching).

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Null Reference | If either `controller` or `action` are `null`. |

**Example**

To determine whether a reference to the Index action of the Example controller is present:
```csharp
if ( user.Breadcrumbs.HasLink(nameof(ExampleController), nameof(ExampleController.Index)) ) {
  // Breadcrumb is present
} else {
  // Breadcrumb is not present
}
```
</div>

### HasLink(*url*)
<div style="margin-left: 1em;">

```CSharp
bool HasLink(string? url)
```
Return `true` if a breadcrumb with a link to the given `url` is in the breadcrumb trail, otherwise it will return a `false`.

**Parameters**

|   Parameter  |  Type   | Description |
|--------------|---------|-------------|
| `url`        | string? | The link to find. |

**Remarks**
- Additional routing information is not included in the comparison.
- `url` is defined as nullable to allow the result of a `Url.Action()`, however, a `null` value will throw a Argument Null exception.

**Exceptions**

|   Exception   | Description |
|---------------|-------------|
| Argument Null | If `url` is `null`. |

**Example**

To determine whether a reference to the Index action of the Example controller is present:
```csharp
if ( user.Breadcrumbs.HasLink(Url.Action("Index", "Example")) ) {
  // Breadcrumb is present
} else {
  // Breadcrumb is not present
}
```
</div>
</div>

## Removing Pages
<div style="margin-left: 1em;">

The following methods allow the removal of breadcrumb items (link or placeholders) from the trail.  They all return an indicator as to whether any items where removed, this can be used to determine where to send the user once the current operation is complete.

### RemoveName(*name*)
<div style="margin-left: 1em;">

```CSharp
Crumb? RemoveName(string name)
```

Remove the item with the given `name` from the trail, returning the item that was removed or `null` if the item was not found.  the controller can inspect the `Link` property this value to determine which breadcrumb was removed.

**Parameters**

|   Parameter  |  Type  | Description |
|--------------|--------|-------------|
| `name`       | string | Display name of the item to remove. |

**Exceptions**

|   Exception    | Description |
|----------------|-------------|
| Null Reference | If `name` is `null`. |

**Example**

To remove the breadcrumb item "Example" from the trail:
```csharp
Crumb? crumb = user.Breadcrumbs.RemoveName("Example");
if ( crumb is null ) {
  // Breadcrumb was not present
} else {
  // crumb is the breadcrumb that was removed.
}
```
</div>

### RemoveLink(*controller*, *action*)
<div style="margin-left: 1em;">

```CSharp
int RemoveLink(string controller, string action)
```

Remove the item with the with a link to the given `action` within the given `controller` from the trail, returning the number of items that have been removed.  This will ignore any additional routing parameters in the link, so may match multiple links, or none.

**Parameters**

|   Parameter  |  Type  | Description |
|--------------|--------|-------------|
| `controller` | string | Name of the controller. |
| `action`     | string | Name of the action. |

**Remarks**

See [Link Matching](#link-matching).

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Null Reference | If either `controller` or `action` are `null`. |

**Example**

To remove the breadcrumb with a reference to the Index action of the Example controller from the trail:
```csharp
if ( user.Breadcrumbs.RemoveLink(nameof(ExampleController), nameof(ExampleController.Index) > 0 ) {
  // Breadcrumbs where removed
} else {
  // No breadcrumbs where removed
}
```
</div>

### RemoveLink(*url*)
<div style="margin-left: 1em;">

```CSharp
int RemoveLink(string? url)
```

Remove the item with the with a link to the given `url` from the trail, returning the number of items that have been removed.  This will ignore any additional routing parameters in the link, so may match multiple links, or none.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `url`     | string? | Link of the breadcrumb. |

**Remarks**
- Additional routing information is not included in the comparison.
- `url` is defined as nullable to allow the result of a `Url.Action()`, however, a `null` value will throw a Argument Null exception.

**Exceptions**

|   Exception   | Description |
|---------------|-------------|
| Argument Null | If `url` is `null`. |

**Example**

To remove the breadcrumb with a reference to the Index action of the Example controller from the trail:
```csharp
if ( user.Breadcrumbs.RemoveLink(Url.Action("Index", "Example") > 0 ) {
  // Breadcrumbs where removed
} else {
  // No breadcrumbs where removed
}
```
</div>

### Drop()
<div style="margin-left: 1em;">

```CSharp
string Drop()
```
Remove the last link in the trail and return the URL associated with the previous link in the trail, i.e., the parent link.

**Exceptions**

|   Exception   | Description |
|---------------|-------------|
| Invalid Operation | If the tail does not contain a parent link. |

**Example**

To read the URL of the parent action a method/action would use:
```CSharp
return RedirectToAction(user.Breadcrumbs.Drop());
```
</div>
</div>

## Trucating the Trail
<div style="margin-left: 1em;">

These methods will remove items from the end of the breadcrumb trail, to a certain point, depending on which method is used.  They all return the modified breadcrumb trail, allowing the add methods to be chained in a fluent manner.

### Clear()
<div style="margin-left: 1em;">

```CSharp
Breadcrumb Clear()
```
Remove all breadcrumb items (both links and placeholders) from the trail, returning the trail to the empty state.

**Example**

To remove the breadcrumb item "Example" from the trail:
```csharp
user.Breadcrumbs
  .Clear()
  .Add("Home", "~/Home/Index");
```
</div>

### Clear(*controller*, *action*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb Clear(Controller controller, [CallerMemberName] string? action = null)
```
Remove all breadcrumb items **after** the first breadcrumb with a link to the given `action` of the given `controller`, leaving that link in place and all items before it in place.

**Parameters**

|   Parameter  |    Type    | Description |
|--------------|------------|-------------|
| `controller` | Controller | A Controller object. |
| `action`     | string     | Name of the action. |

**Remarks**
- The controller name is derived from the `controller` object, allowing the use of the `this` keyword when providing the `controller` parameter.
- Additional routing information is not included in the comparison.
- The order of the parameters are intended to be different from the normal order to make users think.
- The `action` parameter is optional, when not given it will assume the name of the current action.

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Null Reference | If either `controller` or `action` are `null`. |

**Example**

Assuming this is in a action from the ExampleController, the following will remove any breadcrumbs after the reference to the current action:
```csharp
  user.Breadcrumbs.Clear(this);
```

- The `controller` parameter becomes the current controller (i.e., ExampleController)
- The `action` parameter becomes the name of the current action (or method).
</div>

### ClearName(*name*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb ClearName(string name)
```

Remove all of the breadcrumb items **after** the entry with the given display `name`, leaving the item in place.

**Parameters**

|   Parameter  |  Type  | Description |
|--------------|--------|-------------|
| `name`       | string | Display name of the item to remove. |

**Exceptions**

|   Exception    | Description |
|----------------|-------------|
| Null Reference | If `name` is `null`. |

**Example**

The following will remove all items after the "Example" item add a link to the Details action of the current controller:

```csharp
user.Breadcrumbs
  .RemoveName("Example")
  .Add(model.Name, Url.Action("Details", new { ID = model.ID }));
```
</div>

### ClearLink(*controller*, *action*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb ClearLink(string controller, string action)
```
Remove all breadcrumb items **after** the first breadcrumb with a link to the given `action` of the given `controller`, leaving that link in place and all items before it in place.

**Parameters**

|   Parameter  |  Type  | Description |
|--------------|--------|-------------|
| `controller` | string | Name of the controller. |
| `action`     | string | Name of the action. |

**Remarks**

See [Link Matching](#link-matching).

**Exceptions**

| Exception | Description |
|-----------|-------------|
| Null Reference | If either `controller` or `action` are `null`. |

**Example**

The following will remove all items after the link to the Index action of the Example controller and then add a link to the Details action of the current controller:
```csharp
user.Breadcrumbs
  .ClearLink(nameof(ExampleController), nameof(ExampleController.Index)
  .Add(model.Name, Url.Action("Details", new { ID = model.ID });
```
</div>

### ClearLink(*url*)
<div style="margin-left: 1em;">

```CSharp
Breadcrumb ClearLink(string? url)
```

Remove all items **after** the breadcrumb with a link to the given `url` from the trail.

**Parameters**

| Parameter |  Type   | Description |
|-----------|---------|-------------|
| `url`     | string? | Link of the breadcrumb. |

**Remarks**
- Additional routing information is not included in the comparison.
- `url` is defined as nullable to allow the result of a `Url.Action()`, however, a `null` value will throw a Argument Null exception.

**Exceptions**

|   Exception   | Description |
|---------------|-------------|
| Argument Null | If `url` is `null`. |

**Example**

To remove all breadcrumb items after the link to the Index action of the ExampleConttroller from the trail and then add a link to the Details action:
```csharp
user.Breadcrumbs
  .ClearLink(Url.Action("Index", "Example")
  .Add(model.Name, Url.Action("Details", new { ID = model.ID }));
```
</div>

### Home()
<div style="margin-left: 1em;">

```CSharp
Breadcrumb Home()
```
This is a special case of the ClearName method that will remove all breadcrumb items after the item with the display name of "Home".  This can be used to reset the breadcrumb trail at the start of a controller.

**Example**

In the Index method of the ExampleController the following, will reset the breadcrumbs to the current controller:
```csharp
public IActionResult Index() {
  UserModel user = currentUserService.Get(User);
  user.Breadcrumbs
    .Home()
    .Add("Examples", "Index");
}
```
</div>
</div>

## Navigation Patterns

# Further Reading
<div style="margin-left: 1em;">

- [Breadcrumbs](/Components/Breadcrumbs)
- [Breadcrumb Tag Helpers](/Components/Breadcrumbs/Tag-Helpers)
</div>
