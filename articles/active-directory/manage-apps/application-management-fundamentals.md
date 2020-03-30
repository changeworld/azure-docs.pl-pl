---
title: 'Zarządzanie aplikacjami: Najlepsze praktyki i zalecenia | Dokumenty firmy Microsoft'
description: Poznaj najlepsze rozwiązania i zalecenia dotyczące zarządzania aplikacjami w usłudze Azure Active Directory. Dowiedz się więcej o używaniu automatycznego inicjowania obsługi administracyjnej i publikowania aplikacji lokalnych za pomocą serwera proxy aplikacji.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085293"
---
# <a name="application-management-best-practices"></a>Najważniejsze wskazówki dotyczące zarządzania aplikacjami
Ten artykuł zawiera zalecenia i najlepsze rozwiązania dotyczące zarządzania aplikacjami w usłudze Azure Active Directory (Azure AD), przy użyciu automatycznego inicjowania obsługi administracyjnej i publikowania aplikacji lokalnych za pomocą serwera proxy aplikacji.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Aplikacje w chmurze i rekomendacje logowania jednokrotnego
| Zalecenie | Komentarze |
| --- | --- |
| Sprawdź galerię aplikacji usługi Azure AD dla aplikacji  | Usługa Azure AD ma galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone za pomocą logowania jednokrotnego (Logowanie jednokrotne) w przedsiębiorstwie. Aby uzyskać wskazówki dotyczące konfiguracji specyficzne dla aplikacji, zobacz [listę samouczków aplikacji SaaS](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/).  | 
| Użyj sfederowanego samoso opartego na SAML  | Gdy aplikacja obsługuje go, należy użyć Federated, SAML oparte loga loga z usługą Azure AD zamiast loga loga oparte na hasłach i ADFS.  | 
| Używanie funkcji SHA-256 do podpisywania certyfikatów  | Usługa Azure AD domyślnie używa algorytmu SHA-256 do podpisywania odpowiedzi SAML. Użyj sha-256, chyba że aplikacja wymaga SHA-1 (zobacz [Opcje podpisywania certyfikatów](certificate-signing-options.md) i [problem logowania aplikacji](application-sign-in-problem-application-error.md)).)  | 
| Wymagaj przypisywania użytkowników  | Domyślnie użytkownicy mogą uzyskiwać dostęp do aplikacji dla przedsiębiorstw bez przypisywania im. Jeśli jednak aplikacja udostępnia role lub jeśli chcesz, aby aplikacja była wyświetlana w panelu dostępu użytkownika, wymaganie przypisania użytkownika. (Zobacz [wskazówki dla deweloperów dotyczące integracji aplikacji.)](developer-guidance-for-integrating-applications.md)  | 
| Wdrażanie panelu dostępu Moje aplikacje dla użytkowników | [Panel dostępu](end-user-experiences.md) `https://myapps.microsoft.com` jest portalem internetowym, który zapewnia użytkownikom jeden punkt wejścia dla przypisanych im aplikacji chmurowych. W miarę dodawania dodatkowych funkcji, takich jak zarządzanie grupami i samoobsługowe resetowanie haseł, użytkownicy mogą je znaleźć w panelu dostępu. Zobacz [Planowanie wdrożenia panelu dostępu](access-panel-deployment-plan.md).
| Używanie przypisania grupy  | Jeśli subskrypcja zostanie uwzględniona, przypisz grupy do aplikacji, aby można było delegować bieżące zarządzanie dostępem do właściciela grupy. (Zobacz [wskazówki dla deweloperów dotyczące integracji aplikacji.)](developer-guidance-for-integrating-applications.md)   | 
| Ustanawianie procesu zarządzania certyfikatami | Maksymalny okres istnienia certyfikatu podpisywania wynosi trzy lata. Aby zapobiec lub zminimalizować awarię z powodu wygaśnięcia certyfikatu, użyj ról i list dystrybucyjnych poczty e-mail, aby upewnić się, że powiadomienia o zmianach związanych z certyfikatami są ściśle monitorowane. |

## <a name="provisioning-recommendations"></a>Zalecenia dotyczące inicjowania obsługi administracyjnej
| Zalecenie | Komentarze |
| --- | --- |
| Konfigurowanie inicjowania obsługi administracyjnej za pomocą aplikacji w chmurze za pomocą samouczków | Sprawdź [listę samouczków aplikacji SaaS,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) aby uzyskać wskazówki krok po kroku dotyczące konfigurowania inicjowania obsługi administracyjnej aplikacji galerii, którą chcesz dodać. |
| Monitorowanie stanu za pomocą dzienników inicjowania obsługi administracyjnej (podglądu) | [Dzienniki inicjowania obsługi administracyjnej](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) podają szczegółowe informacje o wszystkich akcjach wykonywanych przez usługę inicjowania obsługi administracyjnej, w tym o stanie dla poszczególnych użytkowników. |
| Przypisywanie grupy dystrybucyjnej do wiadomości e-mail z powiadomieniem o inicjowaniu obsługi administracyjnej | Aby zwiększyć widoczność alertów krytycznych wysyłanych przez usługę inicjowania obsługi administracyjnej, przypisz grupę dystrybucyjną do ustawienia Wiadomości e-mail powiadomień. |


## <a name="application-proxy-recommendations"></a>Zalecenia dotyczące serwera proxy aplikacji
| Zalecenie | Komentarze |
| --- | --- |
| Używanie serwera proxy aplikacji do zdalnego dostępu do zasobów wewnętrznych | Serwer proxy aplikacji jest zalecany do udzielania użytkownikom zdalnym dostępu do zasobów wewnętrznych, zastępując potrzebę korzystania z sieci VPN lub odwrotnego serwera proxy. Nie jest przeznaczony do uzyskiwania dostępu do zasobów z sieci firmowej, ponieważ może dodać opóźnienia.
| Używanie domen niestandardowych | Konfigurowanie domen niestandardowych dla aplikacji (zobacz [Konfigurowanie domen niestandardowych),](application-proxy-configure-custom-domain.md)tak aby adresy URL dla użytkowników i między aplikacjami działały z poziomu sieci lub spoza niej. Możesz też kontrolować swoją markę i dostosowywać adresy URL.  Korzystając z niestandardowych nazw domen, zaplanuj uzyskanie certyfikatu publicznego od zaufanego urzędu certyfikacji firmy Microsoft. Usługa Azure Application Proxy obsługuje certyfikaty standardowe ([symbole wieloznaczne](application-proxy-wildcard.md)) lub san. (Zobacz [Planowanie serwera proxy aplikacji](application-proxy-deployment-plan.md).) |
| Synchronizowanie użytkowników przed wdrożeniem serwera proxy aplikacji | Przed wdrożeniem serwera proxy aplikacji należy zsynchronizować tożsamości użytkowników z katalogu lokalnego lub utworzyć je bezpośrednio w usłudze Azure AD. Synchronizacja tożsamości umożliwia usłudze Azure AD wstępne uwierzytelnianie użytkowników przed przyznaniem im dostępu do opublikowanych aplikacji usługi App Proxy. Zawiera również informacje o identyfikatorze użytkownika niezbędne do wykonywania logowania jednokrotnego (Logowanie jednokrotne). (Zobacz [Planowanie serwera proxy aplikacji](application-proxy-deployment-plan.md).) |
| Postępuj zgodnie z naszymi wskazówkami dotyczącymi wysokiej dostępności i równoważenia obciążenia | Aby dowiedzieć się, jak przepływa ruch między użytkownikami, łącznikami serwera proxy aplikacji i serwerami aplikacji zaplecza, a także aby uzyskać wskazówki dotyczące optymalizacji równoważenia wydajności i obciążenia, zobacz [Wysoka dostępność i równoważenie obciążenia łączników i aplikacji serwera proxy aplikacji](application-proxy-high-availability-load-balancing.md). |
| Używanie wielu złączy | Użyj dwóch lub więcej łączników serwera proxy aplikacji, aby uzyskać większą odporność, dostępność i skalę (zobacz [łączniki serwera proxy aplikacji).](application-proxy-connectors.md) Utwórz grupy łączników i upewnij się, że każda grupa łączników ma co najmniej dwa złącza (trzy złącza są optymalne). |
| Lokalizowanie serwerów łączników w pobliżu serwerów aplikacji i upewnienie się, że znajdują się w tej samej domenie | Aby zoptymalizować wydajność, fizycznie zlokalizować serwer łącznika w pobliżu serwerów aplikacji (zobacz [Zagadnienia dotyczące topologii sieci](application-proxy-network-topology.md)). Ponadto serwer łącznika i serwery aplikacji sieci web powinny należeć do tej samej domeny usługi Active Directory lub powinny obejmować domeny ufające. Ta konfiguracja jest wymagana dla protokołu SSO ze zintegrowanym uwierzytelnianiem systemu Windows (IWA) i delegowaniem ograniczonym kerberos (KCD). Jeśli serwery znajdują się w różnych domenach, należy użyć delegowania opartego na zasobach dla logowania jednokrotnego (zobacz [KCD dla logowania jednokrotnego za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Włączanie automatycznych aktualizacji łączników | Włącz automatyczne aktualizacje łączników w celu uzyskania najnowszych funkcji i poprawek błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dla najnowszej wersji łącznika i jednej wersji wcześniej. (Zobacz [historia wersji serwera proxy aplikacji](application-proxy-release-version-history.md).) |
| Ominięcie lokalnego serwera proxy | Aby ułatwić konserwację, należy skonfigurować łącznik, aby pominąć lokalny serwer proxy, aby bezpośrednio łączył się z usługami platformy Azure. (Zobacz [łączniki serwera proxy aplikacji i serwery proxy.)](application-proxy-configure-connectors-with-proxy-servers.md) |
| Korzystanie z serwera proxy aplikacji usługi Azure AD za pośrednictwem serwera proxy aplikacji sieci Web | Użyj serwera proxy aplikacji usługi Azure AD dla większości scenariuszy lokalnych. Serwer proxy aplikacji sieci Web jest preferowany tylko w scenariuszach, które wymagają serwera proxy dla usług AD FS i gdzie nie można używać domen niestandardowych w usłudze Azure Active Directory. (Zobacz [Migracja serwera proxy aplikacji](application-proxy-migration.md)).) |
