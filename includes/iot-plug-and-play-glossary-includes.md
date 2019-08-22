---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880464"
---
## <a name="iot-plug-and-play-device"></a>Urządzenie Plug and Play IoT

Urządzenie IoT Plug and Play jest zazwyczaj niewielką skalowalnym urządzeniem komputerowym, które może zbierać dane lub sterować innymi urządzeniami, a także uruchamiać oprogramowanie lub oprogramowanie układowe, które implementuje możliwości zadeklarowane w [modelu możliwości urządzenia](#device-capability-model).  Na przykład urządzenie IoT Plug and Play może być urządzeniem monitorującym środowisko lub kontrolerem systemu nawadniania inteligentnego agrigulture. Rozwiązania IoT hostowane w chmurze mogą być wpisywane do poleceń, kontroli i odbierania danych z urządzeń Plug and Play IoT. Urządzenia Plug and Play IoT można znaleźć w [katalogu certyfikatu platformy Azure dla IoT](https://catalog.azureiotsolutions.com/). Każde urządzenie Plug and Play IoT w wykazie zostało zweryfikowane i ma [model możliwości urządzenia](#device-capability-model).

## <a name="digital-twin"></a>Cyfrowe sznurki

Digital bliźniaczych reprezentacji to modele urządzeń Plug and Play IoT.  Cyfrowe bliźniaczych reprezentacji są modelowane przy użyciu [języka definicji cyfrowej sznurka](https://aka.ms/DTDL).  Za pomocą interfejsu API usługi Azure IoT można korzystać z programu Digital bliźniaczych reprezentacji. 

## <a name="digital-twin-definition-language"></a>Język definicji cyfrowej przędzy

Język służący do opisywania modeli i interfejsów dla [urządzeń Plug and Play IoT](#iot-plug-and-play-device).  Użyj [języka definicji Digital bliźniaczy](https://aka.ms/DTDL) do opisywania możliwości [cyfrowych przędzy](#digital-twin) i Włącz platformę IoT i rozwiązania IoT, aby korzystać z semantyki jednostki.

## <a name="device-capability-model"></a>Model możliwości urządzenia

Model możliwości urządzenia opisuje urządzenie i definiuje zestaw interfejsów implementowanych przez urządzenie. Utwórz model możliwości urządzenia odpowiadający urządzeniu fizycznemu, produktowi lub jednostce SKU.

## <a name="interface"></a>Interface

Interfejs opisuje powiązane funkcje, które są implementowane przez urządzenie lub wieloosiową cyfrę. Interfejsy mogą być ponownie używane dla różnych modeli możliwości.

## <a name="property"></a>Właściwość

Właściwości są polami danych zdefiniowanymi w [interfejsie](#interface) , który reprezentuje jakiś stan dwucyfrowego sznurka. Można zadeklarować właściwości jako tylko do odczytu lub do zapisu. Właściwości tylko do odczytu są ustawiane przez kod działający w kontekście samego urządzenia Plug and Play IoT, takiego jak numer seryjny.  Właściwości do zapisu są ustawiane przez jednostki zewnętrzne, takie jak progi alarmów.

## <a name="telemetry"></a>Telemetria

Pola telemetrii zdefiniowane [](#interface) w interfejsie reprezentują pomiary. Te pomiary są zazwyczaj wartościami, takimi jak odczyty czujników.

## <a name="command"></a>Polecenie

Polecenia zdefiniowane w [interfejsie](#interface) reprezentują metody, które mogą być wykonywane na dwucyfrowej stawce. Na przykład Zresetuj polecenie resetowania urządzenia.
