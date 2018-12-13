---
title: Tworzenie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie niestandardowych modułów dla usługi Azure IoT Edge, który może komunikować się ze środowiskiem uruchomieniowym i Centrum IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 50e9c0667761e43c63b03fbaf5f8ce93eb49e749
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100093"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Zrozumienie wymagań i narzędzia do tworzenia modułów usługi IoT Edge

W tym artykule opisano, jakie funkcje są dostępne podczas pisania aplikacji, które są uruchamiane jako moduł usługi IoT Edge i jak z nich skorzystać.

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe usługi IoT Edge zapewnia infrastrukturę do integracji funkcji wiele modułów usługi IoT Edge i wdrażania ich na urządzeniach usługi IoT Edge. Na wysokim poziomie można spakować każdy program jako moduł usługi IoT Edge. Jednak do wykorzystania zalet usługi IoT Edge, komunikacji i funkcje zarządzania, program działający w module można łączyć się z lokalnym Centrum IoT Edge, zintegrowane w środowisku uruchomieniowym usługi IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Przy użyciu Centrum usługi IoT Edge
Centrum usługi IoT Edge udostępnia dwie główne funkcje: serwer proxy do Centrum IoT i łączności lokalnej.

### <a name="iot-hub-primitives"></a>Elementy podstawowe usługi IoT Hub
Usługa IoT Hub będzie widział wystąpienia modułu, analogicznie do urządzenia, w tym sensie, że:

* ma ona bliźniaczą reprezentację modułu, który jest odrębna i odizolowane od [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innych bliźniaczych reprezentacjach modułów tych urządzeń;
* może wysłać [komunikatów z urządzenia do chmury](../iot-hub/iot-hub-devguide-messaging.md);
* może ona odbierać [metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md) przeznaczone specjalnie dla swojej tożsamości.

Obecnie modułu nie może odbierać komunikaty z chmury do urządzenia korzystają z funkcji przekazywania plików.

Podczas zapisywania modułu, można użyć [zestawu SDK urządzenia IoT Azure](../iot-hub/iot-hub-devguide-sdks.md) połączyć się z Centrum IoT Edge i korzystać z funkcji powyżej, tak jak w przypadku za pomocą usługi IoT Hub za pomocą aplikacji urządzenia, jedyną różnicą jest, z poziomu aplikacji serwer zaplecza należy odwoływać się do tożsamości modułu, a nie tożsamości urządzenia.

Zobacz [tworzenie i wdrażanie moduł usługi IoT Edge na symulowanym urządzeniu](tutorial-csharp-module.md) na przykład aplikacja modułu, która wysyła komunikaty z urządzenia do chmury i korzysta z bliźniaczej reprezentacji modułu.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury
Aby włączyć złożone przetwarzanie komunikatów przesyłanych z chmury do urządzenia usługi IoT Edge hub udostępnia deklaratywny routing komunikatów między modułami, a także między modułami i IoT Hub. Deklaratywny routing zezwala na moduły, aby przechwycić i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowania ich potoki złożone. Artykuł [budowy modułu](module-composition.md) wyjaśnia sposób tworzenia modułów potoki złożonych przy użyciu trasy.

Moduł usługi IoT Edge, w przeciwieństwie do normalnego aplikacja urządzenia usługi IoT Hub może odbierać komunikaty z urządzenia do chmury, których tworzone są przekazywane przez jego lokalne centrum IoT Edge w celu ich przetwarzania.

Centrum usługi IoT Edge propaguje komunikaty do modułu oparte na trasach deklaratywne opisanego w [budowy modułu](module-composition.md) artykułu. Podczas tworzenia modułu usługi IoT Edge, możesz otrzymać te komunikaty, ustawiając programy obsługi komunikatów.

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcji modułu *wejściowych* i *dane wyjściowe* punktów końcowych. Moduł może odbierać wszystkie komunikaty urządzenie chmura kierowane do niego bez określania żadnych danych i może wysyłać komunikaty z urządzenia do chmury bez określania żadnych danych wyjściowych.
Za pomocą jawnego wejściami i wyjściami, jednak sprawia, że reguły routingu łatwiejsze do zrozumienia. Aby uzyskać więcej informacji na temat reguł routingu i wejściowych i wyjściowych punktów końcowych dla modułów, zobacz [budowy modułu](module-composition.md).

Na koniec komunikatów przesyłanych z chmury do urządzenia obsługiwane przez Centrum usługi Edge są oznaczone zgodnie z poniższymi właściwościami systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, która wysłała komunikat |
| $connectionModuleId | Identyfikator modułu modułu, który wysłał wiadomość |
| $inputName | Dane wejściowe, który otrzymał ten komunikat. Może być pusta. |
| $outputName | Dane wyjściowe, używane do wysyłania wiadomości. Może być pusta. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Łączenie Centrum IoT Edge z modułu
Łączenie z lokalnym Centrum IoT Edge z modułu obejmuje dwa kroki: 
1. Użyj parametrów połączenia, udostępniane przez środowisko uruchomieniowe usługi IoT Edge podczas uruchamiania modułu.
2. Upewnij się, że Twoja aplikacja akceptuje certyfikat przedstawiony przez Centrum usługi IoT Edge na tym urządzeniu.

Ciąg połączenia używany jest wprowadzony przez środowisko uruchomieniowe usługi IoT Edge w zmiennej środowiskowej `EdgeHubConnectionString`. Dzięki temu dostępne dla dowolnego programu, który chce używać go.

Analogicznie, certyfikat służący do weryfikowania połączenia Centrum IoT Edge jest wprowadzony przez środowisko uruchomieniowe usługi IoT Edge w pliku, w których ścieżka jest dostępna w zmiennej środowiskowej `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, po tworzenia modułu jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

