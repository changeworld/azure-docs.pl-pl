---
title: Opcje wdrażania kontenerów systemu Linux
description: Zdecyduj między niestandardowym wdrażaniem kontenera platformy Docker, wielokontenerową i wbudowaną strukturą aplikacji dla usługi App Service w systemie Linux.
keywords: azure app service, aplikacja internetowa, Linux, oss
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687513"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Niestandardowy obraz, wielokontensja lub wbudowany obraz platformy?

[Usługa App Service w systemie Linux](app-service-linux-intro.md) oferuje trzy różne ścieżki do publikowania aplikacji w sieci Web:

- **Niestandardowe wdrożenie obrazu:**"Dockerize" aplikacji do obrazu platformy Docker, który zawiera wszystkie pliki i zależności w pakiecie gotowym do uruchomienia.
- **Wdrożenie wielu kontenerów:**"Dockerize" aplikacji w wielu kontenerach przy użyciu pliku konfiguracji docker compose.
- **Wdrażanie aplikacji z wbudowanym obrazem platformy:** Nasze wbudowane obrazy platformy zawierają typowe środowiska wykonawcze aplikacji internetowej i zależności, takie jak Node i PHP. Użyj jednej z [metod wdrażania usługi Azure App Service,](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) aby wdrożyć aplikację w magazynie aplikacji sieci web, a następnie użyj wbudowanego obrazu platformy, aby go uruchomić.

## <a name="which-method-is-right-for-your-app"></a>Która metoda jest odpowiednia dla Twojej aplikacji? 

Głównymi czynnikami, które należy wziąć pod uwagę, są:

- **Dostępność platformy Docker w przepływie pracy deweloperów:** Niestandardowe tworzenie obrazów wymaga podstawowej wiedzy na temat przepływu pracy tworzenia platformy Docker. Wdrożenie niestandardowego obrazu w aplikacji sieci web wymaga publikacji obrazu niestandardowego na hoście repozytorium, takim jak Centrum platformy Docker. Jeśli znasz program Docker i możesz dodać zadania platformy Docker do przepływu pracy kompilacji lub jeśli już publikujesz aplikację jako obraz platformy Docker, obraz niestandardowy jest prawie na pewno najlepszym wyborem.
- **Architektura wielowarstwowa:** Wdrażanie wielu kontenerów, takich jak warstwa aplikacji sieci web i warstwa interfejsu API, aby oddzielić możliwości przy użyciu wielu kontenerów. 
- **Wydajność aplikacji:** Zwiększ wydajność aplikacji wielokontenerowej przy użyciu warstwy pamięci podręcznej, takiej jak Redis. Wybierz multi-container, aby to osiągnąć.
- **Unikatowe wymagania dotyczące środowiska uruchomieniowego:** wbudowane obrazy platformy są zaprojektowane tak, aby zaspokoić potrzeby większości aplikacji internetowych, ale są ograniczone w ich możliwości dostosowania. Aplikacja może mieć unikatowe zależności lub inne wymagania środowiska wykonawczego, które przekraczają to, co są w stanie wbudowane obrazy.
- **Wymagania dotyczące kompilacji:** dzięki [ciągłego wdrażania](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)możesz uruchomić aplikację na platformie Azure bezpośrednio z kodu źródłowego. Nie jest wymagany proces kompilacji lub publikacji zewnętrznej. Istnieje jednak ograniczenie możliwości dostosowywania i dostępności narzędzi kompilacji w silniku wdrażania [Kudu.](https://github.com/projectkudu/kudu/wiki) Aplikacja może przerastać możliwości Kudu, ponieważ rośnie w jego zależności lub wymagania dotyczące logiki kompilacji niestandardowej.
- **Wymagania dotyczące odczytu/zapisu**dysku: Wszystkie aplikacje internetowe są przydzielane woluminowi pamięci masowej dla zawartości sieci Web. Ten wolumin, wspierany przez usługę `/home` Azure Storage, jest zainstalowany w systemie plików aplikacji. W przeciwieństwie do plików w systemie plików kontenera pliki w woluminie zawartości są dostępne we wszystkich wystąpieniach skali aplikacji, a modyfikacje będą zachowywać się między ponownym uruchomieniem aplikacji. Jednak opóźnienie dysku woluminu zawartości jest wyższa i bardziej zmienna niż opóźnienie lokalnego systemu plików kontenera, a na dostęp mogą mieć wpływ uaktualnienia platformy, nieplanowane przestoje i problemy z łącznością sieciową. Aplikacje, które wymagają dużego dostępu tylko do odczytu do plików zawartości, mogą korzystać z niestandardowego wdrażania obrazów, które umieszcza pliki w systemie plików obrazów, a nie w woluminie zawartości.
- **Tworzenie użycia zasobów:** Gdy aplikacja jest wdrażana ze źródła, skrypty wdrażania uruchamiane przez Kudu używają tych samych zasobów obliczeniowych i magazynowych planu usługi app service, co uruchomiona aplikacja. Wdrożenia dużych aplikacji mogą zużywać więcej zasobów lub czasu niż jest to pożądane. W szczególności wiele przepływów pracy wdrażania generuje dużą aktywność dysku na woluminie zawartości aplikacji, który nie jest zoptymalizowany pod kątem takiej aktywności. Niestandardowy obraz dostarcza wszystkie pliki i zależności aplikacji do platformy Azure w jednym pakiecie bez konieczności dodatkowych transferów plików lub akcji wdrażania.
- **Potrzeba szybkiej iteracji:** Dockerizing aplikacji wymaga dodatkowych kroków kompilacji. Aby zmiany zostały wprowadzone, należy wypchnąć nowy obraz do repozytorium z każdą aktualizacją. Te aktualizacje są następnie ściągane do środowiska platformy Azure. Jeśli jeden z wbudowanych kontenerów spełnia potrzeby aplikacji, wdrażanie ze źródła może oferować szybszy przepływ pracy deweloperów.

## <a name="next-steps"></a>Następne kroki

Kontener niestandardowy:
* [Uruchamianie niestandardowego kontenera](quickstart-docker-go.md)

Pojemnik wielowykonakowy:
* [Tworzenie aplikacji wielokontensjasowej](quickstart-multi-container.md)

Następujące artykuły rozpoczynają pracę z usługą App Service w systemie Linux z wbudowanym obrazem platformy:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)