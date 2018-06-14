---
title: Omówienie powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Omówienie powłoki chmury Azure.
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
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844109"
---
# <a name="overview-of-azure-cloud-shell"></a>Omówienie powłoki w chmurze Azure
Powłoki chmury Azure jest interaktywny, dostępny w przeglądarce powłoki zarządzania zasobami platformy Azure.
Zapewnia elastyczność wyboru środowisko powłoki, który najlepiej odpowiada sposobu pracy.
Użytkownicy systemu Linux mogą wybrać korzystanie z powłoki Bash, zaś użytkownicy systemu Windows mogą wybrać środowisko PowerShell.

Spróbuj z shell.azure.com za pomocą tego przycisku.

[![](https://shell.azure.com/images/launchcloudshell.png "Uruchom powłokę chmury Azure")](https://shell.azure.com)

Spróbuj z portalu Azure, korzystając z ikony w chmurze powłoki.

![Uruchomienie portalu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkcje
### <a name="browser-based-shell-experience"></a>Środowisko powłoki przeglądarki
Chmury powłoki zapewnia dostęp do wiersza polecenia w przeglądarce skompilowanej za pomocą zadań zarządzania platformy Azure na uwadze.
Zapewniają powłoki chmury wykorzystać do pracy autonomiczne z komputera lokalnego w sposób tylko chmury.

### <a name="choice-of-preferred-shell-experience"></a>Wybór preferowanego powłoki środowisko
Linux użytkownicy mogą używać Bash w chmurze powłoki, podczas gdy użytkownicy systemu Windows można użyć programu PowerShell w chmurze powłoki (wersja zapoznawcza) z listy rozwijanej powłoki.

![Bash w powłoce chmury](media/overview/overview-bash-pic.png)

![PowerShell w chmurze powłoki (wersja zapoznawcza)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Uwierzytelniony i skonfigurowane Azure stacji roboczej
Powłoka chmury jest zarządzany przez firmę Microsoft, pochodzi z popularnych narzędzi wiersza polecenia i język pomocy technicznej. Chmura powłoki także bezpiecznie uwierzytelnia automatycznie dla natychmiastowy dostęp do zasobów za pomocą poleceń cmdlet Azure CLI w wersji 2.0 lub Azure PowerShell.

Wyświetl listę narzędzi Pełny dla [Bash środowisko](features.md#tools) i [środowiska PowerShell (wersja zapoznawcza).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Wiele punktów dostępu
Powłoka chmury jest elastyczny narzędzie, które mogą być używane z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentację platformy Azure "Wypróbuj" 2.0 interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Aplikacji mobilnej Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS konto platformy Azure kod rozszerzenia](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Magazyn plików Azure Microsoft Connect
Chmury maszyn powłoki są tymczasowe i wymagają udział plików Azure można zainstalować jako `clouddrive` do utrwalenia plików.

Na pierwszego uruchomienia powłoki chmury monituje o Utwórz zasób grupy, konto magazynu i plików Azure udział w Twoim imieniu. To jest jednorazowe kroku i być automatycznie dołączane do wszystkich sesji. Jeden udział pliku można zamapować i będzie używana przez Bash i programu PowerShell w chmurze powłoki (wersja zapoznawcza).

#### <a name="create-new-storage"></a>Tworzenie nowego magazynu
![](media/overview/basic-storage.png)

Magazyn lokalnie nadmiarowy (LRS) konta i udział plików Azure można tworzyć w Twoim imieniu. Udział plików Azure będzie używany dla środowisk zarówno Bash, jak i programu PowerShell, jeśli chcesz korzystać z obu. Koszty przechowywania regularne mają zastosowanie.

Trzy zasoby zostaną utworzone w Twoim imieniu:
1. Grupa zasobów o nazwie: `cloud-shell-storage-<region>`
2. Konto magazynu o nazwie: `cs<uniqueGuid>`
3. Udział plików o nazwie: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Domyślny obraz dysku 5 GB, aby utrwalić tworzy także bash w chmurze powłoki `$Home`. Wszystkie pliki w katalogu $Home, takie jak klucze SSH są trwale przechowywane w udziale plików na platformę Azure zainstalowanego obrazu dysku użytkownika. Podczas zapisywania plików w katalogu $Home i udziału plików na platformę Azure zainstalowanego mają zastosowanie najlepszych rozwiązań.

#### <a name="use-existing-resources"></a>Korzystać z istniejących zasobów
![](media/overview/advanced-storage.png)

Zaawansowana opcja podano do skojarzenia z istniejącymi zasobami chmury powłoki.
Kliknij przycisk "Pokaż zaawansowane ustawienia" w wierszu polecenia instalacji magazynu, aby wyświetlić dodatkowe opcje.

> [!Note]
> Listę rozwijaną są filtrowane wstępnie przypisany region powłoki w chmurze i kont magazynu LRS/GRS/ZRS.

[Informacje na temat magazynowania powłoki chmury aktualizowanie udziały plików platformy Azure i przekazywanie pobierania plików.](persisting-shell-storage.md)

## <a name="concepts"></a>Pojęcia
* Powłoka działa na hoście tymczasowego na sesji, konkretnych użytkowników w chmurze
* Chmura powłoki upłynie limit czasu po upływie 20 minut bez interaktywnego działania
* Udział plików na platformę Azure ma zostać zainstalowany wymaga powłoki chmury
* Chmura powłoki używa tego samego udziału plików na platformę Azure zarówno Bash, jak i programu PowerShell
* Chmura powłoki przypisano na jednym komputerze na konto użytkownika
* Bash będzie się powtarzał $Home przy użyciu obrazu 5 GB przechowywany w udziale plików
* Uprawnienia zostały ustawione jako zwykły użytkownik w Bash systemu Linux

Dowiedz się więcej o funkcjach [Bash w chmurze powłoki](features.md) i [programu PowerShell w chmurze powłoki (wersja zapoznawcza)](features-powershell.md).

## <a name="pricing"></a>Cennik
Komputer, na którym powłoki chmury jest wolne, wymaga wstępnie posiadania zainstalowany udział plików Azure. Koszty przechowywania regularne mają zastosowanie.

## <a name="next-steps"></a>Kolejne kroki
[Bash w chmurze powłoki Szybki Start](quickstart.md) <br>
[PowerShell w chmurze powłoki (wersja zapoznawcza) — Szybki Start](quickstart-powershell.md)