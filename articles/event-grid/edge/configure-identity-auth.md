---
title: Konfigurowanie IoT Edge tożsamości Azure Event Grid | Microsoft Docs
description: Skonfiguruj tożsamość modułu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992446"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Skonfiguruj tożsamość modułu Event Grid

W tym artykule przedstawiono przykłady możliwych konfiguracji tożsamości dla modułu Event Grid. Domyślnie moduł Event Grid będzie miał certyfikat tożsamości skonfigurowany przez demona zabezpieczeń IoT. Certyfikat tożsamości jest prezentowany przez moduł Event Grid w wychodzących wywołaniach, który jest w przypadku dostarczania zdarzeń. Subskrybent zdarzenia Event Grid może następnie sprawdzić, czy jest on rzeczywiście modułem Event Grid, który wysłał zdarzenie przed zaakceptowaniem zdarzenia.

Wszystkie możliwe konfiguracje można znaleźć w przewodniku dotyczącym [zabezpieczeń i uwierzytelniania](security-authentication.md) .

## <a name="always-present-identity-certificate"></a>Zawsze obecny certyfikat tożsamości
Oto Przykładowa konfiguracja, która zawsze przedstawia certyfikat tożsamości na wywołaniach wychodzących. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Nie podano certyfikatu tożsamości
Oto Przykładowa konfiguracja nieprzedstawiania certyfikatu tożsamości dla wywołań wychodzących. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
