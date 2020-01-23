---
title: Ustawienia portów Azure Relay | Microsoft Docs
description: Ten artykuł zawiera tabelę opisującą wymaganą konfigurację wartości portów dla Azure Relay.
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
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514888"
---
# <a name="azure-relay-port-settings"></a>Ustawienia portu Azure Relay

W poniższej tabeli opisano wymaganą konfigurację dla wartości portów dla Azure Relay.

## <a name="hybrid-connections"></a>Hybrydowe

Połączenia hybrydowe używa obiektów WebSockets na porcie 443 z protokołem SSL jako podstawowego mechanizmu transportowego, który używa tylko **protokołu HTTPS** . 

## <a name="wcf-relays"></a>Przekaźniki WCF
  
|Powiązanie|Zabezpieczenia transportu|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Tak|HTTPS| 
|” |Nie|HTTP|  
|[BasicHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[NetEventRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Tak|9351/HTTPS|  
|” |Nie|9350/HTTP|  
|[NetEventRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Albo|9351/HTTP|  
|[NetTcpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/usługa)|Albo|5671/9352/HTTP (9352/9353, jeśli używasz hybrydowej)|  
|[NetOnewayRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Tak|9351/HTTPS|  
|” |Nie|9350/HTTP|  
|[NetOnewayRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Albo|9351/HTTP|  
|[WebHttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Tak|HTTPS|  
|” |Nie|HTTP|  
|[WebHttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[WS2007HttpRelayBinding — Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Tak|HTTPS|  
|” |Nie|HTTP|  
|[WS2007HttpRelayBinding, Klasa](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Albo|9351/HTTP|

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat Azure Relay, odwiedź następujące linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)