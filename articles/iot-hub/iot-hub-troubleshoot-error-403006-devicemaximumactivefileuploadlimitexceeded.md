---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Dowiedz się, jak naprawić błąd 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960843"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

W tym artykule opisano przyczyny i rozwiązania dla **403006 DeviceMaximumActiveFileUploadLimitExceedededed** błędów.

## <a name="symptoms"></a>Objawy

Żądanie przekazania pliku kończy się niepowodzeniem z kodem błędu **403006** i komunikatem "Liczba żądań aktywnego przekazywania plików nie może przekraczać 10".

## <a name="cause"></a>Przyczyna

Każdy klient urządzenia jest ograniczony do [10 równoczesnych plików.](./iot-hub-devguide-quotas-throttling.md#other-limits) 

Możesz łatwo przekroczyć limit, jeśli urządzenie nie powiadamia Usługi IoT Hub po zakończeniu przekazywania plików. Ten problem jest często spowodowany przez za zawodną sieć po stronie urządzenia.

## <a name="solution"></a>Rozwiązanie

Upewnij się, że urządzenie może niezwłocznie [powiadomić zakończenie przekazywania plików usługi IoT Hub](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Następnie spróbuj zmniejszyć czas [wygaśnięcia tokenu sygnatury dostępu Współdzielonego dla konfiguracji przekazywania plików](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o przekazywaniu plików, zobacz [Przekazywanie plików z Centrum IoT](./iot-hub-devguide-file-upload.md) i [Konfigurowanie przekazywania plików usługi IoT Hub za pomocą witryny Azure portal](./iot-hub-configure-file-upload.md).