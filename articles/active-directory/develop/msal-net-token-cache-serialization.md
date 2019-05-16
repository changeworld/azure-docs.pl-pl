---
title: Token serializacji pamięci podręcznej w Biblioteka Microsoft Authentication Library dla platformy .NET | Azure
description: Informacje o serializacji i serializacja klienta pamięci podręcznej tokenu przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a4c4ca1925a501b10cb86a2cf60646af1e5b57
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544249"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializacja pamięci podręcznej tokenu w platformy MSAL.NET
Po [token jest uzyskiwana](msal-acquire-cache-tokens.md), jest ona buforowana przez Microsoft Authentication Library (MSAL).  Kod aplikacji, należy spróbować pobrać tokenu z pamięci podręcznej przed pobierania tokenu przy użyciu innej metody.  W tym artykule omówiono domyślnej i niestandardowej serializacji pamięci podręcznej tokenu w platformy MSAL.NET.

Ten artykuł jest przeznaczony dla platformy MSAL.NET 3.x. Jeśli interesuje Cię platformy MSAL.NET 2.x, zobacz [tokenu z pamięci podręcznej serializacji w platformy MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serializacji domyślny dla platform urządzeń przenośnych

W platformy MSAL.NET domyślnie znajduje się w pamięci podręcznej tokenu. Serializacja jest udostępniana domyślnie dla platform, w których bezpiecznego magazynu jest dostępna dla użytkownika w ramach platformy. Jest to w przypadku uniwersalnych platformy Windows (UWP), Xamarin.iOS i Xamarin.Android.

> [!Note]
> W przypadku migracji projektu Xamarin.Android z platformy MSAL.NET 1.x do platformy MSAL.NET 3.x, możesz zechcieć dodać `android:allowBackup="false"` do projektu, aby uniknąć tokenów usługi po wdrożeniami programu Visual Studio wyzwalanie przywracania w lokalnej pamięci masowej w pamięci podręcznej stary. Zobacz [wystawiać #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Niestandardowej serializacji dla Windows aplikacji klasycznych i sieci web apps/interfejsów API sieci web

Należy pamiętać, że w niestandardowej serializacji nie jest dostępny na platformach mobilnych (platformy UWP, Xamarin.iOS i Xamarin.Android). Biblioteka MSAL już definiuje bezpieczny i wydajny mechanizm serializacji dla tych platform. .NET — aplikacje klasyczne i aplikacje .NET Core, ale mają różne architektury i Biblioteka MSAL nie może zaimplementować mechanizm serializacji ogólnego przeznaczenia. Na przykład witryn sieci web może wybrać do przechowywania tokenów w pamięci podręcznej Redis lub aplikacje klasyczne magazynu tokenów w zaszyfrowanym pliku. Dlatego serializacji nie podano poza pole. Aby mieć gotową aplikację do trwałego pamięci podręcznej tokenu w klasycznych na platformie .NET i .NET Core, musisz dostosować serializacji.

Następujące klasy i interfejsy są używane w pamięci podręcznej tokenu serializacji:

- `ITokenCache`, który definiuje zdarzenia do subskrybowania żądania serializacji w pamięci podręcznej tokenu, a także metody, aby serializować lub deserializować pamięci podręcznej w różnych formatach (v3.0 biblioteki ADAL, biblioteka MSAL 2.x i Biblioteka MSAL 3.x = v5.0 biblioteki ADAL).
- `TokenCacheCallback` Wywołanie zwrotne przechodzi do zdarzenia, aby można było obsłużyć serializacji. Zostanie wywołana z argumentami typu `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` zapewnia tylko `ClientId` aplikacji i odwołania do użytkownika, dla którego token jest dostępna.

  ![Diagram klas](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> Platformy MSAL.NET tworzy token pamięci podręczne i zapewnia `IToken` pamięci podręcznej podczas wywoływania aplikacji `GetUserTokenCache` i `GetAppTokenCache` metody. Nie powinien implementować interfejs samodzielnie. Odpowiedzialność, podczas implementowania serializacji niestandardowej pamięci podręcznej tokenu, jest:
> - Reagowanie na `BeforeAccess` i `AfterAccess` "miara events". `BeforeAccess` Delegat jest odpowiedzialna deserializować pamięci podręcznej, natomiast `AfterAccess` jeden jest odpowiedzialny za serializacji w pamięci podręcznej.
> - Części te zdarzenia magazynu lub obiektów blob, które są przekazywane za pośrednictwem argumentu zdarzenia można niezależnie od magazynu, które chcesz załadować.

Strategie różnią się w zależności od Jeśli piszesz serializacji pamięci podręcznej tokenu dla [aplikacja kliencka publicznych](msal-client-applications.md) (wersja klasyczna), lub [poufne klienta aplikacji](msal-client-applications.md)) (aplikacja internetowa / interfejs API, sieci web aplikacji demona ).

### <a name="token-cache-for-a-public-client"></a>Pamięć podręczną tokenu dla publicznych klienta 

Ponieważ v2.x platformy MSAL.NET masz kilka opcji serializacji pamięci podręcznej tokenu publicznych klienta. Może wykonywać serializację pamięci podręcznej tylko do formatu platformy MSAL.NET (jednolitym formacie pamięci podręcznej jest wspólna dla platformy i biblioteki MSAL).  Może również obsługiwać [starszych](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token serializacji pamięci podręcznej biblioteki ADAL V3.

Dostosowywanie serializacji pamięci podręcznej tokenu, aby udostępniać pojedynczy stanu logowania jednokrotnego między ADAL.NET 3.x, ADAL.NET 5.x i platformy MSAL.NET zostało wyjaśnione w części z poniższego przykładu: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Format pamięci podręcznej tokenu 1.1.4-preview platformy MSAL.NET nie jest już obsługiwana w MSAL 2.x. W przypadku aplikacji korzystających z platformy MSAL.NET 1.x, użytkownicy będą mieli do ponownie-konta logowania. Alternatywnie jest obsługiwana migracja z biblioteki ADAL 4.x (i 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serializacja prostą pamięci podręcznej tokenu (tylko MSAL)

Poniżej przedstawiono przykład z implementacją naiwni niestandardowej serializacji pamięci podręcznej tokenu dla aplikacji klasycznych. W tym miejscu pamięci podręcznej tokenu użytkownika jest plikiem, w tym samym folderze co aplikacja.

Po skompilowaniu aplikacji włączyć serializacji przez wywołanie `TokenCacheHelper.EnableSerialization()` metody i przekazanie aplikacji `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` Pomocnika klasa jest zdefiniowana jako:

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

Podgląd serializatora plikowym pamięci podręcznej tokenu jakości produktu dla aplikacji klienckich publicznej (dla aplikacji klasycznych, systemem Windows, Mac i Linux) jest dostępny z [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Biblioteka typu open source. Można dołączyć go w swoich aplikacjach z następujący pakiet nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Podwójna pamięci podręcznej tokenu serializacji (MSAL unified pamięci podręcznej i biblioteki ADAL v3)

Jeśli chcesz zaimplementować pamięci podręcznej tokenu serializacji zarówno w formacie ujednoliconego pamięci podręcznej (często ADAL.NET 4.x, platformy MSAL.NET 2.x i innych MSALs generacji odpowiadającej generacji lub starsze, na jednej platformie), zapoznaj się z następującego kodu:

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

Tym razem klasy pomocnika, określony jako:

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
  /// Get the user token cache
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
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Pamięć podręczną tokenu dla aplikacji sieci web (aplikacja poufne klienta)

W aplikacji internetowych lub internetowych interfejsów API pamięci podręcznej może korzystać z sesji, usługi Redis cache lub bazy danych.

Ważne jest, aby pamiętać to, że dla aplikacji sieci web i interfejsów API sieci web, powinien istnieć jeden pamięci podręcznej tokenu dla użytkownika (na konto). Należy serializować pamięci podręcznej tokenu dla każdego konta.

Przykłady dotyczące używania pamięci podręczne tokenu dla aplikacji sieci web i interfejsy API sieci web są dostępne w [samouczek aplikacji sieci web platformy ASP.NET Core](https://ms-identity-aspnetcore-webapp-tutorial) w fazie [pamięci podręcznej tokenu 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). W celu implementacji mają przyjrzeć się następującym folderze [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) w [microsoft uwierzytelniania rozszerzenia do dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteki (w [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) folderu. 

## <a name="next-steps"></a>Kolejne kroki
Poniższe przykłady ilustrują serializacji pamięci podręcznej tokenu.

| Sample | Platforma | Opis|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Pulpitu (WPF) | Aplikacja Windows .NET klasycznych (WPF), wywołanie interfejsu API programu Microsoft Graph. ![Topologia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Pulpitu (Konsola) | Zestaw rozwiązań programu Visual Studio, pokazujący migracji aplikacji w wersji 1.0 usługi Azure AD (przy użyciu ADAL.NET) do aplikacji usługi Azure AD w wersji 2.0, o nazwie zbieżne aplikacje (przy użyciu platformy MSAL.NET), w szczególności [tokenu migracji pamięci podręcznej](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|