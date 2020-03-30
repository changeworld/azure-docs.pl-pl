---
title: Serializacja pamięci podręcznej tokenów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o serializacji i serializacji pamięci podręcznej tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1bd348ad27d892d0421b13c16ce81bc4f5dfb021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262804"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializacja pamięci podręcznej tokenów w MSAL.NET
Po [nabyciu tokenu](msal-acquire-cache-tokens.md)jest on buforowany przez bibliotekę uwierzytelniania firmy Microsoft (MSAL).  Kod aplikacji należy spróbować uzyskać token z pamięci podręcznej przed uzyskaniem tokenu za pomocą innej metody.  W tym artykule omówiono domyślną i niestandardową serializację pamięci podręcznej tokenów w MSAL.NET.

Ten artykuł dotyczy MSAL.NET 3.x. Jeśli jesteś zainteresowany MSAL.NET 2.x, zobacz [Serializacja pamięci podręcznej tokenów w MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Domyślna serializacja dla platform mobilnych

W MSAL.NET domyślnie udostępniana jest pamięć podręczna tokenów w pamięci. Serializacja jest domyślnie dostarczana dla platform, na których bezpieczna pamięć masowa jest dostępna dla użytkownika jako część platformy. Dotyczy to platformy uniwersalnej systemu Windows (PLATFORMY UNIWERSALNEJ), Platformy Xamarin.iOS i platformy Xamarin.Android.

> [!Note]
> Podczas migracji projektu platformy Xamarin.Android z MSAL.NET 1.x do MSAL.NET 3.x, można `android:allowBackup="false"` dodać do projektu, aby uniknąć starych tokenów buforowanych z powrotem, gdy wdrożenia programu Visual Studio wyzwalają przywracanie magazynu lokalnego. Zobacz [#659 problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Niestandardowa serializacja dla aplikacji klasycznych systemu Windows i aplikacji internetowych/internetowych interfejsów API

Pamiętaj, że niestandardowa serializacja nie jest dostępna na platformach mobilnych (UWP, Xamarin.iOS i Xamarin.Android). MSAL już definiuje bezpieczny i wydajny mechanizm serializacji dla tych platform. Aplikacje .NET desktop i .NET Core mają jednak różne architektury i msal nie może zaimplementować mechanizmu serializacji ogólnego przeznaczenia. Na przykład witryny sieci web mogą zdecydować się na przechowywanie tokenów w pamięci podręcznej Redis lub tokeny magazynu aplikacji klasycznych w zaszyfrowanym pliku. Serializacja nie jest więc dostarczana po wyjęciu z pudełka. Aby mieć trwałą aplikację pamięci podręcznej tokenu na pulpicie platformy .NET lub .NET Core, należy dostosować serializację.

Następujące klasy i interfejsy są używane w serializacji pamięci podręcznej tokenów:

- `ITokenCache`, który definiuje zdarzenia do subskrybowania żądań serializacji pamięci podręcznej tokenów, a także metody serializacji lub de-serializacji pamięci podręcznej w różnych formatach (ADAL v3.0, MSAL 2.x i MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback`jest wywołaniem zwrotnym przekazanym do zdarzeń, dzięki czemu można obsługiwać serializacji. Będą one wywoływane z `TokenCacheNotificationArgs`argumentami typu .
- `TokenCacheNotificationArgs`zawiera `ClientId` tylko aplikacji i odwołanie do użytkownika, dla którego token jest dostępny.

  ![Diagram klas](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET tworzy pamięć podręczną tokenu dla Ciebie `IToken` i zapewnia pamięć podręczną podczas wywoływania aplikacji `UserTokenCache` i `AppTokenCache` właściwości. Nie należy samodzielnie implementować interfejsu. Odpowiedzialność, podczas implementowania serializacji niestandardowej pamięci podręcznej tokenu, jest:
> - Reaguj na `BeforeAccess` i `AfterAccess` "wydarzenia" (lub ich asynowe smaki). Pełnomocnik `BeforeAccess` jest odpowiedzialny za deserializacji pamięci `AfterAccess` podręcznej, podczas gdy jeden jest odpowiedzialny za serializacji pamięci podręcznej.
> - Część tych zdarzeń przechowywania lub ładowania obiektów blob, które są przekazywane przez argument zdarzenia do dowolnego magazynu, który chcesz.

Strategie różnią się w zależności od tego, czy piszesz serializacji pamięci podręcznej tokenu dla [publicznej aplikacji klienckiej](msal-client-applications.md) (pulpit), lub [poufnej aplikacji klienckiej](msal-client-applications.md)) (aplikacja internetowa / internetowy interfejs API, aplikacja demona).

### <a name="token-cache-for-a-public-client"></a>Pamięć podręczna tokenu dla klienta publicznego 

Od MSAL.NET wersji 2.x masz kilka opcji serializacji pamięci podręcznej tokenu klienta publicznego. Pamięć podręczną można serializować tylko w formacie MSAL.NET (pamięć podręczna ujednoliconego formatu jest wspólna dla msal i platform).  Można również obsługiwać serializacji pamięci podręcznej [starszych](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) tokenów ADAL V3.

Dostosowywanie serializacji pamięci podręcznej tokenów w celu współużytkowania stanu logowania jednokrotnego między ADAL.NET 3.x, ADAL.NET 5.x i MSAL.NET jest wyjaśnione w części z poniższego przykładu: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Format pamięci podręcznej tokenów MSAL.NET 1.1.4-preview nie jest już obsługiwany w formacie MSAL 2.x. Jeśli masz aplikacje wykorzystujące MSAL.NET 1.x, użytkownicy będą musieli ponownie się zalogować. Alternatywnie jest obsługiwana migracja z 4.x (i 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Prosta serializacja pamięci podręcznej tokenów (tylko MSAL)

Poniżej znajduje się przykład naiwnej implementacji niestandardowej serializacji pamięci podręcznej tokenu dla aplikacji klasycznych. W tym miejscu pamięć podręczna tokenu użytkownika jest plikiem w tym samym folderze co aplikacja.

Po utworzeniu aplikacji można włączyć serializacji, `TokenCacheHelper.EnableSerialization()` wywołując metodę `UserTokenCache`i przekazując aplikację .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Klasa `TokenCacheHelper` pomocnika jest zdefiniowana jako:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Podgląd pliku pamięci podręcznej tokenu jakości produktu opartego na serializatorze dla publicznych aplikacji klienckich (dla aplikacji klasycznych działających w systemach Windows, Mac i Linux) jest dostępny w bibliotece open source [Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Można go dołączyć do aplikacji z następującego pakietu nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serializacja pamięci podręcznej dwóch tokenów (ujednolicona pamięć podręczna MSAL i ADAL v3)

Jeśli chcesz zaimplementować serializację pamięci podręcznej tokenów zarówno w formacie ujednoliconej pamięci podręcznej (typowe dla ADAL.NET 4.x, MSAL.NET 2.x, jak i innych listÓW MSALs tej samej generacji lub starszych, na tej samej platformie), zapoznaj się z następującym kodem:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tym razem klasa pomocnika zdefiniowana jako:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczna tokenu dla aplikacji sieci web (poufna aplikacja kliencka)

W aplikacjach sieci Web lub interfejsach API sieci Web pamięć podręczna może korzystać z sesji, pamięci podręcznej Redis lub bazy danych.

W aplikacjach sieci Web lub interfejsach API sieci Web zachowaj jedną pamięć podręczną tokenu na konto.  W przypadku aplikacji sieci web pamięć podręczna tokenu powinna być kluczem przez identyfikator konta.  W przypadku interfejsów API sieci web konto powinno być kluczem przez skrót tokenu używanego do wywoływania interfejsu API. MSAL.NET zapewnia niestandardową serializację pamięci podręcznej tokenów w platformach podrzędnych .NET Framework i .NET Core. Zdarzenia są uruchamiane, gdy pamięć podręczna jest dostępna, aplikacje można wybrać, czy serializacji lub deserializacji pamięci podręcznej. W poufnych aplikacjach klienckich obsługujących użytkowników (aplikacje sieci web, które logują się do użytkowników i wywołują internetowe interfejsy API, oraz internetowe interfejsy API wywołujące podrzędne interfejsy API sieci web) może istnieć wielu użytkowników, a użytkownicy są przetwarzane równolegle. Ze względów bezpieczeństwa i wydajności naszą rekomendacją jest serializowanie jednej pamięci podręcznej na użytkownika. Zdarzenia serializacji obliczają klucz pamięci podręcznej na podstawie tożsamości przetworzonego użytkownika i serializują/deserialie pamięci podręcznej tokenu dla tego użytkownika.

Przykłady używania pamięci podręcznej tokenów dla aplikacji sieci web i internetowych interfejsów API są dostępne w [samouczku ASP.NET Core aplikacji sieci web](https://ms-identity-aspnetcore-webapp-tutorial) w pamięci podręcznej tokenów fazy [2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). W przypadku implementacji zajrzyj do folderu [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) w [bibliotece microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (w folderze [Microsoft.Identity.Client.Extensions.Web.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 

## <a name="next-steps"></a>Następne kroki
Poniższe przykłady ilustrują serializacji pamięci podręcznej tokenów.

| Sample | Platforma | Opis|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Pulpit (WPF) | Aplikacja Windows Desktop .NET (WPF) wywołująca interfejs API programu Microsoft Graph. ![Topologia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Pulpit (konsola) | Zestaw rozwiązań programu Visual Studio ilustrujących migrację aplikacji usługi Azure AD w wersji 1.0 (przy użyciu ADAL.NET) do aplikacji usługi Azure AD w wersji 2.0, nazywanych również aplikacjami konwergentnymi (przy użyciu MSAL.NET), w szczególności [migracja pamięci podręcznej tokenów](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
