---
title: 'Serwer Proxy aplikacji usługi Azure AD: Historia wersji | Dokumentacja firmy Microsoft'
description: Ten artykuł zawiera listę wszystkich wersji serwera Proxy aplikacji usługi Azure AD i opisuje nowe funkcje i rozwiązane problemy
services: active-directory
documentationcenter: ''
author: msmimart
manager: celested
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf9ee43c6c6b332c05286da8e330812d7e0db6c2
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578593"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Serwer Proxy aplikacji usługi Azure AD: Historia wersji
W tym artykule wymieniono wersje i funkcje serwera Proxy aplikacji usługi Azure Active Directory (Azure AD), które zostały zwolnione. Zespół usługi Azure AD regularnie aktualizuje serwera Proxy aplikacji za pomocą nowych funkcji. Łączników serwera Proxy aplikacji są aktualizowane automatycznie po udostępnieniu nowej wersji.

Poniżej przedstawiono listę powiązanych zasobów:

Zasób |  Szczegóły
--------- | --------- |
Jak włączyć serwer Proxy aplikacji | Wymagania wstępne dla Włączanie serwera Proxy aplikacji i instalowanie i rejestrowanie łącznika są opisane w tym [samouczek](application-proxy-add-on-premises-application.md).
Omówienie łączników serwera Proxy aplikacji usługi Azure AD | Dowiedz się więcej o [zarządzania łącznika](application-proxy-connectors.md) i w jaki sposób łączników [automatyczną aktualizację](application-proxy-connectors.md#automatic-updates).
Pobierz łącznik serwera Proxy aplikacji usługi Azure AD |  [Pobierz najnowszy łącznik](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stan zlecenia

20 września 2018 r.: Ogólnie dostępnych do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę protokołu WebSocket dla aplikacji QlikSense. Aby dowiedzieć się więcej na temat sposobu integrowania QlikSense z serwera Proxy aplikacji, zobacz ten [wskazówki](application-proxy-qlik.md). 
- Ulepszona Kreatora instalacji, aby ułatwić konfigurowanie serwera proxy ruchu wychodzącego. 
- Ustawianie jako domyślnego protokołu TLS 1.2 łączników. 
- Dodać nowe umowy licencyjnej użytkownika oprogramowania (EULA).  

### <a name="fixed-issues"></a>Rozwiązane problemy

- Usunięto usterkę powodującą niektóre przecieków pamięci w łączniku.
- Zaktualizowano wersji usługi Azure Service Bus, która obejmuje poprawkę dla łącznika limitu czasu problemów.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stan zlecenia

19 stycznia 2018 r.: Ogólnie dostępnych do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

- Dodano obsługę domen niestandardowych, które muszą tłumaczenia domeny w pliku cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stan zlecenia 

25 maja 2017 r.: Ogólnie dostępnych do pobrania 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

Ulepszone kontrolę nad limitów połączeń wychodzących dla łączników. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stan zlecenia

15 kwietnia 2017 r.: Ogólnie dostępnych do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Uproszczone dołączania i zarządzania za pomocą mniejszej liczby wymaganych portów. Serwer Proxy aplikacji wymaga teraz otworzyć tylko dwa standardowe porty wychodzące: 443 i 80. Serwer Proxy aplikacji w dalszym ciągu używa tylko wychodzące połączenia, więc nadal nie ma potrzeby składników w strefie DMZ. Aby uzyskać więcej informacji, zobacz nasze [dokumentacją konfiguracji](application-proxy-add-on-premises-application.md).  
- Jeśli jest obsługiwany przy użyciu zewnętrznego serwera proxy lub zapory, teraz możesz otworzyć sieci przez usługę DNS zamiast zakres adresów IP. Usługi serwera Proxy aplikacji wymaga połączenia z *. msappproxy.net i *. servicebus.windows.net tylko.


## <a name="earlier-versions"></a>Wcześniejszych wersjach

Jeśli używasz łącznika serwera Proxy aplikacji w wersji wcześniejszej niż 1.5.36.0, zaktualizuj do najnowszej wersji, aby zapewnić najnowsze funkcje w pełni obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [dostęp zdalny do aplikacji lokalnych za pośrednictwem serwera Proxy aplikacji usługi Azure AD](application-proxy.md).
- Aby rozpocząć korzystanie z serwera Proxy aplikacji, zobacz [samouczka: Dodawanie aplikacji w środowisku lokalnym dostępu zdalnego za pośrednictwem serwera Proxy aplikacji](application-proxy-add-on-premises-application.md).
