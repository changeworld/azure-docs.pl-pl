---
title: Tworzenie dla urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — dowiedz się, jak tworzyć urządzenia przenośne przy użyciu zestawów SDK usługi Azure IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 945b02003a443c04e692fdc06ca5714de362d074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883093"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Tworzenie urządzeń przenośnych przy użyciu zestawów SDK usługi Azure IoT

Rzeczy w Internecie rzeczy mogą odnosić się do szerokiej gamy urządzeń o różnych możliwościach: czujników, mikrokontrolerów, inteligentnych urządzeń, bram przemysłowych, a nawet urządzeń mobilnych.  Urządzenie przenośne może być urządzeniem IoT, gdzie jest wysyłanie danych telemetrycznych między urządzeniami do chmury i zarządzane przez chmurę.  Może to być również urządzenie z uruchomieniem aplikacji usługi zaplecza, która zarządza innymi urządzeniami IoT.  W obu przypadkach [zestawów SDK usługi Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) może służyć do tworzenia aplikacji, które działają na urządzeniach przenośnych.  

## <a name="develop-for-native-ios-platform"></a>Tworzenie dla natywnej platformy iOS

Zestaw SDK usługi Azure IoT Hub zapewnia natywną obsługę platformy iOS za pośrednictwem zestawu C usługi Azure IoT Hub.  Można to potraktować jako sdk iOS, który można włączyć do projektu Swift lub Objective C XCode.  Istnieją dwa sposoby używania SDK C w iOS:

* Bezpośrednio użyj bibliotek CocoaPod w projekcie XCode.  
* Pobierz kod źródłowy dla SDK C i skompiluj dla platformy iOS zgodnie z [instrukcją kompilacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) dla systemu MacOS.  

Zestaw SDK usługi Azure IoT Hub C jest napisany w języku C99 w celu maksymalnej przenoszenia na różnych platformach.  Proces przenoszenia polega na napisaniu cienkiej warstwy adopcyjnej dla komponentów specyficznych dla platformy, którą można znaleźć tutaj dla [systemu iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx).  Funkcje w zestawie SDK języka C można wykorzystać na platformie iOS, w tym podstawowe usługi Azure IoT Hub obsługiwane i funkcje specyficzne dla SDK, takie jak zasady ponawiania próby niezawodności sieci.  Interfejs dla zestawu SDK systemu iOS jest również podobny do interfejsu dla usługi Azure IoT Hub C SDK.  

W tych dokumentach opisano sposób tworzenia aplikacji urządzenia lub aplikacji usługi na urządzeniu z systemem iOS:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-ios.md)  
* [Wysyłanie wiadomości z chmury do urządzenia za pomocą centrum IoT Hub](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Tworzenie za pomocą bibliotek CocoaPod usługi Azure IoT Hub

Zestaw SDK usługi Azure IoT Hub udostępnia zestaw bibliotek CocoaPodu Języka Obiektywnego C dla tworzenia systemu iOS.  Aby wyświetlić najnowszą listę bibliotek CocoaPod, zobacz [CocoaPods for Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md).  Po uwzględnieniu odpowiednich bibliotek do projektu XCode istnieją dwa sposoby pisania kodu powiązanego z centrum IoT Hub:

* Funkcja cel C: Jeśli projekt jest napisany w języku Objective-C, można wywołać interfejsy API z usługi Azure IoT Hub C SDK bezpośrednio.  Jeśli projekt jest napisany w programie `@objc func` Swift, można wywołać przed utworzeniem funkcji i przystąpić do pisania wszystkich logiki związanych z usługi Azure IoT Hub przy użyciu kodu C lub Objective-C.  Zestaw próbek demonstrujących oba można znaleźć w [repozytorium próbek](https://github.com/Azure-Samples/azure-iot-samples-ios).  

* Uwzględnij przykłady języka C: Jeśli napisałeś aplikację urządzenia C, możesz odwoływać się do niej bezpośrednio w projekcie XCode:
    * Dodaj plik sample.c do projektu XCode z XCode.  
    * Dodaj plik nagłówka do zależności.  Plik nagłówka znajduje się w [przykładzie przykładowego repozytorium.](https://github.com/Azure-Samples/azure-iot-samples-ios) Aby uzyskać więcej informacji, odwiedź stronę dokumentacji firmy Apple dla [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="develop-for-android-platform"></a>Opracowanie dla platformy Android
Usługa Azure IoT Hub Java SDK obsługuje platformę systemu Android.  Aby zapoznać się z testowanymi wersjami interfejsu API, odwiedź [stronę pomocy technicznej naszej platformy,](iot-hub-device-sdk-platform-support.md) aby uzyskać najnowszą aktualizację.

Te dokumenty przejść przez sposób tworzenia aplikacji urządzenia lub aplikacji usługi na urządzeniu z systemem Android przy użyciu Gradle i Android Studio:

* [Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-android.md)  
* [Szybki start: sterowanie urządzeniem](quickstart-control-device-android.md) 

## <a name="next-steps"></a>Następne kroki

* [Odwołanie do interfejsu API REST usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/)
* [Kod źródłowy SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c)
