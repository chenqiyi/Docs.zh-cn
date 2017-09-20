---
title: "密钥存储提供程序"
author: rick-anderson
description: "密钥存储提供程序"
keywords: "加密，ASP.NET 核心"
ms.author: riande
manager: wpickett
ms.date: 01/14/2017
ms.topic: article
ms.assetid: 423e0a79-2f34-44c4-aaf3-146a53c39251
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 0d604c1aab1b7bad5b9f5c7d21edf12e8db7c563
ms.sourcegitcommit: 67f54fabbfa4e3942f5bfe1f8a7fdfe4a7a75358
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2017
---
# <a name="key-storage-providers"></a><span data-ttu-id="632c5-104">密钥存储提供程序</span><span class="sxs-lookup"><span data-stu-id="632c5-104">Key storage providers</span></span>

<a name=data-protection-implementation-key-storage-providers></a>

<span data-ttu-id="632c5-105">默认情况下，数据保护系统[使用启发式方法](../configuration/default-settings.md#data-protection-default-settings)以确定应将在其中保留加密的密钥材料。</span><span class="sxs-lookup"><span data-stu-id="632c5-105">By default the data protection system [employs a heuristic](../configuration/default-settings.md#data-protection-default-settings) to determine where cryptographic key material should be persisted.</span></span> <span data-ttu-id="632c5-106">开发人员可以重写启发式方法，并手动指定的位置。</span><span class="sxs-lookup"><span data-stu-id="632c5-106">The developer can override the heuristic and manually specify the location.</span></span>

> [!NOTE]
> <span data-ttu-id="632c5-107">如果您指定一个显式的密钥保持位置，将取消注册数据保护系统在 rest 机制启发式方法提供的默认密钥加密，以便密钥将不再加密对静止。</span><span class="sxs-lookup"><span data-stu-id="632c5-107">If you specify an explicit key persistence location, the data protection system will deregister the default key encryption at rest mechanism that the heuristic provided, so keys will no longer be encrypted at rest.</span></span> <span data-ttu-id="632c5-108">建议你此外[指定显式密钥加密机制](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers)对于生产应用程序。</span><span class="sxs-lookup"><span data-stu-id="632c5-108">It is recommended that you additionally [specify an explicit key encryption mechanism](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest-providers) for production applications.</span></span>

<span data-ttu-id="632c5-109">数据保护系统附带了几个内置密钥存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="632c5-109">The data protection system ships with several in-box key storage providers.</span></span>

## <a name="file-system"></a><span data-ttu-id="632c5-110">文件系统</span><span class="sxs-lookup"><span data-stu-id="632c5-110">File system</span></span>

<span data-ttu-id="632c5-111">我们预计很多应用程序将使用的文件基于系统的密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="632c5-111">We anticipate that many apps will use a file system-based key repository.</span></span> <span data-ttu-id="632c5-112">若要此配置，调用[PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs)配置例程如下所示。</span><span class="sxs-lookup"><span data-stu-id="632c5-112">To configure this, call the [PersistKeysToFileSystem](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) configuration routine as shown below.</span></span> <span data-ttu-id="632c5-113">提供`DirectoryInfo`指向应在其中存储密钥的存储库。</span><span class="sxs-lookup"><span data-stu-id="632c5-113">Provide a `DirectoryInfo` pointing to the repository where keys should be stored.</span></span>

```csharp
sc.AddDataProtection()
       // persist keys to a specific directory
       .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
   ```

<span data-ttu-id="632c5-114">`DirectoryInfo`可以指向本地计算机上的目录或它可以指向网络共享上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="632c5-114">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="632c5-115">如果指向本地计算机上的目录 （和的方案是，只有本地计算机上的应用程序将需要使用此存储库），请考虑使用[Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)来加密存放的密钥。</span><span class="sxs-lookup"><span data-stu-id="632c5-115">If pointing to a directory on the local machine (and the scenario is that only applications on the local machine will need to use this repository), consider using [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt the keys at rest.</span></span> <span data-ttu-id="632c5-116">否则，请考虑使用[X.509 证书](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)来加密存放的密钥。</span><span class="sxs-lookup"><span data-stu-id="632c5-116">Otherwise consider using an [X.509 certificate](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-and-redis"></a><span data-ttu-id="632c5-117">Azure 和 Redis</span><span class="sxs-lookup"><span data-stu-id="632c5-117">Azure and Redis</span></span>

<span data-ttu-id="632c5-118">`Microsoft.AspNetCore.DataProtection.AzureStorage`和`Microsoft.AspNetCore.DataProtection.Redis`包允许将你的数据保护密钥存储在 Azure 存储空间或 Redis 缓存。</span><span class="sxs-lookup"><span data-stu-id="632c5-118">The `Microsoft.AspNetCore.DataProtection.AzureStorage` and `Microsoft.AspNetCore.DataProtection.Redis` packages allow storing your data protection keys in Azure Storage or a Redis cache.</span></span> <span data-ttu-id="632c5-119">可以在 web 应用程序的多个实例之间共享密钥。</span><span class="sxs-lookup"><span data-stu-id="632c5-119">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="632c5-120">你的 ASP.NET Core 应用可以跨多个服务器共享身份验证 cookie 或 CSRF 保护。</span><span class="sxs-lookup"><span data-stu-id="632c5-120">Your ASP.NET Core app can share authentication cookies or CSRF protection across multiple servers.</span></span> <span data-ttu-id="632c5-121">若要在 Azure 上配置，调用之一[PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs)重载，如下所示。</span><span class="sxs-lookup"><span data-stu-id="632c5-121">To configure on Azure, call one of the [PersistKeysToAzureBlobStorage](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.AzureStorage/AzureDataProtectionBuilderExtensions.cs) overloads as shown below.</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));

    services.AddMvc();
}
```

<span data-ttu-id="632c5-122">另请参阅[Azure 测试代码](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs)。</span><span class="sxs-lookup"><span data-stu-id="632c5-122">See also the [Azure test code](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/AzureBlob/Program.cs).</span></span>

<span data-ttu-id="632c5-123">若要在 Redis 上配置，调用之一[PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs)重载，如下所示。</span><span class="sxs-lookup"><span data-stu-id="632c5-123">To configure on Redis, call one of the [PersistKeysToRedis](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection.Redis/RedisDataProtectionBuilderExtensions.cs) overloads as shown below.</span></span>

```
public void ConfigureServices(IServiceCollection services)
{
    // Connect to Redis database.
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");

    services.AddMvc();
}
```

<span data-ttu-id="632c5-124">有关详细信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="632c5-124">See the following for more information:</span></span>

- [<span data-ttu-id="632c5-125">StackExchange.Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="632c5-125">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
- [<span data-ttu-id="632c5-126">Azure Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="632c5-126">Azure Redis Cache</span></span>](https://docs.microsoft.com/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
- <span data-ttu-id="632c5-127">[Redis 测试代码](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs)。</span><span class="sxs-lookup"><span data-stu-id="632c5-127">[Redis test code](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/samples/Redis/Program.cs).</span></span>

## <a name="registry"></a><span data-ttu-id="632c5-128">注册表</span><span class="sxs-lookup"><span data-stu-id="632c5-128">Registry</span></span>

<span data-ttu-id="632c5-129">有时应用程序可能没有写到文件系统的访问权限。</span><span class="sxs-lookup"><span data-stu-id="632c5-129">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="632c5-130">请考虑其中作为是虚拟服务帐户 （如 w3wp.exe 的应用程序池标识） 运行应用程序的方案。</span><span class="sxs-lookup"><span data-stu-id="632c5-130">Consider a scenario where an app is running as a virtual service account (such as w3wp.exe's app pool identity).</span></span> <span data-ttu-id="632c5-131">在这些情况下，管理员可能已设置为服务帐户标识的相应 ACLed 的注册表项。</span><span class="sxs-lookup"><span data-stu-id="632c5-131">In these cases, the administrator may have provisioned a registry key that is appropriate ACLed for the service account identity.</span></span> <span data-ttu-id="632c5-132">调用[PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs)配置例程如下所示。</span><span class="sxs-lookup"><span data-stu-id="632c5-132">Call the [PersistKeysToRegistry](https://github.com/aspnet/DataProtection/blob/rel/1.1.0/src/Microsoft.AspNetCore.DataProtection/DataProtectionBuilderExtensions.cs) configuration routine as shown below.</span></span> <span data-ttu-id="632c5-133">提供`RegistryKey`指向存储加密密钥/值的位置。</span><span class="sxs-lookup"><span data-stu-id="632c5-133">Provide a `RegistryKey` pointing to the location where cryptographic keys/values should be stored.</span></span>

```csharp
   sc.AddDataProtection()
       // persist keys to a specific location in the system registry
       .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
   ```

<span data-ttu-id="632c5-134">如果作为持久性机制使用系统注册表，请考虑使用[Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest)来加密存放的密钥。</span><span class="sxs-lookup"><span data-stu-id="632c5-134">If you use the system registry as a persistence mechanism, consider using [Windows DPAPI](key-encryption-at-rest.md#data-protection-implementation-key-encryption-at-rest) to encrypt the keys at rest.</span></span>

## <a name="custom-key-repository"></a><span data-ttu-id="632c5-135">自定义密钥存储库</span><span class="sxs-lookup"><span data-stu-id="632c5-135">Custom key repository</span></span>

<span data-ttu-id="632c5-136">如果不适合的内置机制，开发人员可以通过提供自定义指定自己的密钥保持机制`IXmlRepository`。</span><span class="sxs-lookup"><span data-stu-id="632c5-136">If the in-box mechanisms are not appropriate, the developer can specify their own key persistence mechanism by providing a custom `IXmlRepository`.</span></span>