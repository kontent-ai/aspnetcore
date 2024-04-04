# ASP.NET Core extensions for Kontent.ai apps

[![NuGet](https://img.shields.io/nuget/vpre/Kontent.Ai.AspNetCore.svg)](https://www.nuget.org/packages/Kontent.Ai.AspNetCore/)
[![Downloads](https://img.shields.io/nuget/dt/Kontent.Ai.AspNetCore.svg)](https://www.nuget.org/packages/Kontent.Ai.AspNetCore/)
[![Build & Test](https://github.com/kontent-ai/aspnetcore/actions/workflows/integrate.yml/badge.svg)](https://github.com/aspnetcore/actions/workflows/integrate.yml)
[![codecov](https://codecov.io/gh/kontent-ai/aspnetcore/branch/master/graph/badge.svg?token=U4Y9PQDW6Q)](https://codecov.io/gh/kontent-ai/aspnetcore)
[![Stack Overflow](https://img.shields.io/badge/Stack%20Overflow-ASK%20NOW-FE7A16.svg?logo=stackoverflow&logoColor=white)](https://stackoverflow.com/tags/kontent-ai)
[![Discord](https://img.shields.io/discord/821885171984891914?color=%237289DA&label=Kontent.ai%20Discord&logo=discord)](https://discord.gg/SKCxwPtevJ)

## Tag Helpers

### `img-asset` tag helper

Useful for rendering responsive images. Supports Assets and Inline images in rich-text elements.

`appsettings.json`:

```json
...
"ImageTransformationOptions": {
    "ResponsiveWidths": [ 200, 300, 400, 600, 800, 1000, 1200, 1400, 1600, 2000 ]
  }
...
```

`\_ViewImports.cshtml`:

```razor
@addTagHelper *, Kontent.Ai.AspNetCore
```

`Startup.cs`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds services required for using options.
    services.AddOptions();

    // Register the ImageTransformationOptions required by Kontent.ai tag helpers
    services.Configure<ImageTransformationOptions>(Configuration.GetSection(nameof(ImageTransformationOptions)));
}
```

`View.cshtml`:

```razor
<img-asset asset="@Model.TeaserImage.First()" class="img-responsive" default-width="300">
  <media-condition min-width="769" image-width="300" />
  <media-condition min-width="330" max-width="768" image-width="689" />
</img-asset>
```

#### Output

```html
<img
  class="img-responsive"
  alt="Coffee Brewing Techniques"
  sizes="(min-width: 769px) 300px, (max-width: 768px) and (min-width: 330px) 689px, 300px"
  src="https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=2000"
  srcset="
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=200   200w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=300   300w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=400   400w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=600   600w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=800   800w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=1000 1000w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=1200 1200w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=1400 1400w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=1600 1600w,
    https://assets-us-01.kc-usercontent.com/975bf280-fd91-488c-994c-2f04416e5ee3/fcbb12e6-66a3-4672-85d9-d502d16b8d9c/which-brewing-fits-you-1080px.jpg?w=2000 2000w
  "
  title="Coffee Brewing Techniques"
/>
```

## Webhooks

Package provides a model for webhook deserialization: `WebhookNotification`. Legacy webhooks are supported via classes `DeliveryWebhookModel` and `ManagementWebhookModel`, to be used with legacy (preview) delivery API and management API triggers respectively. See [Webhooks reference](https://kontent.ai/learn/reference/webhooks-reference/) in Kontent.ai documentation.

## Middlewares

### Webhook signature verification middleware

This middleware verifies the `X-KC-Signature` header. Returns 401 response if the signature is invalid.

`appsettings.json`:

```json
...
"WebhookOptions": {
    "Secret": "<your_secret>"
  },
...
```

`Startup.cs`:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
  // Register the validation middleware for any number of controllers that serve for processing webhooks
  app.UseWebhookSignatureValidator(context => context.Request.Path.StartsWithSegments("/webhooks/webhooks", StringComparison.OrdinalIgnoreCase), Configuration.GetSection(nameof(WebhookOptions)));
}
```
