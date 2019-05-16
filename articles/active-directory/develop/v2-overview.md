---
title: Omówienie platformy (w wersji 2.0) tożsamości firmy Microsoft — Azure
description: Informacje o punkcie końcowym platformy (w wersji 2.0) tożsamości firmy Microsoft i platformy.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd3a1525d7b1f9c0a0b6516014ea83fb8abd0376
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544826"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Przegląd usługi Microsoft identity platformy (w wersji 2.0)

Platforma tożsamości firmy Microsoft to unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które Zaloguj wszystkich tożsamości firmy Microsoft i uzyskać tokeny do wywołania APIs firmy Microsoft, takich jak program Microsoft Graph lub interfejsów API, które programiści. Platforma Microsoft identity składa się z:

- **Usługa CLS standard uwierzytelniania OAuth 2.0 i OpenID Connect** umożliwiającej deweloperom uwierzytelniania dowolnej tożsamości firmy Microsoft, w tym:
  - Konta służbowe (dostarczanymi za pośrednictwem usługi Azure AD)
  - Osobistych kont Microsoft (np. Skype, Xbox i Outlook.com)
  - Kont społecznościowych lub lokalnym (za pośrednictwem usługi Azure AD B2C)
- **Biblioteki typu open-source**: Biblioteki uwierzytelniania firmy Microsoft (MSAL) i pomoc techniczna dla innych bibliotek zgodnych ze standardami
- **Portal zarządzania aplikacji**: Środowisko rejestracji i konfiguracji utworzone w witrynie Azure portal oraz wszystkich innych możliwości zarządzania platformy Azure.
- **Konfiguracja aplikacji interfejsu API i programu PowerShell**: co umożliwia konfigurację programistyczną aplikacji za pośrednictwem interfejsu REST API (Microsoft Graph i Azure Active Directory Graph 1.6) i programu PowerShell, dzięki czemu można zautomatyzować zadania metodyki DevOps.
- **Zawartość dla deweloperów**: koncepcyjne i odwoływać się do dokumentacji, przykładów Szybki Start, przykłady kodu, samouczki i przewodniki z instrukcjami.

Dla deweloperów platforma tożsamości usługi Microsoft oferuje bezproblemowej integracji innowacje w obszarze tożsamości i zabezpieczeń, takie jak uwierzytelnianie bez hasła, stopniowo rosnące wymaganie dotyczące uwierzytelniania i dostępu warunkowego.  Nie trzeba samodzielnie zaimplementowania takich funkcji: aplikacji zintegrowanych z platformą Microsoft identity natywnie korzystać z zalet takie innowacje.

Dzięki platformie tożsamości firmy Microsoft można napisać kod raz i dotrzeć do każdego użytkownika. Można utworzyć aplikację po i jego pracy na wielu platformach, lub utworzyć aplikację, która działa jako klient, jak i zasobów aplikacji (API).

## <a name="getting-started"></a>Wprowadzenie

Praca z tożsamościami nie musi być trudna. Wybierz scenariusz, który ma zastosowanie do Ciebie — każda ścieżka scenariusz ma Szybki Start i strona przeglądu, aby rozpocząć pracę w ciągu kilku minut:

- [Tworzenie aplikacji jednostronicowej](scenario-spa-overview.md)
- [Tworzenie aplikacji internetowej logujący się użytkownicy](scenario-web-app-sign-user-overview.md)
- [Tworzenie aplikacji sieci web, która wywołuje interfejsy API sieci web](scenario-web-app-call-api-overview.md)
- [Tworzenie chronionego internetowego interfejsu API](scenario-protected-web-api-overview.md)
- [Tworzenie internetowego interfejsu API, który wywołuje interfejsy API sieci web](scenario-web-api-call-api-overview.md)
- [Tworzenie aplikacji klasycznej](scenario-desktop-overview.md)
- [Tworzenie aplikacji demona](scenario-daemon-overview.md)
- [Zbuduj aplikację mobilną](scenario-mobile-overview.md)

Poniższej tabeli przedstawiono typowe scenariusze uwierzytelniania — użycie jako odniesienia podczas integrowania platforma tożsamości firmy Microsoft z aplikacją.

[![Scenariusze aplikacji na platformie tożsamości firmy Microsoft](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat podstawowych pojęć uwierzytelniania, zalecamy rozpoczęcie od tych tematów:

- [Informacje podstawowe o uwierzytelnianiu](authentication-scenarios.md)
- [Aplikacji i jednostki usługi](app-objects-and-service-principals.md)
- [grupy odbiorców](v2-supported-account-types.md)
- [Uprawnienia i zgody](v2-permissions-and-consent.md)
- [Tokeny Identyfikatora](id-tokens.md) i [tokeny dostępu](access-tokens.md)

Tworzenie aplikacji ilością danych, która wywołuje [programu Microsoft Graph](https://docs.microsoft.com/graph/overview).

Kiedy wszystko będzie gotowe uruchomić aplikację do **środowiska produkcyjnego**, przejrzyj następujące najlepsze rozwiązania:

- [Włącz rejestrowanie](msal-logging.md) w aplikacji.
- Włącz telemetrię w aplikacji.
- Włącz [serwery proxy i dostosowywanie klientów HTTP](msal-net-provide-httpclient.md).
- Testowanie integracji usługi wykonując [Microsoft tożsamości platformy integracji z listy kontrolnej](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli masz planowania tworzenia aplikacji przeznaczonych dla klientów, logujący się tożsamości społecznościowych i lokalnych, Przyjrzyj się [Omówienie usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).