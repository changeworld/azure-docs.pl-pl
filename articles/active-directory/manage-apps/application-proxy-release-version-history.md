---
title: 'Serwer proxy aplikacji usługi Azure AD platformy Azure: Historia wersji systemu | Microsoft Docs'
description: W tym artykule wymieniono wszystkie wersje platformy Azure serwer proxy aplikacji usługi Azure AD i opisano nowe funkcje oraz naprawione problemy
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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693898"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Serwer proxy aplikacji usługi Azure AD platformy Azure: Historia wersji
W tym artykule wymieniono wersje i funkcje serwera proxy aplikacji Azure Active Directory (Azure AD), który został wystawiony. Zespół usługi Azure AD regularnie aktualizuje serwer proxy aplikacji przy użyciu nowych funkcji i funkcjonalności. Łączniki serwera proxy aplikacji są aktualizowane automatycznie po wydaniu nowej wersji. 

Zalecamy, aby upewnić się, że funkcja autoaktualizacje jest włączona dla łączników, aby upewnić się, że masz najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednią pomoc techniczną dotyczącą najnowszej wersji łącznika i jednej wersji przed.

Poniżej znajduje się lista powiązanych zasobów:

Resource |  Szczegóły
--------- | --------- |
Jak włączyć serwer proxy aplikacji | Wymagania wstępne dotyczące włączania serwera proxy aplikacji oraz instalowania i rejestrowania łącznika są opisane w tym [samouczku](application-proxy-add-on-premises-application.md).
Omówienie łączników serwera Proxy aplikacji usługi Azure AD | Dowiedz się więcej o [zarządzaniu łącznikami](application-proxy-connectors.md) i sposobach [uaktualniania](application-proxy-connectors.md#automatic-updates)łączników.
Pobieranie łącznika usługi Azure serwer proxy aplikacji usługi Azure AD |  [Pobierz najnowszy łącznik](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stan wydania

20 września 2018: Wydane do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę protokołu WebSocket dla aplikacji QlikSense. Aby dowiedzieć się więcej o tym, jak zintegrować QlikSense z serwerem proxy aplikacji, zobacz ten [Przewodnik](application-proxy-qlik.md). 
- Ulepszono Kreatora instalacji, aby ułatwić konfigurowanie wychodzącego serwera proxy. 
- Dla łączników należy ustawić protokół TLS 1,2. 
- Dodano nową umowę licencyjną użytkownika oprogramowania (EULA).  

### <a name="fixed-issues"></a>Rozwiązane problemy

- Naprawiono usterkę, która spowodowała wystąpienie przecieków pamięci w łączniku.
- Zaktualizowano wersję Azure Service Bus, która obejmuje rozwiązywanie problemów z przekroczeniem limitu czasu łącznika.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stan wydania

19 stycznia 2018: Wydane do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

- Dodano obsługę domen niestandardowych, które wymagają tłumaczenia domeny w pliku cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stan wydania 

25 maja 2017: Wydane do pobrania 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

Ulepszona kontrola nad limitami połączeń wychodzących łączników. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stan wydania

15 kwietnia 2017: Wydane do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Uproszczone dołączanie i zarządzanie przy użyciu mniejszej liczby wymaganych portów. Serwer proxy aplikacji wymaga teraz otwarcia tylko dwóch standardowych portów wychodzących: 443 i 80. Serwer proxy aplikacji nadal używa tylko połączeń wychodzących, więc nadal nie są potrzebne żadne składniki w strefie DMZ. Aby uzyskać szczegółowe informacje, zobacz [dokumentację dotyczącą konfiguracji](application-proxy-add-on-premises-application.md).  
- Jeśli jest to obsługiwane przez zewnętrzny serwer proxy lub zaporę, możesz teraz otworzyć sieć przy użyciu systemu DNS, a nie z zakresu adresów IP. Usługi serwera proxy aplikacji wymagają połączeń tylko z *. msappproxy.net i *. servicebus.windows.net.


## <a name="earlier-versions"></a>Wcześniejsze wersje

Jeśli używasz wersji łącznika serwera proxy aplikacji starszej niż 1.5.36.0, zaktualizuj ją do najnowszej wersji, aby upewnić się, że masz najnowsze w pełni obsługiwane funkcje.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [dostępu zdalnego do aplikacji lokalnych za pomocą usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy.md).
- Aby rozpocząć korzystanie z serwera proxy aplikacji [, zobacz Samouczek: Dodaj aplikację lokalną dla dostępu zdalnego za poorednictwem serwera proxy](application-proxy-add-on-premises-application.md)aplikacji.
