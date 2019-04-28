---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445076"
---
## <a name="automatic-device-management"></a>Automatyczne zarządzanie urządzeniami
Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań zarządzania flot duże urządzenia względem całej ich cykle życia. Za pomocą automatycznego zarządzania urządzeniami można docelowe zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją i umożliwić usługi IoT Hub aktualizowania urządzenia, które pochodzą one do zakresu.  Składa się z [konfiguracji urządzeń automatycznego](../articles/iot-hub/iot-hub-auto-device-config.md) i [automatycznego wdrożenia usługi IoT Edge](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Usługa Azure IoT Edge umożliwia oparte na chmurze wdrażanie usług platformy Azure i kodu specyficznego dla rozwiązania na urządzeniach lokalnych. Urządzenia usługi IoT Edge mogą agregować dane z innych urządzeń do wykonywania obliczeń i analizy przed wysłaniem danych do chmury. Aby uzyskać więcej informacji, zobacz [usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent usługi IoT Edge
Część odpowiedzialnych za wdrażanie i monitorowanie moduły środowiska uruchomieniowego usługi IoT Edge.

## <a name="iot-edge-device"></a>Urządzenie usługi IoT Edge
Urządzenia usługi IoT Edge zainstalowanego środowiska uruchomieniowego usługi IoT Edge i są oznaczone jako **urządzenie usługi IoT Edge** w szczegółach urządzenia. Dowiedz się, jak [wdrożenia usługi Azure IoT Edge na symulowanym urządzeniu w systemie Linux — wersja zapoznawcza](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatyczne wdrażanie usługi IoT Edge
Automatyczne wdrożenie usługi IoT Edge konfiguruje zestaw docelowy urządzenia usługi IoT Edge w celu uruchomienia zestawu usługi IoT Edge modułów. Każde wdrożenie stale zapewnia wszystkie urządzenia, które odpowiadają jego warunek docelowy działają określony zestaw modułów, nawet wtedy, gdy nowych urządzeń są tworzone lub są modyfikowane w celu dopasowania warunek docelowy. Każde urządzenie usługi IoT Edge otrzymuje tylko najwyższy priorytet wdrożenia którego warunek docelowy spełnia. Dowiedz się więcej o [automatycznego wdrażania usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest wdrożenia usługi IoT Edge
Dokument Json zawierające informacje, które mają zostać skopiowane do przynajmniej jednego urządzenia usługi IoT Edge twin(s) modułu, aby wdrożyć zestaw modułów, trasy i skojarzonego modułu żądane właściwości.

## <a name="iot-edge-gateway-device"></a>Urządzenie bramy usługi IoT Edge
Urządzenia usługi IoT Edge z urządzeniem podrzędnego. Podrzędne urządzenie może być usługi IoT Edge lub nie urządzenie usługi IoT Edge.

## <a name="iot-edge-hub"></a>Centrum usługi IoT Edge
Część odpowiedzialny za moduł do komunikacji, nadrzędne (do usługi IoT Hub) i podrzędne (od usługi IoT Hub) środowiska uruchomieniowego usługi IoT Edge komunikacji. 

## <a name="iot-edge-leaf-device"></a>Urządzenia liścia IoT Edge
Urządzenia usługi IoT Edge nie podrzędnym urządzenia. 

## <a name="iot-edge-module"></a>Moduł usługi IoT Edge
Moduł usługi IoT Edge to kontener platformy Docker, którą można wdrożyć na urządzeniach usługi IoT Edge. Wykonuje określonych zadań, takich jak wprowadzane komunikatów z urządzenia, przekształcanie wiadomości lub wysyłania komunikatu do usługi IoT hub. On komunikuje się z innymi modułami i wysyła dane do środowiska uruchomieniowego usługi IoT Edge. [Zrozumienie wymagań i narzędzia do tworzenia modułów usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Tożsamość moduł usługi IoT Edge
Rekord w rejestrze tożsamości Centrum IoT Hub modułu ze szczegółami dotyczącymi poświadczenia istnienie i zabezpieczeń do użycia przez moduł do uwierzytelniania przy użyciu Centrum usługi edge i IoT Hub.

## <a name="iot-edge-module-image"></a>Obraz modułu usługi IoT Edge
Obraz platformy docker jest używana przez środowisko uruchomieniowe usługi IoT Edge do utworzenia wystąpienia wystąpienia modułu.

## <a name="iot-edge-module-twin"></a>Bliźniacza reprezentacja modułu usługi IoT Edge
Dokument Json utrwalone w usłudze IoT Hub, która przechowuje informacje o stanie dla wystąpienia modułu.

## <a name="iot-edge-priority"></a>Priorytet usługi IoT Edge
Jeśli dwa wdrożenia usługi IoT Edge tego samego urządzenia, wdrożenie o wyższym priorytecie zostanie zastosowany. Jeśli dwa wdrożenia mają ten sam priorytet, stosowane wdrożenie z późniejszą datą utworzenia. Dowiedz się więcej o [priorytet](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Środowisko uruchomieniowe usługi IoT Edge
Środowisko uruchomieniowe usługi IoT Edge obejmuje wszystko, czego dystrybuuje firmy Microsoft, należy zainstalować na urządzeniu usługi IoT Edge. Zawiera agenta usługi Edge, Centrum usługi Edge i demona zabezpieczeń usługi IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>Usługi IoT Edge Ustaw moduły do jednego urządzenia
Operacja, która kopiuje zawartość manifestu usługi IoT Edge na jednym urządzeniu ' bliźniaczą reprezentację modułu. Podstawowy interfejs API jest ogólny "zastosowanie konfiguracji", który po prostu przyjmuje manifest usługi IoT Edge jako dane wejściowe.

## <a name="iot-edge-target-condition"></a>Warunek docelowy usługi IoT Edge
W przypadku wdrożenia usługi IoT Edge warunek docelowy jest dowolnego warunku typu Boolean na tagów bliźniaczych reprezentacji urządzeń, aby wybrać urządzenia docelowe wdrożenia, na przykład **tag.environment = prod**. Warunek docelowy jest stale oceniany w celu uwzględnienia nowych urządzeń spełniających wymagania lub usunięcia urządzeń, które nie. Dowiedz się więcej o [warunek docelowy](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)