[DigitalOcean Spaces](https://www.digitalocean.com/products/spaces) is an S3-compatible object storage service, making it possible to integrate seamlessly with ABP’s AWS Blob Storage Provider. This guide explains how to configure your ABP application to use DigitalOcean Spaces as a blob storage provider.

## Step 1: Install the AWS Blob Storage Provider

Run the following command in your ABP project:

```sh
# Install AWS Blob Storage Provider
abp add-package Volo.Abp.BlobStoring.Aws
```

## Step 2: Configure DigitalOcean Spaces in ABP

Edit your module configuration file (`YourModule.cs`) and add the AWS provider settings:

```csharp
[DependsOn(
    typeof(AbpBlobStoringModule),
    typeof(AbpBlobStoringAwsModule)
)]
public class YourModule : AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        Configure<AbpBlobStoringOptions>(options =>
        {
            options.Containers.Configure<DigitalOceanContainer>(container =>
            {
                container.UseAws(aws =>
                {
                    aws.AccessKeyId = "your-digitalocean-access-key";
                    aws.SecretAccessKey = "your-digitalocean-secret-key";
                });
            });
        });
    }
}
```

## Step 3: Handling Custom Amazon S3 Client for DigitalOcean Spaces

By default, ABP uses **AWS’s `AmazonS3Client`**, which does **not** allow setting a custom `ServiceURL` directly. Since DigitalOcean Spaces requires a specific `ServiceURL`, we must override the default S3 client factory.

### Why Does DigitalOcean Require a Custom AmazonS3Client?

Unlike AWS S3, **DigitalOcean Spaces requires explicitly setting a `ServiceURL`** to the appropriate regional endpoint (e.g., `https://nyc3.digitaloceanspaces.com`).

```csharp
var config = new AmazonS3Config
{
    ServiceURL = "https://nyc3.digitaloceanspaces.com",
    ForcePathStyle = true
};

var client = new AmazonS3Client("YOUR-ACCESS-KEY", "YOUR-SECRET-KEY", config);
```

Since **ABP does not provide a direct way to set `ServiceURL`**, we **must replace the default `AmazonS3Client` factory**.

### Custom Amazon S3 Client Factory

The following implementation ensures every S3 request is correctly routed to DigitalOcean Spaces:

```csharp
using Amazon.S3;
using Volo.Abp.BlobStoring.Aws;
using Volo.Abp.Caching;
using Volo.Abp.DependencyInjection;
using Volo.Abp.Security.Encryption;

[Dependency(ReplaceServices = true)]
[ExposeServices(typeof(IAmazonS3ClientFactory))]
public class CustomAmazonS3ClientFactory : DefaultAmazonS3ClientFactory
{
    public CustomAmazonS3ClientFactory(
        IDistributedCache<AwsTemporaryCredentialsCacheItem> cache,
        IStringEncryptionService stringEncryptionService)
        : base(cache, stringEncryptionService)
    {
    }

    public override Task<AmazonS3Client> GetAmazonS3Client(AwsBlobProviderConfiguration configuration)
    {
        var config = new AmazonS3Config
        {
            ServiceURL = "https://nyc3.digitaloceanspaces.com",
            ForcePathStyle = true
        };

        return Task.FromResult(new AmazonS3Client(configuration.AccessKeyId, configuration.SecretAccessKey, config));
    }
}
```

## Step 4: Create a Blob Storage Container

Define a blob container for **DigitalOcean Spaces**:

```csharp
using Volo.Abp.BlobStoring;

[BlobContainerName("abp-digital-ocean")]
public class DigitalOceanContainer {}
```

## Step 5: Upload a File to DigitalOcean Spaces

Use dependency injection to access the blob container and upload a file:

```csharp
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;
using Volo.Abp.BlobStoring;
using Volo.Abp.DependencyInjection;

public class HelloWorldService : ITransientDependency
{
    public ILogger<HelloWorldService> Logger { get; set; }
    public IBlobContainer<DigitalOceanContainer> BlobContainer { get; }

    public HelloWorldService(IBlobContainer<DigitalOceanContainer> blobContainer)
    {
        Logger = NullLogger<HelloWorldService>.Instance;
        BlobContainer = blobContainer;
    }

    public async Task UploadFileAsync()
    {
        string filePath = "hi/hello-world.txt";
        string content = "Hello from DigitalOcean Spaces!";
        await BlobContainer.SaveAsync(filePath, content.GetBytes());

        Logger.LogInformation($"File '{filePath}' uploaded successfully.");
    }
}
```

## Additional Blob Storing Operations

ABP.io provides built-in methods to **retrieve**, **delete**, and **manage files** in blob storage. Here are some common operations:

### Get a File
```csharp
var fileBytes = await BlobContainer.GetAllBytesAsync("hi/hello-world.txt");
```

### Delete a File
```csharp
await BlobContainer.DeleteAsync("hi/hello-world.txt");
```

For more advanced scenarios, visit the official documentation: [ABP Blob Storing](https://abp.io/docs/latest/framework/infrastructure/blob-storing).

## Conclusion

Using **DigitalOcean Spaces with ABP’s AWS Blob Storing Provider** is straightforward because DigitalOcean is S3-compatible. If ABP’s built-in AWS integration does not support setting `ServiceURL`, replacing the **Amazon S3 Client Factory** ensures seamless integration.

By following this guide, you can **fully integrate DigitalOcean Spaces** with ABP’s Blob Storing system and use it just like AWS S3! 🚀
