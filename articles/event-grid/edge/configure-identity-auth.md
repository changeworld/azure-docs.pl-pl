---
title: Konfigurowanie tożsamości — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Konfigurowanie tożsamości modułu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841769"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Konfigurowanie tożsamości dla modułu Siatki zdarzeń

W tym artykule pokazano, jak skonfigurować tożsamość siatki na krawędzi. Domyślnie moduł Event Grid przedstawia swój certyfikat tożsamości skonfigurowany przez demona zabezpieczeń IoT. Usługa Event Grid on Edge przedstawia swój certyfikat tożsamości z wywołaniami wychodzącymi, gdy dostarcza zdarzenia. Subskrybent może następnie sprawdzić poprawność to moduł siatki zdarzeń, który wysłał zdarzenie przed zaakceptowaniem.

Zobacz Przewodnik [zabezpieczeń i uwierzytelniania](security-authentication.md) dla wszystkich możliwych konfiguracji.

## <a name="always-present-identity-certificate"></a>Zawsze prezentuj certyfikat tożsamości
Oto przykładowa konfiguracja do zawsze prezentowania certyfikatu tożsamości w połączeniach wychodzących. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nie okazuj certyfikatu tożsamości
Oto przykładowa konfiguracja nieprezentowania certyfikatu tożsamości w połączeniach wychodzących. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
