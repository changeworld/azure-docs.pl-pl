---
title: Tworzenie mostka urządzeń Azure IoT Central | Dokumenty firmy Microsoft
description: Zbuduj mostek urządzenia IoT Central, aby połączyć inne chmury IoT (Sigfox, Particle, The Things Network itp.) z aplikacją IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158422"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Zbuduj mostek urządzenia IoT Central, aby połączyć inne chmury IoT z IoT Central

*Ten temat dotyczy administratorów.*

Mostek urządzeń IoT Central to rozwiązanie typu open source, które łączy sigfox, particle, the Things network i inne chmury z aplikacją IoT Central. Niezależnie od tego, czy korzystasz z urządzeń do śledzenia zasobów podłączonych do sieci o niskiej mocy sygox, czy korzystasz z urządzeń monitorujących jakość powietrza w chmurze urządzeń particle device, czy korzystasz z urządzeń do monitorowania wilgotności gleby w TTN, możesz bezpośrednio wykorzystać moc IoT Centralnie za pomocą mostka urządzenia IoT Central. Mostek urządzenia łączy inne chmury IoT z IoT Central, przesyłając dane wysyłane przez urządzenia do innych chmur do aplikacji IoT Central. W aplikacji IoT Central możesz tworzyć reguły i uruchamiać analizy na tych danych, tworzyć przepływy pracy w aplikacjach Microsoft Flow i Azure Logic, eksportować te dane i wiele więcej. Pobierz [mostek urządzenia IoT Central](https://aka.ms/iotcentralgithubdevicebridge) z GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Co to jest i jak to działa?
Mostek urządzeń IoT Central to rozwiązanie typu open source w usłudze GitHub. Jest gotowy do użycia za pomocą przycisku "Wdrażanie na platformie Azure", który wdraża niestandardowy szablon usługi Azure Resource Manager z kilkoma zasobami platformy Azure w ramach subskrypcji platformy Azure. Zasoby obejmują:
-    Aplikacja Funkcji platformy Azure
-    Konto usługi Azure Storage
-    Plan zużycia
-    W usłudze Azure Key Vault

Aplikacja funkcji jest krytycznym elementem mostka urządzenia. Odbiera żądania HTTP POST z innych platform IoT lub dowolnej platform niestandardowych za pośrednictwem prostej integracji webhook. Podaliśmy przykłady, które pokazują, jak połączyć się z chmurami Sigfox, Particle i TTN. Możesz łatwo rozszerzyć to rozwiązanie, aby połączyć się z niestandardową chmurą IoT, jeśli platforma może wysyłać żądania HTTP POST do aplikacji funkcji.
Aplikacja Function przekształca dane w formacie zaakceptowanym przez IoT Central i przekazuje je dalej za pośrednictwem interfejsów API DPS.

![Zrzut ekranu przedstawiający funkcje platformy Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Jeśli aplikacja IoT Central rozpoznaje urządzenie według identyfikatora urządzenia w wiadomości przesyłane dalej, dla tego urządzenia pojawi się nowy pomiar. Jeśli identyfikator urządzenia nigdy nie był widoczny przez aplikację IoT Central, aplikacja funkcji spróbuje zarejestrować nowe urządzenie przy tym identyfikatorze urządzenia i pojawi się jako "urządzenie bezwyłączone" w aplikacji IoT Central. 

## <a name="how-do-i-set-it-up"></a>Jak go skonfigurować?
Instrukcje są szczegółowo wymienione w pliku README w repozytorium GitHub. 

## <a name="pricing"></a>Cennik
Zasoby platformy Azure będą hostowane w ramach subskrypcji platformy Azure. Więcej informacji na temat cen można dowiedzieć się w [pliku README](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak zbudować mostek urządzeń IoT Central, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Zarządzanie urządzeniami](howto-manage-devices.md)
