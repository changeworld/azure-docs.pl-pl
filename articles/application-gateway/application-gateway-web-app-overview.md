---
title: Omówienie wielodostępnych ponownie kończy się za pomocą usługi Azure Application Gateway
description: Ta strona zawiera omówienie obsługi wielodostępnych zapleczy w usłudze Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/1/2018
ms.author: victorh
ms.openlocfilehash: 8e5f48e42a2a677622dae0b733f9d5af484d1c51
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605810"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Obsługa wielodostępnych zapleczy w usłudze Application Gateway

Usługa Azure Application Gateway obsługuje zestawy skalowania maszyn wirtualnych, interfejsami sieciowymi, publiczny/prywatny adres IP lub w pełni kwalifikowaną nazwę (FQDN) w ramach swoich pul zaplecza. Domyślnie usługa Application Gateway nie zmienia przychodzącego nagłówka hosta HTTP z klienta i wysyła niezmieniony nagłówek do zaplecza. Istnieje wiele usług, takich jak [usługi Azure App Service](../app-service/overview.md) które są wielodostępne z natury i polegają na konkretnym nagłówku hosta lub rozszerzeniu SNI przy rozpoznawaniu właściwego punktu końcowego. Usługa Application Gateway obecnie umożliwia użytkownikom zastępowanie przychodzącego nagłówka hosta HTTP na podstawie ustawień HTTP zaplecza. Ta funkcja umożliwia obsługę wielodostępnych zapleczy usług Azure Web Apps i API Management. Ta możliwość jest dostępna dla standardowej jednostki SKU i jednostki SKU zapory aplikacji internetowych. Obsługa zaplecza wielu dzierżaw współpracuje również z kończenia żądań SSL i typu end to end SSL scenariuszy.

> [!NOTE]
> Konfiguracja certyfikatu uwierzytelniania nie jest wymagana dla zaufanych usług platformy Azure, takich jak Azure Web Apps.

![Scenariusz aplikacji internetowej](./media/application-gateway-web-app-overview/scenario.png)

Możliwość określenia przesłonięcia hosta jest zdefiniowany w ustawieniach HTTP i mogą być stosowane do każdej puli zaplecza podczas tworzenia reguł. Wielodostępne zaplecza obsługują następujące dwa sposoby zastępowania nagłówka hosta i rozszerzenia SNI.

1. Możliwość ustawienia nazwy hosta na stałą wartość w ustawieniach HTTP. Ta funkcja zapewnia, że nagłówek hosta zostanie zastąpiony podaną wartością dla całego ruchu do puli zaplecza, w których są stosowane ustawienia HTTP. W przypadku korzystania z kompleksowej usługi SSL ta zastąpiona nazwa hosta jest używana w rozszerzeniu SNI. Ta funkcja umożliwia realizację scenariuszy, w których farma puli zaplecza oczekuje nagłówka hosta, który różni się od przychodzącego nagłówka hosta klienta.

2. Możliwość uzyskiwania nazwy hosta z adresu IP lub nazwa FQDN składowych puli zaplecza. Ustawienia HTTP udostępniają również możliwość wyboru nazwy hosta z nazwy FQDN składowej puli zaplecza, jeśli skonfigurowane z opcją pobierania nazwy hosta z elementu członkowskiego poszczególnych puli zaplecza. W przypadku korzystania z kompleksowej usługi SSL ta nazwa hosta jest określana na podstawie nazwy FQDN i używana w rozszerzeniu SNI. Ta funkcja umożliwia obsługę scenariuszy, w których pula zaplecza może mieć dwóch lub więcej wielodostępnych usług PaaS w takich jak aplikacje sieci web platformy Azure i nagłówek hosta żądania do każdego członka zawiera nazwę hosta pochodzącą od jego nazwy FQDN.

> [!NOTE]
> W obu powyższych przypadkach ustawienia mają wpływ tylko na aktywny ruch sieciowy, a nie na zachowanie sondy kondycji. Niestandardowe sondy już obsługują możliwość określenia nagłówka hosta w konfiguracji sondowania. Niestandardowe sondy obecnie obsługują również możliwość określania zachowania nagłówka hosta na podstawie aktualnie skonfigurowanych ustawień HTTP. Tę konfigurację można określić za pomocą parametru `PickHostNameFromBackendHttpSettings` w konfiguracji sondowania. Aby kompleksowa funkcja działała, ustawienia sondowania i ustawienia HTTP muszą zostać zmodyfikowane w celu odzwierciedlenia prawidłowej konfiguracji.

Korzystając z tej funkcji, klienci mogą określić opcje w ustawieniach HTTP i niestandardowe sondy na potrzeby prawidłowej konfiguracji. To ustawienie jest następnie wiązane z odbiornikiem i pulą zaplecza przy użyciu reguły.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak skonfigurować bramy aplikacji z aplikacją internetową jako składową puli zaplecza, odwiedzając: [Konfigurowanie aplikacji sieci web usługi App Service z usługą Application Gateway](application-gateway-web-app-powershell.md)
