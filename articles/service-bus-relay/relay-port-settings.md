---
title: Ustawienia portu usługi Azure Relay | Dokumenty firmy Microsoft
description: Ten artykuł zawiera tabelę opisującą wymaganą konfigurację wartości portów dla usługi Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 55bb6b620c7ba9ee13068213cbc867b165acc185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514888"
---
# <a name="azure-relay-port-settings"></a>Ustawienia portu usługi Azure Relay

W poniższej tabeli opisano wymaganą konfigurację wartości portów dla usługi Azure Relay.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe używa WebSockets na porcie 443 z SSL jako podstawowy mechanizm transportu, który używa tylko **HTTPS.** 

## <a name="wcf-relays"></a>Przekaźniki WCF
  
|Wiązanie|Zabezpieczenia transportu|Port|  
|-------------|------------------------|----------|  
|[Podstawowa klasaHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Tak|HTTPS| 
|" |Nie|HTTP|  
|[Podstawowa klasaHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/usługa)|Albo|5671/9352/HTTP (9352/9353 w przypadku używania hybrydy)|  
|[Klasa NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Tak|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[NetOnewayRelayBinding Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa powiązania z sieci Web (klient)](/dotnet/api/microsoft.servicebus.webhttprelaybinding)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[Klasa powiązania z sieci Web (usługa)](/dotnet/api/microsoft.servicebus.webhttprelaybinding)|Albo|9351/HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Tak|HTTPS|  
|" |Nie|HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Albo|9351/HTTP|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o usłudze Azure Relay, odwiedź następujące łącza:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)