---
title: Twórz i uruchamiaj zadania w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Zadania IoT Central platformy Azure umożliwiają obsługę zbiorczych funkcji zarządzania urządzeniami, takich jak aktualizowanie właściwości lub wykonywanie polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206824"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure

Za pomocą IoT Central Microsoft Azure można zarządzać podłączonymi urządzeniami w odpowiedniej skali przy użyciu zadań. Zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości urządzeń i uruchamianie poleceń. W tym artykule pokazano, jak rozpocząć pracę z zadaniami w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji pokazano, jak utworzyć i uruchomić zadanie. Pokazuje, jak ustawić próg światła dla grupy urządzeń z bramą logistyczną.

1. Przejdź do **zadania** w okienku po lewej stronie.

2. Wybierz pozycję **+ Nowy** , aby utworzyć nowe zadanie:

    > [!div class="mx-imgBorder"]
    > ![utworzyć nowe zadanie](./media/howto-run-a-job/createnewjob.png)

3. Wprowadź nazwę i opis, aby zidentyfikować tworzone zadanie.

4. Wybierz docelową grupę urządzeń, do której chcesz zastosować zadanie. Liczba urządzeń, do których odnosi się konfiguracja zadania, znajduje się w sekcji **Podsumowanie** .

5. Następnie wybierz jedną z **Właściwości** lub **polecenie** jako typ zadania do skonfigurowania. Aby skonfigurować konfigurację zadania **Właściwości** , wybierz właściwość i ustaw jej nową wartość. Aby skonfigurować **polecenie**lub wybrać polecenie do uruchomienia. Zadanie właściwości można ustawić wiele właściwości:

    > [!div class="mx-imgBorder"]
    > ![skonfigurować](./media/howto-run-a-job/configurejob.png) zadania

6. Po wybraniu urządzeń wybierz pozycję **Uruchom** lub **Zapisz**. Zadanie zostanie wyświetlone na stronie główne **zadania** . Na tej stronie można zobaczyć aktualnie uruchomione zadanie i historię wszystkich poprzednio uruchomionych lub zapisanych zadań. Zapisane zadanie można otworzyć ponownie w dowolnym momencie, aby kontynuować jego edytowanie lub uruchomić:

    > [!div class="mx-imgBorder"]
    > ](./media/howto-run-a-job/viewjob.png) zadania widoku ![

    > [!NOTE]
    > Możesz wyświetlić 30-dniową historię poprzednio wykonywanych zadań.

7. Aby uzyskać przegląd zadania, wybierz z listy zadanie do wyświetlenia. To omówienie zawiera szczegóły zadania, urządzenia i wartości stanu urządzenia. Z tego omówienia można także wybrać opcję **Pobierz szczegóły zadania** , aby pobrać plik CSV zawierający szczegóły zadania, w tym urządzenia i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów:

    > [!div class="mx-imgBorder"]
    > Wyświetl ![stanu urządzenia](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zatrzymaj uruchomione zadanie

Aby zatrzymać jedno z uruchomionych zadań, otwórz je i wybierz pozycję **Zatrzymaj**. Stan zadania zmienia się, aby odzwierciedlić zadanie zostało zatrzymane. Sekcja **podsumowania** pokazuje, które urządzenia zostały ukończone, zakończone niepowodzeniem lub nadal oczekuje:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Uruchom zadanie zatrzymane

Aby uruchomić zadanie, które jest aktualnie zatrzymane, zaznacz je, a następnie wybierz polecenie **Uruchom**. Stan zadania zmienia się w celu odzwierciedlenia, że zadanie jest teraz uruchomione ponownie. Sekcja **podsumowania** kontynuuje aktualizację z najnowszym postępem:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować jedno z istniejących zadań, otwórz je i wybierz pozycję **Kopiuj**. Zostanie wyświetlona kopia konfiguracji zadania do edycji, a na końcu nazwy zadania zostanie dodana **kopia** . Możesz zapisać lub uruchomić nowe zadanie:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Wyświetl stan zadania

Po utworzeniu zadania kolumna **stan** będzie aktualizowana przy użyciu najnowszego komunikatu o stanie zadania. Poniższa tabela zawiera listę możliwych wartości stanu:

| Komunikat o stanie       | Stan znaczenie                                          |
| -------------------- | ------------------------------------------------------- |
| Zakończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie zostało w pełni wykonane na urządzeniach.  |
| Oczekiwanie              | To zadanie jeszcze nie rozpoczęło wykonywania na urządzeniach.         |
| Działanie              | To zadanie jest obecnie wykonywane na urządzeniach.             |
| Zatrzymano              | To zadanie zostało ręcznie zatrzymane przez użytkownika.           |

Po komunikacie o stanie następuje przegląd urządzeń w zadaniu. Poniższa tabela zawiera listę możliwych wartości stanu urządzenia:

| Komunikat o stanie       | Stan znaczenie                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Powodzenie            | Liczba urządzeń, na których zadanie zostało pomyślnie wykonane.       |
| Niepowodzenie               | Liczba urządzeń, na których wykonywanie zadania nie powiodło się.       |

### <a name="view-the-device-status"></a>Wyświetlanie stanu urządzenia

Aby wyświetlić stan zadania i wszystkie urządzenia, których to dotyczy, wybierz zadanie. Aby pobrać plik CSV zawierający szczegóły zadania, w tym listę urządzeń i ich wartości stanu, wybierz pozycję **Pobierz szczegóły zadania**. Obok każdej nazwy urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Stan znaczenie                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Zakończone            | Zadanie zostało wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Wykonanie zadania na tym urządzeniu nie powiodło się. Komunikat o błędzie zawiera więcej informacji.  |
| Oczekiwanie              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                   |

> [!NOTE]
> Jeśli urządzenie zostało usunięte, nie można wybrać urządzenia. Jest on wyświetlany jako usunięty z IDENTYFIKATORem urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć zadania w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
