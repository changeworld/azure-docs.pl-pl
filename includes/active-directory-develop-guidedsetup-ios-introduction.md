---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: ace6c36f1bc4582b3210c049a4ff8cb1f770bf88
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203543"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Wywołanie interfejsu API programu Microsoft Graph z poziomu aplikacji dla systemu iOS

W tym przewodniku przedstawiono, jak aplikacji natywnych dla systemów iOS (Swift) może wywołać interfejsy API, które wymagają tokenów dostępu z punktu końcowego v2.0 Microsoft Azure Active Directory (Azure AD). Przewodnik wyjaśnia, jak uzyskiwanie tokenów dostępu i używać ich w wywołaniach interfejsu API programu Microsoft Graph i innych interfejsów API.

Po wykonaniu ćwiczeń opisanych w tym przewodniku, aplikacja może wywoływać interfejs API chroniony z firmy lub organizacji, która ma usługę Azure AD. Aplikację można utworzyć chronionych wywołań interfejsu API przy użyciu konta służbowego lub szkolnego, a także kont osobistych, takich jak outlook.com, live.com i innych.

## <a name="prerequisites"></a>Wymagania wstępne

- XCode w wersji 10.x jest wymagany dla przykładu, który jest tworzony w tym przewodniku. Możesz pobrać środowisko XCode ze [witryny sieci Web w programie iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adres URL pobierania w środowisku XCode").
- [Carthage](https://github.com/Carthage/Carthage) Menedżera zależności jest wymagana do zarządzania pakietami.

## <a name="how-this-guide-works"></a>Jak działa ten przewodnik

![Przedstawia, jak przykładowa aplikacja wygenerowany przez ta działa samouczki](media/active-directory-develop-guidedsetup-ios-introduction/iosintro-updated.png)

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
