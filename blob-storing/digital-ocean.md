[DigitalOcean Spaces](https://www.digitalocean.com/products/spaces) is an S3-compatible object storage service, making it easy to integrate with ABP’s AWS Blob Storage Provider. However, because DigitalOcean requires a custom ServiceURL, we need a few changes to make it work seamlessly.

This guide walks you through setting up DigitalOcean Spaces as your blob storage provider in an ABP application.

## Step 1: Install the Required Blob Storage Packages  

Before setting up DigitalOcean Spaces, make sure you have the **core Blob Storing package** installed:  

```sh
abp add-package Volo.Abp.BlobStoring
```

Then, install the **AWS Blob Storage Provider** to enable S3-compatible storage:  

```sh
abp add-package Volo.Abp.BlobStoring.Aws
```
---

## Step 2: Configure DigitalOcean Spaces

Modify your module configuration file (`YourModule.cs`) and include the AWS provider settings:

```csharp
// required DependsOn
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
---

## Step 3: Implement a Custom S3 Client Factory

#### ❓ Why Do We Need a Custom `AmazonS3Client`?  

Unlike AWS S3, **DigitalOcean Spaces requires setting a specific `ServiceURL`** (e.g., `https://nyc3.digitaloceanspaces.com`). The **default `AmazonS3Client` in ABP does not allow this**, so we need to override the S3 client factory.

Here’s what a standard [C# DigitalOcean S3 client](https://docs.digitalocean.com/products/spaces/how-to/use-aws-sdks/#configure-a-client) looks like:  

```csharp
var config = new AmazonS3Config
{
    ServiceURL = "https://nyc3.digitaloceanspaces.com",
    ForcePathStyle = true
};

var client = new AmazonS3Client("YOUR-ACCESS-KEY", "YOUR-SECRET-KEY", config);
```

Since ABP doesn’t support setting the `ServiceURL` directly, we must **replace the default S3 client factory**, here’s how you override the **default Amazon S3 client** in ABP:

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
            ServiceURL = "https://nyc3.digitaloceanspaces.com", // Replace with your region's endpoint
            ForcePathStyle = true
        };

        return Task.FromResult(new AmazonS3Client(configuration.AccessKeyId, configuration.SecretAccessKey, config));
    }
}
```

This ensures all **S3 requests are correctly routed** to DigitalOcean Spaces.

---

## Step 4: Define a Blob Storage Container

To store files in DigitalOcean Spaces, create a **blob container**:

```csharp
using Volo.Abp.BlobStoring;

[BlobContainerName("abp-digital-ocean")]
public class DigitalOceanContainer {}
```

## Step 5: Upload Files to DigitalOcean Spaces

Inject the **blob container** into your service and start uploading files:

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

Once your integration is set up, you can **retrieve, delete, and manage files** easily:

**Get a file:**  
```csharp
var fileBytes = await BlobContainer.GetAllBytesAsync("hi/hello-world.txt");
```

**Delete a file:**  
```csharp
await BlobContainer.DeleteAsync("hi/hello-world.txt");
```

For more details, check the [**official ABP documentation**](https://abp.io/docs/latest/framework/infrastructure/blob-storing)

---

## Conclusion

Using **DigitalOcean Spaces with ABP’s AWS Blob Storing Provider** is straightforward because DigitalOcean is S3-compatible. If ABP’s built-in AWS integration does not support setting `ServiceURL`, replacing the **Amazon S3 Client Factory** ensures seamless integration.

By following this guide, you can **fully integrate DigitalOcean Spaces** with ABP’s Blob Storing system and use it just like AWS S3! 🚀
