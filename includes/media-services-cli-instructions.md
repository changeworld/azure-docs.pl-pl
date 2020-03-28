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
ms.openlocfilehash: 5ebbac39c8850737ea6f9ef333e45d305a520655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461218"
---
## <a name="use-cli-shell"></a>Użyj powłoki CLI

Zaleca się, aby używać [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do wykonywania poleceń interfejsu wiersza polecenia. **Cloud Shell** to bezpłatna, interaktywna powłoka, której można użyć do uruchomienia kroków w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Oferuje ona elastyczny wybór środowiska powłoki, które najlepiej pasuje do sposobu, w jaki wykonujesz swoją pracę. Użytkownicy systemu Linux mogą wybrać środowisko powłoki Bash, a użytkownicy systemu Windows — program PowerShell.

Można również zainstalować cli lokalnie. Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) aby uzyskać instrukcje dotyczące platformy.

### <a name="sign-in"></a>Logowanie

Przy użyciu lokalnej instalacji interfejsu wiersza polecenia wymaga zalogowania się na platformie Azure. Ten krok nie jest wymagany dla usługi Azure Cloud Shell. Zaloguj się `az login` za pomocą polecenia.

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć stronę przeglądarki i postępować zgodnie z instrukcjami w https://aka.ms/devicelogin wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do przeglądarki.

### <a name="specify-location-of-files"></a>Określanie lokalizacji plików

Wiele poleceń interfejsu wiersza polecenia usługi Media Services umożliwia przekazanie parametru o nazwie pliku. Jeśli używasz **usługi Cloud Shell,** możesz przesłać plik do usługi clouddrive (przy użyciu programu Bash lub PowerShell). 

![Przekazywanie plików]

Niezależnie od tego, czy używasz lokalnego interfejsu wiersza polecenia, czy **powłoki chmury,** należy określić ścieżkę pliku zgodnie z używanym programem OS lub Cloud Shell (Bash lub PowerShell). Poniżej znajduje się kilka przykładów:

Ścieżka względna do pliku (cały system operacyjny)

* `@"mytestfile.json"`
* `@"../mytestfile.json"`

Bezwzględna ścieżka pliku w systemach Linux/Mac i Windows OS

* `@ "/usr/home/mytestfile.json"`
*    `@"c:\tmp\user\mytestfile.json"`

Użyj, `{file}` jeśli polecenie prosi o ścieżkę do pliku. Na przykład `az ams transform create -a amsaccount -g resourceGroup -n custom --preset .\customPreset.json`. <br/> Użyj, `@{file}` jeśli polecenie ma zamiar załadować określony plik. Na przykład `az ams account-filter create -a amsaccount -g resourceGroup -n filterName --tracks @tracks.json`.

[Przekazywanie plików]: ./media/media-services-cli/upload-download-files.png
