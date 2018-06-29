---
title: Opracowanie modułów Azure IoT Edge | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia niestandardowych modułów Edge IoT Azure
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dbbd07e93602855afb0c9755e8872e0b46557611
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030023"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Zrozumieć wymagania i narzędzi do tworzenia modułów krawędzi IoT

W tym artykule opisano, jakie funkcje są dostępne podczas pisania aplikacji działających jako moduł krawędzi IoT i jak korzystać z nich.

## <a name="iot-edge-runtime-environment"></a>Środowisko uruchomieniowe krawędzi IoT
Środowiska uruchomieniowego krawędzi IoT zapewnia infrastrukturę do integracji funkcji wiele modułów krawędzi IoT i można je wdrożyć na urządzenia brzegowe IoT. Na wysokim poziomie wszystkie programy można spakować jako moduł IoT krawędzi. Jednak do w pełni korzystać krawędzi IoT, komunikację i funkcje zarządzania, program działający w module można nawiązać połączenia lokalnego Centrum IoT krawędzi, zintegrowane w środowisku wykonawczym IoT krawędzi.

## <a name="using-the-iot-edge-hub"></a>Przy użyciu Centrum IoT krawędzi
Centrum IoT krawędzi udostępnia dwie główne funkcje: serwer proxy Centrum IoT i lokalnej komunikacji.

### <a name="iot-hub-primitives"></a>Elementy podstawowe Centrum IoT
Widzi Centrum IoT w module wystąpienia analogously na urządzeniu, w tym sensie, że:

* ma dwie moduł, który jest odrębny i odizolowane od [dwie urządzenia] [ lnk-devicetwin] i innych twins modułu tych urządzeń;
* może wysłać [wiadomości urządzenia do chmury][lnk-iothub-messaging];
* możliwość odbierania [bezpośrednie metody] [ lnk-methods] skierowane w szczególności jego tożsamość.

Obecnie modułu nie może odbierać komunikaty z chmury do urządzenia ani używać funkcji przekazywania plików.

Podczas zapisywania moduł, możesz użyć [SDK urządzenia IoT Azure] [ lnk-devicesdk] do nawiązywania połączeń z Centrum IoT krawędzi i korzystać z funkcji powyżej, jak w przypadku, gdy przy użyciu Centrum IoT z aplikacją urządzenia tylko Różnica między nimi polega, z poziomu aplikacji zaplecza, trzeba odwoływać się do tożsamości modułu zamiast tożsamości tego urządzenia.

Zobacz [opracowanie i wdrożyć moduł krawędzi IoT symulowane urządzenie] [ lnk-tutorial2] przykład aplikacji modułu, która wysyła komunikaty urządzenia do chmury, a używa dwie modułu.

### <a name="device-to-cloud-messages"></a>Komunikaty z urządzenia do chmury
Aby włączyć złożonych, przetwarzanie wiadomości urządzenia do chmury Centrum IoT krawędzi zapewnia deklaratywne routing wiadomości między modułami i między modułami i Centrum IoT.
Dzięki temu moduł do przechwycenia i przetwarzania komunikatów wysłanych przez inne moduły i propagować je do złożonych potoków.
Artykuł [budowy modułu] [ lnk-module-comp] opisano sposób tworzenia modułów do złożonych potoki za pomocą trasy.

Inaczej niż normalne aplikacji urządzenia IoT Hub moduł krawędzi IoT mogą odbierać wiadomości urządzenia do chmury, które są przekazywane przez serwer proxy przez jego lokalnego Centrum IoT krawędzi, w celu ich przetworzenia.

Centrum IoT krawędzi propaguje wiadomości do modułu oparte na trasach deklaratywne opisanego w [budowy modułu] [ lnk-module-comp] artykułu. Podczas tworzenia modułu IoT krawędzi, można otrzymywać te wiadomości przez ustawienie programy obsługi wiadomości, opisane w samouczku [programowanie i wdrażanie modułu krawędzi IoT symulowane urządzenie][lnk-tutorial2].

W celu uproszczenia procesu tworzenia tras, krawędzi IoT dodaje pojęcie modułu *wejściowych* i *dane wyjściowe* punktów końcowych. Moduł może odbierać wszystkie wiadomości urządzenia do chmury kierowane do niej bez określania żadnych danych i może wysyłać komunikaty urządzenia do chmury bez określania żadnych danych wyjściowych.
Za pomocą jawnego wejściach i wyjściach, jednak sprawia, że reguły routingu łatwiejsze do zrozumienia. Zobacz [budowy modułu] [ lnk-module-comp] Aby uzyskać więcej informacji na temat reguł routingu i wejściowymi i wyjściowymi punktów końcowych dla modułów.

Ponadto urządzenia do chmury komunikaty obsługiwane przez Centrum krawędzi oznaczony są następujące właściwości systemu:

| Właściwość | Opis |
| -------- | ----------- |
| $connectionDeviceId | Identyfikator urządzenia klienta, który wysłał wiadomość |
| $connectionModuleId | Identyfikator modułu modułu, który wysłał wiadomość |
| $inputName | Dane wejściowe ten komunikat. Może być pusta. |
| $outputName | Dane wyjściowe używane do wysyłania wiadomości. Może być pusta. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Łączenie z Centrum IoT krawędzi z modułu
Łączenie z lokalnym Centrum IoT krawędzi z modułu wymaga wykonania dwóch kroków: Użyj parametrów połączenia, pod warunkiem przez środowisko uruchomieniowe krawędzi IoT podczas uruchamiania modułu i upewnij się, że aplikacja akceptuje certyfikat przedstawiony przez Centrum IoT Edge na tym urządzeniu.

Ciąg połączenia używany jest wstrzyknięte przez środowisko uruchomieniowe IoT krawędzi w zmiennej środowiskowej `EdgeHubConnectionString`. Dzięki temu dostępne dla dowolnego programu, który chce go używać.

Analogously, certyfikat służący do weryfikowania połączeń Centrum IoT krawędzi jest wstrzyknięte przez środowisko uruchomieniowe IoT krawędzi w pliku, w których ścieżka jest dostępna w zmiennej środowiskowej `EdgeModuleCACertificateFile`.

Samouczek [opracowanie i wdrożyć moduł krawędzi IoT symulowane urządzenie] [ lnk-tutorial2] pokazano, jak i upewnij się, że certyfikat znajduje się w magazynie komputera w aplikacji modułu. Wyraźnie widać innych metod, aby zaufać połączeń przy użyciu tego certyfikatu.

## <a name="packaging-as-an-image"></a>OPAKOWYWANIE jako obrazu
Krawędź IoT moduły są dostarczane jako obrazy usługi Docker.
Jak pokazano w samouczku można użyć bezpośrednio łańcuch narzędzi platformy Docker lub Visual Studio Code [opracowanie i wdrożyć moduł krawędzi IoT symulowane urządzenie][lnk-tutorial2].

## <a name="next-steps"></a>Kolejne kroki

Po opracowywanie modułu, Dowiedz się jak [wdrażanie i monitorowanie krawędzi IoT modułów na dużą skalę][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
