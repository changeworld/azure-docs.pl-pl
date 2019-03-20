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
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5a1ff10901d10fb274a6fa1418f04e1f20113cb7
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203603"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET

Ten przewodnik demonstruje sposób implementacji logowania z firmą Microsoft przy użyciu rozwiązania platformy ASP.NET MVC przy użyciu tradycyjnych aplikacji sieci web opartej na przeglądarce przy użyciu protokołu OpenID Connect.

Na końcu tego przewodnika aplikacja będzie akceptować logowania konta osobiste (w tym outlook.com, live.com i inne) oraz pracy i służbowego konta z firmy lub organizacji, która została zintegrowana z usługą Azure Active Directory.

> Ten przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017.  Nie masz tego programu?  [Pobierz bezpłatnie program Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Przedstawia, jak przykładowa aplikacja wygenerowany przez ta działa samouczki](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral-updated.png)

Przykładowa aplikacja utworzona w tym przewodniku opiera się na scenariuszu, gdy użytkownik korzysta przeglądarki, aby dostęp do żądania użytkownika do uwierzytelniania za pomocą przycisku logowania w witrynie internetowej ASP.NET. W tym scenariuszu większość działań związanych z renderowaniem strony internetowej odbywa się po stronie serwera.

## <a name="libraries"></a>Biblioteki

Ten przewodnik korzysta z następujących bibliotek:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które umożliwia aplikacji wykorzystywanie do uwierzytelniania protokołu OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, które umożliwia aplikacji obsługę sesji użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umożliwia uruchamianie aplikacji zgodnych ze standardem OWIN w ramach usług IIS przy użyciu potoku żądań programu ASP.NET|
