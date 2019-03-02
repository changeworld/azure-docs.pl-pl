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
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: a02642540e6eb39f35b9cc0d38d187a7afa36b7a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243452"
---
# <a name="using-the-azure-cloud-shell-window"></a>W oknie usługi Azure Cloud Shell

W tym dokumencie wyjaśniono, jak używać okno usługi Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Przełączać się między środowiskami Bash i program PowerShell
![](media/using-the-shell-window/env-selector.png)

Użyj selektora środowiska na pasku narzędzi usługi Cloud Shell, przełączać się między środowiskami Bash, jak i programu PowerShell.

## <a name="restart-cloud-shell"></a>Ponowne uruchamianie usługi Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Ponowne uruchomienie usługi Cloud Shell spowoduje zresetowanie stanu maszyny i wszelkie pliki nie utrwalone przez platformę Azure udział pliku zostaną utracone.

* Kliknij ikonę ponowne uruchomienie w pasku narzędzi usługi Cloud Shell, aby zresetować stan maszyny.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizuj & zmaksymalizuj okno usługi Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Kliknij ikonę Minimalizuj w górnym rogu okna, aby je ukryć. Kliknij ikonę usługi Cloud Shell ponownie, aby odkryć.
* Kliknij ikonę Maksymalizuj, aby ustawić maksymalną wysokość okna. Aby przywrócić pierwotny rozmiar okna, kliknij przycisk Przywróć.

## <a name="concurrent-sessions"></a>Równoczesnych sesji
Usługa cloud Shell umożliwia wielu równoczesnych sesji między karty przeglądarki, umożliwiając każdej sesji istnieje w oddzielnym procesie powłoki Bash.
Kończenie sesji, należy wyjść z każdego okna sesji, ponieważ każdy proces działa niezależnie, chociaż są one uruchomione na tym samym komputerze.

## <a name="copy-and-paste"></a>Kopiowanie i wklejanie
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Zmień rozmiar okna usługi Cloud Shell
* Kliknij i przeciągnij górnej krawędzi paska narzędzi w górę lub w dół do zmiany rozmiaru okna usługi Cloud Shell.

## <a name="scrolling-text-display"></a>Przewijanie wyświetlania tekstu
* Przewiń przy użyciu myszy lub płytki dotykowej, aby przenieść terminalu tekst.

## <a name="changing-the-text-size"></a>Zmiana rozmiaru tekstu
![](media/using-the-shell-window/text-size.png)
* Kliknij ikonę ustawienia w górnym lewym rogu okna, a następnie umieść kursor nad opcją "Rozmiar tekstu" i wybierz rozmiar odpowiedni tekst. Wybór zostaną utrwalone między sesjami.

## <a name="exit-command"></a>Exit — polecenie
Uruchamianie `exit` kończy aktywną sesję. To zachowanie domyślne po upływie 20 minut bez interakcji.

## <a name="next-steps"></a>Kolejne kroki

[Powłoka bash w przewodniku Szybki Start Cloud Shell](quickstart.md)
[programu PowerShell w przewodniku Szybki Start Cloud Shell](quickstart-powershell.md)