---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482331"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET

Ten przewodnik demonstruje sposób implementacji logowania z firmą Microsoft przy użyciu rozwiązania platformy ASP.NET MVC przy użyciu tradycyjnych aplikacji sieci web opartej na przeglądarce przy użyciu protokołu OpenID Connect.

Na końcu tego przewodnika będzie mógł akceptować logowania konta osobiste, na przykład outlook.com, live.com i innych aplikacji. Te konta również uwzględnia niezbędne prace naprawcze kont służbowych z firmy lub organizacji, która została zintegrowana z usługą Azure Active Directory.

> Ten przewodnik wymaga programu Visual Studio 2019 r.  Nie masz tego programu?  [Pobierz bezpłatnie program Visual Studio 2019 r.](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Pokazuje, jak działa przykładowej aplikacji wygenerowanych w ramach tego samouczka](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

Przykładowej aplikacji, które możesz utworzyć opiera się na scenariuszu, gdzie użyj przeglądarki, aby otworzyć witrynę sieci web platformy ASP.NET, żądający na uwierzytelnianie za pomocą przycisku logowania. W tym scenariuszu większość działań związanych z renderowaniem strony internetowej odbywa się po stronie serwera.

## <a name="libraries"></a>Biblioteki

Ten przewodnik korzysta z następujących bibliotek:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które umożliwia aplikacji wykorzystywanie do uwierzytelniania protokołu OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, które umożliwia aplikacji obsługę sesji użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umożliwia uruchamianie aplikacji zgodnych ze standardem OWIN w ramach usług IIS przy użyciu potoku żądań programu ASP.NET|
