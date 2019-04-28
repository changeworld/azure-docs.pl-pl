---
title: Wdrażanie obrazu niestandardowego wielokontenerowych lub wbudowanych obrazów — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Decydowania między wdrożenia niestandardowego kontenera platformy Docker, obsługą wielu kontenerów i framework wbudowanej aplikacji dla usługi App Service w systemie Linux
keywords: Aplikacja sieci web, linux, oss w usłudze Azure app service
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c8a700bcd2780ef7b0c7ad1fbb513d4b4febffcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849986"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Niestandardowy obraz, wielokontenerowych lub obraz wbudowany platformy?

[App Service w systemie Linux](app-service-linux-intro.md) oferuje trzy różne ścieżki umieszczania aplikacji opublikowanych w sieci Web:

- **Wdrażanie obrazu niestandardowego**: "Przekształcać" aplikacji do obrazu platformy Docker, który zawiera wszystkie pliki i zależności w pakiecie gotowe do uruchomienia.
- **Wdrażanie wielu kontenerów**: "Przekształcać" aplikacji w wielu kontenerach przy użyciu narzędzia Docker Compose lub plik konfiguracji Kubernetes.
- **Wdrażanie aplikacji za pomocą obrazu platformy wbudowanych**: Nasze obrazy wbudowane platformy zawierają typowe środowisk uruchomieniowych aplikacji sieci web i zależności, takie jak Node i PHP. Skorzystaj z jednej z [metody wdrażania w usłudze Azure App Service](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) wdrażanie aplikacji w usłudze Magazyn aplikacji sieci web, a następnie użyć obrazu platformy wbudowanych do jej uruchomienia.

## <a name="which-method-is-right-for-your-app"></a>Która metoda jest odpowiednia dla aplikacji? 

Dostępne są następujące podstawowe czynniki do rozważenia:

- **Dostępność platformy docker w przepływie pracy rozwoju**: Tworzenie niestandardowych obrazów wymaga podstawową wiedzę na temat przepływu pracy tworzenia oprogramowania platformy Docker. Wdrażanie obrazu niestandardowego w aplikacji sieci web wymaga publikacji obraz niestandardowy do hosta repozytorium, takich jak usługi Docker Hub. Jeśli są zaznajomieni z platformy Docker i można dodać zadania platformy Docker do pracy kompilacji lub już publikujesz aplikację jako obraz platformy Docker, najlepszym rozwiązaniem jest prawie na pewno obraz niestandardowy.
- **Architektura wielowarstwowych**: Wdrażanie wielu kontenerów, np. warstwa aplikacji sieci web i warstwę interfejsu API, aby rozdzielić funkcje za pomocą wielu kontenerów. 
- **Wydajność aplikacji**: Zwiększyć wydajność aplikacji obsługującej wiele kontenerów przy użyciu warstwy pamięci podręcznej, takich jak pamięci podręcznej Redis. Wybierz wiele kontenerów, aby to osiągnąć.
- **Wymagania środowiska wykonawczego unikatowy**: Obrazy platformy wbudowane są przeznaczone do zaspokajania potrzeb większości aplikacji sieci web, ale są ograniczone w ich między. Aplikacja może mieć unikatowe zależności i inne wymagania środowiska uruchomieniowego, które przekraczają co wbudowane obrazy są w stanie.
- **Wymagania dotyczące kompilacji**: Za pomocą [ciągłe wdrażanie](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), uzyskasz aplikacja działa na platformie Azure bezpośrednio z kodu źródłowego. Nie jest wymagany żaden zewnętrznych proces kompilacji lub publikacji. Jednak ma limitu dostosowywalności i dostępność narzędzia do kompilacji w ramach [Kudu](https://github.com/projectkudu/kudu/wiki) aparacie wdrażania. Aplikacja może wykraczają poza możliwości możliwości firmy Kudu wzrostem jej zależności lub wymagania logiki niestandardowej kompilacji.
- **Wymagania dotyczące odczytu/zapisu dysku**: Wszystkie aplikacje sieci web są przydzielane na woluminie magazynu dla zawartości sieci web. Ten wolumin, wspierana przez usługi Azure Storage jest zainstalowany na `/home` w aplikacji w systemie plików. W przeciwieństwie do plików w systemie plików kontenera pliki na woluminie zawartości są dostępne we wszystkich wystąpieniach skalowania aplikacji, a modyfikacji będzie zachowywane między ponowne uruchomienie aplikacji. Jednak opóźnienie dysku ilość zawartości jest większe i zmienna więcej niż opóźnienia systemu plików kontenera lokalnego i dostępu może mieć wpływ na uaktualnienia platformy, nieplanowane przestoje i problemy z połączeniem sieciowym. Aplikacje, które wymagają mocno dostęp tylko do odczytu do plików zawartości mogą korzystać z wdrożenie obrazu niestandardowego, w którym umieszcza pliki w systemie plików obrazów zamiast na woluminie zawartości.
- **Tworzenie użycia zasobów**: Po wdrożeniu aplikacji ze źródła wdrożenia skrypty uruchamiane przy użyciu narzędzia Kudu tego samego planu usługi App Service zasoby obliczeniowe i magazynowe jako uruchomionej aplikacji. Wdrożenia dużych aplikacji może zużywać więcej zasobów lub czas niż wymagany. W szczególności wiele przepływów pracy wdrażania dysku duże wygenerować działania umieszczane na woluminie zawartości aplikacji, która nie jest zoptymalizowany pod kątem takiego działania. Obraz niestandardowy zapewnia wszystkie pliki i zależności aplikacji na platformie Azure w jednym pakiecie nie jest potrzebne do transferu plików dodatkowych lub akcji wdrażania.
- **Niezbędne do szybkiego iteracji**: Dockerizing aplikacji wymaga dodatkowych kroków kompilacji. Aby zmiany zaczęły obowiązywać należy wypchnąć obraz nowe repozytorium w każdej aktualizacji. Aktualizacje te są następnie ściągane do środowiska platformy Azure. Jeśli jeden z kontenerów wbudowanych spełnia wymagania aplikacji, wdrażanie ze źródła może zaoferować szybsze przepływ pracy tworzenia oprogramowania.
