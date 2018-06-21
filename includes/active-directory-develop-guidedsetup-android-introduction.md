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
ms.openlocfilehash: e25c8d97698d7a61501094a457174202322d146f
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36297953"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Logowania użytkowników i wywołania interfejsu API programu Microsoft Graph aplikacji systemu Android

W tym przewodniku pokazano, jak natywnych aplikacji systemu Android mogą pobrać token dostępu i wywołania interfejsu API programu Graph firmy Microsoft lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego usługi Azure Active Directory w wersji 2.

Po zakończeniu przewodnika, aplikacja będzie mogła akceptować logowania konta osobiste (takie jak outlook.com, live.com i inne) oraz kont służbowych z firmy lub organizacji, która używa usługi Azure Active Directory. Aplikacja zostanie następnie wywołać interfejs API, który jest chroniony przez punkt końcowy usługi Azure Active Directory w wersji 2.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik
![Jak działa w tym przykładzie](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

Przykładowej aplikacji utworzonej za pomocą tego przewodnika opiera się na scenariuszu, w których aplikacja systemu Android służy do interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego usługi Azure Active Directory w wersji 2 (Microsoft Graph API, w tym przypadku). W tym scenariuszu aplikacja dodaje nabytych przez niego token na żądania HTTP za pośrednictwem nagłówek autoryzacji. Biblioteka uwierzytelniania firmy Microsoft (MSAL) obsługuje nabycia tokenu i odnawiania dla Ciebie.

## <a name="prerequisites"></a>Wymagania wstępne
* Ten przewodnik instalacji koncentruje się na Android Studio, ale dopuszczalne jest również inne środowiska programowania aplikacji dla systemu Android. 
* Zestaw SDK systemu android 21 lub nowszy jest wymagany (zalecane 25 zestawu SDK).
* Google Chrome lub przeglądarki sieci web, która korzysta kart niestandardowych jest wymagany dla tej wersji programu MSAL dla systemu Android.

> [!NOTE]
> Google Chrome nie jest dołączony do programu Visual Studio Emulator for Android. Zaleca się przetestowanie kod na emulatorze z interfejsu API 25 lub obraz z interfejsu API 21 lub nowszej mającej Google Chrome zainstalowane.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Obsługa tokenów nabycia do uzyskiwania dostępu do chronionego interfejsów API sieci Web

Po uwierzytelnieniu użytkownika Przykładowa aplikacja odbiera token dostępu, który może służyć do badania interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który jest chroniony przez usługę Azure Active Directory w wersji 2.

Interfejsy API, takich jak Microsoft Graph wymagają tokenu dostępu, aby zezwolić na dostęp do określonych zasobów. Na przykład token dostępu jest wymagany do odczytu profilu użytkownika, dostęp do kalendarza użytkownika lub Wyślij wiadomość e-mail. Aplikacja może zażądać tokenu dostępu przy użyciu MSAL dostępu do tych zasobów, określając zakresy interfejsu API. Ten token dostępu jest dodawane do nagłówka HTTP autoryzacji dla każdego wywołania utworzona przed chronionego zasobu. 

MSAL zarządza buforowanie i odświeżanie tokenów dostępu, dzięki czemu nie trzeba aplikacji.

## <a name="libraries"></a>Biblioteki

W tym przewodniku korzysta z bibliotek następujące:

|Biblioteka|Opis|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteka uwierzytelniania firmy Microsoft (MSAL)|
