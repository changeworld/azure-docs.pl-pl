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
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 4ee02bc2a1956994da0ba49a24eefabf9608565c
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856464"
---
# <a name="overview-of-azure-cloud-shell"></a>Omówienie usługi Azure Cloud Shell
Usługa Azure Cloud Shell jest powłoką interaktywne, dostępny w przeglądarce do zarządzania zasobami platformy Azure.
Zapewnia elastyczność wyboru powłoki, która najlepiej pasuje do sposobu, w jaki pracujesz.
Użytkownicy systemu Linux mogą wybrać korzystanie z powłoki Bash, zaś użytkownicy systemu Windows mogą wybrać środowisko PowerShell.

Wypróbuj od shell.azure.com za pomocą tego przycisku.

[![](https://shell.azure.com/images/launchcloudshell.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)

Spróbuj z witryny Azure portal za pomocą ikony usługi Cloud Shell.

![Uruchamianie portalu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkcje
### <a name="browser-based-shell-experience"></a>Oparte na przeglądarce środowisko powłoki
Usługi cloud Shell umożliwia dostęp do opartego na przeglądarce środowisko wiersza polecenia utworzonych za pomocą zadań zarządzania platformy Azure na uwadze.
Wykorzystaj Cloud Shell, aby pracować autonomiczne z komputera lokalnego w sposób tylko chmura może zapewnić.

### <a name="choice-of-preferred-shell-experience"></a>Wybór preferowaną powłokę
Użytkownicy systemu Linux użyć narzędzia Bash w usłudze Cloud Shell, podczas gdy użytkownicy Windows mogą używać programu PowerShell w usłudze Cloud Shell (wersja zapoznawcza) z listy rozwijanej powłoki.

![Powłoka bash w usłudze Cloud Shell](media/overview/overview-bash-pic.png)

![Program PowerShell w usłudze Cloud Shell (wersja zapoznawcza)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Uwierzytelniony i skonfigurowane Azure stacji roboczej
Usługa cloud Shell jest zarządzany przez firmę Microsoft, więc pochodzi ona z obsługą języka i popularnych narzędzi wiersza polecenia. Aby uzyskać natychmiastowy dostęp do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub programu Azure PowerShell polecenia cmdlet usługi cloud Shell jednocześnie możliwość bezpiecznego uwierzytelnia automatycznie.

Wyświetlić pełne [lista narzędzi.](features.md#tools)

### <a name="multiple-access-points"></a>Wiele punktów dostępu
Usługa cloud Shell jest elastyczne narzędzie, które mogą być używane:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentacja usługi Azure CLI 2.0 "Wypróbuj"](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Aplikacja mobilna platformy Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Rozszerzenie kodu Azure Account programu VS](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Połącz magazyn plików pakietu Microsoft Azure
Cloud Shell maszyn są tymczasowe i wymagają udziału plików platformy Azure ma zostać zainstalowany jako `clouddrive` do utrwalania plików.

Przy pierwszym uruchomieniu usługi Cloud Shell monituje o utworzenie zasobu grupy, konto magazynu i usługi Azure Files udział w Twoim imieniu. Jest to jednorazowy krok i zostanie automatycznie dołączone do wszystkich sesji. Jeden udział pliku można zamapować i będą używane przez zarówno w przypadku powłoki Bash, jak i programu PowerShell w usłudze Cloud Shell (wersja zapoznawcza).

#### <a name="create-new-storage"></a>Tworzenie nowego magazynu
![](media/overview/basic-storage.png)

Konto magazynu lokalnie nadmiarowego (LRS) i udział plików platformy Azure można tworzyć w Twoim imieniu. Udział plików platformy Azure będą używane w przypadku środowisk zarówno powłoki Bash, jak i programu PowerShell, jeśli zdecydujesz się używać ich obu. Obowiązują koszty związane z regularnych magazynu.

Trzy zasoby zostaną utworzone w Twoim imieniu:
1. Grupa zasobów o nazwie: `cloud-shell-storage-<region>`
2. Konto magazynu o nazwie: `cs<uniqueGuid>`
3. Udział plików o nazwie: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Powłoka bash w usłudze Cloud Shell tworzy również domyślny obraz dysku 5 GB, aby utrwalić `$Home`. Wszystkie pliki w katalogu $Home takie jak klucze SSH są zachowywane w obrazie dysku użytkownika przechowywanych w udziale plików platformy Azure zainstalowany. Podczas zapisywania plików w katalogu $Home i udziału plików platformy Azure zainstalowany, należy stosować najlepsze rozwiązania.

#### <a name="use-existing-resources"></a>Korzystać z istniejących zasobów
![](media/overview/advanced-storage.png)

Zaawansowana opcja znajduje się do skojarzenia z istniejących zasobów do usługi Cloud Shell.
Kliknij pozycję "Pokaż zaawansowane ustawienia" w wierszu polecenia instalacji magazynu, aby wyświetlić dodatkowe opcje.

> [!Note]
> Listy rozwijane są filtrowane wstępnie przypisanych regionu usługi Cloud Shell i kont magazynu LRS/GRS/ZRS.

[Informacje na temat magazynowania usługi Cloud Shell, aktualizowanie udziałów plików platformy Azure i Trwa przekazywanie/pobieranie plików.](persisting-shell-storage.md)

## <a name="concepts"></a>Pojęcia
* Usługi cloud Shell jest uruchamiane na hoście tymczasowe na sesji, konkretnych użytkowników
* Usługa cloud Shell upłynie limit czasu po upływie 20 minut bez interaktywnych działań
* Usługa cloud Shell wymaga udziału plików platformy Azure ma zostać zainstalowany
* Usługa cloud Shell używa tego samego udziału plików platformy Azure dla zarówno powłoki Bash, jak i programu PowerShell
* Usługa cloud Shell jest przypisany jeden komputer dla konta użytkownika
* Usługa cloud Shell będzie się powtarzał $Home przy użyciu obrazu 5 GB przechowywanych w udziale plików
* Uprawnienia zostały ustawione jako zwykły użytkownik systemu Linux w powłoce Bash

Dowiedz się więcej o funkcjach [Bash w usłudze Cloud Shell](features.md) i [programu PowerShell w usłudze Cloud Shell (wersja zapoznawcza)](features-powershell.md).

## <a name="pricing"></a>Cennik
Komputer, na którym usługa Cloud Shell jest bezpłatną, wstępnie posiadania zainstalowanego udziału plików platformy Azure. Obowiązują koszty związane z regularnych magazynu.

## <a name="next-steps"></a>Kolejne kroki
[Powłoka bash w usłudze Cloud Shell przewodnika Szybki Start](quickstart.md) <br>
[Program PowerShell w przewodniku Szybki Start usługi Cloud Shell (wersja zapoznawcza)](quickstart-powershell.md)