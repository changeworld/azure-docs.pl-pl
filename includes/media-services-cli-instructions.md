---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/28/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: db431d7815cfcc006563bd6da438154ef77ae6e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814707"
---
## <a name="cli-shell"></a>Interfejs wiersza polecenia powłoki

Zalecane jest użycie [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do wykonywania poleceń interfejsu wiersza polecenia. **Cloud Shell** jest bezpłatna, interaktywne powłoce, która służy do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Zapewnia elastyczność wyboru powłoki, która najlepiej pasuje do sposobu, w jaki pracujesz. Użytkownicy systemu Linux mogą wybrać korzystanie z powłoki Bash, zaś użytkownicy systemu Windows mogą wybrać środowisko PowerShell.

Można także zainstalować interfejs wiersza polecenia lokalnie. Zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instrukcje dla danej platformy.

### <a name="sign-in"></a>Logowanie

Korzystanie z lokalnej instalacji interfejsu wiersza polecenia wymaga, logowanie do platformy Azure. Ten krok nie jest wymagane dla usługi Azure Cloud Shell. Zaloguj się przy użyciu `az login` polecenia.

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie trzeba otworzyć stronę przeglądarki i postępuj zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do https://aka.ms/devicelogin w przeglądarce.

### <a name="specify-location-of-files"></a>Określ lokalizację plików

Wiele poleceń interfejsu wiersza polecenia Media Services umożliwia przekazanie parametru o nazwie pliku. Jeśli używasz **Cloud Shell**, możesz przekazać plik do usługi clouddrive (przy użyciu powłoki Bash lub programu PowerShell). 

![Przekazywanie plików]

Czy używasz lokalnej interfejsu wiersza polecenia lub **Cloud Shell**, należy określić ścieżkę pliku, zgodnie z systemu operacyjnego lub w usłudze Cloud Shell (powłoki Bash lub programu PowerShell), którego używasz. Poniżej znajduje się kilka przykładów:

Względna ścieżka do pliku (wszystkie OS)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Ścieżka bezwzględna do pliku w systemach operacyjnych Windows i Linux lub Mac

* `@ "/usr/home/mytestfile.json"`
*   `@"c:\tmp\user\mytestfile.json"`

Użyj `{file}` Jeśli polecenie pyta o ścieżkę do pliku. Na przykład `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Użyj `@{file}` Jeśli polecenie będzie można załadować określonego pliku. Na przykład `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Przekazywanie plików]: ./media/media-services-cli/upload-download-files.png
