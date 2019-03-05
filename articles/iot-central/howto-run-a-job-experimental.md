---
title: Tworzenie i uruchamianie zadań w aplikacji usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Zadania usługi Azure IoT Central umożliwia zbiorcze zarządzanie urządzeniami, takie jak aktualizowanie właściwości urządzenia, ustawienia lub wykonywania polecenia.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 8667b7747a8874451e21fbf8aea19f51b6b6252f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314232"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Twórz i uruchamiaj zadanie w aplikacji centralnej IoT platformy Azure

Microsoft Azure IoT Central umożliwia zarządzanie połączonych urządzeń na dużą skalę przy użyciu zadań. Funkcje zadań umożliwiają wykonywania aktualizacji zbiorczych, do właściwości urządzenia, ustawienia i polecenia. Ten artykuł przeprowadzi Cię jak rozpocząć pracę, przy użyciu zadań w swojej aplikacji.

## <a name="create-and-run-a-job"></a>Tworzenie i uruchamianie zadania

W tej sekcji dowiesz się, jak utworzyć i uruchomić zadanie. Każdy krok przechodzi przez przykładu, który demonstruje sposób uruchamiania zadania urządzeń mrożone Automat, które muszą mieć ich prędkość wentylator zwiększona.

1. Przejdź do zadania w okienku nawigacji.

1. Wybierz **+ nowy** aby rozpocząć tworzenie nowego zadania.

    ![Tworzenie nowego zadania](./media/howto-run-a-job-experimental/createnewjob.png)

1. Wprowadź nazwę i opis, które pomagają identyfikować zadanie, które tworzysz.

1. Wybierz zestaw urządzenia mają zadania mają być stosowane do. Po wybraniu urządzenia, zostaną wyświetlone po prawej stronie wypełniona urządzeń w zestawie wybranego urządzenia. Jeśli wybierzesz zestaw uszkodzone urządzeń, urządzenia nie będą wyświetlane i zostanie wyświetlony komunikat wyjaśniający, czy zestaw urządzenia został przerwany.

1. Następnie wybierz typ zadania, który ma być zdefiniowane (ustawienie, właściwość lub polecenie). Wybierz **+** obok typ zadania zaznaczone, a następnie dodać żądanej operacji.

    ![Konfigurowanie zadania](./media/howto-run-a-job-experimental/configurejob.png)

1. Po prawej stronie wybranie tylko urządzenia, aby zadanie zostanie uruchomione. Zaznaczając pole wyboru w górnej, wszystkie urządzenia są wybierane w zestawie całego urządzenia. Zaznaczając pole wyboru obok nazwy, są wybierane wszystkie urządzenia na bieżącej stronie.

1. Po wybraniu urządzenia żądaną wybierz **Uruchom**. Zadania będą wyświetlane na głównego **zadań** strony. W ramach tego widoku jesteś w stanie zobaczyć aktualnie uruchomione zadania i historii wszelkich uprzednio uruchamiania zadań. Uruchamianie zadania zawsze będzie wyświetlany w górnej części listy.

    ![Wyświetl zadanie](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > Będzie można wyświetlić historię poprzednio uruchomionych zadań przez maksymalnie 30 dni.

1. Aby uzyskać przegląd zadania, wybierz nazwę zadania, które chcesz wyświetlić z listy. Ten przegląd zawiera szczegóły zadania, urządzeń i stanów urządzeń.

    ![Wyświetlanie stanu urządzenia](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Zatrzymaj uruchomione zadanie

Jeśli chcesz zatrzymać zadanie, które jest aktualnie uruchomione, wybierz nazwę uruchomionego zadania, które chcesz zatrzymać. Wybierz **zatrzymać** przycisk na panel. Zobaczysz, że stan zadania został zmieniony, aby odzwierciedlić, że zadanie zostało zatrzymane.

   ![Zatrzymaj zadanie](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>Uruchom zadanie zatrzymania

Jeśli chcesz uruchomić zadanie, które jest obecnie zatrzymana, wybierz nazwę zatrzymania zadania, które chcesz uruchomić. Wybierz **Uruchom** przycisk na panel. Zobaczysz, że stan zadania został zmieniony na odzwierciedlić, że zadanie zostało teraz uruchomione ponownie.

   ![Wznowiono zadanie](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>Wyświetlanie stanu zadania

Po utworzeniu zadania **stan** kolumny spowoduje zaktualizowanie najnowsze komunikatem o stanie zadania. Komunikaty o stanie mają następujące znaczenie:

| Komunikat o stanie       | Znaczenie stanu                                          |
| -------------------- | ------------------------------------------------------- |
| Zakończone            | To zadanie zostało wykonane na wszystkich urządzeniach.              |
| Niepowodzenie               | To zadanie ma nie powiodło się i nie są w pełni wykonane na urządzeniach.  |
| Oczekujące              | To zadanie nie zostało jeszcze uruchomione wykonywania na urządzeniach.        |
| Działanie              | To zadanie jest w trakcie wykonywania na urządzeniach.             |
| Zatrzymano              | To zadanie została ręcznie zatrzymana przez użytkownika.           |

Komunikat o stanie następuje przegląd urządzeń w ramach zadania. Te stany urządzeń mają następujące znaczenie:

| Komunikat o stanie       | Znaczenie stanu                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Powodzenie            | Liczba urządzeń, które zadania zostało pomyślnie wykonane na.  |
| Niepowodzenie               | Liczba urządzeń, które zadania nie udało się wykonać na.      |

### <a name="view-the-device-status"></a>Wyświetlanie stanu urządzeń

Aby wyświetlić stan poszczególnych urządzeń w ramach zadania, wybierz nazwę zadania. W tym miejscu zobaczysz szczegółowe informacje o zadaniu i wszystkie urządzenia, które były częścią tego określonego zadania. Obok nazwy każdego urządzenia zostanie wyświetlony jeden z następujących komunikatów o stanie:

| Komunikat o stanie       | Znaczenie stanu                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Zakończone            | Zadanie zostało wykonane na tym urządzeniu.                                     |
| Niepowodzenie               | Zadanie nie powiodło się wykonanie na tym urządzeniu. Wyświetlony komunikat o błędzie będzie wyświetlić więcej informacji.  |
| Oczekujące              | Zadanie nie zostało jeszcze wykonane na tym urządzeniu.                                  |

> [!NOTE]
> Jeśli urządzenie zostało usunięte, nie będzie można wybrać urządzenie i będzie wyświetlany jako usunięte z identyfikatorem urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz jak tworzyć zadania w aplikacji usługi Azure IoT Central, Oto następne kroki:

- [Korzystanie ze zbiorów urządzeń](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Zarządzanie urządzeniami](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Wersja szablonu urządzenia](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
