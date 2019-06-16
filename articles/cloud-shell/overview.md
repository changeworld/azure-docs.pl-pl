---
title: Omówienie usługi Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Omówienie usługi Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199695"
---
# <a name="overview-of-azure-cloud-shell"></a>Omówienie usługi Azure Cloud Shell
Usługa Azure Cloud Shell jest powłoką interaktywne, dostępny w przeglądarce do zarządzania zasobami platformy Azure.
Zapewnia elastyczność wyboru powłoki, która najlepiej pasuje do sposobu, w jaki pracujesz.
Użytkownicy systemu Linux mogą wybrać korzystanie z powłoki Bash, zaś użytkownicy systemu Windows mogą wybrać środowisko PowerShell.

Wypróbuj od shell.azure.com, klikając przycisk poniżej.

[![Osadzanie uruchamiania](https://shell.azure.com/images/launchcloudshell.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)

Spróbuj z witryny Azure portal za pomocą ikony usługi Cloud Shell.

![Uruchamianie portalu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkcje

### <a name="browser-based-shell-experience"></a>Oparte na przeglądarce środowisko powłoki
Usługi cloud Shell umożliwia dostęp do opartego na przeglądarce środowisko wiersza polecenia utworzonych za pomocą zadań zarządzania platformy Azure na uwadze.
Wykorzystaj Cloud Shell, aby pracować autonomiczne z komputera lokalnego w sposób tylko chmura może zapewnić.

### <a name="choice-of-preferred-shell-experience"></a>Wybór preferowaną powłokę
Użytkownicy mogą wybierać między powłoki Bash lub programu PowerShell z listy rozwijanej powłoki.

![Powłoka bash w usłudze Cloud Shell](media/overview/overview-bash-pic.png)

![Program PowerShell w usłudze Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Uwierzytelniony i skonfigurowane Azure stacji roboczej
Usługa cloud Shell jest zarządzany przez firmę Microsoft, więc pochodzi ona z obsługą języka i popularnych narzędzi wiersza polecenia. Aby uzyskać natychmiastowy dostęp do zasobów przy użyciu wiersza polecenia platformy Azure lub programu Azure PowerShell polecenia cmdlet usługi cloud Shell jednocześnie możliwość bezpiecznego uwierzytelnia automatycznie.

Wyświetlić pełne [listę narzędzia zainstalowane w usłudze Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Zintegrowane edytora usługi Cloud Shell
Usługa cloud Shell oferuje zintegrowane graficzny Edytor oparty na edytorze Monaco typu open source. Po prostu Utwórz i edytuj pliki konfiguracji, uruchamiając `code .` zapewnia bezproblemowe wdrożenie za pomocą wiersza polecenia platformy Azure lub programu Azure PowerShell.

[Dowiedz się więcej na temat edytora usługi Cloud Shell](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Zintegrowana z witryną docs.microsoft.com

Usługa Cloud Shell można użyć bezpośrednio w dokumentacji w serwisie [docs.microsoft.com](https://docs.microsoft.com). Jest zintegrowane z [Microsoft Learn](https://docs.microsoft.com/learn/), [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) i [dokumentacji interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure) — kliknij przycisk "Try It" we fragmencie kodu, aby otworzyć powłokę programu przeznaczonego środowisko. 

### <a name="multiple-access-points"></a>Wiele punktów dostępu
Usługa cloud Shell jest elastyczne narzędzie, które mogą być używane:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentacja interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
* [Dokumentacja usługi Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio rozszerzenie Azure Account Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Połącz magazyn plików pakietu Microsoft Azure
Cloud Shell maszyn są tymczasowe i wymagają nowego lub istniejącego udziału plików platformy Azure ma zostać zainstalowany jako `clouddrive` do utrwalania plików.

Przy pierwszym uruchomieniu usługi Cloud Shell monituje o utworzenie zasobu grupy, konto magazynu i usługi Azure Files udział w Twoim imieniu. Jest to jednorazowy krok i zostanie automatycznie dołączone do wszystkich sesji. Jeden udział pliku można zamapować i będą używane przez zarówno w przypadku powłoki Bash, jak i programu PowerShell w usłudze Cloud Shell.

Dowiedz się więcej na temat sposobu instalacji [konta magazynu do nowego lub istniejącego](persisting-shell-storage.md).

## <a name="concepts"></a>Pojęcia
* Usługi cloud Shell jest uruchamiane na hoście tymczasowe na sesji, konkretnych użytkowników
* Usługa cloud Shell upłynie limit czasu po upływie 20 minut bez interaktywnych działań
* Usługa cloud Shell wymaga udziału plików platformy Azure ma zostać zainstalowany
* Usługa cloud Shell używa tego samego udziału plików platformy Azure dla zarówno powłoki Bash, jak i programu PowerShell
* Usługa cloud Shell jest przypisany jeden komputer dla konta użytkownika
* Usługa cloud Shell będzie się powtarzał $HOME przy użyciu obrazu 5 GB przechowywanych w udziale plików
* Uprawnienia zostały ustawione jako zwykły użytkownik systemu Linux w powłoce Bash

Dowiedz się więcej o funkcjach [Bash w usłudze Cloud Shell](features.md) i [programu PowerShell w usłudze Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Cennik
Komputer, na którym usługa Cloud Shell jest bezpłatną, wstępnie posiadania zainstalowanego udziału plików platformy Azure. Obowiązują koszty związane z regularnych magazynu.

## <a name="next-steps"></a>Kolejne kroki
[Powłoka bash w usłudze Cloud Shell przewodnika Szybki Start](quickstart.md) <br>
[Program PowerShell w usłudze Cloud Shell Przewodnik Szybki Start](quickstart-powershell.md)
