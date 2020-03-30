---
title: Tworzenie i uruchamianie zadań w aplikacji Azure IoT Central | Dokumenty firmy Microsoft
description: Zadania usługi Azure IoT Central umożliwiają korzystanie z funkcji zarządzania urządzeniami zbiorczymi, takich jak aktualizowanie właściwości lub wykonywanie polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157759"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Tworzenie i uruchamianie zadania w aplikacji Azure IoT Central

Za pomocą usługi Microsoft Azure IoT Central można zarządzać podłączonymi urządzeniami na dużą skalę przy użyciu zadań. Zadania umożliwiają zbiorcze aktualizowanie właściwości urządzenia i uruchamianie poleceń. W tym artykule pokazano, jak rozpocząć korzystanie z zadań we własnej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji pokazano, jak utworzyć i uruchomić zadanie. Pokazuje, jak ustawić próg światła dla grupy urządzeń bramy logistycznej.

1. Przejdź do **pozycji Zadania** z lewego okienka.

2. Wybierz **+ Nowy,** aby utworzyć nowe zadanie:

    ![Tworzenie nowego zadania](./media/howto-run-a-job/createnewjob.png)

3. Wprowadź nazwę i opis, aby zidentyfikować tworzenie zadania.

4. Wybierz docelową grupę urządzeń, do której ma zostać zastosowane zadanie. W sekcji **Podsumowanie** możesz sprawdzić, ile urządzeń dotyczy konfiguracja zadania.

5. Następnie wybierz **właściwość cloud,** **właściwość** lub **polecenie** jako typ zadania do skonfigurowania. Aby skonfigurować konfigurację zadania **właściwości,** wybierz właściwość i ustaw jej nową wartość. Aby skonfigurować **polecenie**, wybierz polecenie do uruchomienia. Zadanie właściwości można ustawić wiele właściwości:

    ![Konfigurowanie zadania](./media/howto-run-a-job/configurejob.png)

6. Po utworzeniu zadania wybierz pozycję **Uruchom** lub **Zapisz**. Zadanie pojawi się teraz na głównej stronie **zadania.** Na tej stronie możesz zobaczyć aktualnie uruchomione zadanie i historię wszystkich wcześniej uruchomionych lub zapisanych zadań. Zapisane zadanie można otworzyć ponownie w dowolnym momencie, aby kontynuować edycję lub uruchomić:

    ![Wyświetl zadanie](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Dla wcześniej uruchomionych zadań można wyświetlić 30 dni historii.

7. Aby uzyskać przegląd zadania, wybierz zadanie do wyświetlenia z listy. Ten przegląd zawiera szczegóły zadania, urządzenia i wartości stanu urządzenia. Z tego przeglądu możesz również wybrać **opcję Pobierz szczegóły zadania,** aby pobrać plik CSV ze szczegółami zadania, w tym urządzeniami i ich wartościami stanu. Te informacje mogą być przydatne do rozwiązywania problemów:

    ![Wyświetlanie stanu urządzenia](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Zarządzanie zadaniem

Aby zatrzymać jedno z uruchomionych zadań, otwórz je i wybierz **pozycję Zatrzymaj**. Stan zadania zmienia się, aby odzwierciedlić zadanie jest zatrzymane. Sekcja **Podsumowanie** pokazuje, które urządzenia zostały ukończone, nie powiodło się lub nadal są oczekujące.

Aby uruchomić zadanie, które jest obecnie zatrzymane, zaznacz je, a następnie wybierz pozycję **Uruchom**. Stan zadania zmienia się, aby odzwierciedlić zadanie jest teraz uruchomiony ponownie. Sekcja **Podsumowanie** jest nadal aktualizowana z najnowszymi postępami.

![Zarządzanie zadaniem](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Kopiowanie zadania

Aby skopiować jedno z istniejących zadań, zaznacz je na stronie **Zadania** i wybierz pozycję **Kopiuj**. Zostanie otwarta kopia konfiguracji zadania do edycji, a **copy** zostanie dołączona do nazwy zadania. Możesz zapisać lub uruchomić nowe zadanie:

![Kopiuj zadanie](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania kolumna **Stan** zostanie zaktualizowana z najnowszym komunikatem o stanie zadania. W poniższej tabeli wymieniono możliwe wartości stanu:

| Komunikat o stanie       | Znaczenie stanu                                          |
| -------------------- | ------------------------------------------------------- |
| Zakończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie nie powiodło się i nie jest w pełni wykonane na urządzeniach.  |
| Oczekujące              | To zadanie nie zostało jeszcze rozpoczęte na urządzeniach.         |
| Działanie              | To zadanie jest obecnie wykonywane na urządzeniach.             |
| Zatrzymano              | To zadanie zostało ręcznie zatrzymane przez użytkownika.           |

Po komunikacie o stanie następuje przegląd urządzeń w zadaniu. W poniższej tabeli wymieniono możliwe wartości stanu urządzenia:

| Komunikat o stanie       | Znaczenie stanu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Powodzenie            | Liczba urządzeń, na których zadanie zostało pomyślnie wykonane.       |
| Niepowodzenie               | Liczba urządzeń, na których zadanie nie zostało wykonane.       |

### <a name="view-the-device-status"></a>Wyświetlanie stanu urządzenia

Aby wyświetlić stan zadania i wszystkich urządzeń, których dotyczy problem, otwórz zadanie. Aby pobrać plik CSV zawierający szczegóły zadania, w tym listę urządzeń i ich wartości stanu, wybierz **pozycję Pobierz szczegóły zadania**. Obok każdej nazwy urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Znaczenie stanu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Zakończone            | Zadanie zostało wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Zadanie nie powiodło się na tym urządzeniu. Komunikat o błędzie pokazuje więcej informacji.  |
| Oczekujące              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                   |

> [!NOTE]
> Jeśli urządzenie zostało usunięte, nie można go wybrać. Jest wyświetlany jako usunięty z identyfikatorem urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak tworzyć zadania w aplikacji Azure IoT Central, oto kilka następnych kroków:

- [Zarządzanie urządzeniami](howto-manage-devices.md)
- [Wersja szablonu urządzenia](howto-version-device-template.md)
