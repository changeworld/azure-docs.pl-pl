---
title: Widoczność i kontrola aplikacji za pomocą Microsoft Cloud App Security
description: Dowiedz się, jak identyfikować poziomy ryzyka aplikacji, zatrzymywać naruszenia i wycieki w czasie rzeczywistym oraz korzystać z łączników aplikacji, aby korzystać z interfejsów API dostawcy w celu zapewnienia widoczności i zarządzania.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 02/03/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e1a06acddae0973d5dbc7e64212026149f217
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069742"
---
# <a name="cloud-app-visibility-and-control"></a>Widoczność i kontrola aplikacji w chmurze

Aby w pełni korzystać z aplikacji i usług w chmurze, zespół IT musi znaleźć odpowiednie saldo dostępu pomocniczego przy zachowaniu kontroli ochrony danych krytycznych. Microsoft Cloud App Security zapewnia zaawansowaną widoczność, kontrolę nad przejazdami danych oraz zaawansowaną analizę, aby identyfikować i zwalczać zagrożenia cybernetycznymi w ramach wszystkich usług w chmurze firmy Microsoft i innych firm.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Odkryj i Zarządzaj w tle w sieci

Gdy Administratorzy IT są proszeni o liczbę aplikacji w chmurze, których używają pracownicy, średnio 30 lub 40, w rzeczywistości średnia jest ponad 1 000 oddzielnymi aplikacjami używanymi przez pracowników w organizacji. W tle pomocne są informacje o tym, które aplikacje są używane i jakie są poziom ryzyka. 80 procent pracowników korzysta z niezaakceptowanych oficjalnie aplikacji, które nie zostały zrecenzowane i mogą nie być zgodne z zasadami zabezpieczeń i zgodności. Ze względu na to, że pracownicy mogą uzyskiwać dostęp do Twoich zasobów i aplikacji spoza sieci firmowej, nie wystarczy już mieć zasad i zasad zapory.

Użyj funkcji odnajdywania aplikacji Microsoft Cloud (funkcja Azure Active Directory — wersja Premium P1), aby sprawdzić, które aplikacje są używane, poznać ryzyko tych aplikacji, skonfigurować zasady w celu identyfikowania nowych ryzykownych aplikacji oraz odrzucania zaakceptowania ich w sposób natywny przy użyciu serwer proxy lub urządzenie zapory.

- Odkryj i zidentyfikuj cień IT
- Oceń i Analizuj
- Zarządzanie aplikacjami
- Zaawansowane raporty wykrywania IT w tle
- Kontrola zaakceptowanych aplikacji
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Odkryj i Zarządzaj w tle w sieci](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Aplikacje odnalezione za pomocą Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Widoczność i kontrola sesji użytkownika 

W obszarze roboczym dzisiejsza, często nie jest wystarczająco, aby wiedzieć, co się dzieje w Twoim środowisku chmury w późniejszym czasie. Chcesz zrezygnować z naruszeń i wycieków w czasie rzeczywistym, zanim pracownicy zacelowo lub przypadkowo umieściją dane i Twoją organizację. Razem z usługą Azure Active Directory (Azure AD) Microsoft Cloud App Security udostępniają te funkcje w całościowym i zintegrowanym środowisku z Kontrola dostępu warunkowego aplikacji. 

Kontrola sesji używa architektury zwrotnego serwera proxy i jest unikatowo zintegrowana z dostępem warunkowym usługi Azure AD. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych warunków. Warunki definiują użytkowników lub grupę użytkowników oraz informacje o tym, co (w których aplikacjach w chmurze) oraz o tym, gdzie (które lokalizacje i sieci) są stosowane zasady dostępu warunkowego. Po ustaleniu warunków można kierować użytkowników do Cloud App Security, gdzie można chronić dane w czasie rzeczywistym.  

Za pomocą tego formantu można:  
- Sterowanie pobraniami plików
- Monitoruj scenariusze B2B  
- Kontrolowanie dostępu do plików  
- Ochrona dokumentów przy pobieraniu  
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Zaawansowana widoczność i kontrolki aplikacji 

Łączniki aplikacji używają interfejsów API dostawców aplikacji, aby umożliwić zapewnia lepszy wgląd i kontrolę aplikacji, którymi są nawiązywane połączenia, Microsoft Cloud App Security. Cloud App Security korzysta z interfejsów API dostarczonych przez dostawcę chmury. Każda usługa ma swoją własną strukturę i ograniczenia interfejsu API, takie jak ograniczanie przepustowości, interfejs API limity, dynamiczne okna zmiany czasu interfejsu API i inne. Zespół produktu Cloud App Security pracował z tymi usługami w celu zoptymalizowania użycia interfejsów API i zapewnienia najlepszej wydajności. Biorąc pod uwagę różne ograniczenia usług nakładających się na interfejsy API, aparaty Cloud App Security używają ich maksymalnej dozwolonej pojemności. Niektóre operacje, takie jak skanowanie wszystkich plików w dzierżawie, wymagają wielu wywołań interfejsu API, dzięki czemu są rozłożone w dłuższym okresie. Niektóre zasady mogą działać przez kilka godzin lub dni. 
 
### <a name="learn-more"></a>Dowiedz się więcej  

- [Łączenie aplikacji w Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Następne kroki

- [Odkryj i Zarządzaj w tle w sieci](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Aplikacje odnalezione za pomocą Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Ochrona aplikacji za pomocą kontroli sesji w Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Łączenie aplikacji w Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
