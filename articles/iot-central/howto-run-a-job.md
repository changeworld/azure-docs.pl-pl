---
title: Twórz i uruchamiaj zadania w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Zadania IoT Central platformy Azure umożliwiają obsługę zbiorczych funkcji zarządzania urządzeniami, takich jak aktualizowanie właściwości urządzenia, Ustawianie lub wykonywanie polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 298770b1b2da816ddef9154fafb20d7c6cb82df3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849036"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji IoT Central platformy Azure

Za pomocą IoT Central Microsoft Azure można zarządzać podłączonymi urządzeniami w odpowiedniej skali przy użyciu zadań. Zadania umożliwiają wykonywanie aktualizacji zbiorczych do właściwości, ustawień i poleceń urządzeń. W tym artykule przedstawiono sposób rozpoczynania pracy z zadaniami we własnej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji pokazano, jak utworzyć i uruchomić zadanie. Pokazuje, jak zwiększyć szybkość wentylatorów dla wielu maszyn z chłodnym automatem.

1. Przejdź do zadania w okienku nawigacji.

1. Wybierz pozycję **+ Nowy** , aby utworzyć nowe zadanie.

    ![Utwórz nowe zadanie](./media/howto-run-a-job/createnewjob.png)

1. Wprowadź nazwę i opis, aby zidentyfikować tworzone zadanie.

1. Wybierz zestaw urządzeń, dla którego chcesz zastosować zadanie. Po wybraniu zestawu urządzeń zobaczysz odpowiednie wypełnienie strony z urządzeniami w zestawie urządzeń. W przypadku wybrania uszkodzonego zestawu urządzeń nie są wyświetlane żadne urządzenia i zostanie wyświetlony komunikat o przerwaniu konfiguracji urządzenia.

1. Następnie wybierz typ zadania do zdefiniowania (ustawienie, właściwość lub polecenie). Wybierz **+** pozycję obok wybranego typu zadania i Dodaj swoje operacje.

    ![Konfigurowanie zadania](./media/howto-run-a-job/configurejob.png)

1. Po prawej stronie Wybierz urządzenia, na których chcesz uruchomić zadanie. Zaznaczając górne pole wyboru, wszystkie urządzenia są wybierane w całym zestawie urządzeń. Zaznaczając pole wyboru obok **nazwy**, wybierane są wszystkie urządzenia na bieżącej stronie.

1. Po wybraniu urządzeń wybierz pozycję **Uruchom** lub **Zapisz**. Zadanie zostanie wyświetlone na stronie główne **zadania** . W tym widoku można zobaczyć aktualnie uruchomione zadanie i historię wszystkich poprzednio uruchomionych zadań. Uruchomione zadanie zawsze jest wyświetlane w górnej części listy. Zapisane zadanie można otworzyć ponownie w dowolnym momencie, aby kontynuować edycję lub uruchomienie.

    ![Wyświetl zadanie](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Możesz wyświetlić historię poprzednio wykonywanych zadań przez maksymalnie 30 dni.

1. Aby uzyskać przegląd zadania, wybierz z listy zadanie do wyświetlenia. To omówienie zawiera szczegóły zadania, urządzenia i wartości stanu urządzenia. Z tego omówienia można także wybrać opcję **Pobierz szczegóły zadania** , aby pobrać plik CSV zawierający szczegóły zadania, w tym urządzenia i ich wartości stanu. Te informacje mogą być przydatne podczas rozwiązywania problemów.

    ![Wyświetlanie stanu urządzenia](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Zatrzymaj uruchomione zadanie

Aby zatrzymać uruchomione zadanie, zaznacz je i wybierz pozycję **Zatrzymaj** na panelu. Stan zadania zmienia się, aby odzwierciedlić zadanie zostało zatrzymane.

   ![Zatrzymaj zadanie](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Uruchom zadanie zatrzymane

Aby uruchomić zadanie, które jest aktualnie zatrzymane, wybierz zatrzymane zadanie. Wybierz **Uruchom** na panelu. Stan zadania zmienia się w celu odzwierciedlenia, że zadanie jest teraz uruchomione ponownie.

   ![Wznowione zadanie](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować istniejące utworzone zadanie, wybierz je ze strony główne zadania i wybierz pozycję **Kopiuj**. Zostanie otwarta nowa kopia konfiguracji zadania, którą można edytować. Możesz zapisać lub uruchomić nowe zadanie. Jeśli wprowadzono zmiany w wybranym zestawie urządzeń, zostaną one odzwierciedlone w tym zadaniu skopiowanym do edycji.

   ![Kopiuj zadanie](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Wyświetl stan zadania

Po utworzeniu zadania kolumna **stan** będzie aktualizowana przy użyciu najnowszego komunikatu o stanie zadania. Poniższa tabela zawiera listę możliwych wartości stanu:

| Komunikat o stanie       | Stan znaczenie                                          |
| -------------------- | ------------------------------------------------------- |
| Ukończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie zostało w pełni wykonane na urządzeniach.  |
| Oczekujące              | To zadanie jeszcze nie rozpoczęło wykonywania na urządzeniach.         |
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
| Ukończone            | Zadanie zostało wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Wykonanie zadania na tym urządzeniu nie powiodło się. Komunikat o błędzie zawiera więcej informacji.  |
| Oczekujące              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                   |

> [!NOTE]
> Jeśli urządzenie zostało usunięte, nie można wybrać urządzenia i zostanie ono wyświetlone jako usunięte z IDENTYFIKATORem urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak tworzyć zadania w aplikacji IoT Central platformy Azure, Oto kilka następnych kroków:

- [Korzystanie ze zbiorów urządzeń](howto-use-device-sets.md)
- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
