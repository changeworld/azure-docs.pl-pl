---
title: Podaj serwer proxy & HttpClient (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o dostarczaniu własnych usług HttpClient i serwera proxy do łączenia się z usługą Azure AD przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695057"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Zapewnienie własnego HttpClient i proxy za pomocą MSAL.NET
Podczas [inicjowania publicznej aplikacji klienckiej](msal-net-initializing-client-applications.md)można użyć `.WithHttpClientFactory method` do podania własnego httpclient.  Zapewnienie własnego httpclient umożliwia zaawansowane scenariusze, takie szczegółowe kontroli serwera proxy HTTP, dostosowywanie nagłówków agenta użytkownika lub zmuszanie MSAL do korzystania z określonego HttpClient (na przykład w ASP.NET Core aplikacji sieci web/ interfejsów API).

## <a name="initialize-with-httpclientfactory"></a>Inicjowanie za pomocą protokołu HttpClientFactory
Poniższy przykład pokazuje, `HttpClientFactory` aby utworzyć, a następnie zainicjować publiczną aplikację kliencką z nim:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient i Xamarin iOS
Podczas korzystania z systemu Xamarin iOS, zaleca się `HttpClient` `NSURLSession`utworzenie, który jawnie używa obsługi opartej na systemach iOS 7 i nowszych. MSAL.NET automatycznie `HttpClient` tworzy, który używa `NSURLSessionHandler` dla systemu iOS 7 i nowsze. Aby uzyskać więcej informacji, przeczytaj [dokumentację systemu Xamarin dla systemu iOS dla protokołu HttpClient](/xamarin/cross-platform/macios/http-stack).