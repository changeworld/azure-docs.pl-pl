---
title: Tworzenie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie niestandardowych modułów dla usługi Azure IoT Edge, który może komunikować się ze środowiskiem uruchomieniowym i Centrum IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5285490ca1a27494cbcd3ea3d6527b78c7d38c8c
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833437"
---
# <a name="develop-your-own-iot-edge-modules"></a>Twórz własne moduły usługi IoT Edge

Moduły usługi IoT Edge platformy Azure można połączyć się z innymi usługami platformy Azure i Współtworzenie większych potoku danych chmury. W tym artykule opisano, jak można tworzyć modułów do komunikowania się ze środowiska uruchomieniowego usługi IoT Edge i IoT Hub oraz w związku z tym rest chmury platformy Azure. 

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

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury
Aby włączyć złożone przetwarzanie komunikatów przesyłanych z chmury do urządzenia usługi IoT Edge hub udostępnia deklaratywny routing komunikatów między modułami, a także między modułami i IoT Hub. Deklaratywny routing zezwala na moduły, aby przechwycić i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowania ich potoki złożone. Aby uzyskać więcej informacji, zobacz [wdrażać moduły oraz określenia trasy w usługi IoT Edge](module-composition.md).

Moduł usługi IoT Edge, w przeciwieństwie do normalnego aplikacja urządzenia usługi IoT Hub może odbierać komunikaty z urządzenia do chmury, których tworzone są przekazywane przez jego lokalne centrum IoT Edge w celu ich przetwarzania.

Centrum usługi IoT Edge propaguje komunikaty do modułu oparte na trasach deklaratywne opisanego w [manifest wdrożenia](module-composition.md). Podczas tworzenia modułu usługi IoT Edge, możesz otrzymać te komunikaty, ustawiając programy obsługi komunikatów.

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcji modułu *danych wejściowych* i *dane wyjściowe* punktów końcowych. Moduł może odbierać wszystkie komunikaty urządzenie chmura kierowane do niego bez określania żadnych danych i może wysyłać komunikaty z urządzenia do chmury bez określania żadnych danych wyjściowych. Za pomocą jawnego wejściami i wyjściami, jednak sprawia, że reguły routingu łatwiejsze do zrozumienia. 

Na koniec komunikatów przesyłanych z chmury do urządzenia obsługiwane przez Centrum usługi Edge są oznaczone zgodnie z poniższymi właściwościami systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, która wysłała komunikat |
| $connectionModuleId | Identyfikator modułu modułu, który wysłał wiadomość |
| $inputName | Dane wejściowe, który otrzymał ten komunikat. Może być pusta. |
| $outputName | Dane wyjściowe, używane do wysyłania wiadomości. Może być pusta. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Łączenie Centrum IoT Edge z modułu
Łączenie z lokalnym Centrum IoT Edge z modułu obejmuje dwa kroki: 
1. Utwórz wystąpienie ModuleClient w aplikacji.
2. Upewnij się, że Twoja aplikacja akceptuje certyfikat przedstawiony przez Centrum usługi IoT Edge na tym urządzeniu.

Utwórz wystąpienie ModuleClient do łączenia z modułu do Centrum IoT Edge, działające na urządzeniu, podobnie jak wystąpień DeviceClient połączyć urządzenia IoT do usługi IoT Hub. Aby uzyskać więcej informacji na temat klasy ModuleClient i jego metod komunikacji zobacz dokumentacja interfejsu API preferowanego języka zestawu SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C i Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), lub [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).


## <a name="next-steps"></a>Kolejne kroki

[Przygotuj swój rozwój i środowisko testowe dla usługi IoT Edge](development-environment.md)

[Programowanie przy użyciu programu Visual Studio C# moduły usługi IoT Edge](how-to-visual-studio-develop-module.md)

[Użyj programu Visual Studio Code do tworzenia modułów dla usługi IoT Edge](how-to-vs-code-develop-module.md)

