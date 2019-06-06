---
title: Aplikacje klienckie (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej o publicznych klienta i poufne klienta aplikacji w programie Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430830"
---
# <a name="public-client-and-confidential-client-applications"></a>Publicznych klienta i poufne klienta aplikacji
Microsoft Authentication Library (MSAL) definiuje dwa typy klientów: publiczny klientów i poufne. Typy dwóch klientów są rozróżniane na podstawie zdolności do bezpiecznego uwierzytelniania za pomocą serwera autoryzacji i zachowania poufności ich poświadczeń klienta. Z kolei używa usługi Azure AD Authentication Library (ADAL) tzw *kontekstu uwierzytelniania* (co ma połączenie z usługą Azure AD).

- **Aplikacje klienckie poufne** są aplikacje, które działają na serwerach (aplikacje sieci web, aplikacje interfejsu API sieci Web lub nawet aplikacji demona/usługi). Były uważane za trudna, aby uzyskać dostęp do i z tego powodu zapewnić utrzymywanie wpis tajny aplikacji. Klientami poufnymi mogą przechowywać wpisy tajne czasu konfiguracji. Każde wystąpienie klienta mają różne konfiguracje (w tym identyfikator klienta oraz klucz tajny klienta). Te wartości są trudne dla użytkowników końcowych do wyodrębnienia. Aplikacja sieci web jest najczęściej poufne klienta. Identyfikator klienta jest dostępna za pośrednictwem przeglądarki sieci web, ale klucz tajny jest przekazany tylko kanału zwrotnego i nigdy nie są bezpośrednio widoczne.

    Aplikacje poufne klienta: <BR>
    ![Aplikacja sieci Web](media/msal-client-applications/web-app.png) ![interfejsu API sieci Web](media/msal-client-applications/web-api.png) ![demona/usługi](media/msal-client-applications/daemon-service.png)

- **Aplikacje klienckie publicznych** są aplikacje, które działają na urządzeniach lub komputerów stacjonarnych lub w przeglądarce sieci web. Nie są one zaufane, aby bezpiecznie przechowywać kluczy tajnych aplikacji, dzięki czemu mogą uzyskać dostęp tylko do interfejsów API sieci Web w imieniu użytkownika. (Obsługiwane są też tylko przepływy publicznych klienta). Klienci nie mogą pomieścić czasu konfiguracji wpisów tajnych oraz ich, aby nie musieli wpisów tajnych klienta.

    Aplikacje klienckie publiczny: <BR>
    ![Aplikacja klasyczna](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![aplikacji mobilnej](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js istnieje bez separacji aplikacje klienckie poufnych i publicznych.  MSAL.js przedstawia aplikacje klienckie jako użytkownika agenta aplikacji opartych na klientów publicznych, w których kod klienta jest wykonywane w agenta użytkownika, takie jak przeglądarki sieci web. Tacy klienci nie przechowuj wpisów tajnych, ponieważ kontekst przeglądarki jest dostępna w sposób jawny.

## <a name="comparing-the-client-types"></a>Porównywanie typów klienta
Poniżej przedstawiono pewne podobieństwa i różnice między klientem publicznym i poufne klienta aplikacji:

- Oba rodzaje aplikacji obsługuje pamięć podręczną tokenu użytkownika i można uzyskać token w trybie dyskretnym (gdy token jest już w pamięci podręcznej tokenu). Aplikacje poufne klienta mają również aplikacji pamięci podręcznej tokenu dla tokenów, które służą do samej aplikacji.
- Oba rodzaje aplikacji Zarządzanie kontami użytkowników i można uzyskać konto z pamięci podręcznej tokenu użytkownika, uzyskać konto z jego identyfikator lub usunąć konto.
- Aplikacje klienckie publiczny ma cztery sposoby na uzyskanie tokenu (cztery przepływów uwierzytelniania). Aplikacje poufne klienta mają trzy sposoby, aby uzyskać token (i punktu końcowego autoryzacji w jednym ze sposobów obliczenia adres URL dostawcy tożsamości). Aby uzyskać więcej informacji, zobacz [uzyskiwania tokenów](msal-acquire-cache-tokens.md).

Jeśli używano biblioteki ADAL, użytkownik może należy zauważyć, że w przeciwieństwie do kontekstu uwierzytelniania ADAL firmy, w MSAL identyfikator klienta (nazywanych również *identyfikator aplikacji* lub *Identyfikatora aplikacji*) jest przekazywany jeden raz w konstrukcji aplikacji. Nie musi on być przekazywany ponownie, gdy aplikacja uzyskuje token. Jest to istotne zarówno w przypadku aplikacji poufnych i publicznych klienta. Konstruktory poufne klienta aplikacji również są przekazywane poświadczenia klienta: klucz tajny są współużytkowane z dostawcy tożsamości.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej:
- [Opcje konfiguracji aplikacji klienta](msal-client-application-configuration.md)
- [Utworzenie wystąpienia aplikacji klienckich przy użyciu platformy MSAL.NET](msal-net-initializing-client-applications.md)
- [Utworzenie wystąpienia aplikacji klienckich za pomocą MSAL.js](msal-js-initializing-client-applications.md)
