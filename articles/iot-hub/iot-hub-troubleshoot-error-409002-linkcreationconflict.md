---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 409002 LinkCreationConflict
description: Dowiedz się, jak naprawić błąd 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960778"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

W tym artykule opisano przyczyny i rozwiązania dla **błędów 409002 LinkCreationConflict.**

## <a name="symptoms"></a>Objawy

Zostanie wyświetlony błąd **409002 LinkCreationConflict** zalogowany w dziennikach diagnostycznych wraz z odłączenie urządzenia lub błąd komunikatu chmury do urządzenia. 

<!-- When using AMQP? -->

## <a name="cause"></a>Przyczyna

Ogólnie rzecz biorąc ten błąd występuje, gdy Usługa IoT Hub wykryje, że klient ma więcej niż jedno połączenie. W rzeczywistości po odebraniu nowego żądania połączenia dla urządzenia z istniejącym połączeniem, Centrum IoT zamyka istniejące połączenie z tym błędem.

### <a name="cause-1"></a>Przyczyna 1

W najczęstszym przypadku osobny problem (na przykład [404104 DeviceConnectionClosedOneRemotely)](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)powoduje odłączenie urządzenia. Urządzenie próbuje natychmiast przywrócić połączenie, ale centrum IoT hub nadal uważa, że urządzenie jest podłączone. Usługa IoT Hub zamyka poprzednie połączenie i rejestruje ten błąd.

### <a name="cause-2"></a>Przyczyna 2

Błędna logika po stronie urządzenia powoduje, że urządzenie ustanawia połączenie, gdy jest już otwarte.

## <a name="solution"></a>Rozwiązanie

Ten błąd zwykle pojawia się jako efekt uboczny innego, przejściowego problemu, więc poszukaj innych błędów w dziennikach, aby rozwiązać dalsze problemy. W przeciwnym razie należy wydać nowe żądanie połączenia tylko wtedy, gdy połączenie spadnie.
