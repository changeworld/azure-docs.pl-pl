---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 01/25/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b335cf996de41f4eea15af1899a0c6654c2f679f
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104987"
---
## <a name="open-cli-shell"></a>Otwórz interfejs wiersza polecenia powłoki

Zalecane jest użycie [usługi Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do wykonywania poleceń interfejsu wiersza polecenia. **Cloud Shell** jest bezpłatna, interaktywne powłoce, która służy do wykonywania kroków opisanych w tym artykule. Typowe narzędzia platformy Azure są wstępnie zainstalowane i skonfigurowane w usłudze Cloud Shell na potrzeby użycia z poziomu konta. Wystarczy wybrać przycisk kopiowania, aby skopiować kod, wklej go w usłudze Cloud Shell, a następnie naciśnij Enter, aby go uruchomić. Usługę Cloud Shell można otworzyć na kilka sposobów:

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

### <a name="login"></a>Login

Aby rozpocząć korzystanie z powłoki interfejsu wiersza polecenia (w chmurze lub lokalnie), uruchom `az login` do utworzenia połączenia z platformą Azure.

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie trzeba otworzyć stronę przeglądarki i postępuj zgodnie z instrukcjami w wierszu polecenia, aby wprowadzić kod autoryzacji po przejściu do https://aka.ms/devicelogin w przeglądarce.

### <a name="specify-location-of-files"></a>Określ lokalizację plików

Wiele poleceń interfejsu wiersza polecenia Media Services umożliwia przekazanie parametru o nazwie pliku. 

Jeśli używasz **usługi Azure Cloud Shell**, Przekaż plik programu do **usługi Azure Cloud Shell**. Możesz znaleźć przycisk pobierania/przekazywania plików w górnej części okna powłoki. Następnie Odwołaj się do pliku następująco: `@{FileName}.` 

![Przekazywanie plików]

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, należy określić ścieżkę całego pliku. Na przykład `@c:\tracks.json`.


[Przekazywanie plików]: ./media/media-services-cli/upload-download-files.png
