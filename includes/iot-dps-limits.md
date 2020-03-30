---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77592434"
---
W poniższej tabeli wymieniono limity, które mają zastosowanie do zasobów usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba usług inicjowania obsługi urządzeń na subskrypcję platformy Azure | 10 |
| Maksymalna liczba rejestracji | 1 000 000 |
| Maksymalna liczba rejestracji | 1 000 000 |
| Maksymalna liczba grup rejestracji | 100 |
| Maksymalna liczba ce | 25 |
| Maksymalna liczba połączonych centrów IoT | 50 |
| Maksymalny rozmiar wiadomości | 96 KB|

> [!NOTE]
> Aby zwiększyć liczbę rejestracji i rejestracji w usłudze inicjowania obsługi administracyjnej, skontaktuj się z [pomocą techniczną firmy Microsoft](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Zwiększenie maksymalnej liczby ceów nie jest obsługiwane.

Usługa inicjowania obsługi administracyjnej urządzeń ogranicza żądania po przekroczeniu następujących przydziałów.

| Ograniczenie | Wartość jednostkowa |
| --- | --- |
| Operacje | 200/min/serwis |
| Rejestracje urządzeń | 200/min/serwis |
| Operacja sondowania urządzeń | 5/10 s/urządzenie |
