---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: bc439cbe5e690077213b5d7953a4e74488988c3b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988548"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Dodawanie logowania z firmą Microsoft do aplikacji sieci web platformy ASP.NET

Ten przewodnik demonstruje sposób implementacji logowania z firmą Microsoft przy użyciu rozwiązania platformy ASP.NET MVC przy użyciu tradycyjnych aplikacji sieci web opartej na przeglądarce przy użyciu protokołu OpenID Connect.

Na końcu tego przewodnika aplikacja będzie akceptować logowania konta osobiste (w tym outlook.com, live.com i inne) oraz pracy i służbowego konta z firmy lub organizacji, która została zintegrowana z usługą Azure Active Directory.

> Ten przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017.  Nie masz tego programu?  [Pobierz bezpłatnie program Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Jak działa ten przewodnik](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.png)

Przykładowa aplikacja utworzona w tym przewodniku opiera się na scenariuszu, gdy użytkownik korzysta przeglądarki, aby dostęp do żądania użytkownika do uwierzytelniania za pomocą przycisku logowania w witrynie internetowej ASP.NET. W tym scenariuszu większość działań związanych z renderowaniem strony internetowej odbywa się po stronie serwera.

## <a name="libraries"></a>Biblioteki

Ten przewodnik korzysta z następujących bibliotek:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Oprogramowanie pośredniczące, które umożliwia aplikacji wykorzystywanie do uwierzytelniania protokołu OpenIdConnect|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Oprogramowanie pośredniczące, które umożliwia aplikacji obsługę sesji użytkownika przy użyciu plików cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Umożliwia uruchamianie aplikacji zgodnych ze standardem OWIN w ramach usług IIS przy użyciu potoku żądań programu ASP.NET|
