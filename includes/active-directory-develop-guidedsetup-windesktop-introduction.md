---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: d7f268618888eb1f3fcd98b13b67635d9e2d056d
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988209"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Wywoływanie interfejsu API programu Microsoft Graph z aplikacji pulpitu Windows

Ten przewodnik przedstawia, jak natywną aplikację .NET Desktop Windows (XAML) można uzyskać token dostępu i wywołania interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego v2.0 usługi Azure Active Directory.

Po zakończeniu przewodnika aplikacji będzie do wywoływania chronionego interfejsu API, który używa konta osobiste (w tym outlook.com, live.com i inne). Aplikacja będzie również używać Praca kont służbowych z firmy lub organizacji, która używa usługi Azure Active Directory.  

> [!NOTE]
> Przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017. Nie masz jedna z tych wersji? [Pobierz bezpłatnie program Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Jak działa ten przewodnik](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Przykładowej aplikacji, utworzonej za pomocą tego przewodnika umożliwia aplikacji Windows Desktop, który odpytuje interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego v2.0 usługi Azure Active Directory. W tym scenariuszu należy dodać token do żądań HTTP za pomocą nagłówka autoryzacji. Microsoft Authentication Library (MSAL) obsługuje uzyskanie tokenu i odnawiania.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa uzyskanie tokenu do uzyskiwania dostępu do chronionego interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token, który może służyć do wykonywania zapytań interfejsu API Microsoft Graph i interfejsu API sieci Web, która jest zabezpieczony przez usługi Azure Active Directory w wersji 2.

Interfejsy API, takich jak program Microsoft Graph wymaga tokenu, aby zezwolić na dostęp do określonych zasobów. Na przykład token jest wymagany odczytuj profil użytkownika, dostęp do kalendarza użytkownika lub Wyślij wiadomość e-mail. Aplikację można żądać tokenu dostępu, przy użyciu biblioteki MSAL dostępu do tych zasobów, określając zakresy interfejsu API. Ten token dostępu jest dodawane do nagłówka autoryzacji HTTP, za każde wywołanie skierowanego do chronionego zasobu.

Biblioteka MSAL zarządza buforowanie i odświeżanie tokenów dostępu, dzięki czemu Twoja aplikacja nie wymaga.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używane są następujące pakiety NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|
