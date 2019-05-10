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
ms.openlocfilehash: b1edd4777839c947cf8f60bb6857f939553e2603
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467626"
---
# <a name="configure-the-application-dashboard"></a>Konfigurowanie pulpit nawigacyjny aplikacji

**Pulpit nawigacyjny** jest strona, która ładuje, gdy użytkownicy, którzy mają dostęp do aplikacji, przejdź do adresu URL aplikacji. Jeśli została wybrana **Contoso przykładowe** lub **Devkits przykładowe** szablon aplikacji do tworzenia aplikacji, aplikacja jest wstępnie zdefiniowany pulpit nawigacyjny. Jeśli została wybrana opcja **aplikacji niestandardowych** szablon aplikacji pulpitu nawigacyjnego jest pusta.

> [!NOTE]
> Użytkownicy mogą również [tworzenie własnych osobistych pulpitów nawigacyjnych](howto-personalize-dashboard.md) zamiast domyślnego pulpitu nawigacyjnego aplikacji.

## <a name="add-tiles"></a>Dodaj Kafelki

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji, który został utworzony na podstawie **Contoso przykładowe** szablonu. Aby dostosować domyślny pulpit nawigacyjny dla swojej aplikacji, wybierz pozycję **Edytuj** w prawym górnym rogu strony.

![Pulpit nawigacyjny dla aplikacji na podstawie szablonu "Contoso próbki"](media/howto-configure-homepage/image1a.png)

Wybieranie **Edytuj**, zostanie otwarty panel Biblioteka pulpitu nawigacyjnego. Biblioteka zawiera Kafelki i podstawowych pulpitu nawigacyjnego, którym można dostosować pulpit nawigacyjny.

![Biblioteka pulpitu nawigacyjnego](media/howto-configure-homepage/image2a.png)

Na przykład można dodać **ustawień i właściwości** Kafelek, aby pokazywać zaznaczenie bieżące wartości ustawień i właściwości dla urządzenia. Aby to zrobić, najpierw wybierz **szablon urządzenia** polecenie **wystąpienia urządzenia**. Po które podają kafelka, tytuł i wybierz **ustawienie** lub **właściwość** do wyświetlenia. Poniższy zrzut ekranu przedstawia ustawienia i właściwości wybranego do dodania do kafelka. Wybierz **gotowe** można zapisać zmiany do pulpitu nawigacyjnego.

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](media/howto-configure-homepage/image3a.png)

Teraz gdy operator Wyświetla domyślny pulpit nawigacyjny aplikacji, zobacz temat nowy Kafelek z **Ustaw temperatury** ustawienia dla urządzenia:

![Karta "Pulpit nawigacyjny" z wyświetlanych ustawień i właściwości dla kafelka](media/howto-configure-homepage/image4a.png)

Możesz eksplorować inne typy kafelków w bibliotece Dowiedz się, jak dostosować domyślny pulpit nawigacyjny aplikacji.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposób konfigurowania usługi Azure IoT Central domyślnego aplikacji pulpitu nawigacyjnego, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Dowiedz się, jak przygotować i przekazać obrazy](howto-prepare-images.md)
