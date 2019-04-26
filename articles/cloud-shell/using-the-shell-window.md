---
title: W oknie usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Omówienie sposobu użycia okna usługi Azure Cloud Shell.
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
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200704"
---
# <a name="using-the-azure-cloud-shell-window"></a>W oknie usługi Azure Cloud Shell

W tym dokumencie wyjaśniono, jak używać okno usługi Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Przełączać się między środowiskami Bash i program PowerShell

Użyj selektora środowiska na pasku narzędzi usługi Cloud Shell, przełączać się między środowiskami Bash, jak i programu PowerShell.  
![Wybierz środowisko](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Ponowne uruchamianie usługi Cloud Shell
Kliknij ikonę ponowne uruchomienie w pasku narzędzi usługi Cloud Shell, aby zresetować stan maszyny.  
![Ponowne uruchamianie usługi Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Ponowne uruchomienie usługi Cloud Shell spowoduje zresetowanie stanu maszyny i wszelkie pliki nie utrwalone przez platformę Azure udział pliku zostaną utracone.

## <a name="change-the-text-size"></a>Zmień rozmiar tekstu
Kliknij ikonę ustawienia w górnym lewym rogu okna, a następnie umieść kursor nad opcją "Rozmiar tekstu" i wybierz rozmiar odpowiedni tekst. Wybór zostaną utrwalone między sesjami.
![Rozmiar tekstu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Zmiana czcionki
Kliknij ikonę ustawienia w górnym lewym rogu okna, a następnie Najedź kursorem opcji "Czcionki" i wybierz żądany czcionki.  Wybór zostaną utrwalone między sesjami.
![Czcionka](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Przekazywanie i pobieranie plików
Kliknij ikonę pobierania/przekazywania plików u góry po lewej krawędzi okna, następnie wybierz przekazywania lub pobierania.  
![Przekazywanie/pobieranie plików](media/using-the-shell-window/uploaddownload.png)
* Przekazywania plików, należy użyć oknie podręcznym, przejdź do pliku na komputerze lokalnym, wybierz żądany plik i kliknij przycisk "Otwarte".  Plik zostanie przekazany do `/home/user` katalogu.
* Do pobierania plików, wprowadź w pełni kwalifikowaną ścieżkę w oknie podręcznym, a następnie wybierz przycisk "Pobierz".  
> [!NOTE] 
> Pliki i ścieżki plików są z uwzględnieniem wielkości liter w usłudze Cloud Shell. Sprawdź swoje wielkość liter w wyrazie w ścieżce pliku.

## <a name="open-another-cloud-shell-window"></a>Otwiera inne okno usługi Cloud Shell
Usługa cloud Shell umożliwia wielu równoczesnych sesji między karty przeglądarki, umożliwiając każdej sesji istnieje w oddzielnym procesie.
Kończenie sesji, należy wyjść z każdego okna sesji, ponieważ każdy proces działa niezależnie, chociaż są one uruchomione na tym samym komputerze.  
Kliknij ikonę Otwórz nową sesję w górnym lewym rogu okna. Nowa karta zostanie otwarty z inną sesją podłączony do istniejącego kontenera.
![Otwórz nową sesję](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell edytora
* Zapoznaj się [przy użyciu edytora usługi Azure Cloud Shell](using-cloud-shell-editor.md) strony.

## <a name="web-preview"></a>Podgląd internetowy
Kliknij ikonę sieci web (wersja zapoznawcza) w górnym lewym rogu okna, wybierz pozycję "Konfiguruj", określ odpowiedni port, aby otworzyć.  Wybierz albo "otwartego portu" tylko do otwierania portu lub "Otwórz i Przeglądaj" Otwórz port i port na nowej karcie w wersji zapoznawczej.  
![Podgląd w sieci Web](media/using-the-shell-window/preview.png)  
<br>
![Konfigurowanie portów](media/using-the-shell-window/preview-configure.png)  
Kliknij ikonę sieci web (wersja zapoznawcza) u góry po lewej krawędzi okna, wybierz pozycję "W wersji zapoznawczej portu..." Aby wyświetlić podgląd otwartego portu w nowej karcie. Kliknij ikonę sieci web (wersja zapoznawcza) u góry po lewej krawędzi okna, wybierz opcję "Zamknij port..." Aby zamknąć otwartego portu.  
![Wersja zapoznawcza/Zamknij port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizuj & zmaksymalizuj okno usługi Cloud Shell
Kliknij ikonę Minimalizuj w górnym rogu okna, aby je ukryć. Kliknij ikonę usługi Cloud Shell ponownie, aby odkryć.
Kliknij ikonę Maksymalizuj, aby ustawić maksymalną wysokość okna. Aby przywrócić pierwotny rozmiar okna, kliknij przycisk Przywróć.  
![Minimalizowanie lub zmaksymalizuj okno](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiowanie i wklejanie
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Zmień rozmiar okna usługi Cloud Shell
Kliknij i przeciągnij górnej krawędzi paska narzędzi w górę lub w dół do zmiany rozmiaru okna usługi Cloud Shell.

## <a name="scrolling-text-display"></a>Przewijanie wyświetlania tekstu
Przewiń przy użyciu myszy lub płytki dotykowej, aby przenieść terminalu tekst.

## <a name="exit-command"></a>Exit — polecenie
Uruchamianie `exit` kończy aktywną sesję. To zachowanie domyślne po upływie 20 minut bez interakcji.

## <a name="next-steps"></a>Kolejne kroki

[Powłoka bash w przewodniku Szybki Start Cloud Shell](quickstart.md) <br>
[Program PowerShell w przewodniku Szybki Start Cloud Shell](quickstart-powershell.md)