---
title: Tworzenie dla urządzeń przenośnych przy użyciu usługi Azure IoT SDK | Dokumentacja firmy Microsoft
description: Deweloper przewodnik — więcej informacji na temat sposobu tworzenia dla urządzeń przenośnych przy użyciu zestawów SDK Centrum IoT Azure.
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634951"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Tworzenie dla urządzeń przenośnych przy użyciu zestawów SDK IoT Azure

Elementy Internetu rzeczy może odwoływać się do wielu urządzeń o różnych możliwościach: czujników, mikrokontrolerów urządzeń inteligentnych, przemysłowych bram i urządzeń przenośnych nawet.  Urządzenie przenośne można urządzenia IoT, gdy wysyła dane telemetryczne urządzenia do chmury i zarządzane przez chmurę.  Można też urządzenie, na którym uruchomiono aplikację usługi zaplecza, która zarządza innymi urządzeniami IoT.  W obu przypadkach [zestawów SDK Centrum IoT Azure] [ lnk-sdk-overview] może służyć do tworzenia aplikacji, które działają na urządzeniach przenośnych.  

## <a name="develop-for-native-ios-platform"></a>Tworzenie dla platformy iOS natywnego

Zestawy Azure IoT Hub SDK zapewniają obsługę platformy natywnej dla systemu iOS przy użyciu zestawu SDK usługi Azure IoT Hub C.  Jego można traktować jako iOS SDK, który można zastosować w projekcie Swift lub Objective C XCode.  Istnieją dwa sposoby używania zestawu SDK C w systemie iOS:
- Użyj biblioteki CocoaPod w projekcie XCode bezpośrednio.  
- Pobieranie kodu źródłowego C zestawu SDK i tworzyć dla następujących platform iOS [kompilacji instrukcji] [ lnk-c-devbox] dla MacOS.  

Azure IoT Hub C zestawu SDK są zapisywane w C99 przenośności maksymalną na różnych platformach.  Proces przenoszenia polega na zapisywanie warstwy elastycznej przyjęcia składników specyficzne dla platformy, które można znaleźć tutaj [iOS][lnk-ios-pal].  Można użyć funkcji w zestawie SDK C na platformie iOS, w tym podstawowych Centrum IoT Azure obsługiwane i funkcji specyficznych dla zestawu SDK, takich jak zasady niezawodności sieci ponawiania.  Interfejs dla systemu iOS SDK również jest podobny do interfejsu dla zestawu SDK usługi Azure IoT Hub C.  

Te dokumenty przeprowadzenie tworzenia urządzenia, aplikacji lub usługi aplikacji na urządzeniu z systemem iOS:
- [Szybki Start: Wysyłania danych telemetrycznych z urządzenia do Centrum IoT][lnk-device-ios-quickstart]  
- [Wysyłanie komunikatów z chmury do urządzenia z Centrum IoT][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Tworzenie z bibliotekami CocoaPod Centrum IoT Azure

Zestawy Azure IoT Hub SDK udostępnia zestaw bibliotek CocoaPod języka Objective C dla opracowywania aplikacji systemu iOS.  Aby wyświetlić najbardziej aktualną listę bibliotek CocoaPod, zobacz [programu CocoaPods dla programu Microsoft Azure IoT][lnk-cocoapod-list].  Po odpowiednich biblioteki są włączone do projektu XCode, istnieją dwa sposoby zapisu Centrum IoT powiązane kodu:
- Funkcja Objective C: Jeśli Twój projekt został napisany w języku Objective-C, można wywołać interfejsy API z zestawu SDK usługi Azure IoT Hub C bezpośrednio.  Jeśli projekt został napisany w języku Swift, należy wywołać ``@objc func`` przed Tworzenie funkcji i przejdź do zapisywania wszystkie warunki logiczne związane z Centrum IoT Azure przy użyciu kodu C lub Objective-C.  Zestaw przykładów prezentacja zarówno znajdują się w [repozytorium przykładowej][lnk-ios-samples-repo].  
- Uwzględnienie przykłady C: Jeśli w języku C zastosowanie urządzenia, możesz odwoływać się do niej bezpośrednio w projekcie XCode:
    - Dodaj plik sample.c do projektu XCode z XCode.  
    - Dodaj plik nagłówka do zależność.  Plik nagłówka jest uwzględniona w [repozytorium przykładowej] [ lnk-ios-samples-repo] jako przykład.  Aby uzyskać więcej informacji, odwiedź stronę dokumentacji firmy Apple dla [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Kolejne kroki
* [Dokumentacja interfejsu API REST Centrum IoT][lnk-rest-ref]
* [Kod źródłowy zestawu SDK C IoT Azure][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
