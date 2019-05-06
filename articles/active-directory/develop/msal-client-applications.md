---
title: Aplikacje klienckie (Microsoft Authentication Library) | Azure
description: Dowiedz się więcej o publicznych klienta i poufne klienta aplikacji w programie Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077143"
---
# <a name="public-client-and-confidential-client-applications"></a>Publicznych klienta i poufne klienta aplikacji
Microsoft Authentication Library (MSAL) definiuje dwa typy klientów: publiczny klientów i poufne. Typy dwóch klientów są rozróżniane na podstawie zdolności do bezpiecznego uwierzytelniania za pomocą serwera autoryzacji i zachowania poufności ich poświadczeń klienta.  Z kolei usługa Azure AD Authentication Library (ADAL) korzysta z koncepcji kontekstu uwierzytelniania, (czyli połączenia z usługą Azure AD).

- **Aplikacje klienckie poufne** są aplikacje, które są uruchamiane na serwerach (aplikacje sieci Web, interfejs API sieci Web lub nawet aplikacje demona/usługi). Są one traktowane jako trudno dostęp i w związku z tym zapewnić utrzymywanie wpis tajny aplikacji. Umożliwia przechowywanie kluczy tajnych w czasie konfiguracji są klientami poufnymi. Każde wystąpienie klienta mają różne konfiguracje (w tym identyfikatora klienta oraz klucz tajny). Te wartości są trudne dla użytkowników końcowych do wyodrębnienia. Aplikacja sieci web jest najczęściej poufne klienta. Identyfikator klienta jest dostępna za pośrednictwem przeglądarki sieci web, ale klucz tajny jest przekazany tylko kanału zwrotnego i nigdy nie są bezpośrednio widoczne.

    Aplikacje poufne klienta: <BR>
    ![Aplikacja sieci Web](media/msal-client-applications/web-app.png) ![interfejsu API sieci Web](media/msal-client-applications/web-api.png) ![demona/usługi](media/msal-client-applications/daemon-service.png)

- **Aplikacje klienckie publicznych** są aplikacje, które są uruchamiane na urządzeniach i komputerach stacjonarnych lub w przeglądarce sieci web. Nie są one zaufane, można bezpiecznie przechowywać klucze tajne aplikacji, a w związku z tym tylko dostęp do interfejsów API sieci Web w imieniu użytkownika (tylko obsługują przepływów publicznych klienta). Klienci będą mogli przechowywania wpisów tajnych w czasie konfiguracji, a w rezultacie mieć nie klucza tajnego klienta.

    Aplikacje klienckie publiczny: <BR>
    ![Aplikacja klasyczna](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![aplikacji mobilnej](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> MSAL.js istnieje bez separacji aplikacje klienckie poufnych i publicznych.  MSAL.js reprezentuje aplikacje klienckie, jak w przypadku aplikacje oparte na agentach użytkownika publicznych klienta, w którym kod klienta jest wykonywane w agenta użytkownika, takie jak przeglądarki sieci web.  Tacy klienci nie należy przechowywać klucze tajne, ponieważ kontekst przeglądarki jest dostępna w sposób jawny.

## <a name="comparing-the-client-types"></a>Porównywanie typów klienta
Istnieją pewne commonalities i różnice między klientem publicznym i poufne klienta aplikacji:

- Oba rodzaje aplikacji obsługuje pamięć podręczną tokenu użytkownika i można uzyskać token w trybie dyskretnym (w przypadkach, gdy token jest już w pamięci podręcznej tokenu). Aplikacje poufne klienta mają również tokenu pamięć podręczna rozwiązania app tokenów, które są do samej aplikacji.
- Zarówno Zarządzanie kontami użytkowników i można uzyskać kont z pamięci podręcznej tokenu użytkownika, uzyskać konto z jego identyfikator lub usunąć konto.
- Aplikacje klienckie publiczny ma cztery sposoby pobierania tokenu (cztery przepływów uwierzytelniania), natomiast poufne klienta aplikacji mają trzy (i punktu końcowego autoryzacji w jednej metody do obliczenia adres URL dostawcy tożsamości). Aby uzyskać więcej informacji, zobacz scenariusze i uzyskiwania tokenów.

Jeśli używasz biblioteki ADAL w przeszłości, można zauważyć, że sprzecznie kontekstu uwierzytelniania ADAL firmy, w MSAL klienta, którego identyfikator (zwaną również identyfikator aplikacji lub identyfikator aplikacji) jest przekazywany jeden raz w konstrukcji aplikacji, a nie znajduje się już musi być powtórzone podczas uzyskiwania tokenu. Dotyczy to zarówno dla aplikacji klienckich publicznych i poufne. Konstruktory poufne klienta aplikacji również są przekazywane poświadczenia klienta: klucz tajny są współużytkowane z dostawcy tożsamości.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej:
- [Opcje konfiguracji aplikacji klienta](msal-client-application-configuration.md)
- [Utworzenie wystąpienia aplikacji klienckich przy użyciu platformy MSAL.NET](msal-net-initializing-client-applications.md).
- [Utworzenie wystąpienia aplikacji klienckich za pomocą MSAL.js](msal-js-initializing-client-applications.md).
