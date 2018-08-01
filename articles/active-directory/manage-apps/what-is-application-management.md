---
title: Zarządzanie aplikacjami za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Ten artykuł korzyści wynikające z integracji usługi Azure Active Directory z lokalnych, chmurze i aplikacji SaaS.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: bf53829a2d2578132f9a3595c0bac5e8eb588916
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366775"
---
# <a name="managing-applications-with-azure-active-directory"></a>Zarządzanie aplikacjami za pomocą usługi Azure Active Directory
Poza faktyczny przepływ pracy lub zawartości firm ma dwa podstawowe wymagania dla wszystkich aplikacji:

1. Aby zwiększyć wydajność, aplikacje powinny mieć można łatwo znaleźć i uzyskać dostęp
2. Aby włączyć zabezpieczenia i nadzór, organizacja musi kontrolę i nadzór nad kto ma dostęp i faktycznie kto uzyskuje dostęp do każdej aplikacji

Na świecie aplikacji w chmurze, najlepiej można to osiągnąć przy użyciu tożsamości w celu sterowania "*KTO może co wykonywać*."

W terminologię dotyczącą przetwarzania:

* *Kto* jest znany jako *tożsamości* — Zarządzanie użytkownikami i grupami
* *Co* jest znany jako *zarządzanie dostępem* — zarządzanie dostępem do chronionych zasobów

Oba te składniki są ze sobą znane jako *tożsamość i zarządzanie dostępu (IAM)*, który jest definiowany przez [firmy Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) grupy jako "*dyscyplina zabezpieczeń, która umożliwia osobom prawo dostęp do odpowiednich zasobów po prawej stronie razy powodów prawo*".

OK, dlatego na czym polega problem? W przypadku zarządzania tożsamościami i Dostępem *niezarządzanych* w jednym miejscu za pomocą zintegrowanego rozwiązania:

* Tożsamość Administratorzy mają indywidualnie tworzenie i aktualizowanie kont użytkowników we wszystkich aplikacjach oddzielnie, działanie nadmiarowy i czasochłonna.
* Użytkownicy muszą pamiętania wielu poświadczeń do uzyskania dostępu do aplikacji, które są im potrzebne do pracy z. W rezultacie użytkownicy zwykle zanotować swoje hasła lub użycie innych rozwiązań do zarządzania hasłem. Te możliwości wprowadzenia innych zagrożenia dla bezpieczeństwa danych.
* Nadmiarowe, dużo czasu działania zmniejszenia czasu użytkownicy i administratorzy poświęcają na pracy nad działalności, zwiększające dochody firmy.

Tak co zwykle powstrzymuje firmy przed wdrażanie zintegrowanych rozwiązań zarządzania tożsamościami i Dostępem?

* Rozwiązania techniczne najbardziej zależą od platformy oprogramowania, które muszą być wdrożone i dostosowane przez każdej organizacji dla ich własnych aplikacji.
* Aplikacje w chmurze często są przyjęte w poziomie wyższym niż organizacji IT można zintegrować z istniejącymi rozwiązaniami do zarządzania tożsamościami i Dostępem.
* Bezpieczeństwem i monitorowaniem narzędzi wymagają dodatkowych dostosowań i integracji w celu osiągnięcia kompleksowe scenariusze E2E.

## <a name="azure-active-directory-integrated-with-applications"></a>Usługa Azure Active Directory jest zintegrowany z aplikacjami
Azure Active Directory to kompleksowe tożsamością firmy Microsoft jako rozwiązanie usługą (IDaaS) który:

* Umożliwia zarządzanie dostępem i Tożsamościami jako usługa w chmurze 
* Udostępnia centralnego zarządzania dostępem, logowanie jednokrotne (SSO) i raportowanie 
* Obsługuje zarządzanie zintegrowanego dostępu [tysiące aplikacji](https://azure.microsoft.com/marketplace/active-directory/) w galerii aplikacji, w tym Salesforce, Google Apps, Box i Concur. 

Za pomocą usługi Azure Active Directory wszystkie aplikacje, możesz opublikować dla partnerów i klientów (firmy lub konsumenta) ma inną tożsamość i dostęp do możliwości zarządzania.<br> Dzięki temu można znacznie zmniejszyć koszty operacyjne.

Co zrobić, jeśli musisz wdrożyć aplikację, która nie ma jeszcze w galerii aplikacji? Gdy jest to nieco więcej czasu niż Konfigurowanie logowania jednokrotnego dla aplikacji z galerii aplikacji, usługa Azure AD zapewnia kreatora, który pomoże Ci przy użyciu konfiguracji.

Zalety usługi Azure AD wykracza poza "tak" w aplikacjach w chmurze. Można również korzystania z aplikacji lokalnych, zapewniając bezpieczny dostęp zdalny. Bezpieczny dostęp zdalny można wyeliminować potrzebę sieci VPN lub inne implementacje Zarządzanie tradycyjne dostępu zdalnego.

Podając zarządzania centralnej dostępu i logowania jednokrotnego (SSO) dla wszystkich aplikacji, usługa Azure AD zapewnia rozwiązanie do problemów wydajność i bezpieczeństwo danych głównych.

* Użytkownicy mogą korzystać z wielu aplikacji, za pomocą jednego logowania jednokrotnego dzięki czemu do dochodu Generowanie lub pracy działania operacje wykonywane.
* Tożsamość Administratorzy mogą zarządzać dostępem do aplikacji w jednym miejscu.

Korzyścią dla użytkownika, jak i w firmie jest oczywiste. Administrator tożsamości i organizacji, Przyjrzyjmy się dokładniej poznać korzyści.

## <a name="integrated-application-benefits"></a>Korzyści zintegrowanej aplikacji
Proces logowania jednokrotnego obejmuje dwa kroki:

* Uwierzytelnianie, proces weryfikowania tożsamości użytkownika.
* Autoryzacja jest decyzja o włączenie lub blokowanie dostępu do zasobów za pomocą zasad dostępu.

Korzystając z usługi Azure AD do zarządzania aplikacjami w celu włączenia funkcji logowania jednokrotnego:

* Uwierzytelnianie jest wykonywane na lokalnej (np. usługi AD) lub konta usługi Azure AD dla użytkownika.
* Autoryzacji wykonuje dla usługi Azure AD przypisania i ochrony zasady zapewnienie spójnego użytkownika końcowego i pozwala na dodawanie przypisania, lokalizacji i warunki MFA w dowolnej aplikacji, niezależnie od jej możliwości.

Jest ważne dowiedzieć się, że, sposób, w jaki autoryzacji jest wprowadzany w aplikacji docelowej różni się w zależności od tego, jak aplikacja została zintegrowana z usługą Azure AD.

* **Aplikacje są wstępnie zintegrowane przez dostawcę usług** takich jak Office 365 i Azure, są to aplikacje wbudowane bezpośrednio w usłudze Azure AD i opierając się na jej do swoich kompleksowe funkcje zarządzania tożsamościami i dostępem. Dostęp do tych aplikacji jest włączona przy użyciu informacji o katalogu i wystawiania tokenu.
* **Aplikacje są wstępnie zintegrowane przez firmę Microsoft i aplikacje niestandardowe** są aplikacje w chmurze niezależne, zależą od katalogu aplikacji wewnętrznych, które mogą działać niezależnie od usługi Azure AD. Dostęp do tych aplikacji jest włączony przez wystawienie poświadczeń specyficznych dla aplikacji mapowany do konta aplikacji. W zależności od możliwości aplikacji poświadczenie może być tokenu Federacji lub nazwę użytkownika i hasło dla konta które zostało wcześniej udostępnionych w aplikacji.
* **Aplikacje lokalne** aplikacje opublikowane za pośrednictwem serwera proxy aplikacji usługi Azure AD, przede wszystkim Włączanie dostępu do aplikacji lokalnych. Aplikacje te zależą od centralnej lokalnego katalogu, takie jak Windows Server Active Directory. Dostęp do tych aplikacji można włączyć poprzez wyzwalania serwera proxy do dostarczania zawartości aplikacji użytkownika końcowego, przy jednoczesnym zachowaniu wymagania logowania jednokrotnego w środowisku lokalnym.

Na przykład jeśli użytkownik nie przyłączy Twojej organizacji, należy utworzyć konta dla użytkownika w usłudze Azure AD dla operacji logowania głównych. Jeśli ten użytkownik wymaga dostępu do aplikacji zarządzanej, takich jak Salesforce, należy również utworzyć konto dla tego użytkownika w usłudze Salesforce i połączyć go z kontem platformy Azure, aby działa usługa rejestracji Jednokrotnej. Gdy użytkownik opuszcza organizację, zaleca się usuwanie konta usługi Azure AD i przechowuje aplikacje, do którego użytkownik ma dostęp do wszystkich kont odpowiednika w zarządzania tożsamościami i Dostępem.

## <a name="access-detection"></a>Dostęp do wykrywania
W nowoczesnych przedsiębiorstwach działom IT często nie rozpoznają wszystkich aplikacji, które są używane w chmurze. W połączeniu z odnajdywania aplikacji w chmurze usługa Azure AD zapewnia rozwiązanie, aby wykryć te aplikacje.

## <a name="account-management"></a>Zarządzanie kontami
Tradycyjnie zarządzania kontami w różnych aplikacji jest procesem wykonywanym ręcznie wykonywanych przez IT lub personelu działu pomocy technicznej w organizacji. Usługa Azure AD w pełni automatyzuje zarządzanie kontami aplikacji zintegrowanych przez dostawców usług i aplikacji, wstępnie zintegrowane przez firmę Microsoft w obsłudze użytkownika automatyczne Inicjowanie obsługi administracyjnej lub SAML just in Time inicjowania obsługi administracyjnej.

## <a name="automated-user-provisioning"></a>Inicjowanie obsługi użytkowników automatycznych
Niektóre aplikacje zapewniają interfejsów automatyzacji tworzenia i usuwania (lub dezaktywacji) kont. Jeśli dostawca obsługuje taki interfejs, wartość jest wykorzystywane przez usługę Azure AD. Zmniejsza koszty operacyjne, ponieważ zadania administracyjne odbywa się automatycznie i zwiększa bezpieczeństwo środowiska, ponieważ zmniejsza prawdopodobieństwo nieautoryzowanego dostępu.

## <a name="access-management"></a>Zarządzanie dostępem
Za pomocą usługi Azure AD można zarządzać dostęp do aplikacji przy użyciu pojedynczych lub reguły na podstawie przypisania. Możesz również delegować dostęp do zarządzania do odpowiednich osób w organizacji, zapewnienie najlepszych nadzoru i zmniejszenie obciążenia pomocy technicznej.

## <a name="on-premises-applications"></a>Aplikacje lokalne
Serwer proxy aplikacji wbudowanej umożliwia publikowanie lokalnych aplikacji dla użytkowników, co spowoduje jednocześnie spójny dostęp do korzystania z aplikacji z nowoczesnym rozwiązaniom w chmurze i korzyści z możliwości monitorowania, raportowania i zabezpieczeń usługi Azure AD.

## <a name="reporting-and-monitoring"></a>Monitorowanie i raportowanie
Usługi Azure AD udostępnia wstępnie zintegrowanych raportowania i monitorowania funkcji, które umożliwiają wiedzieć, kto ma dostęp do aplikacji i stosowania ich faktycznie je.

## <a name="related-capabilities"></a>Funkcje pokrewne
Za pomocą usługi Azure AD można zabezpieczyć swoje aplikacje za pomocą zasad dostępu szczegółowej i wstępnie zintegrowane uwierzytelnianie wieloskładnikowe. Aby dowiedzieć się więcej o usłudze Azure MFA, zobacz [usługi Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć Integrowanie aplikacji z usługą Azure AD, zapoznaj się z [przewodnik Wprowadzenie do integracji usługi Azure Active Directory z aplikacjami wprowadzenie](plan-an-application-integration.md).

## <a name="see-also"></a>Zobacz także
* [Indeks artykułów dotyczących zarządzania aplikacjami w usłudze Azure Active Directory](../active-directory-apps-index.md)
* [Wdrożenie krok po kroku planowanie logowania jednokrotnego do aplikacji SaaS](http://aka.ms/ssodeploymentplan)

