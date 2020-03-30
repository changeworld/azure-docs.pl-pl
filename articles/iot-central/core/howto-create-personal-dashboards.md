---
title: Tworzenie osobistych pulpitów nawigacyjnych usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Jako użytkownik dowiedz się, jak tworzyć osobiste pulpity nawigacyjne i zarządzać nimi.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: db886006ff5b9adf3de0932951f6cce4958e8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158133"
---
# <a name="create-and-manage-multiple-dashboards"></a>Tworzenie wielu pulpitów nawigacyjnych i zarządzanie nimi

Pulpit **nawigacyjny** to strona, która ładuje się podczas pierwszej nawigacji do aplikacji. **Konstruktor** w aplikacji definiuje domyślny pulpit nawigacyjny aplikacji dla wszystkich użytkowników. Ten domyślny pulpit nawigacyjny można zastąpić własnym, spersonalizowanym pulpitem nawigacyjnym aplikacji. Możesz mieć kilka pulpitów nawigacyjnych, które wyświetlają różne dane i przełączają się między nimi. 

Jeśli jesteś **administratorem** aplikacji, możesz również utworzyć maksymalnie 10 pulpitów nawigacyjnych poziomu aplikacji, aby udostępnić je innym użytkownikom aplikacji. Tylko **administratorzy** mają możliwość tworzenia, edytowania i usuwania pulpitów nawigacyjnych na poziomie aplikacji. 

## <a name="create-dashboard"></a>Tworzenie pulpitu nawigacyjnego

Poniższy zrzut ekranu przedstawia pulpit nawigacyjny w aplikacji utworzonej na podstawie szablonu **aplikacji niestandardowej.** Domyślny pulpit nawigacyjny aplikacji można zastąpić pulpitem nawigacyjnym aplikacji, a jeśli jesteś administratorem, innym pulpitem nawigacyjnym na poziomie aplikacji. Aby to zrobić, wybierz **+ Nowy** w lewym górnym rogu strony.
 
> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny dla aplikacji oparty na szablonie "Aplikacja niestandardowa"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Wybranie **+ Nowy** otwiera edytor pulpitu nawigacyjnego. W edytorze możesz nadać panelowi nazwę i wybrać elementy z biblioteki. Biblioteka zawiera kafelki i elementy pierwotne pulpitu nawigacyjnego, których można użyć do dostosowania pulpitu nawigacyjnego.

> [!div class="mx-imgBorder"]
> ![Biblioteka pulpitu nawigacyjnego](media/howto-create-personal-dashboards/dashboard-library.png)

Jeśli jesteś **administratorem** aplikacji, otrzymasz opcję przełączania, jeśli chcesz utworzyć pulpit nawigacyjny na poziomie osobistym lub pulpit nawigacyjny na poziomie aplikacji. Jeśli utworzysz osobisty pulpit nawigacyjny poziomu, tylko Ty będzie można go zobaczyć. Jeśli utworzysz pulpit nawigacyjny na poziomie aplikacji, każdy użytkownik aplikacji będzie mógł go zobaczyć. Po wprowadzeniu tytułu i wybraniu typu pulpitu nawigacyjnego, który chcesz utworzyć, można później zapisać i dodać kafelki. Jeśli jesteś gotowy i dodałeś szablon urządzenia i wystąpienie urządzenia, możesz utworzyć pierwszy kafelek. 

> [!div class="mx-imgBorder"]
> ![Konfigurowanie formularza Szczegóły urządzenia z informacjami dotyczącymi temperatury](media/howto-create-personal-dashboards/device-details.png)

Na przykład można dodać kafelek **Telemetrii** dla bieżącej temperatury urządzenia. W tym celu:
1. Wybieranie **szablonu urządzenia**
1. Wybierz **wystąpienie urządzenia** dla urządzenia, które chcesz wyświetlić na kafelku pulpitu nawigacyjnego. Następnie zostanie wyświetlona lista właściwości urządzenia, które mogą być używane na kafelku.
1. Aby utworzyć kafelek na pulpicie nawigacyjnym, kliknij **pozycję Temperatura** i przeciągnij go do obszaru pulpitu nawigacyjnego. Możesz również kliknąć pole wyboru obok **pozycji Temperatura** i kliknąć pozycję **Połącz**. Poniższy zrzut ekranu przedstawia wybranie szablonu urządzenia i wystąpienia urządzenia, a następnie utworzenie kafelka telemetrii temperatury na pulpicie nawigacyjnym.
1. Wybierz **pozycję Zapisz** w lewym górnym rogu, aby zapisać kafelek na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Karta Dashboard" ze szczegółami kafelka Temperatura](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Teraz podczas wyświetlania osobistego pulpitu nawigacyjnego zostanie wyświetlony nowy kafelek z ustawieniem **Temperatura** dla urządzenia:

> [!div class="mx-imgBorder"]
> ![Karta Dashboard" ze szczegółami kafelka Temperatura](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Możesz eksplorować inne typy kafelków w bibliotece, aby dowiedzieć się, jak jeszcze bardziej dostosować osobiste pulpity nawigacyjne.

Aby dowiedzieć się więcej o używaniu kafelków w usłudze Azure IoT Central, zobacz [Dodawanie kafelków do pulpitu nawigacyjnego](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Zarządzanie pulpitami nawigacyjnymi

Możesz mieć kilka osobistych pulpitów nawigacyjnych i przełączać się między nimi lub wybierać jeden z domyślnych pulpitów nawigacyjnych aplikacji:

> [!div class="mx-imgBorder"]
> ![Przełączanie między pulpitami nawigacyjnymi](media/howto-create-personal-dashboards/switch-dashboards.png)

Możesz edytować osobiste pulpity nawigacyjne i usuwać wszystkie pulpity nawigacyjne, których już nie potrzebujesz. Jeśli jesteś **administratorem,** masz również możliwość edytowania lub usuwania pulpitów nawigacyjnych poziomu aplikacji.

> [!div class="mx-imgBorder"]
> ![Usuwanie pulpitów nawigacyjnych](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak tworzyć osobiste pulpity nawigacyjne i zarządzać nimi, możesz [dowiedzieć się, jak zarządzać preferencjami aplikacji](howto-manage-preferences.md)
