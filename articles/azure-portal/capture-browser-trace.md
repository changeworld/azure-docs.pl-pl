---
title: Przechwytywanie śledzenia przeglądarki w celu rozwiązywania problemów | Dokumenty firmy Microsoft
description: Przechwytywanie informacji o sieci ze śledzenia przeglądarki, aby pomóc w rozwiązywaniu problemów z witryną Azure portal.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310700"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Przechwytywanie danych śledzenia przeglądarki na potrzeby rozwiązywania problemów

Jeśli rozwiązujesz problem z witryną Azure portal i musisz skontaktować się z pomocą techniczną firmy Microsoft, zalecamy najpierw przechwycenie śledzenia przeglądarki i dodatkowych informacji. Gromadzone informacje mogą dostarczyć ważnych informacji o portalu w momencie wystąpienia problemu. Postępuj zgodnie z instrukcjami w tym artykule dla narzędzi programistycznych w przeglądarce, z której korzystasz: Google Chrome lub Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) lub Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome i Microsoft Edge (Chromium)

Google Chrome i Microsoft Edge (Chromium) są oparte na [projekcie open source Chromium.](https://www.chromium.org/Home) Poniższe kroki pokazują, jak korzystać z narzędzi deweloperskich, które są bardzo podobne w dwóch przeglądarkach. Aby uzyskać więcej informacji, zobacz [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) i [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Przed rozpoczęciem śledzenia należy _zalogować_ się, aby śledzenie nie zawierało poufnych informacji związanych z logiem. 

1. Rozpocznij nagrywanie kroków, które należy wykonać w portalu, używając [rejestratora kroków](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Naciśnij ![klawisz F12 lub wybierz](media/capture-browser-trace/chromium-icon-settings.png) > zrzut ekranu z ikoną ustawień przeglądarki**Więcej narzędzi** > **Narzędzia programistyczne**.

1. Domyślnie przeglądarka przechowuje informacje śledzenia tylko dla strony, która jest aktualnie załadowana. Ustaw następujące opcje, aby przeglądarka przechowuje wszystkie informacje śledzenia, nawet jeśli repro wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć,** a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "Zachowaj dziennik"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Wybierz kartę **Konsola,** wybierz pozycję **Ustawienia konsoli,** a następnie wybierz pozycję **Zachowaj dziennik**. Ponownie wybierz **pozycję Ustawienia konsoli,** aby zamknąć okienko ustawień.

          ![Zrzut ekranu przedstawiający "Zachowaj dziennik"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Wybierz kartę **Sieć,** a następnie wybierz pozycję **Zatrzymaj rejestrowanie dziennika sieci** i **Wyczyść**.

    ![Zrzut ekranu przedstawiający "Zatrzymaj rejestrowanie dziennika sieci" i "Wyczyść"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Wybierz **pozycję Zarejestruj dziennik sieci,** a następnie odtwórz problem w portalu.

    ![Zrzut ekranu przedstawiający "Rozpocznij profilowanie sesji"](media/capture-browser-trace/chromium-start-session.png)

    Zobaczysz wyjście sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Po odtworzeniu nieoczekiwanego zachowania portalu wybierz **pozycję Zatrzymaj rejestrowanie dziennika sieciowego**, a następnie wybierz pozycję **Eksportuj HAR** i zapisz plik.

    ![Zrzut ekranu przedstawiający "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Zatrzymaj Rejestrator kroków i zapisz plik.

1. Po powrocie do okienka Narzędzia programistyczne przeglądarki wybierz kartę **Konsola.** Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Zapisz jako...** i zapisz dane wyjściowe konsoli w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/chromium-console-select.png)

1. Zapakuj plik HAR, wyjście konsoli i nagrywanie ekranu w skompresowanym formacie, takim jak .zip, i udostępnij je za pomocą pomocy technicznej firmy Microsoft.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Poniższe kroki pokazują, jak korzystać z narzędzi deweloperskich w programie Microsoft Edge (EdgeHTML). Aby uzyskać więcej informacji, zobacz [Narzędzia programistyczne Microsoft Edge (EdgeHTML).](/microsoft-edge/devtools-guide)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Przed rozpoczęciem śledzenia należy _zalogować_ się, aby śledzenie nie zawierało poufnych informacji związanych z logiem. 

1. Rozpocznij nagrywanie kroków, które należy wykonać w portalu, używając [rejestratora kroków](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Naciśnij ![klawisz F12 lub wybierz](media/capture-browser-trace/edge-icon-settings.png) > zrzut ekranu z ikoną ustawień przeglądarki**Więcej narzędzi** > **Narzędzia programistyczne**.

1. Domyślnie przeglądarka przechowuje informacje śledzenia tylko dla strony, która jest aktualnie załadowana. Ustaw następujące opcje, aby przeglądarka przechowuje wszystkie informacje śledzenia, nawet jeśli repro wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć,** a następnie wyczyść opcję **Wyczyść wpisy podczas nawigacji**.

          ![Zrzut ekranu przedstawiający "Wyczyść wpisy w nawigacji"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Wybierz kartę **Konsola,** a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "Zachowaj dziennik"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Wybierz kartę **Sieć,** a następnie wybierz pozycję **Zatrzymaj sesję profilowania** i **Wyczyść sesję**.

    ![Zrzut ekranu przedstawiający "Stop sesji profilowania" i "Wyczyść sesję"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Wybierz **pozycję Rozpocznij profilowanie sesji**, a następnie odtwórz problem w portalu.

    ![Zrzut ekranu przedstawiający "Rozpocznij profilowanie sesji"](media/capture-browser-trace/edge-start-session.png)

    Zobaczysz wyjście sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/edge-browser-trace-results.png)

1. Po odtworzeniu nieoczekiwanego zachowania portalu wybierz **pozycję Zatrzymaj sesję profilowania**, a następnie wybierz pozycję **Eksportuj jako HAR** i zapisz plik.

    ![Zrzut ekranu przedstawiający "Eksportuj jako HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Zatrzymaj Rejestrator kroków i zapisz plik.

1. W okienku Narzędzi deweloperskich przeglądarki wybierz kartę **Konsola** i rozwiń okno. Umieść kursor na początku danych wyjściowych konsoli, a następnie przeciągnij i wybierz całą zawartość danych wyjściowych. Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Kopiuj**i zapisz dane wyjściowe konsoli w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/edge-console-select.png)

1. Zapakuj plik HAR, wyjście konsoli i nagrywanie ekranu w skompresowanym formacie, takim jak .zip, i udostępnij je za pomocą pomocy technicznej firmy Microsoft.

## <a name="apple-safari"></a>Apple Safari

W poniższych krokach pokazano, jak korzystać z narzędzi programistycznych w przeglądarce Apple Safari. Aby uzyskać więcej informacji, zobacz [Omówienie narzędzi deweloperskich safari](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Włącz narzędzia programistyczne w przeglądarce Apple Safari:

    1. Wybierz **pozycję Safari**, a następnie wybierz pozycję **Preferencje**.

        ![Zrzut ekranu przedstawiający preferencje przeglądarki Safari](media/capture-browser-trace/safari-preferences.png)

    1. Wybierz kartę **Zaawansowane,** a następnie wybierz polecenie **Pokaż menu Rozwijaj na pasku menu**.

        ![Zrzut ekranu przedstawiający zaawansowane preferencje przeglądarki Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Przed rozpoczęciem śledzenia należy _zalogować_ się, aby śledzenie nie zawierało poufnych informacji związanych z logiem. 

1. Rozpocznij nagrywanie kroków, które należy wykonać w portalu. Aby uzyskać więcej informacji, zobacz [Jak nagrać ekran na komputerze Mac](https://support.apple.com/HT208721).

1. W portalu przejdź do kroku tuż przed wystąpieniem problemu.

1. Wybierz **polecenie Rozwijaj**, a następnie wybierz pozycję **Pokaż Inspektora sieci Web**.

    ![Zrzut ekranu przedstawiający "Pokaż inspektora sieci Web"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Domyślnie przeglądarka przechowuje informacje śledzenia tylko dla strony, która jest aktualnie załadowana. Ustaw następujące opcje, aby przeglądarka przechowuje wszystkie informacje śledzenia, nawet jeśli repro wymaga przechodzenia do więcej niż jednej strony:

    1. Wybierz kartę **Sieć,** a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "Zachowaj dziennik"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Wybierz kartę **Konsola,** a następnie wybierz pozycję **Zachowaj dziennik**.

          ![Zrzut ekranu przedstawiający "Zachowaj dziennik"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Wybierz kartę **Sieć,** a następnie wybierz pozycję **Wyczyść elementy sieciowe**.

    ![Zrzut ekranu przedstawiający "Wyczyść elementy sieciowe"](media/capture-browser-trace/safari-clear-session.png)

1. Odtwórz problem w portalu. Zobaczysz wyjście sesji podobne do poniższej ilustracji.

    ![Zrzut ekranu przedstawiający wyniki śledzenia przeglądarki](media/capture-browser-trace/safari-browser-trace-results.png)

1. Po odtworzeniu nieoczekiwanego zachowania portalu wybierz pozycję **Eksportuj** i zapisz plik.

    ![Zrzut ekranu przedstawiający "Eksport"](media/capture-browser-trace/safari-network-export-har.png)

1. Zatrzymaj rejestrator ekranu i zapisz plik.

1. W okienku Narzędzi deweloperskich przeglądarki wybierz kartę **Konsola** i rozwiń okno. Umieść kursor na początku danych wyjściowych konsoli, a następnie przeciągnij i wybierz całą zawartość danych wyjściowych. Użyj polecenia C, aby skopiować dane wyjściowe i zapisać je w pliku tekstowym.

    ![Zrzut ekranu przedstawiający dane wyjściowe konsoli](media/capture-browser-trace/safari-console-select.png)

1. Zapakuj plik HAR, wyjście konsoli i nagrywanie ekranu w skompresowanym formacie, takim jak .zip, i udostępnij je za pomocą pomocy technicznej firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

[Azure Portal — omówienie](azure-portal-overview.md)