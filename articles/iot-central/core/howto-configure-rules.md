---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono, jako konstruktor, jak skonfigurować reguły i akcje oparte na telemetrii w aplikacji Azure IoT Central.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 509f9557a8128df12353ad02a7c7db02b7b42631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158475"
---
# <a name="configure-rules"></a>Konfigurowanie reguł



*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Reguły w centrum IoT służą jako konfigurowalne narzędzie reagowania, które wyzwala aktywnie monitorowane zdarzenia z podłączonych urządzeń. W poniższych sekcjach opisano sposób oceny reguł.

## <a name="select-target-devices"></a>Wybieranie urządzeń docelowych

Użyj sekcji Urządzenia docelowe, aby wybrać, na jakich urządzeniach zostanie zastosowana ta reguła. Filtry umożliwiają dalsze udoskonalanie urządzeń, które powinny zostać uwzględnione. Filtry używają właściwości szablonu urządzenia do filtrowania zestawu urządzeń. Same filtry nie wywołują akcji. Na poniższym zrzucie ekranu urządzenia, które są kierowane są typu szablon urządzenia **Lodówka**. Filtr stwierdza, że reguła powinna obejmować tylko **lodówki,** w których właściwość **wyprodukowanego państwa** jest równa **Waszyngtonowi.**

![Warunki](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Korzystaj z wielu warunków

Warunki są to, co reguły wyzwalają. Obecnie po dodaniu wielu warunków do reguły są one logicznie i są razem. Innymi słowy, wszystkie warunki muszą być spełnione, aby reguła została oceniona jako prawdziwa.  

Na poniższym zrzucie ekranu warunki sprawdzają, kiedy temperatura jest większa niż 70&deg; F, a wilgotność jest mniejsza niż 10. Gdy obie te instrukcje są prawdziwe, reguła ocenia true i wyzwala akcję.

![Warunki](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Użyj agregacji okien

Reguły oceniają zagregowane okna czasu jako okna. Na poniższym zrzucie ekranu przedział czasu wynosi pięć minut. Co pięć minut reguła ocenia dane z ostatnich pięciu minut. Dane są oceniane tylko raz w oknie, do którego odpowiada.

![Upadek systemu Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Używanie reguł z modułami IoT Edge

Ograniczenie ma zastosowanie do reguł, które są stosowane do modułów IoT Edge. Reguły dotyczące danych telemetrycznych z różnych modułów nie są oceniane jako prawidłowe reguły. Weźmy następujący przykład. Pierwszy warunek reguły jest na telemetrii temperatury z modułu A. Drugi warunek reguły jest na telemetrii wilgotności w module B. Ponieważ oba warunki pochodzą z różnych modułów, jest to nieprawidłowy zestaw warunków. Reguła jest nieprawidłowa i spowoduje błąd podczas próby zapisania reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować regułę w aplikacji Azure IoT Central, możesz:

> [!div class="nextstepaction"]
> [Analizuj swoje dane na bieżąco](howto-create-analytics.md)
