---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183350"
---
## <a name="automatic-device-management"></a>Automatyczne zarządzanie urządzeniami
Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań związanych z zarządzaniem flotami dużych urządzeń w całym cyklu ich życia. Dzięki automatycznemu zarządzaniu urządzeniami można kierować zestaw urządzeń na podstawie ich właściwości, definiować żądaną konfigurację i pozwolić centrum IoT hub na aktualizowanie urządzeń za każdym razem, gdy wchodzą w zakres.  Składa się z [automatycznych konfiguracji urządzeń](../articles/iot-hub/iot-hub-auto-device-config.md) i [automatycznych wdrożeń usługi IoT Edge.](../articles/iot-edge/how-to-deploy-monitor.md)

## <a name="iot-edge"></a>IoT Edge
Usługa Azure IoT Edge umożliwia oparte na chmurze wdrażanie usług platformy Azure i kodu specyficznego dla rozwiązania na urządzeniach lokalnych. Urządzenia usługi IoT Edge mogą agregować dane z innych urządzeń w celu wykonywania przetwarzania i analizy przed wysłaniem danych do chmury. Aby uzyskać więcej informacji, zobacz [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Część środowiska wykonawczego usługi IoT Edge odpowiedzialna za wdrażanie i monitorowanie modułów.

## <a name="iot-edge-device"></a>Urządzenie usługi IoT Edge
Urządzenia IoT Edge mają zainstalowane środowisko uruchomieniowe IoT Edge i są oflagowane jako **urządzenie IoT Edge** w szczegółach urządzenia. Dowiedz się, jak [wdrożyć usługę Azure IoT Edge na symulowanym urządzeniu w systemie Linux — wersja zapoznawcza](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatyczne wdrażanie usługi IoT Edge
Automatyczne wdrożenie usługi IoT Edge konfiguruje docelowy zestaw urządzeń usługi IoT Edge do uruchamiania zestawu modułów usługi IoT Edge. Każde wdrożenie stale zapewnia, że wszystkie urządzenia, które spełniają jego warunek docelowy są uruchomione określony zestaw modułów, nawet wtedy, gdy nowe urządzenia są tworzone lub są modyfikowane, aby dopasować warunek docelowy. Każde urządzenie Usługi IoT Edge otrzymuje tylko wdrożenie o najwyższym priorytecie, którego warunek docelowy spełnia. Dowiedz się więcej o [automatycznym wdrażaniu usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest wdrażania usługi IoT Edge
Dokument Json zawierający informacje, które mają być kopiowane w co najmniej jednym urządzeniu IoT Edge moduł bliźniaczych bliźniaczych reprezentacji w celu wdrożenia zestawu modułów, tras i skojarzonych modułów żądanych właściwości.

## <a name="iot-edge-gateway-device"></a>Urządzenie bramy usługi IoT Edge
Urządzenie IoT Edge z urządzeniem podrzędnym. Urządzeniem podrzędnym może być urządzenie IoT Edge lub nie IoT Edge.

## <a name="iot-edge-hub"></a>Koncentrator usługi IoT Edge
Część środowiska wykonawczego usługi IoT Edge odpowiedzialna za komunikację modułu, komunikację nadrzędną (w kierunku centrum IoT Hub) i niższą (z dala od usługi IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Urządzenie liściowe IoT Edge
Urządzenie IoT Edge bez urządzenia podrzędnego. 

## <a name="iot-edge-module"></a>Moduł IoT Edge
Moduł usługi IoT Edge to kontener platformy Docker, który można wdrożyć na urządzeniach usługi IoT Edge. Wykonuje określone zadanie, takie jak pozyskiwania wiadomości z urządzenia, przekształcania wiadomości lub wysyłania wiadomości do centrum IoT hub. Komunikuje się z innymi modułami i wysyła dane do środowiska wykonawczego usługi IoT Edge. [Poznaj wymagania i narzędzia do tworzenia modułów IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Tożsamość modułu usługi IoT Edge
Rekord w rejestrze tożsamości modułu Usługi IoT Hub zawierający szczegółowe informacje o istnieniu i poświadczeniach zabezpieczeń, które mają być używane przez moduł do uwierzytelniania za pomocą koncentratora brzegowego lub centrum IoT Hub.

## <a name="iot-edge-module-image"></a>Obraz modułu IoT Edge
Obraz docker, który jest używany przez środowisko uruchomieniowe IoT Edge do tworzenia wystąpienia wystąpień modułu.

## <a name="iot-edge-module-twin"></a>Bliźniaczy moduł IoT Edge
Dokument Json utrwalone w Centrum IoT, który przechowuje informacje o stanie dla wystąpienia modułu.

## <a name="iot-edge-priority"></a>Priorytet IoT Edge
Gdy dwa wdrożenia usługi IoT Edge są przeznaczone dla tego samego urządzenia, zostanie zastosowane wdrożenie o wyższym priorytecie. Jeśli dwa wdrożenia mają ten sam priorytet, wdrożenie z późniejszą datą utworzenia zostanie zastosowane. Dowiedz się więcej o [priorytecie](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe usługi IoT Edge zawiera wszystko, co firma Microsoft dystrybuuje do zainstalowania na urządzeniu usługi IoT Edge. Zawiera agenta usługi Edge, koncentratora usługi Edge i demona zabezpieczeń usługi IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge ustawia moduły na jednym urządzeniu
Operacja, która kopiuje zawartość manifestu IoT Edge na jednym urządzeniu modułu bliźniaczej reprezentacji. Podstawowy interfejs API jest ogólny "zastosuj konfigurację", który po prostu przyjmuje manifest usługi IoT Edge jako dane wejściowe.

## <a name="iot-edge-target-condition"></a>Warunek docelowy ioT Edge
W przypadku wdrożenia usługi IoT Edge warunkiem docelowym jest dowolny warunek logiczny na tagach bliźniąt urządzeń, aby wybrać urządzenia docelowe wdrożenia, na przykład **tag.environment = prod**. Warunek docelowy jest stale oceniane w celu uwzględnienia nowych urządzeń, które spełniają wymagania lub usunąć urządzenia, które już nie. Dowiedz się więcej o [stanie docelowym](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)