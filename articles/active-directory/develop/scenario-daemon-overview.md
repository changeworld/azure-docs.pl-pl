---
title: Aplikacja demona wywołująca interfejsy API sieci Web (omówienie) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae0912203f2427694d2a9b8611966a55e1e6889e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056396"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenariusz: Aplikacja demona, która wywołuje interfejsy API sieci Web

Dowiedz się, co należy zrobić, aby utworzyć aplikację demona, która wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Omówienie

Aplikacja może uzyskać token wywołujący internetowy interfejs API w imieniu samego siebie (nie w imieniu użytkownika). Ten scenariusz jest przydatny w przypadku aplikacji demona. Korzysta ona z przydzielenia [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) uwierzytelniania OAuth 2,0.

![Aplikacje demona](./media/scenario-daemon-app/daemon-app.svg)

Oto przykładowe przypadki użycia dla aplikacji demonów:

- Aplikacje sieci Web, które są używane do aprowizacji lub administrowania użytkownikami, lub do procesów wsadowych w katalogu
- Aplikacje klasyczne (takie jak usługi systemu Windows w systemie Windows lub demony procesów na komputerze z systemem Linux) wykonujące zadania wsadowe lub usługę systemu operacyjnego działającą w tle
- Interfejsy API sieci Web, które wymagają manipulowania katalogami, a nie określonymi użytkownikami

Istnieje inny typowy przypadek, w którym aplikacje niedemona używają poświadczeń klienta: nawet jeśli działają w imieniu użytkowników, muszą uzyskać dostęp do internetowego interfejsu API lub zasobu z ich tożsamością ze względów technicznych. Przykładem jest dostęp do wpisów tajnych w magazynie kluczy lub w bazie danych SQL Azure dla pamięci podręcznej.

Aplikacje, które uzyskują token dla własnych tożsamości:

- Są poufnymi aplikacjami klienckimi. Te aplikacje, z uwzględnieniem, że uzyskują dostęp do zasobów niezależnie od użytkownika, muszą udowodnić swoją tożsamość. Są one również raczej poufnymi aplikacjami, które muszą zostać zatwierdzone przez administratorów dzierżawy usługi Azure Active Directory (Azure AD).
- Zarejestrowano wpis tajny (hasło aplikacji lub certyfikat) w usłudze Azure AD. Ten wpis tajny jest przesyłany w trakcie wywołania usługi Azure AD w celu uzyskania tokenu.

## <a name="specifics"></a>Szczegółowych informacji

> [!IMPORTANT]
>
> - Interakcja użytkownika nie jest możliwa w przypadku aplikacji demona. Aplikacja demona wymaga własnej tożsamości. Ten typ aplikacji żąda tokenu dostępu przy użyciu tożsamości aplikacji i prezentuje identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i identyfikator URI aplikacji do usługi Azure AD. Po pomyślnym uwierzytelnieniu demon otrzymuje token dostępu (i token odświeżania) z punktu końcowego platformy tożsamości firmy Microsoft, który jest następnie używany do wywoływania internetowego interfejsu API (i jest odświeżany w razie potrzeby).
> - Ponieważ Interakcje użytkownika nie są możliwe, nie będzie możliwe wyrażenie przyrostowe. Wszystkie wymagane uprawnienia interfejsu API muszą zostać skonfigurowane podczas rejestracji aplikacji, a kod aplikacji tylko żąda uprawnień zdefiniowanych statycznie. Oznacza to również, że aplikacje demona nie obsługują przyrostowej zgody.

Dla deweloperów kompleksowe środowisko dla tego scenariusza ma następujące aspekty:

- Aplikacje demonów mogą funkcjonować tylko w dzierżawach usługi Azure AD. Nie ma sensu Tworzenie aplikacji demona próbującej manipulować kontami osobistymi firmy Microsoft. Jeśli jesteś deweloperem aplikacji biznesowych (LOB), utworzysz aplikację demona w dzierżawie. Jeśli jesteś niezależnym dostawcą oprogramowania, możesz chcieć utworzyć aplikację demona z wieloma dzierżawcami. Musi on być uznawany przez każdego administratora dzierżawy.
- Podczas [rejestracji aplikacji](./scenario-daemon-app-registration.md)nie jest wymagany **Identyfikator URI odpowiedzi** . Musisz udostępnić wpisy tajne lub certyfikaty lub potwierdzenia z usługą Azure AD, a także zażądać uprawnień aplikacji i przyznać administratorowi zgodę na korzystanie z tych uprawnień aplikacji.
- [Konfiguracja aplikacji](./scenario-daemon-app-configuration.md) wymaga podania poświadczeń klienta jako współużytkowanych z usługą Azure AD podczas rejestracji aplikacji.
- [Zakres](scenario-daemon-acquire-token.md#scopes-to-request) używany do uzyskiwania tokenu z przepływem poświadczeń klienta musi być zakresem statycznym.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — Rejestracja aplikacji](./scenario-daemon-app-registration.md)
