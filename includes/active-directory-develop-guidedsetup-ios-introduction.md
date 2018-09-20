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
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 93a1772eba291b5141a0bba4ee9a777feb51ab9e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466174"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Wywołanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu iOS

W tym przewodniku przedstawiono, jak aplikacji natywnych dla systemów iOS (Swift) może wywołać interfejsy API, które wymagają tokenów dostępu z punktu końcowego v2.0 Microsoft Azure Active Directory (Azure AD). Przewodnik wyjaśnia, jak uzyskiwanie tokenów dostępu i używać ich w wywołaniach interfejsu API programu Microsoft Graph i innych interfejsów API.

Po wykonaniu ćwiczeń opisanych w tym przewodniku, aplikacja może wywoływać interfejs API chroniony z firmy lub organizacji, która ma usługę Azure AD. Aplikację można utworzyć chronionych wywołań interfejsu API przy użyciu konta służbowego lub szkolnego, a także kont osobistych, takich jak outlook.com, live.com i innych.

## <a name="prerequisites"></a>Wymagania wstępne
- XCode w wersji 10.x jest wymagany dla przykładu, który jest tworzony w tym przewodniku. Możesz pobrać środowisko XCode ze [witryny sieci Web w programie iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adres URL pobierania w środowisku XCode").
- [Carthage](https://github.com/Carthage/Carthage) Menedżera zależności jest wymagana do zarządzania pakietami.

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Jak działa ten przewodnik](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.png)

W tym przewodniku przykładowej aplikacji umożliwia aplikacji systemu iOS do wykonywania zapytań interfejsu API programu Microsoft Graph lub internetowy interfejs API, który akceptuje tokeny od punktu końcowego v2.0 usługi Azure AD. W tym scenariuszu token zostanie dodany do żądań HTTP za pomocą **autoryzacji** nagłówka. Uzyskanie tokenu i odnawianie są obsługiwane przez Microsoft Authentication Library (MSAL).


### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Pozyskiwanie tokenu dostępu do chronionego interfejsów API sieci web

Po użytkownik jest uwierzytelniany, przykładowa aplikacja odbiera token. Token jest używany do wykonywania zapytań interfejsu API programu Microsoft Graph lub internetowy interfejs API, która jest zabezpieczony przez punktu końcowego v2.0 usługi Azure AD.

Interfejsy API, takich jak program Microsoft Graph wymaga tokenu dostępu, aby zezwolić na dostęp do określonych zasobów. Tokeny są wymagane do odczytuj profil użytkownika, dostęp użytkownika kalendarza, Wyślij wiadomość e-mail i tak dalej. Twoja aplikacja może zażądać tokenu dostępu przy użyciu biblioteki MSAL i określając zakresy interfejsu API. Token dostępu zostanie dodany do HTTP **autoryzacji** nagłówka za każde wywołanie skierowanego do chronionego zasobu.

Biblioteka MSAL zarządza buforowanie i odświeżanie tokenów dostępu, dzięki czemu Twoja aplikacja nie wymaga.


## <a name="libraries"></a>Biblioteki

W tym przewodniku używane są następujące biblioteki:

|Biblioteka|Opis|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Microsoft Authentication Library w wersji zapoznawczej dla systemu iOS|

