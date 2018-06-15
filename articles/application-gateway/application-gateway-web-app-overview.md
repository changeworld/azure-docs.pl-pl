---
title: Omówienie wielodostępnych zapleczy w usłudze Azure Application Gateway | Microsoft Docs
description: Ta strona zawiera omówienie obsługi wielodostępnych zapleczy w usłudze Application Gateway.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: 7c00369737d05f414f9ac23d9d3e8918b908fc80
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200998"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Obsługa wielodostępnych zapleczy w usłudze Application Gateway

Usługa Azure Application Gateway obsługuje zestawy skalowania maszyn wirtualnych, interfejsy sieciowe, publiczne/prywatne adresy IP i w pełni kwalifikowane nazwy domeny (FQDN, fully qualified domain name) w ramach swoich pul zaplecza. Domyślnie usługa Application Gateway nie zmienia przychodzącego nagłówka hosta HTTP z klienta i wysyła niezmieniony nagłówek do zaplecza. Istnieje wiele usług, takich jak [Azure Web Apps](../app-service/app-service-web-overview.md), które są wielodostępne z natury i polegają na konkretnym nagłówku hosta lub rozszerzeniu SNI przy rozpoznawaniu właściwego punktu końcowego. Usługa Application Gateway obecnie umożliwia użytkownikom zastępowanie przychodzącego nagłówka hosta HTTP na podstawie ustawień HTTP zaplecza. Ta funkcja umożliwia obsługę wielodostępnych zapleczy usług Azure Web Apps i API Management. Ta możliwość jest dostępna dla standardowej jednostki SKU i jednostki SKU zapory aplikacji internetowych. Obsługa zaplecza wielodostępnego działa również ze scenariuszami kończenia żądań SSL i kompleksowej usługi SSL.

![Scenariusz aplikacji internetowej](./media/application-gateway-web-app-overview/scenario.png)

Możliwość określenia przesłonięcia hosta jest definiowana w ustawieniach HTTP i może być stosowana do każdej puli zaplecza podczas tworzenia reguł. Wielodostępne zaplecza obsługują następujące dwa sposoby zastępowania nagłówka hosta i rozszerzenia SNI.

1. Możliwość ustawienia nazwy hosta na stałą wartość w ustawieniach HTTP. Ta funkcja zapewnia, że nagłówek hosta zostanie zastąpiony podaną wartością dla całego ruchu do puli zaplecza, w której są stosowane ustawienia HTTP. W przypadku korzystania z kompleksowej usługi SSL ta zastąpiona nazwa hosta jest używana w rozszerzeniu SNI. Ta funkcja umożliwia realizację scenariuszy, w których farma puli zaplecza oczekuje nagłówka hosta różniącego się od przychodzącego nagłówka hosta klienta.

2. Możliwość uzyskiwania nazwy hosta na podstawie adresu IP lub nazwy FQDN składowych puli zaplecza. Ustawienia HTTP udostępniają również możliwość wyboru nazwy hosta z nazwy FQDN składowej puli zaplecza, jeśli jest skonfigurowana z opcją pobierania nazwy hosta na podstawie poszczególnych składowych puli zaplecza. W przypadku korzystania z kompleksowej usługi SSL ta nazwa hosta jest określana na podstawie nazwy FQDN i używana w rozszerzeniu SNI. Ta funkcja umożliwia obsługę scenariuszy, w których pula zaplecza może mieć dwie lub więcej wielodostępnych usług PaaS, takich jak usługa Azure Web Apps, a nagłówek hosta żądania do każdego członka zawiera nazwę hosta pochodzącą od jego nazwy FQDN.

> [!NOTE]
> W obu powyższych przypadkach ustawienia mają wpływ tylko na aktywny ruch sieciowy, a nie na zachowanie sondy kondycji. Niestandardowe sondy już obsługują możliwość określenia nagłówka hosta w konfiguracji sondowania. Niestandardowe sondy obecnie obsługują również możliwość określania zachowania nagłówka hosta na podstawie aktualnie skonfigurowanych ustawień HTTP. Tę konfigurację można określić za pomocą parametru `PickHostNameFromBackendHttpSettings` w konfiguracji sondowania. Aby kompleksowa funkcja działała, ustawienia sondowania i ustawienia HTTP muszą zostać zmodyfikowane w celu odzwierciedlenia prawidłowej konfiguracji.

Korzystając z tej funkcji, klienci mogą określić opcje w ustawieniach HTTP i niestandardowe sondy na potrzeby prawidłowej konfiguracji. To ustawienie jest następnie wiązane z odbiornikiem i pulą zaplecza przy użyciu reguły.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować bramę aplikacji z aplikacją internetową jako składową puli zaplecza, odwiedzając stronę [Configure App Service web apps with Application Gateway (Konfigurowanie aplikacji internetowych usługi App Service za pomocą usługi Application Gateway)](application-gateway-web-app-powershell.md)
