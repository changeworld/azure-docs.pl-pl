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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121590"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Wywoływanie interfejsu API programu Microsoft Graph z aplikacji pulpitu Windows

Ten przewodnik pokazuje, jak natywne Windows Desktop .NET (XAML) aplikacja może uzyskiwanie tokenu dostępu i wywołania interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają dostępu tokenów z platformą Microsoft identity dla punktu końcowego v2.0 deweloperów (dawniej usługa Azure AD).

Po zakończeniu przewodnika aplikacji będzie do wywoływania chronionego interfejsu API, który używa konta osobiste (w tym outlook.com, live.com i inne). Aplikacja będzie również używać Praca kont służbowych z firmy lub organizacji, która używa usługi Azure Active Directory.  

> [!NOTE]
> Przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017. Nie masz jedna z tych wersji? [Pobierz bezpłatnie program Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Przedstawia, jak przykładowa aplikacja wygenerowany przez ta działa samouczki](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Przykładowej aplikacji, utworzonej za pomocą tego przewodnika umożliwia aplikacji Windows Desktop, który odpytuje interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu należy dodać token do żądań HTTP za pomocą nagłówka autoryzacji. Microsoft Authentication Library (MSAL) obsługuje uzyskanie tokenu i odnawiania.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa uzyskanie tokenu do uzyskiwania dostępu do chronionego interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token, który może służyć do wykonywania zapytań interfejsu API Microsoft Graph i interfejsu API sieci Web, która jest zabezpieczony przez platforma tożsamości firmy Microsoft dla deweloperów.

Interfejsy API, takich jak program Microsoft Graph wymaga tokenu, aby zezwolić na dostęp do określonych zasobów. Na przykład token jest wymagany odczytuj profil użytkownika, dostęp do kalendarza użytkownika lub Wyślij wiadomość e-mail. Aplikację można żądać tokenu dostępu, przy użyciu biblioteki MSAL dostępu do tych zasobów, określając zakresy interfejsu API. Ten token dostępu jest dodawane do nagłówka autoryzacji HTTP, za każde wywołanie skierowanego do chronionego zasobu.

Biblioteka MSAL zarządza buforowanie i odświeżanie tokenów dostępu, dzięki czemu Twoja aplikacja nie wymaga.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używane są następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka Microsoft Authentication (platformy MSAL.NET)|
