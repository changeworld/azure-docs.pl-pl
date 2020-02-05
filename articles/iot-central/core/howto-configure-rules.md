---
title: Konfigurowanie reguł i akcji w usłudze Azure IoT Central | Microsoft Docs
description: Ten artykuł zawiera opis sposobu konfigurowania reguł i akcji opartych na danych telemetrycznych w aplikacji IoT Central platformy Azure.
author: vavilla
ms.author: vavilla
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ca308522d0c6b9c3c5b39d73a2f4a278a5b20db8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026993"
---
# <a name="configure-rules"></a>Konfigurowanie reguł



*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Reguły w IoT Central pełnią rolę narzędzia do dostosowywania odpowiedzi, które wyzwalają aktywne monitorowane zdarzenia z połączonych urządzeń. W poniższych sekcjach opisano, jak są oceniane reguły.

## <a name="select-target-devices"></a>Wybierz urządzenia docelowe

Sekcja urządzenia docelowe służy do wybierania rodzaju urządzeń, na których zostanie zastosowana ta reguła. Filtry umożliwiają dokładniejsze zawężenie urządzeń, które powinny być uwzględniane. Filtry używają właściwości w szablonie urządzenia do filtrowania zestawu urządzeń. Same filtry nie wyzwalają akcji. Na poniższym zrzucie ekranu urządzenia, które są wskazywane, są typu szablonu urządzenia **chłodziarkd**. Filtr wskazuje, że reguła powinna zawierać tylko **chłodziarki** , w których właściwość **stan wyprodukowana** jest równa **Waszyngton**.

![Warunki](media/howto-configure-rules/filters.png)

## <a name="use-multiple-conditions"></a>Użycie wielu warunków

Warunki są wyzwalane przez reguły. Obecnie po dodaniu wielu warunków do reguły są one logicznie i razem. Innymi słowy, aby reguła mogła oszacować jako prawda, muszą zostać spełnione wszystkie warunki.  

Na poniższym zrzucie ekranu warunki sprawdzają, kiedy temperatura jest większa niż 70&deg; F, a wilgotność jest mniejsza niż 10. Gdy obie te instrukcje są spełnione, reguła zwraca wartość true i wyzwala akcję.

![Warunki](media/howto-configure-rules/conditions.png)

## <a name="use-aggregate-windowing"></a>Użyj okna agregacji

Reguły obliczają łączny czas systemu Windows jako wirowania Windows. Na poniższym zrzucie ekranu czas przedziału wynosi pięć minut. Co pięć minut reguła jest szacowana w ciągu ostatnich pięciu minut danych. Dane są oceniane tylko raz w oknie, do którego się odnosi.

![Wirowania systemu Windows](media/howto-configure-rules/tumbling-window.png)

## <a name="use-rules-with-iot-edge-modules"></a>Używanie reguł z modułami IoT Edge

Ograniczenie ma zastosowanie do reguł, które są stosowane do modułów IoT Edge. Reguły dotyczące danych telemetrycznych z różnych modułów nie są oceniane jako prawidłowe reguły. Zapoznaj się z poniższym przykładem. Pierwszy warunek reguły znajduje się na danych telemetrycznych temperatury z modułu A. Drugi warunek reguły znajduje się na telemetrcie wilgotności w module B. Ponieważ dwa warunki pochodzą z różnych modułów, jest to nieprawidłowy zestaw warunków. Reguła jest nieprawidłowa i zgłosi błąd podczas próby zapisania reguły.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować regułę w aplikacji IoT Central platformy Azure, możesz:

> [!div class="nextstepaction"]
> [Analizuj dane na bieżąco](howto-create-analytics.md)
