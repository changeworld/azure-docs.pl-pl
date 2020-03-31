---
title: Rozwiązywanie problemów z błędami wewnętrznymi usługi Azure IoT Hub 500xxx
description: Dowiedz się, jak naprawić błędy wewnętrzne 500xxx
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271046"
---
# <a name="500xxx-internal-errors"></a>500xxx błędy wewnętrzne

W tym artykule opisano przyczyny i rozwiązania **błędów wewnętrznych 500xxx**.

## <a name="symptoms"></a>Objawy

Żądanie do usługi IoT Hub kończy się niepowodzeniem z powodu błędu, który zaczyna się od 500 i/lub jakiegoś "błędu serwera". Niektóre możliwości to:

* **500001 ServerError**: Usługa IoT Hub napotkała problem po stronie serwera.

* **500008 GenericTimeout:** Centrum IoT nie może ukończyć żądania połączenia przed przesuniem limitu czasu.

* **ServiceUnavailable (brak kodu błędu)**: Usługa IoT Hub napotkał błąd wewnętrzny.

* **InternalServerError (bez kodu błędu)**: IoT Hub napotkał błąd wewnętrzny.

## <a name="cause"></a>Przyczyna

Może istnieć wiele przyczyn odpowiedzi na błąd 500xxx. We wszystkich przypadkach problem jest najprawdopodobniej przejściowy. Podczas gdy zespół usługi IoT Hub ciężko pracuje, aby utrzymać [umowy SLA,](https://azure.microsoft.com/support/legal/sla/iot-hub/)małe podzbiory węzłów usługi IoT hub mogą czasami doświadczać błędów przejściowych. Gdy urządzenie próbuje połączyć się z węzłem, który ma problemy, pojawia się ten błąd.

## <a name="solution"></a>Rozwiązanie

Aby ograniczyć błędy 500xxx, ponów próbę z urządzenia. Aby [automatycznie zarządzać ponownymi próbami,](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)upewnij się, że używasz najnowszej wersji [zestawów SDK usługi Azure IoT](./iot-hub-devguide-sdks.md). Aby uzyskać najlepsze rozwiązania dotyczące obsługi błędów przejściowych i ponownych prób, zobacz [Obsługa błędów przejściowych](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Jeśli problem będzie się powtarzał, sprawdź [kondycję zasobów](./iot-hub-monitor-resource-health.md#use-azure-resource-health) i [stan platformy Azure,](https://status.azure.com/) aby sprawdzić, czy usługa IoT Hub ma znany problem. Można również użyć [funkcji ręcznego pracy awaryjnej](./tutorial-manual-failover.md). Jeśli nie ma znanych problemów i problem będzie kontynuowany, [skontaktuj się z pomocą techniczną](https://azure.microsoft.com/support/options/) w celu dalszego zbadania.
