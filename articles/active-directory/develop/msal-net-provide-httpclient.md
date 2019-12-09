---
title: Udostępnianie HttpClient i serwera proxy (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zapewnić własne HttpClient i serwer proxy do łączenia się z usługą Azure AD przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7b9a8d90c505cb93b6cf237ed67e9c47d9be0bb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915591"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Udostępnianie własnych HttpClient i proxy za pomocą MSAL.NET
Podczas [inicjowania publicznej aplikacji klienckiej](msal-net-initializing-client-applications.md)można użyć `.WithHttpClientFactory method`, aby zapewnić własne HttpClient.  Udostępnienie własnych HttpClient umożliwia zaawansowane scenariusze, takie jak precyzyjne sterowanie serwerem proxy HTTP, Dostosowywanie nagłówków agentów użytkowników lub wymuszanie MSAL przy użyciu określonego HttpClient (na przykład w ASP.NET Core aplikacje/interfejsy API sieci Web).

## <a name="initialize-with-httpclientfactory"></a>Inicjowanie za pomocą HttpClientFactory
Poniższy przykład pokazuje, aby utworzyć `HttpClientFactory` a następnie zainicjować publiczną aplikację kliencką:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient i Xamarin iOS
W przypadku korzystania z platformy Xamarin iOS zaleca się utworzenie `HttpClient`, która jawnie używa programu obsługi opartego na `NSURLSession`dla systemu iOS 7 lub nowszego. MSAL.NET automatycznie tworzy `HttpClient`, który używa `NSURLSessionHandler` dla systemu iOS 7 lub nowszego. Aby uzyskać więcej informacji, Przeczytaj [dokumentację platformy Xamarin dla systemu iOS dla HttpClient](/xamarin/cross-platform/macios/http-stack).