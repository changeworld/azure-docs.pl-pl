---
title: Tworzenie i uruchamianie zadań w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Zadania usługi Azure IoT Central umożliwia zbiorcze zarządzanie urządzeniami, takie jak aktualizowanie właściwości urządzenia, ustawienia lub wykonywania polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519360"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Twórz i uruchamiaj zadanie w aplikacji centralnej IoT platformy Azure

Microsoft Azure IoT Central umożliwia zarządzanie połączonych urządzeń na dużą skalę przy użyciu zadań. Zadania umożliwiają zbiorczej aktualizacji właściwości urządzenia, ustawienia i polecenia. W tym artykule przedstawiono sposób rozpocząć korzystanie z zadań w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji dowiesz się, jak utworzyć i uruchomić zadanie. Jego pokazano, jak zwiększyć szybkość wentylator dla wielu mrożone automaty.

1. Przejdź do zadania w okienku nawigacji.

1. Wybierz **+ nowy** do utworzenia nowego zadania.

    ![Tworzenie nowego zadania](./media/howto-run-a-job/createnewjob.png)

1. Wprowadź nazwę i opis, aby zidentyfikować zadania, które tworzysz.

1. Wybierz zestaw urządzenia mają zadania mają dotyczyć. Po wybraniu urządzenia, zostanie wyświetlony po prawej stronie wypełniona urządzeń w zestawie urządzeń. Jeśli wybierzesz zestaw uszkodzone urządzeń, urządzenia nie są wyświetlane i zostanie wyświetlony komunikat, że zestaw urządzenia jest uszkodzony.

1. Następnie wybierz typ zadania, aby zdefiniować (ustawienie, właściwość lub polecenie). Wybierz **+** obok typ zadania zaznaczone, a następnie dodaj operacji.

    ![Konfigurowanie zadania](./media/howto-run-a-job/configurejob.png)

1. Po prawej stronie Wybierz urządzenia, o których chcesz zadanie zostanie uruchomione. Zaznaczając pole wyboru w górnej, wszystkie urządzenia są wybierane w zestawie całego urządzenia. Zaznaczając pole wyboru obok **nazwa**, są zaznaczone wszystkie urządzenia na bieżącej stronie.

1. Po wybraniu urządzenia, wybierz **Uruchom** lub **Zapisz**. Zadanie będzie teraz widoczna na głównego **zadań** strony. W tym widoku widać, aktualnie uruchomione zadanie i historii wszelkich uprzednio uruchamiania zadań. Uruchamianie zadania zawsze wyświetlane w górnej części listy. Zapisane zadania można otworzyć w dowolnym momencie, by kontynuować edycję lub uruchomić ponownie.

    ![Wyświetl zadanie](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Możesz wyświetlić historię poprzednio uruchomionych zadań przez maksymalnie 30 dni.

1. Aby uzyskać przegląd zadania, wybierz zadanie, aby wyświetlić z listy. Ten przegląd zawiera szczegóły zadania, urządzeń i wartości stanu urządzenia. W tym omówieniu, możesz również wybrać **szczegóły zadania pobierania** do pobrania plik CSV z szczegóły zadania, w tym urządzeniami i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów.

    ![Wyświetlanie stanu urządzenia](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zatrzymaj uruchomione zadanie

Aby zatrzymać uruchomione zadanie, zaznacz ją i wybierz **zatrzymać** na panelu. Zmiany stanu zadania, aby odzwierciedlić zadania został zatrzymany.

   ![Zatrzymaj zadanie](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Uruchom zadanie zatrzymania

Aby uruchomić zadanie, które jest obecnie zatrzymana, wybierz zadanie zatrzymania. Wybierz **Uruchom** na panelu. Zmiany stanu zadania, aby odzwierciedlić zadania jest teraz uruchomiona ponownie.

   ![Wznowiono zadanie](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiuj zadanie

Aby skopiować istniejącego zadania został utworzony, wybierz ją ze strony główne zadania, a następnie wybierz **kopiowania**. Otwiera nową kopię konfiguracji zadania do edycji. Można zapisać lub Uruchom nowe zadanie. Jeśli wprowadzono zmiany do zestawu wybranych urządzeń, są zostaną uwzględnione w ramach tego zadania skopiowany do edycji.

   ![Zadanie kopiowania](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania **stan** aktualizacje kolumn przy użyciu najnowszych komunikat o stanie zadania. W poniższej tabeli wymieniono wartości stanu możliwe:

| Komunikat o stanie       | Znaczenie stanu                                          |
| -------------------- | ------------------------------------------------------- |
| Zakończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie ma nie powiodło się i nie są w pełni wykonane na urządzeniach.  |
| Oczekujące              | To zadanie nie zostało jeszcze rozpoczęte wykonywania na urządzeniach.         |
| Działanie              | To zadanie jest w trakcie wykonywania na urządzeniach.             |
| Zatrzymano              | To zadanie została ręcznie zatrzymana przez użytkownika.           |

Komunikat o stanie następuje przegląd urządzeń w ramach zadania. W poniższej tabeli wymieniono wartości stanu dostępnego urządzenia:

| Komunikat o stanie       | Znaczenie stanu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Powodzenie            | Liczba urządzeń, które zadania są pomyślnie wykonywane na.       |
| Niepowodzenie               | Liczba urządzeń, które zadania nie udało się wykonać na.       |

### <a name="view-the-device-status"></a>Wyświetlanie stanu urządzeń

Aby wyświetlić stan zadania i uwzględnionych urządzeń, wybierz zadanie. Aby pobrać plik CSV zawierający szczegóły zadania, w tym listę urządzeń i ich wartości stanu wybierz **Pobierz szczegóły zadania**. Obok nazwy każdego urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Znaczenie stanu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Zakończone            | Zadanie zostało wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Zadanie nie powiodło się wykonanie na tym urządzeniu. Komunikat o błędzie zawiera więcej informacji.  |
| Oczekujące              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                   |

> [!NOTE]
> Usunięcie urządzenia z systemem nie można wybrać urządzenie i wyświetlane jako usunięte z identyfikatorem urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak tworzyć zadania w aplikacji usługi Azure IoT Central, Oto następne kroki:

- [Korzystanie ze zbiorów urządzeń](howto-use-device-sets.md)
- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-devicetemplate.md)
