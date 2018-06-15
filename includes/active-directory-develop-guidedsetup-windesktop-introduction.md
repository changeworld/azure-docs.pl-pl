---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 79b6b8e5d81d3885f9c125f971f3e32e695cf2b1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32200619"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Wywoływanie Microsoft Graph API z pulpitu aplikacji systemu Windows

W tym przewodniku pokazano, jak natywnych aplikacji .NET pulpitu systemu Windows (XAML) można uzyskać token dostępu i wywołania interfejsu API programu Graph firmy Microsoft lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego usługi Azure Active Directory w wersji 2.

Po zakończeniu przewodnika aplikacji będzie można wywołać chronionego interfejsu API, który używa konta osobiste (w tym outlook.com, live.com i inne). Aplikacja będzie również używać Praca kont służbowych z firmy lub organizacji, która używa usługi Azure Active Directory.  

> [!NOTE] 
> Przewodnik wymaga programu Visual Studio 2015 Update 3 lub Visual Studio 2017 r.  Nie masz żadnego z tych wersji? [Bezpłatnie pobrać Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Jak działa w tym przewodniku](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

Przykładowej aplikacji utworzonej za pomocą tego przewodnika umożliwia aplikacji pulpitu systemu Windows, który odpytuje interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego usługi Azure Active Directory w wersji 2. W tym scenariuszu należy dodać do żądań HTTP za pośrednictwem nagłówek uwierzytelnienia tokenu. Biblioteka uwierzytelniania firmy Microsoft (MSAL) obsługuje nabycia tokenu i odnawiania.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa tokenów nabycia do uzyskiwania dostępu do chronionego interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token, który może służyć do badania interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który jest chroniony przez usługę Azure Active Directory w wersji 2.

Interfejsy API, takich jak Microsoft Graph wymagają token, aby zezwolić na dostęp do określonych zasobów. Na przykład token jest wymagany do odczytu profilu użytkownika, dostęp do kalendarza użytkownika lub Wyślij wiadomość e-mail. Aplikacja może zażądać tokenu dostępu przy użyciu MSAL dostępu do tych zasobów, określając zakresy interfejsu API. Ten token dostępu jest dodawane do nagłówka HTTP autoryzacji dla każdego wywołania utworzona przed chronionego zasobu. 

MSAL zarządza buforowanie i odświeżanie tokenów dostępu, dzięki czemu nie trzeba aplikacji.

## <a name="nuget-packages"></a>Pakiety NuGet

W tym przewodniku używa następujących pakietów NuGet:

|Biblioteka|Opis|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Biblioteka uwierzytelniania firmy Microsoft (MSAL)|

