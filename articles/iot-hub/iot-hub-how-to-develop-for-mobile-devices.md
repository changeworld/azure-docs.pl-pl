---
title: Opracowywanie zawartości dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT | Dokumentacja firmy Microsoft
description: Dewelopera przewodnik dotyczący — więcej informacji na temat tworzenia aplikacji dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT Hub.
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 5256a58a2b68584888abcac915392d8e389e9772
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399372"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Opracowywanie zawartości dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT

Elementy w Internecie rzeczy mogą odwoływać się do szerokiej gamy urządzeń z różnymi możliwościami: czujniki, mikrokontrolerów, inteligentnych urządzeń, przemysłowe bram i urządzeń przenośnych nawet.  Urządzenie przenośne może być urządzenie IoT, gdzie jest wysyłania danych telemetrycznych z urządzenia do chmury i zarządzane w chmurze.  Można też urządzenia z uruchomioną aplikacją usługi zaplecza, który zarządza innymi urządzeniami IoT.  W obu przypadkach [zestawami SDK Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) może służyć do tworzenia aplikacji, które działają dla urządzeń przenośnych.  

## <a name="develop-for-native-ios-platform"></a>Opracowywanie zawartości dla platformy natywnej dla systemu iOS

Usługa Azure IoT Hub SDKs zapewniają obsługę platformy natywnych dla systemów iOS, za pomocą zestawu SDK usługi Azure IoT Hub C.  Można traktować go jako zestawu iOS SDK, które można zastosować w projekcie Swift i Objective C w środowisku XCode.  Istnieją dwa sposoby użycia zestawu SDK języka C w systemie iOS:

* Korzystanie z bibliotek CocoaPod, w projekcie XCode bezpośrednio.  
* Pobierz kod źródłowy dla zestawu SDK języka C i tworzyć dla następujących platform iOS [kompilacji instrukcji](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dla systemu MacOS.  

Zestaw SDK usługi Azure IoT Hub C są zapisywane w C99 do uzyskania maksymalnej przenośności do różnych platform.  Proces przenoszenia wymaga zapisywania warstwę alokowania elastycznego wdrażania dla składników specyficzne dla platformy, które można znaleźć tutaj [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkcje zestawu SDK języka C nadającego się na platformy iOS, w tym elementy podstawowe usługi Azure IoT Hub, obsługiwany i funkcjami specyficznymi dla zestawu SDK, takie jak zasady niezawodności sieci ponawiania.  Interfejs dla systemu iOS SDK przypomina również interfejs dla zestawu SDK usługi Azure IoT Hub C.  

Te dokumenty przedstawiają sposób tworzenia aplikacji urządzenia lub aplikacji usługi na urządzeniu z systemem iOS:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-ios.md)  
* [Wysyłanie komunikatów z chmury do urządzenia z usługą IoT hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Programowanie z użyciem biblioteki Azure IoT Hub CocoaPod

Usługa Azure IoT Hub SDKs zwalnia zestaw bibliotek języka Objective-C CocoaPod do tworzenia aplikacji dla systemu iOS.  Aby wyświetlić najnowszą listę bibliotek CocoaPod, zobacz [CocoaPods dla Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Gdy odpowiednie biblioteki są włączone w projekcie XCode, istnieją dwa sposoby zapisu usługi IoT Hub powiązanego kodu:

* Język Objective C funkcji: Jeśli projekt został napisany w języku Objective-C, można bezpośrednio wywoływać interfejsy API z zestawu SDK usługi Azure IoT Hub C.  Jeśli projekt został napisany w języku Swift, można wywołać `@objc func` przed tworzenia funkcji i przejść do pisania wszystkie warunki logiczne związane z usługą Azure IoT Hub przy użyciu kodu C lub języka Objective-C.  Uzyskać zestaw przykładów zarówno ukazujące znajdują się w [przykładowego repozytorium](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Przykłady w języku C zawierały: Jeśli w języku C zastosowanie urządzenia, możesz odwołać je bezpośrednio w projekcie XCode:
    * Dodaj plik sample.c do projektu XCode z narzędzia XCode.  
    * Dodaj plik nagłówkowy, do Twojego zależności.  Plik nagłówka znajduje się w [przykładowego repozytorium](https://github.com/Azure-Samples/azure-iot-samples-ios) jako przykład. Aby uzyskać więcej informacji, odwiedź stronę dokumentacji firmy Apple dla [języka Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Opracowywanie zawartości dla platformy systemu Android
Zestaw SDK usługi Azure IoT Hub Java obsługuje platformy systemu Android.  Dla określonej wersji interfejsu API, przetestowane, odwiedź nasz [stronę pomocy technicznej platformy](iot-hub-device-sdk-platform-support.md) najnowszych aktualizacji.

Te dokumenty przedstawiają sposób tworzenia aplikacji urządzenia lub aplikacji usługi na urządzeniu z systemem Android przy użyciu narzędzia Gradle i Android Studio:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do usługi IoT hub](quickstart-send-telemetry-android.md)  
* [Szybki start: Kontrolowanie urządzenia](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja interfejsu API REST Centrum IoT](https://docs.microsoft.com/rest/api/iothub/)
* [Kod źródłowy zestawu SDK C usługi IoT platformy Azure](https://github.com/Azure/azure-iot-sdk-c)
