---
title: Demon aplikacji wywoływania interfejsów API sieci web (omówienie) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona, który wywołuje interfejsy API sieci web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075883"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenariusz: Demon aplikacja wywołuje interfejsy API sieci web

Dowiedz się, wszystko, czego potrzebujesz do tworzenia aplikacji demona, który wywołuje interfejsy API sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Omówienie

Aplikację można uzyskać tokenu służącego do wywoływania interfejsu API sieci web imieniu sam (nie w imieniu użytkownika). Ten scenariusz jest przydatne w przypadku aplikacji demona. Używa standardowego protokołu OAuth 2.0 [poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md) udzielić.

![Aplikacje demona](./media/scenario-daemon-app/daemon-app.svg)

Poniżej przedstawiono kilka przykładów przypadki użycia dla aplikacji demona:

- Aplikacje sieci Web, które są używane do aprowizacji lub zarządzać użytkownikami lub batch procesów w katalogu
- Aplikacje klasyczne, takich jak (usługi systemu windows na Windows) lub demonów procesy w systemie Linux, wykonujących zadania wsadowe lub usługa systemu operacyjnego, który jest uruchomiona w tle
- Interfejsy API, które potrzeba manipulowania katalogów nie określonych użytkowników w sieci Web

Istnieje inny często zdarza, gdzie aplikacje inne niż demona używać poświadczeń klienta: nawet wtedy, gdy ich działanie w imieniu użytkowników, muszą uzyskać dostęp do interfejsu API sieci web lub zasobów za pomocą tożsamości usługi przyczyn technicznych. Przykładem jest dostęp do wpisów tajnych usługi KeyVault lub Azure SQL database na potrzeby pamięci podręcznej.

Aplikacje, które uzyskać token dla własnej tożsamości:

- są poufne klienta aplikacji. Te aplikacje, biorąc pod uwagę, że mogą uzyskiwać dostęp do zasobów, niezależnie od użytkownika, należy potwierdzić swoją tożsamość. Są one też raczej w przypadku poufnych aplikacji, które muszą zostać zatwierdzone przez administratorów dzierżawy usługi Azure Active Directory (Azure AD).
- Zarejestrowano wpis tajny (hasło aplikacji lub certyfikat) z usługą Azure AD. Ten klucz tajny jest przekazywane podczas wywołania do usługi Azure AD w celu pobrania tokenu.

## <a name="specifics"></a>Szczegółowe informacje

> [!IMPORTANT]
>
> - Interakcja użytkownika nie jest możliwe za pomocą aplikacji demona. Aplikacji demona wymaga własnej tożsamości. Aplikacje tego typu żądania tokenu dostępu przy użyciu tożsamości aplikacji i zaprezentowanie jej identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i aplikacji identyfikator URI do usługi Azure AD. Po pomyślnym uwierzytelnieniu demona odbiera token dostępu (i token odświeżania) z punktu końcowego platformy tożsamości firmy Microsoft, w której jest następnie używany do wywoływania interfejsu API sieci web (i odświeżane zgodnie z potrzebami).
> - Ponieważ interakcja użytkownika nie jest możliwe, przyrostowe zgody nie będzie możliwe. Wszystkich wymaganych uprawnień interfejsu API, które muszą być skonfigurowane na rejestrowanie aplikacji i kodu aplikacji po prostu żąda statycznie zdefiniowanych uprawnień. Oznacza to, że aplikacje demona nie obsługuje przyrostowych zgody.

Dla deweloperów środowisko end-to-end dla tego scenariusza zawiera następujące aspekty:

- Demon aplikacji może pracować tylko w dzierżawach usługi Azure AD. Go nie miałoby sensu do tworzenia aplikacji demona, który podejmie próbę manipulowania osobistych kont Microsoft. Jeśli jesteś deweloperem aplikacji line-of-business (LOB), utworzysz aplikację demona w dzierżawie. Jeśli jesteś niezależnym dostawcą oprogramowania, można utworzyć aplikację wielodostępną demona. Musisz wyrazić przez każdego administratora dzierżawy.
- Podczas [Rejestracja aplikacji](./scenario-daemon-app-registration.md), **identyfikator URI odpowiedzi** nie jest wymagane. Musisz udostępnić klucze tajne i certyfikaty z usługą Azure AD, a następnie należy zażądać uprawnień aplikacji i udzielić zgody administratora, aby użyć tych uprawnień aplikacji.
- [Konfiguracji aplikacji](./scenario-daemon-app-configuration.md) wymaga podania poświadczeń klienta jako udostępniony z usługą Azure AD podczas rejestracji aplikacji.
- [Zakres](scenario-daemon-acquire-token.md#scopes-to-request) używane w celu uzyskania tokenu przy użyciu poświadczeń klienta przepływ musi być z zakresu statycznego.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Demon aplikacji — rejestrowanie aplikacji](./scenario-daemon-app-registration.md)
