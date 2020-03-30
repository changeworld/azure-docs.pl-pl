---
title: Widoczność i kontrola aplikacji za pomocą programu Microsoft Cloud App Security
description: Dowiedz się, jak zidentyfikować poziomy ryzyka aplikacji, zatrzymać naruszenia i przecieki w czasie rzeczywistym i użyć łączników aplikacji, aby korzystać z interfejsów API dostawcy w celu widoczności i nadzoru.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77069742"
---
# <a name="cloud-app-visibility-and-control"></a>Aplikacja w chmurze — widoczność i kontrola

Aby uzyskać pełne korzyści z aplikacji i usług w chmurze, zespół IT musi znaleźć właściwą równowagę między wspieraniem dostępu przy zachowaniu kontroli w celu ochrony krytycznych danych. Usługa Microsoft Cloud App Security zapewnia ekswalność, kontrolę nad przesyłania danych i zaawansowane analizy umożliwiające identyfikację i zwalczanie zagrożeń cybernetycznych we wszystkich usługach firmy Microsoft i innych firm w chmurze.

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Odnajdywanie niezatwierdzonych zasobów IT w sieci i zarządzanie nimi

Gdy administratorzy IT są pytani, ile aplikacji w chmurze ich zdaniem korzystają ich pracownicy, średnio mówią 30 lub 40, podczas gdy w rzeczywistości średnia wynosi ponad 1000 oddzielnych aplikacji używanych przez pracowników w organizacji. Dział IT w tle pomaga dowiedzieć się i określić, które aplikacje są używane i jaki jest twój poziom ryzyka. Osiemdziesiąt procent pracowników korzysta z nieusankcjonowanych aplikacji, których nikt nie przejrzał i które mogą nie być zgodne z zasadami zabezpieczeń i zgodności. A ponieważ pracownicy mają dostęp do zasobów i aplikacji spoza sieci firmowej, nie wystarczy już reguły i zasady na zaporach.

Użyj funkcji Microsoft Cloud App Discovery (funkcja Usługi Azure Active Directory Premium P1), aby dowiedzieć się, które aplikacje są używane, zbadać ryzyko związane z tymi aplikacjami, skonfigurować zasady w celu identyfikowania nowych ryzykownych aplikacji i odznaczyć te aplikacje, aby zablokować je natywnie za pomocą serwera proxy lub zapory.

- Odnajdywanie i identyfikowanie niezatwierdzonych zasobów IT
- Ocena i analiza
- Zarządzanie aplikacjami
- Zaawansowane raportowanie odnajdowania it w tle
- Kontrola aplikacji objętych sankcjami
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Odkrywanie komputera IT w sieci i zarządzanie nim](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Odnalezione aplikacje z zabezpieczeniami aplikacji w chmurze](https://docs.microsoft.com/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Widoczność i kontrola sesji użytkownika 

W dzisiejszym miejscu pracy często nie wystarczy wiedzieć, co dzieje się w środowisku chmury po fakcie. Chcesz zatrzymać naruszenia i wycieki w czasie rzeczywistym, zanim pracownicy celowo lub nieumyślnie narazić twoje dane i twoją organizację na ryzyko. Wraz z usługą Azure Active Directory (Azure AD) usługa Microsoft Cloud App Security zapewnia te funkcje w całościowym i zintegrowanym usłudze kontroli aplikacji dostępu warunkowego. 

Kontrola sesji używa architektury odwrotnego serwera proxy i jest jednoznacznie zintegrowany z usługą Azure AD Dostęp warunkowy. Dostęp warunkowy usługi Azure AD umożliwia wymuszanie kontroli dostępu w aplikacjach organizacji na podstawie określonych warunków. Warunki określają, kto (użytkownik lub grupa użytkowników) i jakie (które aplikacje w chmurze) i gdzie (które lokalizacje i sieci) zasady dostępu warunkowego jest stosowany do. Po określiniu warunków możesz kierować użytkowników do usługi Cloud App Security, gdzie można chronić dane w czasie rzeczywistym.  

Za pomocą tej kontroli można:  
- Pobieranie plików kontrolnych
- Monitorowanie scenariuszy B2B  
- Kontrolowanie dostępu do plików  
- Ochrona dokumentów podczas pobierania  
 
### <a name="learn-more"></a>Dowiedz się więcej

- [Ochrona aplikacji za pomocą kontroli sesji w bezpieczeństwie aplikacji w chmurze](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Zaawansowana widoczność i kontrolki aplikacji 

Łączniki aplikacji używają interfejsów API dostawców aplikacji, aby umożliwić lepszą widoczność i kontrolę nad aplikacjami, z którymi się łączysz. Usługa Cloud App Security korzysta z interfejsów API dostarczanych przez dostawcę chmury. Każda usługa ma własną strukturę i ograniczenia interfejsu API, takie jak ograniczanie przepustowości, limity interfejsu API, dynamiczne okna interfejsu API z przesunięciem w czasie i inne. Zespół produktów Cloud App Security pracował z tymi usługami, aby zoptymalizować korzystanie z interfejsów API i zapewnić najlepszą wydajność. Biorąc pod uwagę różne ograniczenia, jakie usługi nakładają na ich interfejsy API, aparaty cloud app security używają maksymalnej dozwolonej pojemności. Niektóre operacje, takie jak skanowanie wszystkich plików w dzierżawie, wymagają wielu wywołań interfejsu API, dzięki czemu są rozłożone na dłuższy okres. Spodziewaj się, że niektóre zasady będą działać przez kilka godzin lub dni. 
 
### <a name="learn-more"></a>Dowiedz się więcej  

- [Łączenie aplikacji w usłudze Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Następne kroki

- [Odkrywanie komputera IT w sieci i zarządzanie nim](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)
- [Odnalezione aplikacje z zabezpieczeniami aplikacji w chmurze](https://docs.microsoft.com/cloud-app-security/discovered-apps)
- [Ochrona aplikacji za pomocą kontroli sesji w bezpieczeństwie aplikacji w chmurze](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Łączenie aplikacji w usłudze Cloud App Security](https://docs.microsoft.com/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)
