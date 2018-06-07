---
title: Tworzenie niestandardowych analytics aplikacji Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Jako operatora jak utworzyć niestandardowe analytics aplikacji Azure IoT centralnej.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5facdf3f02b71e154a23d8f26c7bcc40b5c71e35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629307"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Jak używać analytics do analizowania danych urządzenia

Centrum IoT Azure Microsoft oferuje możliwości rozbudowanej analizy rozsądnie dużych ilości danych z urządzeń. Służy do wyświetlania i analizowania danych dla analityka [zestaw urządzenia](howto-use-device-sets.md) w aplikacji. Zestaw urządzenia jest zdefiniowane przez użytkownika grupy urządzeń. Można zawęzić analizy niewielki zbiór urządzeń lub pojedynczego urządzenia.

Operator wybrać **Analytics** w menu nawigacji po lewej stronie Wybierz zbiór urządzeń, a następnie wybierz wartości, tak aby wyświetlić na wykresie. Poniżej przedstawiono kilka narzędzi, używanych do dalszego wycinka danych:

* **Pomiary:** wybierz wartości, tak aby wyświetlić przykład temperatury i wilgotności.
* **Agregacja:** wybierz funkcję agregacji do pomiarów. Na przykład **Minimum** lub **średni**.
* **Podziel przez:** przechodzenie przez podział danych przez właściwości urządzenia lub nazwę urządzenia. Na przykład podział według lokalizacji urządzenia:

     ![Strona główna analiza](media\howto-create-analytics\analytics-main.png)

* **Przedział czasu:** można wybrać jeden z przedziałów czasu wstępnie zdefiniowanego zakresu czasu lub utworzyć zakres czasu niestandardowe: ![zakres czasu analizy](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Zmień wizualizacji

Można zmienić wykresy zgodnie z wymaganiami wizualizacji, wybierając jedną z trzech trybów:

* **Skumulowany:** jest skumulowany wykres dla każdego pomiaru i wykresów mieć własne osi y. Skumulowany wykresy są przydatne, gdy mają wiele pomiarów wybrane i ma być widoków pomiarów.
* **Nieskumulowanych:** wykresu dla każdego miary są kreślone na jednej osi y, ale wartości na osi y zostaną zmienione na podstawie wyróżnione miary. Wykresy nieskumulowanych są przydatne do nakładki wielu miar zobaczyć wzorce w tych środków dla tego samego zakresu czasu.
* **Udostępniony osi y:** wszystkie wykresy udostępnianie tej samej osi y, a nie powoduje zmiany wartości osi. Udostępniony wykresy osi y są przydatne, gdy chcesz przejrzeć jednej miary podczas tworzenia wycinków danych z podziału przez.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób tworzenia niestandardowych analytics Azure IoT centralnej aplikacji, w tym miejscu sugerowane następnym krokiem jest:

> [!div class="nextstepaction"]
> [Przygotowanie i połącz aplikację Node.js](howto-connect-nodejs.md)