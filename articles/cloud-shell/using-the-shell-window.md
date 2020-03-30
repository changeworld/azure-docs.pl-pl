---
title: Korzystanie z okna usługi Azure Cloud Shell | Dokumenty firmy Microsoft
description: Omówienie sposobu korzystania z okna usługi Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860320"
---
# <a name="using-the-azure-cloud-shell-window"></a>Korzystanie z okna usługi Azure Cloud Shell

W tym dokumencie wyjaśniono, jak korzystać z okna Powłoki chmury.

## <a name="swap-between-bash-and-powershell-environments"></a>Zamiana między środowiskami Bash i PowerShell

Użyj selektora środowiska na pasku narzędzi Cloud Shell, aby przełączać się między środowiskami Bash i PowerShell.  
![Wybieranie środowiska](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Uruchom ponownie powłokę chmury
Kliknij ikonę ponownego uruchamiania na pasku narzędzi Cloud Shell, aby zresetować stan komputera.  
![Uruchom ponownie powłokę chmury](media/using-the-shell-window/restart.png)
> [!WARNING]
> Ponowne uruchomienie usługi Cloud Shell spowoduje zresetowanie stanu komputera i wszystkie pliki, które nie zostaną utrwalone przez udział plików platformy Azure, zostaną utracone.

## <a name="change-the-text-size"></a>Zmienianie rozmiaru tekstu
Kliknij ikonę ustawień w lewym górnym rogu okna, a następnie umieść wskaźnik myszy na opcji "Rozmiar tekstu" i wybierz żądany rozmiar tekstu. Wybór będzie zachowywany w różnych sesjach.
![Rozmiar tekstu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Zmienianie czcionki
Kliknij ikonę ustawień w lewym górnym rogu okna, a następnie umieść wskaźnik myszy na opcji "Czcionka" i wybierz żądaną czcionkę.  Wybór będzie zachowywany w różnych sesjach.
![Czcionka](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Przekazywanie i pobieranie plików
Kliknij ikonę przesyłania/pobierania plików w lewym górnym rogu okna, a następnie wybierz pozycję Przekaż lub pobierz.  
![Przesyłanie/pobieranie plików](media/using-the-shell-window/uploaddownload.png)
* W przypadku przesyłania plików użyj okna podręcznego, aby przejść do pliku na komputerze lokalnym, wybierz żądany plik i kliknij przycisk "Otwórz".  Plik zostanie przesłany do `/home/user` katalogu.
* Aby pobrać plik, wprowadź w pełni kwalifikowaną ścieżkę pliku w wyskakującym `/home/user` oknie (tj. w zasadzie ścieżkę pod katalogiem, który pojawia się domyślnie) i wybierz przycisk "Pobierz".  
> [!NOTE] 
> W plikach i ścieżkach plików rozróżniana jest wielkość liter w usłudze Cloud Shell. Sprawdź dokładnie swoją obudowę w ścieżce pliku.

## <a name="open-another-cloud-shell-window"></a>Otwieranie innego okna powłoki chmury
Usługa Cloud Shell umożliwia wiele równoczesnych sesji na kartach przeglądarki, umożliwiając każdej sesji istnieje jako oddzielny proces.
Jeśli kończy się sesja, należy zakończyć z każdego okna sesji, jak każdy proces działa niezależnie, mimo że są one uruchamiane na tym samym komputerze.  
Kliknij ikonę otwórz nową sesję w lewym górnym rogu okna. Zostanie otwarta nowa karta z inną sesją połączoną z istniejącym kontenerem.
![Otwórz nową sesję](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Edytor powłoki chmury
* Zapoznaj się ze stroną [Za pomocą edytora usługi Azure Cloud Shell.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Podgląd sieci Web
Kliknij ikonę podglądu sieci Web w lewym górnym rogu okna, wybierz "Konfiguruj", określ żądany port do otwarcia.  Wybierz opcję "Otwórz port", aby otworzyć tylko port, lub "Otwórz i przeglądaj", aby otworzyć port i wyświetlić podgląd portu na nowej karcie.  
![Podgląd sieci Web](media/using-the-shell-window/preview.png)  
<br>
![Konfigurowanie portu](media/using-the-shell-window/preview-configure.png)  
Kliknij ikonę podglądu sieci Web w lewym górnym rogu okna, wybierz "Podgląd portu ..." , aby wyświetlić podgląd otwartego portu na nowej karcie. Kliknij ikonę podglądu sieci Web w lewym górnym rogu okna, wybierz "Zamknij port ..." , aby zamknąć otwarty port.  
![Port podglądu/zamykania](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizowanie & maksymalizacji okna powłoki chmury
Kliknij ikonę minimalizacji w prawym górnym rogu okna, aby ją ukryć. Kliknij ponownie ikonę Cloud Shell, aby odkryć.
Kliknij ikonę maksymalizacji, aby ustawić maksymalną wysokość okna. Aby przywrócić okno do poprzedniego rozmiaru, kliknij przycisk Przywróć.  
![Minimalizowanie lub maksymalizacja okna](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiowanie i wklejanie
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Ponowne rozmiary okna powłoki chmury
Kliknij i przeciągnij górną krawędź paska narzędzi w górę lub w dół, aby zmienić rozmiar okna Powłoki chmury.

## <a name="scrolling-text-display"></a>Przewijanie tekstu
Przewiń za pomocą myszy lub płytki dotykowej, aby przenieść tekst terminala.

## <a name="exit-command"></a>Polecenie Zakończ
Uruchamianie `exit` kończy aktywną sesję. To zachowanie występuje domyślnie po 20 minutach bez interakcji.

## <a name="next-steps"></a>Następne kroki

[Bash w Cloud Shell Szybki start](quickstart.md) <br>
[Program PowerShell w programie Cloud Shell Szybki start](quickstart-powershell.md)
