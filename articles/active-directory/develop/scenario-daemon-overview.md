---
title: Tworzenie aplikacji demona, która wywołuje internetowe interfejsy API - platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje internetowe interfejsy API
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
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5718a23e5669de6ba16354a718d72b68d14bbf49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78894548"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenariusz: Aplikacja demona, która wywołuje internetowe interfejsy API

Dowiedz się wszystkiego, czego potrzebujesz, aby utworzyć aplikację demona, która wywołuje internetowe interfejsy API.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Omówienie

Aplikacja może uzyskać token do wywoływania internetowego interfejsu API w imieniu samym (nie w imieniu użytkownika). Ten scenariusz jest przydatny w aplikacjach demonów. Używa standardowej dotacji [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0.

![Aplikacje demona](./media/scenario-daemon-app/daemon-app.svg)

Oto kilka przykładów przypadków użycia aplikacji demonów:

- Aplikacje sieci Web używane do inicjowania obsługi administracyjnej lub administrowania użytkownikami lub wykonywania procesów wsadowych w katalogu
- Aplikacje klasyczne (takie jak usługi Windows w systemie Windows lub procesy demonów w systemie Linux), które wykonują zadania wsadowe lub usługa systemu operacyjnego działająca w tle
- Interfejsy API sieci Web, które muszą manipulować katalogami, a nie określonymi użytkownikami

Istnieje inny typowy przypadek, w którym aplikacje niebędące demonami używają poświadczeń klienta: nawet jeśli działają w imieniu użytkowników, muszą uzyskać dostęp do internetowego interfejsu API lub zasobu pod własną tożsamością ze względów technicznych. Przykładem jest dostęp do wpisów tajnych w usłudze Azure Key Vault lub bazy danych SQL platformy Azure dla pamięci podręcznej.

Aplikacje, które nabywają token dla własnych tożsamości:

- Są poufne aplikacje klienckie. Te aplikacje, biorąc pod uwagę, że uzyskują dostęp do zasobów niezależnie od użytkowników, muszą udowodnić swoją tożsamość. Są to również dość poufne aplikacje. Muszą one zostać zatwierdzone przez administratorów dzierżawy usługi Azure Active Directory (Azure AD).
- Zarejestrowałeś klucz tajny (hasło aplikacji lub certyfikat) w usłudze Azure AD. Ten klucz tajny jest przekazywany podczas wywołania usługi Azure AD, aby uzyskać token.

## <a name="specifics"></a>Specyfiki

> [!IMPORTANT]
>
> - Użytkownicy nie mogą wchodzić w interakcje z aplikacją demona. Aplikacja demona wymaga własnej tożsamości. Ten typ aplikacji żąda tokenu dostępu przy użyciu jego tożsamości aplikacji i prezentacji jego identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i identyfikatora aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demon odbiera token dostępu (i token odświeżania) z punktu końcowego platformy tożsamości firmy Microsoft. Ten token jest następnie używany do wywoływania internetowego interfejsu API (i jest odświeżany w razie potrzeby).
> - Ponieważ użytkownicy nie mogą wchodzić w interakcje z aplikacjami demonów, przyrostowa zgoda nie jest możliwa. Wszystkie wymagane uprawnienia interfejsu API muszą być skonfigurowane podczas rejestracji aplikacji. Kod aplikacji po prostu żąda statycznie zdefiniowanych uprawnień. Oznacza to również, że aplikacje demonów nie będą obsługiwać przyrostowej zgody.

Dla deweloperów kompleksowe środowisko dla tego scenariusza ma następujące aspekty:

- Aplikacje demonów mogą działać tylko w dzierżawach usługi Azure AD. Nie ma sensu tworzyć aplikacji demona, która próbuje manipulować kontami osobistymi Microsoft. Jeśli jesteś deweloperem aplikacji biznesowej (LOB), utworzysz aplikację demona w dzierżawie. Jeśli jesteś niezależną instytucją życiorysową, możesz utworzyć wielodożliwą aplikację demona. Każdy administrator dzierżawy będzie musiał wyrazić zgodę.
- Podczas [rejestracji aplikacji](./scenario-daemon-app-registration.md)identyfikator URI odpowiedzi nie jest potrzebny. Musisz udostępnić wpisy tajne lub certyfikaty lub podpisane potwierdzenia z usługą Azure AD. Należy również poprosić o uprawnienia aplikacji i udzielić zgody administratora na korzystanie z tych uprawnień aplikacji.
- [Konfiguracja aplikacji](./scenario-daemon-app-configuration.md) musi dostarczyć poświadczenia klienta udostępnione za pomocą usługi Azure AD podczas rejestracji aplikacji.
- [Zakres](scenario-daemon-acquire-token.md#scopes-to-request) używany do uzyskiwania tokenu z przepływem poświadczeń klienta musi być zakresem statycznym.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja Daemon - rejestracja aplikacji](./scenario-daemon-app-registration.md)
