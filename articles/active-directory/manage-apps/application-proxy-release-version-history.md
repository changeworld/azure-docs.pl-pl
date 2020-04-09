---
title: 'Serwer proxy aplikacji usługi Azure AD: historia wersji | Dokumenty firmy Microsoft'
description: W tym artykule wymieniono wszystkie wersje serwera proxy aplikacji usługi Azure AD i opisano nowe funkcje i problemy z naprawą
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
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983895"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Serwer proxy aplikacji usługi Azure AD: historia wersji
W tym artykule wymieniono wersje i funkcje serwera proxy aplikacji usługi Azure Active Directory (Azure AD), które zostały wydane. Zespół usługi Azure AD regularnie aktualizuje serwer proxy aplikacji o nowe funkcje i funkcje. Łączniki serwera proxy aplikacji są aktualizowane automatycznie po wydaniu nowej wersji. 

Firma Microsoft zaleca upewnienie się, że automatyczne aktualizacje są włączone dla łączników, aby upewnić się, że masz najnowsze funkcje i poprawki błędów. Firma Microsoft zapewnia bezpośrednie wsparcie dla najnowszej wersji łącznika i jednej wersji wcześniej.

Oto lista powiązanych zasobów:

Zasób |  Szczegóły
--------- | --------- |
Jak włączyć serwer proxy aplikacji | Wymagania wstępne dotyczące włączania serwera proxy aplikacji oraz instalowania i rejestrowania łącznika są opisane w tym [samouczku](application-proxy-add-on-premises-application.md).
Opis łączników serwera proxy aplikacji usługi Azure AD | Dowiedz się więcej o [zarządzaniu łącznikami](application-proxy-connectors.md) i o [automatycznej aktualizacji](application-proxy-connectors.md#automatic-updates)złączy .
Pobieranie łącznika serwera proxy aplikacji usługi Azure AD |  [Pobierz najnowszy łącznik](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Stan wydania

7 kwietnia 2020: Premiera do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia
-   Złącza używają tylko protokołu TLS 1.2 dla wszystkich połączeń. Aby uzyskać więcej [informacji, zobacz wymagania wstępne łącznika.](application-proxy-add-on-premises-application.md#before-you-begin)
- Ulepszona sygnalizacja między usługami łącznika i platformy Azure. Obejmuje to obsługę niezawodnych sesji dla komunikacji WCF między usługami łącznika i platformy Azure oraz ulepszenia buforowania DNS dla komunikacji WebSocket.
- Obsługa konfigurowania serwera proxy między łącznikiem a aplikacją wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Rozwiązane problemy
- Usunięto powrót do portu 8080 w celu komunikacji z usługi łącznika do platformy Azure.
- Dodano ślady debugowania dla komunikacji WebSocket. 
- Rozwiązano problem zachowania atrybutu SameSite podczas ustawiania plików cookie aplikacji wewnętrznej bazy danych.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Stan wydania

20 września 2018: Premiera do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Dodano obsługę WebSocket dla aplikacji QlikSense. Aby dowiedzieć się więcej o tym, jak zintegrować QlikSense z serwerem proxy aplikacji, zobacz ten [instruktaż](application-proxy-qlik.md). 
- Udoskonalono kreatora instalacji, aby ułatwić konfigurowanie wychodzącego serwera proxy. 
- Ustaw protokół TLS 1.2 jako domyślny protokół dla łączników. 
- Dodano nową umowę licencyjną użytkownika końcowego (EULA).  

### <a name="fixed-issues"></a>Rozwiązane problemy

- Naprawiono błąd, który powodował pewne przecieki pamięci w złączu.
- Zaktualizowano wersję usługi Azure Service Bus, która zawiera poprawkę błędu w przypadku problemów z limitem czasu łącznika.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Stan wydania

Styczeń 19, 2018: Wydany do pobrania

### <a name="fixed-issues"></a>Rozwiązane problemy

- Dodano obsługę domen niestandardowych, które wymagają tłumaczenia domeny w pliku cookie.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Stan wydania 

25 maja 2017: Wydany do pobrania 

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia 

Poprawiono kontrolę nad limitami połączeń wychodzących łączników. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Stan wydania

15 kwietnia 2017: Wydany do pobrania

### <a name="new-features-and-improvements"></a>Nowe funkcje i ulepszenia

- Uproszczone dołączanie i zarządzanie z mniejszą liczbą wymaganych portów. Serwer proxy aplikacji wymaga teraz otwarcia tylko dwóch standardowych portów wychodzących: 443 i 80. Serwer proxy aplikacji nadal używa tylko połączeń wychodzących, więc nadal nie potrzebujesz żadnych składników w strefie DMZ. Szczegółowe informacje można znaleźć w naszej [dokumentacji konfiguracyjnej.](application-proxy-add-on-premises-application.md)  
- Jeśli jest obsługiwany przez zewnętrzny serwer proxy lub zaporę, można teraz otworzyć sieć przez dns zamiast zakresu IP. Usługi proxy aplikacji wymagają tylko połączeń z plikami *.msappproxy.net i *.servicebus.windows.net.


## <a name="earlier-versions"></a>Wcześniejsze wersje

Jeśli używasz wersji łącznika serwera proxy aplikacji wcześniej niż 1.5.36.0, zaktualizuj do najnowszej wersji, aby upewnić się, że masz najnowsze w pełni obsługiwane funkcje.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [zdalnym dostępie do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji usługi Azure AD.](application-proxy.md)
- Aby rozpocząć korzystanie z serwera proxy aplikacji, zobacz [Samouczek: Dodawanie aplikacji lokalnej do zdalnego dostępu za pośrednictwem serwera proxy aplikacji](application-proxy-add-on-premises-application.md).
