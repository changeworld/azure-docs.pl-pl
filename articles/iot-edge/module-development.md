---
title: Tworzenie modułów dla usługi Azure IoT Edge | Dokumenty firmy Microsoft
description: Tworzenie niestandardowych modułów dla usługi Azure IoT Edge, które mogą komunikować się ze środowiska wykonawczego i Usługi IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271384"
---
# <a name="develop-your-own-iot-edge-modules"></a>Opracowanie własnych modułów IoT Edge

Moduły usługi Azure IoT Edge mogą łączyć się z innymi usługami platformy Azure i współtworzyć większy potok danych w chmurze. W tym artykule opisano, jak można tworzyć moduły do komunikowania się ze środowiska wykonawczego usługi IoT Edge i Usługi IoT Hub, a zatem w pozostałej części chmury platformy Azure.

## <a name="iot-edge-runtime-environment"></a>Środowisko wykonawcze IoT Edge

Środowisko uruchomieniowe usługi IoT Edge udostępnia infrastrukturę do integracji funkcji wielu modułów usługi IoT Edge i wdrażania ich na urządzeniach usługi IoT Edge. Każdy program może być spakowany jako moduł IoT Edge. Aby w pełni korzystać z funkcji komunikacji i zarządzania usługi IoT Edge, program uruchomiony w module może używać sdk urządzeń Usługi Azure IoT do łączenia się z lokalnym centrum usługi IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Korzystanie z koncentratora IoT Edge

Centrum usługi IoT Edge udostępnia dwie główne funkcje: serwer proxy do usługi IoT Hub i komunikację lokalną.

### <a name="iot-hub-primitives"></a>Prymitywy IoT Hub

Centrum IoT hub widzi wystąpienie modułu analogicznie do urządzenia, w tym sensie, że:

* ma bliźniaczącze modułu, który jest odrębny i odizolowany od [bliźniaczej części urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innych bliźniaczych modułów tego urządzenia;
* może wysyłać [wiadomości z urządzenia do chmury;](../iot-hub/iot-hub-devguide-messaging.md)
* może otrzymać [bezpośrednie metody](../iot-hub/iot-hub-devguide-direct-methods.md) ukierunkowane w szczególności na jego tożsamość.

Obecnie moduły nie mogą odbierać wiadomości z chmury do urządzenia ani korzystać z funkcji przekazywania plików.

Podczas pisania modułu można użyć [zestawu SDK urządzenia usługi Azure IoT,](../iot-hub/iot-hub-devguide-sdks.md) aby połączyć się z centrum usługi IoT Edge i korzystać z powyższych funkcji, tak jak w przypadku korzystania z usługi IoT Hub z aplikacją urządzenia. Jedyną różnicą między modułami usługi IoT Edge i aplikacjami urządzeń IoT jest to, że musisz odwołać się do tożsamości modułu zamiast tożsamości urządzenia.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury

Aby umożliwić złożone przetwarzanie komunikatów między urządzeniami do chmury, centrum usługi IoT Edge zapewnia deklaratywne routingu wiadomości między modułami oraz między modułami i Centrum IoT Hub. Routing deklaratywny umożliwia modułom przechwytywanie i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowanie ich do złożonych potoków. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów i ustanawianie tras w uliczce IoT.](module-composition.md)

Moduł usługi IoT Edge, w przeciwieństwie do normalnej aplikacji urządzenia Usługi IoT Hub, może odbierać komunikaty między urządzeniami do chmury, które są proxied przez jego lokalnego centrum usługi IoT Edge do ich przetwarzania.

Centrum usługi IoT Edge propaguje komunikaty do modułu na podstawie tras deklaratywnych opisanych w [manifeście wdrażania.](module-composition.md) Podczas tworzenia modułu usługi IoT Edge, można odbierać te komunikaty, ustawiając programy obsługi wiadomości.

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcję *wejściowych* i *wyjściowych* punktów końcowych modułu. Moduł może odbierać wszystkie komunikaty między urządzeniami do chmury kierowane do niego bez określania żadnych danych wejściowych i może wysyłać komunikaty z urządzenia do chmury bez określania żadnych danych wyjściowych. Przy użyciu jawnych danych wejściowych i wyjściowych, jednak sprawia, że reguły routingu prostsze do zrozumienia.

Na koniec komunikaty między urządzeniami do chmury obsługiwane przez koncentratora usługi Edge są stemplowane następującymi właściwościami systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, który wysłał wiadomość |
| $connectionModuleId | Identyfikator modułu, który wysłał wiadomość |
| $inputName | Dane wejściowe, które odebrały ten komunikat. Może być pusty. |
| $outputName | Dane wyjściowe używane do wysyłania wiadomości. Może być pusty. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Łączenie się z koncentratorem IoT Edge z modułu

Połączenie z lokalnym centrum usługi IoT Edge z modułu wymaga dwóch kroków:

1. Tworzenie wystąpienia ModuleClient w aplikacji.
2. Upewnij się, że aplikacja akceptuje certyfikat przedstawiony przez centrum usługi IoT Edge na tym urządzeniu.

Utwórz wystąpienie ModuleClient, aby połączyć moduł z koncentratorem usługi IoT Edge działającym na urządzeniu, podobnie jak wystąpienia DeviceClient łączą urządzenia IoT z centrum IoT Hub. Aby uzyskać więcej informacji na temat klasy ModuleClient i jej metod komunikacji, zobacz odwołanie do interfejsu API preferowanego języka SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)lub [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Obsługa języka i architektury

Usługi IoT Edge obsługuje wiele systemów operacyjnych, architektur urządzeń i języków programowania, dzięki czemu można utworzyć scenariusz odpowiadający Twoim potrzebom. Ta sekcja służy do zapoznania się z opcjami tworzenia niestandardowych modułów usługi IoT Edge. Więcej informacji na temat obsługi narzędzi i wymagań dotyczących poszczególnych języków można dowiedzieć się więcej na temat obsługi narzędzi i wymagań dotyczących każdego języka w [aplikacji Przygotowanie środowiska deweloperskiego i testowego dla aplikacji IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Dla wszystkich języków w poniższej tabeli IoT Edge obsługuje rozwój dla urządzeń AMD64 i ARM32 Linux.

| Języka programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Tworzenie i debugowanie wsparcie dla urządzeń ARM64 Linux jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [Tworzenie i debugowanie modułów usługi ARM64 IoT Edge w programie Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Dla wszystkich języków w poniższej tabeli usługa IoT Edge obsługuje program rozwoju dla urządzeń z systemem AMD64 z systemem Windows.

| Języka programowania | Narzędzia programistyczne |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (brak możliwości debugowania)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Następne kroki

[Przygotowanie środowiska deweloperskiego i testowego dla aplikacji IoT Edge](development-environment.md)

[Tworzenie modułów języka C# dla programu IoT Edge za pomocą programu Visual Studio](how-to-visual-studio-develop-module.md)

[Tworzenie modułów dla programu IoT Edge za pomocą programu Visual Studio Code](how-to-vs-code-develop-module.md)

[Opis zestawów SDK usługi Azure IoT Hub i korzystania z niej](../iot-hub/iot-hub-devguide-sdks.md)
