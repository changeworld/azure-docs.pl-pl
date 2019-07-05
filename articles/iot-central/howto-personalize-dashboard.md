---
title: Tworzenie usługi Azure IoT Central osobistych pulpitów nawigacyjnych | Dokumentacja firmy Microsoft
description: Jako użytkownik Dowiedz się, jak tworzyć i zarządzać nimi Osobiste pulpity nawigacyjne.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c048ae8c0daba0e467a9243f4dd83f8d95921e10
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502652"
---
# <a name="create-and-manage-personal-dashboards"></a>Tworzenie i zarządzanie nimi osobistych pulpitów nawigacyjnych

**Pulpit nawigacyjny** jest strona, która ładuje, gdy po raz pierwszy przejdziesz do aplikacji. A **konstruktora** definiuje domyślny pulpit nawigacyjny aplikacji dla wszystkich użytkowników w aplikacji. To domyślny pulpit nawigacyjny można zastąpić własnymi, pulpit nawigacyjny spersonalizowanych aplikacji. Może mieć kilka pulpitów nawigacyjnych, które wyświetlania różnych danych i przełączać się między nimi.

## <a name="create-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji, który został utworzony na podstawie **Contoso przykładowe** szablonu. Osobisty pulpit nawigacyjny można zastąpić domyślny pulpit nawigacyjny aplikacji. Aby to zrobić, wybierz **+ nowy** w prawym górnym rogu strony.

![Pulpit nawigacyjny dla aplikacji na podstawie szablonu "Contoso próbki"](media/howto-personalize-dashboard/defaultdashboard.png)

Wybieranie **+ nowy**, zostanie otwarty Edytor pulpitu nawigacyjnego. W edytorze pulpitu nawigacyjnego można nadać nazwę, a wybrane elementy z biblioteki. Biblioteka zawiera Kafelki i podstawowych pulpitu nawigacyjnego, którym można dostosować pulpit nawigacyjny.

![Biblioteka pulpitu nawigacyjnego](media/howto-personalize-dashboard/dashboardeditor.png)

Na przykład można dodać **ustawień i właściwości** Kafelek, aby wyświetlić wartości ustawień i właściwości dla jednego z urządzeń, którymi zarządzasz. Aby to zrobić, najpierw wybierz **szablon urządzenia** polecenie **wystąpienia urządzenia**. Następnie nadaj kafelka, tytuł i wybierz **ustawienie** lub **właściwość** do wyświetlenia. Poniższy zrzut ekranu przedstawia **obsługuje szybkość** ustawienie wybranego do dodania do kafelka. Wybierz **gotowe** można zapisać zmiany do swojego pulpitu nawigacyjnego.

!["Configure szczegóły urządzenia" formularz Szczegóły ustawień i właściwości](media/howto-personalize-dashboard/dashboardsetting.png)

Teraz po wyświetleniu pulpitu nawigacyjnego osobistych, zostanie wyświetlony nowy Kafelek z **obsługuje szybkość** ustawienia dla urządzenia:

![Karta "Pulpit nawigacyjny" z wyświetlanych ustawień i właściwości dla kafelka](media/howto-personalize-dashboard/personaldashboard.png)

Możesz eksplorować inne typy kafelków w bibliotece Dowiedz się, jak dostosować swoje osobiste pulpity nawigacyjne.

Aby dowiedzieć się więcej o sposobie używania fragmentacji w usłudze Azure IoT Central, zobacz [Użyj Kafelki pulpitu nawigacyjnego](howto-use-tiles.md).

## <a name="manage-dashboards"></a>Zarządzanie pulpitami nawigacyjnymi

Może mieć kilka osobistych pulpitów nawigacyjnych i przełączać się między nimi, lub wybierz domyślny pulpit nawigacyjny aplikacji:

![Pulpit nawigacyjny przełącznika](media/howto-personalize-dashboard/switchdashboards.png)

Możesz edytować swoje osobiste pulpity nawigacyjne i usuń te, które nie są już potrzebne:

![Usuwanie pulpitu nawigacyjnego](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak tworzyć i zarządzać nimi osobistych pulpitów nawigacyjnych, możesz wykonywać następujące czynności:

> [!div class="nextstepaction"]
> [Dowiedz się, jak zarządzać swoimi preferencjami dotyczącymi aplikacji](howto-manage-preferences.md)
