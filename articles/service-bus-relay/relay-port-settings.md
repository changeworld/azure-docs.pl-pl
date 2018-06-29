---
title: Ustawienia portów przekaźnika usługi Azure | Dokumentacja firmy Microsoft
description: Szczegółowe informacje o wartości portu przekaźnika usługi Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 56688b6941d58c0ecc8d0ff4ba3c8a8392e71496
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029377"
---
# <a name="azure-relay-port-settings"></a>Ustawienia portów przekaźnika usługi Azure

W poniższej tabeli opisano wymaganej konfiguracji dla wartości portu dla przekaźnika usługi Azure.

## <a name="hybrid-connections"></a>Połączenia hybrydowe

Połączenia hybrydowe używa protokołu Websocket na porcie 443 przy użyciu protokołu SSL jako podstawowy mechanizm transportu, który używa **HTTPS** tylko. 

## <a name="wcf-relays"></a>Przekaźniki WCF
  
|Powiązanie|Zabezpieczenia transportu|Port|  
|-------------|------------------------|----------|  
|[Klasa BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Yes|HTTPS| 
|" |Nie|HTTP|  
|[Klasa BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[Klasa NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klienta/usługa)|Albo|5671/9352/HTTP (9352/9353, jeśli przy użyciu hybrydowego)|  
|[Klasa NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |Nie|9350/HTTP|  
|[Klasa NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Yes|HTTPS|  
|" |Nie|HTTP|  
|[Klasa WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (usługa)|Albo|9351/HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Yes|HTTPS|  
|" |Nie|HTTP|  
|[Klasa WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (usługa)|Albo|9351/HTTP|

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat przekaźnika usługi Azure, odwiedź te linki:
* [Co to jest usługa Azure Relay?](relay-what-is-it.md)
* [Często zadawane pytania dotyczące usługi Relay](relay-faq.md)