---
title: Omówienie usługi Azure Cloud Shell | Dokumenty firmy Microsoft
description: Omówienie usługi Azure Cloud Shell.
services: ''
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
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832458"
---
# <a name="overview-of-azure-cloud-shell"></a>Omówienie usługi Azure Cloud Shell
Azure Cloud Shell to interaktywna, uwierzytelniona, dostępna w przeglądarce powłoka służąca do zarządzania zasobami platformy Azure.
Oferuje ona elastyczny wybór środowiska powłoki, Bash lub PowerShell, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę.

Spróbuj z shell.azure.com klikając poniżej.

[![Uruchom osadź](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

Spróbuj z witryny Azure portal przy użyciu ikony powłoki chmury.

![Uruchomienie portalu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkcje

### <a name="browser-based-shell-experience"></a>Środowisko powłoki opartej na przeglądarce
Usługa Cloud Shell umożliwia dostęp do środowiska wiersza polecenia opartego na przeglądarce, opartego na zadaniach zarządzania platformy Azure.
Wykorzystaj usługę Cloud Shell do pracy na komputerze się z komputerem lokalnym w sposób, który może zapewnić tylko chmura.

### <a name="choice-of-preferred-shell-experience"></a>Wybór preferowanego doświadczenia powłoki
Użytkownicy mogą wybierać między Bash lub PowerShell.
1. Wybierz **powłokę chmury**.

    ![Ikona aplikacji Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Wybierz **bash** lub **PowerShell**.

    ![Wybierz bash lub powershell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Uwierzytelniona i skonfigurowana stacja robocza platformy Azure
Usługa Cloud Shell jest zarządzana przez firmę Microsoft, dzięki czemu jest wyposażona w popularne narzędzia wiersza polecenia i obsługę języków. Usługa Cloud Shell również bezpiecznie uwierzytelnia się automatycznie w celu natychmiastowego dostępu do zasobów za pośrednictwem poleceń cmdlet interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

Wyświetl pełną [listę narzędzi zainstalowanych w usłudze Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Zintegrowany edytor Cloud Shell
Cloud Shell oferuje zintegrowany graficzny edytor tekstu oparty na edytorze Monaco open source. Po prostu utwórz i `code .` edytuj pliki konfiguracyjne, uruchamiając bezproblemowe wdrażanie za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.

[Dowiedz się więcej o edytorze Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Zintegrowany z docs.microsoft.com

Usługa Cloud Shell można używać bezpośrednio z dokumentacji hostowanego w [docs.microsoft.com](https://docs.microsoft.com). Jest zintegrowany z dokumentacją [programu Microsoft Learn](https://docs.microsoft.com/learn/), Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview) i [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) — kliknij przycisk "Wypróbuj" we we weszło kodu, aby otworzyć wciągające środowisko powłoki. 

### <a name="multiple-access-points"></a>Wiele punktów dostępu
Cloud Shell to elastyczne narzędzie, które może być używane z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentacja interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
* [Dokumentacja programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Rozszerzenie konta platformy Azure kodu programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Łączenie magazynu plików platformy Microsoft Azure
Maszyny Cloud Shell są tymczasowe, ale pliki są utrwalone na dwa sposoby: za pośrednictwem obrazu dysku i za pośrednictwem zainstalowanego udziału plików o nazwie `clouddrive`.  Podczas pierwszego uruchomienia usługa Cloud Shell monituje o utworzenie grupy zasobów, konta magazynu i udziału usługi Azure Files w Twoim imieniu. Jest to krok jednorazowy i zostanie automatycznie dołączony do wszystkich sesji. Pojedynczy udział plików mogą być mapowane i będą używane zarówno bash i PowerShell w Cloud Shell.

Przeczytaj więcej, aby dowiedzieć się, jak zamontować [nowe lub istniejące konto magazynu](persisting-shell-storage.md) lub dowiedzieć się więcej o [mechanizmach trwałości używanych w usłudze Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Zapora magazynu platformy Azure nie jest obsługiwana dla kont magazynu powłoki w chmurze.

## <a name="concepts"></a>Pojęcia
* Usługa Cloud Shell działa na tymczasowym hoście dostępnym dla użytkownika w przełęcze
* Przesąknie czasu cloud shell po 20 minutach bez interaktywnej aktywności
* Usługa Cloud Shell wymaga zamontowania udziału plików platformy Azure
* Usługa Cloud Shell używa tego samego udziału plików platformy Azure zarówno dla programu Bash, jak i programu PowerShell
* Usługa Cloud Shell ma przypisany jeden komputer na konto użytkownika
* Usługa Cloud Shell utrzymuje się $HOME przy użyciu obrazu o rozmiarze 5 GB przechowywanego w udziale plików
* Uprawnienia są ustawiane jako zwykły użytkownik Linuksa w Bash

Dowiedz się więcej o funkcjach [Bash w usłudze Cloud Shell](features.md) i [PowerShell w usłudze Cloud Shell.](features-powershell.md)

## <a name="pricing"></a>Cennik
Komputer hosting Cloud Shell jest bezpłatny, z warunkiem wstępnym zainstalowanego udziału usługi Azure Files. Obowiązują standardowe koszty przechowywania.

## <a name="next-steps"></a>Następne kroki
[Bash w cloud shell szybki start](quickstart.md) <br>
[Program PowerShell w programie Cloud Shell szybki start](quickstart-powershell.md)
