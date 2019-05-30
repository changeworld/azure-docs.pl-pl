---
title: Podaj HttpClient i serwera proxy (platformy MSAL.NET) | Azure
description: Naucz się, podając własne HttpClient i serwera proxy do łączenia z usługą Azure AD przy użyciu Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET).
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
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ab8a85ecc5649352382469e09d7dfd83a5ddfa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305719"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Dostarczanie własnego HttpClient i serwer proxy przy użyciu platformy MSAL.NET
Gdy [inicjowania aplikacji publicznych klienta](msal-net-initializing-client-applications.md), możesz użyć `.WithHttpClientFactory method` zapewnienie własnego HttpClient.  Dostarczanie własnego HttpClient umożliwia zaawansowanych scenariuszy takich precyzyjne kontrolowanie serwera proxy HTTP, dostosowywanie nagłówków agenta użytkownika lub wymuszanie biblioteki MSAL do użycia określonego HttpClient (na przykład w aplikacji sieci web platformy ASP.NET Core/API).

## <a name="initialize-with-httpclientfactory"></a>Zainicjuj przy użyciu HttpClientFactory
Poniższy przykład pokazuje, aby utworzyć `HttpClientFactory` i następnie Zainicjowanie aplikacji klienckiej publicznych z nią:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient i platformy Xamarin dla systemu iOS
Korzystając z platformy Xamarin iOS, zalecane jest utworzenie `HttpClient` jawnie używającej `NSURLSession`— na podstawie programu obsługi dla systemu iOS 7 i nowszych. Automatycznie tworzy platformy MSAL.NET `HttpClient` , który używa `NSURLSessionHandler` dla systemu iOS 7 i nowszych. Aby uzyskać więcej informacji, przeczytaj [Xamarin iOS dokumentacji HttpClient](/xamarin/cross-platform/macios/http-stack).