---
title: Konfigurowanie usługi Azure IoT Central pulpit nawigacyjny aplikacji | Dokumentacja firmy Microsoft
description: Jako Konstruktor Dowiedz się, jak skonfigurować domyślny usługi Azure IoT Central aplikacji pulpit nawigacyjny.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 033a7207765f2ee6712f8552d5c1585280250d5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312073"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpit nawigacyjny aplikacji

**Pulpit nawigacyjny** jest strona, która ładuje, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdź do adresu URL aplikacji. Jeśli została wybrana **Contoso przykładowe** lub **Devkits przykładowe** szablon aplikacji do tworzenia aplikacji, aplikacja jest wstępnie zdefiniowany pulpit nawigacyjny. Jeśli została wybrana opcja **aplikacji niestandardowych** szablon aplikacji pulpitu nawigacyjnego jest pusta.

> [!NOTE]
> Użytkownicy mogą również [tworzenie własnych osobistych pulpitów nawigacyjnych](howto-personalize-dashboard.md) zamiast domyślnego pulpitu nawigacyjnego aplikacji.

## <a name="add-tiles"></a>Dodaj Kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji, który został utworzony na podstawie **Contoso przykładowe** szablonu. Aby dostosować domyślny pulpit nawigacyjny dla swojej aplikacji, wybierz pozycję **Edytuj** w prawym górnym rogu strony.

![Pulpit nawigacyjny dla aplikacji na podstawie szablonu "Contoso próbki"](media/howto-configure-homepage/image1.png)

Wybieranie **Edytuj**, zostanie otwarty panel Biblioteka pulpitu nawigacyjnego. Biblioteka zawiera Kafelki i podstawowych pulpitu nawigacyjnego, którym można dostosować pulpit nawigacyjny.

![Biblioteka pulpitu nawigacyjnego](media/howto-configure-homepage/image2.png)

Na przykład można dodać **ustawień i właściwości** Kafelek, aby pokazywać zaznaczenie bieżące wartości ustawień i właściwości dla urządzenia. Aby to zrobić, najpierw wybierz **szablon urządzenia** polecenie **wystąpienia urządzenia**. Po które podają kafelka, tytuł i wybierz **ustawienie** lub **właściwość** do wyświetlenia. Poniższy zrzut ekranu przedstawia **Ustaw temperatury** ustawienie wybranego do dodania do kafelka. Wybierz **gotowe** można zapisać zmiany do pulpitu nawigacyjnego.

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](media/howto-configure-homepage/image3.png)

Teraz gdy operator Wyświetla domyślny pulpit nawigacyjny aplikacji, zobacz temat nowy Kafelek z **Ustaw temperatury** ustawienia dla urządzenia:

![Karta "Pulpit nawigacyjny" z wyświetlanych ustawień i właściwości dla kafelka](media/howto-configure-homepage/image4.png)

Możesz eksplorować inne typy kafelków w bibliotece Dowiedz się, jak dostosować domyślny pulpit nawigacyjny aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania usługi Azure IoT Central domyślnego aplikacji pulpitu nawigacyjnego, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Dowiedz się, jak przygotować i przekazać obrazy](howto-prepare-images.md)
