---
title: Usługa aplikacji Azure na wdrażanie systemu Linux — niestandardowy obraz, wielu kontenera lub obrazu platformy wbudowanego?  | Microsoft Docs
description: Jak określić między niestandardowe wdrożenie kontenera Docker, wielu kontenera i framework wbudowaną aplikację usługi aplikacji w systemie Linux
keywords: Usługa aplikacji Azure, aplikacji sieci web, linux, oss
services: app-service
documentationCenter: ''
authors: msangapu
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.openlocfilehash: 012f78fc07f237e8ed532246c81a3c86bb6ab4ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764346"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Obraz niestandardowy, wielu kontenera lub obrazu platformy wbudowanych?

[Usługa aplikacji w systemie Linux](app-service-linux-intro.md) oferuje trzy różne ścieżki do pobierania aplikacji publikowanych w sieci Web:

- **Wdrażanie niestandardowego obrazu**: "Dockerize" aplikację do obrazu Docker, który zawiera wszystkie pliki i zależności w pakiecie gotowy do uruchomienia.
- **Wdrożenie wielu kontenera**: "Dockerize" aplikacji na wiele kontenerów przy użyciu rozwiązania Docker Compose lub Kubernetes pliku konfiguracji. Aby uzyskać więcej informacji, zobacz [aplikacji kontenera wielu](#multi-container-apps-supportability).
- **Wdrażanie aplikacji przy użyciu obrazu platformy wbudowanych**: naszych obrazy platformy wbudowanych zawierają wspólne środowisk uruchomieniowych aplikacji sieci web i zależności, takich jak Node i PHP. Skorzystaj z jednej z [metody wdrażania usługi Azure App Service](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) wdrażanie aplikacji w usłudze Magazyn aplikacji sieci web, a następnie użyć obrazu platformy wbudowanych go uruchomić.

##<a name="which-method-is-right-for-your-app"></a>Która metoda jest odpowiednia dla aplikacji? 

Podstawowy czynniki, które należy wziąć pod uwagę są:

- **Dostępność Docker w przepływie pracy programowanie**: tworzenie obrazów niestandardowych wymaga podstawową wiedzę na temat przepływu pracy programowania Docker. Wdrażanie niestandardowych obrazów aplikacji sieci web wymaga publikacji niestandardowego obrazu na hoście repozytorium, takich jak Centrum Docker. Jeśli znasz Docker i można dodawać zadania Docker do przepływu pracy kompilacji lub są już publikowanie aplikacji jako obraz Docker, najlepszym rozwiązaniem jest prawie na pewno niestandardowego obrazu.
- **Architektura wielowarstwowy**: Wdrażanie wielu kontenerów, takie jak warstwy aplikacji sieci web i warstwę interfejsu API można rozdzielić funkcje za pomocą wielu kontenera. 
- **Wydajność aplikacji**: zwiększyć wydajność aplikacji kontenera usługi za pomocą warstwy pamięci podręcznej, takich jak Redis. Wybierz kontener usługi można to osiągnąć.
- **Wymagania środowiska uruchomieniowego unikatowy**: obrazy wbudowane platformy zostały zaprojektowane na potrzeby większości aplikacji sieci web, ale są ograniczone w ich dostosowywalności. Aplikacja może mieć unikatowe zależności oraz innych wymagań środowiska uruchomieniowego, przekraczające co wbudowane obrazy są w stanie.
- **Wymagania dotyczące kompilacji**: Z [ciągłe wdrażanie](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), można pobrać aplikacji działa na platformie Azure bezpośrednio z kodu źródłowego. Nie jest wymagany żaden zewnętrznych proces kompilacji lub publikacji. Jednak ma limitu dostosowywalności i dostępności narzędzia kompilacji w [Kudu](https://github.com/projectkudu/kudu/wiki) aparat wdrażania. Aplikacji może pokonać możliwości Kudu w miarę zwiększania się w zależności lub wymagania dotyczące kompilacji niestandardowej logiki.
- **Wymagania dotyczące odczytu/zapisu na dysku**: wszystkie aplikacje sieci web są przydzielane wolumin magazynu dla zawartości sieci web. Jest zainstalowany ten wolumin, obsługiwanej przez usługi Azure Storage `/home` w aplikacji w systemie plików. W przeciwieństwie do plików w kontenerze systemu plików pliki zawartości na woluminie są dostępne we wszystkich wystąpieniach skalowania aplikacji, a modyfikacji zostanie zachowany po uruchomieniu aplikacji. Jednak opóźnienie zawartości woluminu dysku jest większy i więcej zmiennych niż opóźnienia kontenera lokalnego systemu plików i dostępu może mieć wpływ uaktualnienia platformy, nieplanowane przestoje i problemy z połączeniem sieciowym. Aplikacje, które wymagają duże dostęp tylko do odczytu do plików zawartości mogą korzystać z wdrożenia niestandardowego obrazu, który umieszcza pliki w systemie plików obrazu zamiast na woluminie zawartości.
- **Tworzenie użycia zasobów**: po wdrożeniu aplikacji ze źródła skryptów wdrażania uruchamiane przy użyciu Kudu tego samego planu usługi App Service zasoby obliczeniowe i magazynujące uruchomionej aplikacji. Wdrożenia dużych aplikacji może zużywać więcej zasobów lub czas niż wymagany. W szczególności wiele przepływów pracy wdrażania Generowanie działania mocno dysku na woluminie zawartości aplikacji, która nie jest zoptymalizowana pod kątem takiego działania. Obraz niestandardowy zapewnia wszystkie pliki i zależności aplikacji na platformie Azure w jednym pakiecie bez potrzeby do transferu plików dodatkowych lub akcji wdrażania.
- **Potrzebne do szybkiego iteracji**: Dockerizing aplikacji wymaga kompilacji dodatkowych kroków. Aby zmiany zaczęły obowiązywać należy wypchnąć nowego obrazu do repozytorium przy każdej aktualizacji. Te aktualizacje są następnie są pobierane do środowiska platformy Azure. Jeśli jednego z wbudowanych kontenerów zaspokoi potrzeby aplikacji, wdrażanie ze źródła może zaoferować szybsze programowanie przepływu pracy.

## <a name="multi-container-apps-supportability"></a>Możliwość obsługi wielu kontenera aplikacji

### <a name="supported-docker-compose-configuration-options"></a>Obsługiwane opcje konfiguracji rozwiązania Docker Compose
- polecenie
- punkt wejścia
- environment
- Obraz
- Porty
- Ponowne uruchomienie
- Usługi
- Woluminy

### <a name="unsupported-docker-compose-configuration-options"></a>Opcje konfiguracji rozwiązania Docker Compose nieobsługiwane
- Kompilacja (nie jest dozwolone)
- depends_on (zignorowany)
- sieci (zignorowany)
- klucze tajne (zignorowany)
- porty inne niż 80 i 8080 (zignorowany)

> [!NOTE]
> Inne opcje nie jest jawnie uznawany również są ignorowane w publicznej wersji zapoznawczej.

### <a name="supported-kubernetes-configuration-options"></a>Obsługiwane opcje konfiguracji Kubernetes
- argumentów
- polecenie
- containers
- Obraz
- name
- Porty
- Specyfikacja

> [!NOTE]
>Inne opcje Kubernetes nie jest jawnie uznawany nie są obsługiwane w publicznej wersji zapoznawczej.
>