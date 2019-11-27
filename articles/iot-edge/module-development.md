---
title: Tworzenie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Tworzenie niestandardowych modułów dla usługi Azure IoT Edge, który może komunikować się ze środowiskiem uruchomieniowym i Centrum IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 37f55165d1fea8a69d10003baeb0006199326cba
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456619"
---
# <a name="develop-your-own-iot-edge-modules"></a>Opracowywanie własnych modułów IoT Edge

Moduły Azure IoT Edge mogą łączyć się z innymi usługami platformy Azure i współtworzyć w większym potoku danych w chmurze. W tym artykule opisano sposób opracowywania modułów w celu komunikowania się z IoT Edge środowiska uruchomieniowego i IoT Hub, a w związku z tym w pozostałej części chmury platformy Azure. 

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe usługi IoT Edge zapewnia infrastrukturę do integracji funkcji wiele modułów usługi IoT Edge i wdrażania ich na urządzeniach usługi IoT Edge. Na wysokim poziomie można spakować każdy program jako moduł usługi IoT Edge. Jednak do wykorzystania zalet usługi IoT Edge, komunikacji i funkcje zarządzania, program działający w module można łączyć się z lokalnym Centrum IoT Edge, zintegrowane w środowisku uruchomieniowym usługi IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Przy użyciu Centrum usługi IoT Edge
Centrum usługi IoT Edge udostępnia dwie główne funkcje: serwer proxy do Centrum IoT i łączności lokalnej.

### <a name="iot-hub-primitives"></a>Elementy podstawowe usługi IoT Hub
Usługa IoT Hub będzie widział wystąpienia modułu, analogicznie do urządzenia, w tym sensie, że:

* ma dwuosiowy moduł, który jest odrębny i odizolowany od [sznurka urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innego modułu bliźniaczych reprezentacji tego urządzenia;
* może wysyłać [komunikaty z urządzenia do chmury](../iot-hub/iot-hub-devguide-messaging.md);
* może otrzymywać [bezpośrednie metody](../iot-hub/iot-hub-devguide-direct-methods.md) ukierunkowane na jego tożsamość.

Obecnie modułu nie może odbierać komunikaty z chmury do urządzenia korzystają z funkcji przekazywania plików.

Podczas pisania modułu można używać [zestawu SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md) do łączenia się z Centrum IoT Edge i korzystać z powyższych funkcji, tak jak w przypadku korzystania z IoT Hub z aplikacją urządzenia, jedyną różnicą jest to, że w zapleczu aplikacji należy odwołać się do tożsamości modułu zamiast tożsamości urządzenia.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury
Aby włączyć złożone przetwarzanie komunikatów przesyłanych z urządzeń do chmury, usługa IoT Edge Hub zapewnia deklaratywny Routing komunikatów między modułami oraz między modułami i IoT Hub. Deklaratywny routing zezwala na moduły, aby przechwycić i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowania ich potoki złożone. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów i ustanawianie tras w IoT Edge](module-composition.md).

Moduł usługi IoT Edge, w przeciwieństwie do normalnego aplikacja urządzenia usługi IoT Hub może odbierać komunikaty z urządzenia do chmury, których tworzone są przekazywane przez jego lokalne centrum IoT Edge w celu ich przetwarzania.

IoT Edge Hub propaguje komunikaty do modułu w oparciu o trasy deklaratywne opisane w [manifeście wdrożenia](module-composition.md). Podczas tworzenia modułu usługi IoT Edge, możesz otrzymać te komunikaty, ustawiając programy obsługi komunikatów.

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcję punktów końcowych *danych wejściowych* i *wyjściowych* modułu. Moduł może odbierać wszystkie komunikaty urządzenie chmura kierowane do niego bez określania żadnych danych i może wysyłać komunikaty z urządzenia do chmury bez określania żadnych danych wyjściowych. Za pomocą jawnego wejściami i wyjściami, jednak sprawia, że reguły routingu łatwiejsze do zrozumienia. 

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

Utwórz wystąpienie ModuleClient, aby połączyć moduł z Centrum IoT Edge uruchomionego na urządzeniu, podobnie jak wystąpienia DeviceClient połączą urządzenia IoT z IoT Hub. Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz Dokumentacja interfejsu API dla preferowanego języka SDK [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet):, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)lub [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Obsługa języków i architektury

IoT Edge obsługuje wiele systemów operacyjnych, architektury urządzeń i języki programowania, dzięki czemu można stworzyć scenariusz, który odpowiada Twoim potrzebom. Ta sekcja służy do poznania opcji tworzenia niestandardowych modułów IoT Edge. Więcej informacji o obsłudze narzędzi i wymaganiach dla każdego języka można znaleźć w temacie [Przygotowywanie środowiska deweloperskiego i testowego do IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 i ARM32 Linux. 

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Obsługa programowania i debugowania dla urządzeń z systemem ARM64 Linux jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [programowanie i debugowanie modułów IoT Edge arm64 w Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

W przypadku wszystkich języków w poniższej tabeli IoT Edge obsługuje programowanie dla urządzeń z systemem AMD64 Windows.

| Język programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (bez możliwości debugowania)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Następne kroki

[Przygotuj środowisko deweloperskie i testowe dla IoT Edge](development-environment.md)

[Korzystanie z programu Visual Studio C# do opracowywania modułów dla IoT Edge](how-to-visual-studio-develop-module.md)

[Użyj Visual Studio Code do opracowania modułów dla IoT Edge](how-to-vs-code-develop-module.md)

[Omówienie zestawów SDK IoT Hub platformy Azure i korzystanie z nich](../iot-hub/iot-hub-devguide-sdks.md)