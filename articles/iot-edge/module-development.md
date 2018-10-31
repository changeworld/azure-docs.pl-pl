---
title: Tworzenie modułów dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć niestandardowe moduły dla usługi Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 761485de4bf52b7261ac8f1f8c3d937486c66546
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248004"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Zrozumienie wymagań i narzędzia do tworzenia modułów usługi IoT Edge

W tym artykule opisano, jakie funkcje są dostępne podczas pisania aplikacji, które są uruchamiane jako moduł usługi IoT Edge i jak z nich skorzystać.

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe usługi IoT Edge zapewnia infrastrukturę do integracji funkcji wiele modułów usługi IoT Edge i wdrażania ich na urządzeniach usługi IoT Edge. Na wysokim poziomie można spakować każdy program jako moduł usługi IoT Edge. Jednak do wykorzystania zalet usługi IoT Edge, komunikacji i funkcje zarządzania, program działający w module można łączyć się z lokalnym Centrum IoT Edge, zintegrowane w środowisku uruchomieniowym usługi IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Przy użyciu Centrum usługi IoT Edge
Centrum usługi IoT Edge udostępnia dwie główne funkcje: serwer proxy do Centrum IoT i łączności lokalnej.

### <a name="iot-hub-primitives"></a>Elementy podstawowe usługi IoT Hub
IoT Hub widzi moduł wystąpienia analogicznie do urządzenia, w tym sensie, że:

* ma ona bliźniaczą reprezentację modułu, który jest odrębna i odizolowane od [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i innych bliźniaczych reprezentacjach modułów tych urządzeń;
* może wysłać [komunikatów z urządzenia do chmury](../iot-hub/iot-hub-devguide-messaging.md);
* może ona odbierać [metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md) przeznaczone specjalnie dla swojej tożsamości.

Obecnie modułu nie może odbierać komunikaty z chmury do urządzenia korzystają z funkcji przekazywania plików.

Podczas zapisywania modułu, można po prostu użyć [zestawu SDK urządzenia IoT Azure](../iot-hub/iot-hub-devguide-sdks.md) połączyć się z Centrum IoT Edge i korzystać z funkcji powyżej, tak jak w przypadku za pomocą usługi IoT Hub za pomocą aplikacji urządzenia, jedyną różnicą, że pochodzącej z usługi zaplecze aplikacji, należy odwołać się do odpowiedniej tożsamości modułu, a nie tożsamości urządzenia.

Zobacz [tworzenie i wdrażanie moduł usługi IoT Edge na symulowanym urządzeniu](tutorial-csharp-module.md) na przykład aplikacja modułu, która wysyła komunikaty z urządzenia do chmury i korzysta z bliźniaczej reprezentacji modułu.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury
Aby włączyć złożone przetwarzanie komunikatów przesyłanych z chmury do urządzenia usługi IoT Edge hub udostępnia deklaratywny routing komunikatów między modułami, a także między modułami i IoT Hub.
Dzięki temu modułów, aby przechwycić i przetwarzanie komunikatów wysyłanych przez inne moduły i propagowania ich potoki złożone.
Artykuł [budowy modułu](module-composition.md) wyjaśnia sposób tworzenia modułów potoki złożonych przy użyciu trasy.

Moduł usługi IoT Edge, inaczej niż normalna aplikacja urządzenia usługi IoT Hub może odbierać komunikaty z urządzenia do chmury, których tworzone są przekazywane przez jego lokalne centrum IoT Edge, aby można było je przetworzyć.

Centrum usługi IoT Edge propaguje komunikaty do modułu oparte na trasach deklaratywne opisanego w [budowy modułu](module-composition.md) artykułu. Podczas tworzenia modułu usługi IoT Edge, może pojawiają się następujące komunikaty, ustawiając programy obsługi komunikatów, jak pokazano w tym samouczku [tworzenie i wdrażanie moduł usługi IoT Edge na symulowanym urządzeniu] [lnk-tutorial2].

Aby uprościć tworzenie tras, IoT Edge dodaje koncepcji modułu *wejściowych* i *dane wyjściowe* punktów końcowych. Moduł może odbierać wszystkie komunikaty urządzenie chmura kierowane do niego bez określania żadnych danych i może wysyłać komunikaty z urządzenia do chmury bez określania żadnych danych wyjściowych.
Za pomocą jawnego wejściami i wyjściami, jednak sprawia, że reguły routingu łatwiejsze do zrozumienia. Zobacz [budowy modułu](module-composition.md) Aby uzyskać więcej informacji na temat reguł routingu i wejściowych i wyjściowych punktów końcowych dla modułów.

Na koniec komunikatów przesyłanych z chmury do urządzenia obsługiwane przez Centrum usługi Edge są oznaczone zgodnie z poniższymi właściwościami systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, która wysłała komunikat |
| $connectionModuleId | Identyfikator modułu modułu, który wysłał wiadomość |
| $inputName | Dane wejściowe, który otrzymał ten komunikat. Może być pusta. |
| $outputName | Dane wyjściowe, używane do wysyłania wiadomości. Może być pusta. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Łączenie Centrum IoT Edge z modułu
Łączenie z lokalnym Centrum IoT Edge z modułu obejmuje dwa kroki: Użyj parametrów połączenia, dostarczone przez środowisko uruchomieniowe usługi IoT Edge podczas uruchamiania modułu i upewnij się, że Twoja aplikacja akceptuje certyfikat przedstawiony przez Centrum usługi IoT Edge na tym urządzeniu.

Ciąg połączenia używany jest wprowadzony przez środowisko uruchomieniowe usługi IoT Edge w zmiennej środowiskowej `EdgeHubConnectionString`. Dzięki temu dostępne dla dowolnego programu, który chce używać go.

Analogicznie, certyfikat służący do weryfikowania połączenia Centrum IoT Edge jest wprowadzony przez środowisko uruchomieniowe usługi IoT Edge w pliku, w których ścieżka jest dostępna w zmiennej środowiskowej `EdgeModuleCACertificateFile`.

Samouczek [tworzenie i wdrażanie moduł usługi IoT Edge na symulowanym urządzeniu] [lnk-tutorial2] Pokazuje, jak upewnić się, że certyfikat znajduje się w magazynie maszyny w aplikacji modułu. Wyraźnie widać każda inna metoda ufać połączeń przy użyciu tego certyfikatu.

## <a name="packaging-as-an-image"></a>Tworzenie pakietów w formie obrazu
Moduły usługi IoT Edge są spakowane w postaci obrazów platformy Docker.
Jak pokazano w tym samouczku można użyć bezpośrednio łańcuch narzędzi platformy Docker lub Visual Studio Code [tworzenie i wdrażanie moduł usługi IoT Edge na symulowanym urządzeniu] [lnk-tutorial2].

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, po tworzenia modułu jak [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

