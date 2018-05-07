---
title: Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Ten artykuł z zalet integracji z lokalnymi, chmurze i aplikacjami SaaS usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: e05b2d515b997e769306146a5390d4d44fd5cf50
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="managing-applications-with-azure-active-directory"></a>Zarządzanie aplikacjami przy użyciu usługi Azure Active Directory
Poza rzeczywiste przepływu pracy lub zawartości przedsiębiorstwa mają dwa podstawowe wymagania dotyczące wszystkich aplikacji:

1. Aby zwiększyć wydajność, powinna być łatwe do odnalezienia i dostęp do aplikacji
2. Aby włączyć zabezpieczeń i zarządzania, organizacja musi kontroli i nadzoru, na który można i faktycznie uzyskuje dostęp do poszczególnych aplikacji

W świecie najlepiej można to osiągnąć przy użyciu tożsamości do kontroli aplikacji w chmurze "*użytkowników, którzy mają co zrobić*".

W przypadku komputerów terminologią:

* *Kto* nosi nazwę *tożsamości* — Zarządzanie użytkownikami i grupami
* *Co* nosi nazwę *dostępu administracyjnego* — Zarządzanie dostęp do chronionych zasobów

Oba te składniki są ze sobą znane jako *tożsamości i dostępu do zarządzania (IAM)*, która jest zdefiniowana przez [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) grupy jako "*dyscypliny zabezpieczeń, umożliwiający prawa osobom dostęp do zasobów w prawym rogu razy prawej ze względu na*".

OK, co to jest problem? Jeśli jest IAM *niezarządzanych* w jednym miejscu z zintegrowanego rozwiązania:

* Tożsamość Administratorzy mają indywidualnie tworzenie i aktualizowanie kont użytkowników we wszystkich aplikacjach oddzielnie, działanie nadmiarowe i czasochłonna.
* Konieczność pamiętania wiele poświadczeń dostępu do aplikacji, które są im niezbędne do pracy z. W związku z tym użytkownicy często Zapisz hasła lub użycie innych rozwiązań zarządzania hasła, które wprowadza inne zagrożenia dla bezpieczeństwa danych.
* Nadmiarowe, czasochłonne działania zmniejszyć liczbę użytkowników i administratorów pracuje działalności, które zwiększają mierzenie Twojej firmy.

Tak co uniemożliwia zazwyczaj organizacje od przyjmowania zintegrowanego rozwiązania IAM?

* Najbardziej techniczne rozwiązania są oparte na platformy oprogramowania, które muszą być wdrożone i dostosowane przez poszczególnych organizacji w celu ich własnych aplikacji.
* Aplikacje w chmurze są często stosowane poziomie wyższym niż organizacji IT można zintegrować z istniejącymi rozwiązaniami IAM.
* Monitorowanie narzędzi i zabezpieczeń wymagają dodatkowe dostosowanie i integracji w celu osiągnięcia kompleksowe E2E scenariuszy.

## <a name="azure-active-directory-integrated-with-applications"></a>Usługa Azure Active Directory jest zintegrowany z aplikacjami
Azure Active Directory jest kompleksowe tożsamość firmy Microsoft jako rozwiązaniem Service (IDaaS) który:

* Włącza IAM jako usługa w chmurze 
* Umożliwia zarządzanie dostępu, jednokrotnego (SSO) i raportowania 
* Obsługuje zarządzanie zintegrowanego dostępu dla [tysięcy aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w galerii aplikacji w tym usług Salesforce, Google Apps, pole i Concur. 

Z usługą Azure Active Directory wszystkie aplikacje publikowania dla partnerów i klientów (biznesowe lub klienta) ma taką samą tożsamość oraz dostęp do możliwości zarządzania.<br> Dzięki temu można znacznie zmniejszyć koszty operacyjne.

Co zrobić, jeśli musisz wdrożyć aplikację, która nie ma jeszcze w galerii aplikacji? Jest to nieco więcej czasu niż Konfigurowanie logowania jednokrotnego dla aplikacji w galerii aplikacji, usługa Azure AD zapewnia kreatora pomagający w konfiguracji.

Wartość usługi Azure AD wykracza poza "tak" aplikacje w chmurze. Umożliwia także go z aplikacjami lokalnymi przez zapewnienie bezpiecznego dostępu zdalnego. Bezpieczny dostęp zdalny, można wyeliminować potrzebę sieci VPN lub innych implementacji zarządzania tradycyjnych dostępu zdalnego.

Zapewniając dostępu administracyjnego i logowania jednokrotnego (SSO) dla wszystkich aplikacji, usługa Azure AD zapewnia rozwiązania problemów dotyczących zabezpieczeń i wydajności danych głównych.

* Użytkownicy mogą uzyskiwać dostęp do wielu aplikacji za pomocą jednego logowania co więcej czasu na rachunku generowania lub pracy działania operacje wykonywane.
* Tożsamość Administratorzy mogą zarządzać dostęp do aplikacji w jednym miejscu.

Korzyści dla użytkownika i firmy jest oczywiste. Spójrzmy bliższe spojrzenie na korzyści dla administratora tożsamości i organizacji.

## <a name="integrated-application-benefits"></a>Korzyści zintegrowanej aplikacji
Proces logowania jednokrotnego ma dwa kroki:

* Uwierzytelnianie, proces weryfikowania tożsamości użytkownika.
* Autoryzacja, decyzja o enable lub blokowanie dostępu do zasobów przy użyciu zasad dostępu.

Jeśli zarządzanie aplikacjami i włączenia funkcji logowania jednokrotnego przy użyciu usługi Azure AD:

* Uwierzytelnianie jest wykonywane lokalnie (np. usługi AD) użytkownika lub konta usługi Azure AD.
* Wykonuje autoryzacji dla usługi Azure AD przypisania i ochrony zasady zapewnienia spójności użytkownika końcowego i dzięki któremu można dodać przypisania, lokalizacji i warunki MFA w dowolnej aplikacji, niezależnie od jej możliwości.

Należy poznać który sposób Autoryzacja jest wprowadzany w aplikacji docelowej zależy od typu integracji aplikacji z usługą Azure AD.

* **Aplikacje wstępnie zintegrowanych przez dostawcę usług** takich jak usługi Office 365 i Azure są to aplikacje wbudowane bezpośrednio w usłudze Azure AD i zależne dla ich kompleksowe funkcje zarządzania tożsamościami i dostępem. Dostęp do tych aplikacji jest włączone za pośrednictwem informacji katalogowych i wydawania tokenów.
* **Aplikacje wstępnie zintegrowanych przez firmę Microsoft i niestandardowych aplikacji** są to aplikacje w chmurze niezależne, które zależą od katalogu aplikacji wewnętrznych i może działać niezależnie od usługi Azure AD. Dostęp do tych aplikacji jest włączone przez wystawienie aplikacji poświadczenie mapowany na konto aplikacji. W zależności od możliwości aplikacji poświadczenie może być token Federacji lub nazwę użytkownika i hasło dla konta, które zostało wcześniej zainicjowane w aplikacji.
* **Aplikacje lokalne** aplikacji opublikowanych przy użyciu serwera proxy aplikacji usługi Azure AD, przede wszystkim Włączanie dostępu do aplikacji lokalnych. Te aplikacje polegają na katalog lokalny centralnej, takie jak Windows Server Active Directory. Dostęp do tych aplikacji jest włączana przez wyzwalania serwera proxy do dostarczania zawartości aplikacji dla użytkownika końcowego, przy jednoczesnym zachowaniu wymaganie logowania lokalnego.

Na przykład jeśli użytkownik nie przyłączy organizacji, należy utworzyć konto użytkownika w usłudze Azure AD dla operacji logowania głównych. Jeśli ten użytkownik wymaga dostępu do aplikacji zarządzanej, takie jak Salesforce, należy utworzyć konto dla tego użytkownika w usłudze Salesforce i połączyć konto platformy Azure, aby usługa rejestracji Jednokrotnej w pracy. Gdy użytkownik opuści organizacji, należy usunąć konto usługi Azure AD i przechowuje wszystkie konta odpowiednika w IAM aplikacji, których użytkownik ma dostęp do.

## <a name="access-detection"></a>Dostęp do wykrywania
W przedsiębiorstwach nowoczesnego działu IT często nie są znane wszystkich aplikacji, które są używane w chmurze. W połączeniu z usługi Cloud App Discovery usługi Azure AD zapewnia rozwiązanie, aby wykryć te aplikacje.

## <a name="account-management"></a>Zarządzanie kontami
Zarządzanie kontami w różnych aplikacjach tradycyjnym jest wykonywane przez proces ręczny IT lub pracownikami pomocy technicznej w organizacji. Usługi Azure AD pełni zautomatyzowanie zarządzania kontami przez wszystkie aplikacje dostawcy zintegrowane usługi i aplikacje, te wstępnie zintegrowanych przez firmę Microsoft pomocniczych użytkownika automatycznego inicjowania obsługi lub inicjowania obsługi administracyjnej SAML just in time.

## <a name="automated-user-provisioning"></a>Inicjowanie obsługi użytkowników automatycznych
Niektóre aplikacje Podaj interfejsów automatyzacji tworzenia i usuwania (lub dezaktywacji) kont. Jeśli dostawca oferuje takiego interfejsu, jest wykorzystywana przez usługę Azure AD. Zmniejsza koszty operacyjne, ponieważ zadania administracyjne odbywa się automatycznie i zwiększa bezpieczeństwo środowiska, ponieważ zmniejsza prawdopodobieństwo nieautoryzowanego dostępu.

## <a name="access-management"></a>Zarządzanie dostępem
Z usługą Azure AD można zarządzać dostępu do aplikacji za pomocą poszczególnych lub reguły zmiennych przypisania. Możesz również delegować dostęp administracyjny do odpowiednich osób w organizacji zapewnienia najlepsze nadzoru i zmniejszenie obciążenia pomocy technicznej.

## <a name="on-premises-applications"></a>Aplikacje lokalne
Wbudowanych w aplikacji serwera proxy umożliwia publikowanie aplikacji lokalnych użytkowników, co powoduje jednocześnie spójny dostęp dzięki chmurze nowoczesnych aplikacji i korzyści wynikające z możliwości monitorowania, raportowania i zabezpieczeń usługi Azure AD.

## <a name="reporting-and-monitoring"></a>Monitorowanie i raportowanie
Usługi Azure AD zapewnia wstępnie zintegrowanych raportowania i możliwości, które umożliwiają Ci się dowiedzieć, monitorowania, kto ma dostęp do aplikacji i, jeśli ich rzeczywiście używane.

## <a name="related-capabilities"></a>Funkcje pokrewne
Z usługą Azure AD można zabezpieczyć swoje aplikacje za pomocą zasad dostępu szczegółowego i wstępnie zintegrowane uwierzytelnianie wieloskładnikowe. Aby dowiedzieć się więcej na temat usługi Azure MFA, zobacz [usługi Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć Integrowanie aplikacji z usługą Azure AD, Przyjrzyjmy się [przewodnik Wprowadzenie do integracji Azure Active Directory z aplikacjami pobierania](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Zobacz także
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](active-directory-apps-index.md)
* [Plan wdrożenie krok po kroku dla logowania jednokrotnego do aplikacji SaaS](http://aka.ms/ssodeploymentplan)

