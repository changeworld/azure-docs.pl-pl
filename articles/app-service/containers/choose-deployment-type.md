---
title: Wdrażanie obrazu niestandardowego, wiele kontenerów lub wbudowanego obrazu — Azure App Service | Microsoft Docs
description: Jak podjąć decyzję między niestandardowym wdrożeniem kontenera platformy Docker, wielokontenerem i wbudowaną strukturą aplikacji dla App Service w systemie Linux
keywords: usługa Azure App Service, aplikacja sieci Web, system Linux, usługa OSS
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066788"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Obraz niestandardowy, wiele kontenerów lub wbudowanego obrazu platformy?

[App Service w systemie Linux](app-service-linux-intro.md) oferuje trzy różne ścieżki umożliwiające opublikowanie aplikacji w sieci Web:

- **Wdrożenie obrazu niestandardowego**: "Przekształcać" aplikację do obrazu platformy Docker, który zawiera wszystkie pliki i zależności w gotowym do uruchomienia pakiecie.
- **Wdrożenie z obsługą kilku kontenerów**: "Przekształcać" swoją aplikację w wielu kontenerach przy użyciu pliku konfiguracji Docker Compose.
- **Wdrażanie aplikacji z wbudowanym obrazem platformy**: Nasze wbudowane obrazy platformy zawierają typowe środowiska uruchomieniowe i zależności aplikacji sieci Web, takie jak Node i PHP. Użyj jednej z [Azure App Service metod wdrażania](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) , aby wdrożyć aplikację w magazynie aplikacji sieci Web, a następnie użyj wbudowanego obrazu platformy do jego uruchomienia.

## <a name="which-method-is-right-for-your-app"></a>Która metoda jest odpowiednia dla aplikacji? 

Podstawowe czynniki, które należy wziąć pod uwagę:

- **Dostępność platformy Docker w przepływie pracy deweloperskiej**: Niestandardowe programowanie obrazów wymaga podstawowej znajomości przepływu pracy programowania platformy Docker. Wdrożenie obrazu niestandardowego w aplikacji internetowej wymaga publikacji obrazu niestandardowego na hoście repozytorium, takim jak Docker Hub. Jeśli znasz platformę Docker i możesz dodać zadania platformy Docker do przepływu pracy kompilacji lub jeśli aplikacja jest już publikowana jako obraz platformy Docker, obraz niestandardowy prawie najlepiej nadaje się do wyboru.
- **Architektura**wielowarstwowa: Wdróż wiele kontenerów, takich jak warstwa aplikacji sieci Web i warstwa interfejsu API, aby oddzielić możliwości przy użyciu wielu kontenerów. 
- **Wydajność aplikacji**: Zwiększ wydajność aplikacji wielokontenera przy użyciu warstwy pamięci podręcznej, takiej jak Redis. Wybierz pozycję wiele kontenerów, aby to osiągnąć.
- **Unikatowe wymagania środowiska uruchomieniowego**: Wbudowane obrazy platformy są przeznaczone do zaspokajania potrzeb większości aplikacji sieci Web, ale są ograniczone do ich szerszym. Twoja aplikacja może mieć unikatowe zależności lub inne wymagania dotyczące środowiska uruchomieniowego, które przekraczają możliwości programu.
- **Wymagania dotyczące kompilacji**: Dzięki [ciągłemu wdrażaniu](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)aplikacja może zostać uruchomiona na platformie Azure bezpośrednio z poziomu kodu źródłowego. Nie jest wymagany żaden zewnętrzny proces kompilacji ani publikacji. Istnieje jednak ograniczenie szerszym i dostępności narzędzi do kompilacji w ramach aparatu wdrażania [kudu](https://github.com/projectkudu/kudu/wiki) . Twoja aplikacja może skalowalność kudu możliwości w miarę rozwoju ich zależności lub wymagań dla niestandardowej logiki kompilacji.
- **Wymagania dotyczące odczytu/zapisu na dysku**: Wszystkie aplikacje sieci Web są przydzieleni do woluminu magazynu dla zawartości sieci Web. Ten wolumin, objęty usługą Azure Storage, jest instalowany `/home` w systemie plików aplikacji. W przeciwieństwie do plików w systemie plików kontenera pliki w woluminie zawartości są dostępne we wszystkich wystąpieniach skalowania aplikacji, a modyfikacje będą zachowywane między ponownymi uruchomieniami aplikacji. Opóźnienie dysku woluminu zawartości jest jednak większe i bardziej zmienne niż opóźnienie lokalnego systemu plików kontenera, a dostęp może mieć wpływ na uaktualnienia platformy, nieplanowany przestój i problemy z łącznością sieciową. Aplikacje wymagające dużego dostępu tylko do odczytu do plików zawartości mogą korzystać z niestandardowego wdrożenia obrazu, które umieszcza pliki w systemie plików obrazów zamiast na woluminie zawartości.
- **Użycie zasobów kompilacji**: Po wdrożeniu aplikacji ze źródła skrypty wdrażania uruchamiane przez kudu używają tych samych App Service Planuj zasoby obliczeniowe i magazynowe jako działającą aplikację. Duże wdrożenia aplikacji mogą zużywać więcej zasobów lub czasu. W szczególności wiele przepływów pracy wdrożenia generuje aktywność dysku w woluminie zawartości aplikacji, która nie jest zoptymalizowana pod kątem tego działania. Obraz niestandardowy zapewnia wszystkie pliki i zależności aplikacji na platformie Azure w jednym pakiecie bez potrzeby dodatkowych transferów plików ani akcji wdrażania.
- **Potrzeba szybkiej iteracji**: Dockerizing aplikacja wymaga dodatkowych kroków kompilacji. Aby zmiany zaczęły obowiązywać, należy wypchnąć nowy obraz do repozytorium z każdą aktualizacją. Te aktualizacje są następnie ściągane do środowiska platformy Azure. Jeśli jeden z kontenerów wbudowanych spełnia potrzeby aplikacji, wdrożenie ze źródła może oferować szybszy przepływ pracy programistycznej.

## <a name="next-steps"></a>Następne kroki

Kontener niestandardowy:
* [Uruchom kontener niestandardowy](quickstart-docker-go.md)

Wiele kontenerów:
* [Tworzenie aplikacji z obsługą kontenera](quickstart-multi-container.md)

Poniższe artykuły zawierają wprowadzenie do App Service w systemie Linux z wbudowanym obrazem platformy:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)