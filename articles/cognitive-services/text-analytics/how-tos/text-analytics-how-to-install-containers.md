---
title: Instalowanie i uruchamianie kontenerów
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Jak pobrać, zainstalować i uruchamiaj kontenery analizy tekstu, w tym samouczku wskazówki.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 11798c3bfd4032ad10c738032a816a2a0488ce67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090537"
---
# <a name="install-and-run-containers"></a>Instalowanie i uruchamianie kontenerów

Konteneryzacji to podejście do dystrybucji oprogramowania, w której aplikacja lub usługa jest dostarczana w postaci obrazu kontenera. Konfiguracja i zależności dla aplikacji lub usługi są objęte obrazu kontenera. Następnie można wdrożyć obraz kontenera na hoście kontenera, z niewielkich modyfikacji. Kontenery są odizolowane od siebie nawzajem i zasadniczego systemu operacyjnego za pomocą mniejszy wyświetlacz niż maszyny wirtualnej. Kontenery mogą utworzonych na podstawie obrazów kontenera do krótkoterminowych zadań i usuwane, gdy nie będą już potrzebne.

Analiza tekstu oferuje następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji:

| Kontener| Opis |
|----------|-------------|
|Wyodrębnianie kluczowych fraz | Wyodrębnianie kluczowych fraz można identyfikować jego główne punkty. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|Wykrywanie języka | W przypadku maksymalnie 120 języków wykrywa i raporty w jakim języku napisano tekstu wejściowego. Kontener raportów jednego języka kodu dla każdego dokumentu, który znajduje się w żądaniu. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|Analiza tonacji | Analizuje nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolonych, za pomocą rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. W przypadku [wybranych języków](../language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="preparation"></a>Przygotowanie

Przed rozpoczęciem korzystania z kontenerów analizy tekstu, musi spełniać następujące wymagania wstępne:

**Aparat platformy docker**: konieczne jest posiadanie aparat platformy Docker zainstalowany lokalnie. Środowisko docker zawiera pakiety, które konfigurują środowisko platformy Docker na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), i [Windows](https://docs.docker.com/docker-for-windows/). W Windows platformy Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy docker — można również wdrożyć bezpośrednio do [usługi Azure Kubernetes Service](/azure/aks/), [usługi Azure Container Instances](/azure/container-instances/), lub [Kubernetes](https://kubernetes.io/) klastra wdrożone [Usługi azure Stack](/azure/azure-stack/). Aby uzyskać więcej informacji na temat wdrażania rozwiązania Kubernetes do usługi Azure Stack, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker należy skonfigurować w taki sposób, aby umożliwić kontenerów, aby nawiązać połączenie z, a następnie wysyłać danych dotyczących rozliczeń do platformy Azure.

**Znajomość Microsoft Container Registry i Docker**: powinien mieć podstawową wiedzę na temat koncepcji Microsoft Container Registry i Docker, takich jak rejestrów, repozytoriów, kontenerów, a obrazy kontenerów, a także wiedzę na temat podstawowe `docker` poleceń.  

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Kontener wymagania i zalecenia

W poniższej tabeli przedstawiono minimalne i zalecane rdzeni procesora CPU, co najmniej 2,6 gigaherc (GHz) lub szybszy i pamięci w gigabajtach (GB) do przydzielenia dla każdego kontenera analizy tekstu.

| Kontener | Minimalne | Zalecane |
|-----------|---------|-------------|
|Wyodrębnianie kluczowych fraz | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |
|Wykrywanie języka | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |
|Analiza tonacji | 1 rdzeń, 2 GB pamięci | 1 rdzeń, 4 GB pamięci RAM |

## <a name="download-container-images-from-microsoft-container-registry"></a>Pobieranie obrazów kontenera z rejestru kontenerów firmy Microsoft

Obrazy kontenerów dla analizy tekstu są dostępne z rejestru kontenerów firmy Microsoft. Poniższa tabela zawiera listę dostępnych repozytoriów z rejestru kontenerów firmy Microsoft dla kontenerów z analizy tekstu. Każdego repozytorium zawiera obraz kontenera, który musi zostać pobrany w celu uruchomienia kontenera lokalnie.

| Kontener | Repozytorium |
|-----------|------------|
|Wyodrębnianie kluczowych fraz | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Wykrywanie języka | `mcr.microsoft.com/azure-cognitive-services/language` |
|Analiza tonacji | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Użyj [polecenie docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać obraz kontenera z repozytorium. Na przykład aby pobrać najnowszy klucz frazy obrazu kontenera z repozytorium, użyj następującego polecenia:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Pełny opis dostępnych tagów dla kontenerów analizy tekstu zobacz następujące kontenery w usłudze Docker Hub:

* [Wyodrębnianie kluczowych fraz](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Wykrywanie języka](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Analiza tonacji](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> Możesz użyć [obrazów platformy docker](https://docs.docker.com/engine/reference/commandline/images/) polecenie, aby wyświetlić listę obrazów kontenerów pobrany. Na przykład następujące polecenie wyświetla identyfikator, repozytorium i tag każdy obraz kontenera pobrany, sformatowane jako tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Utwórz wystąpienie kontenera z obrazu kontenera pobrany

Użyj [platformy docker, uruchom](https://docs.docker.com/engine/reference/commandline/run/) polecenie, aby utworzyć wystąpienie kontenera z obrazu kontenera pobrany. Na przykład następujące polecenie:

* Tworzy wystąpienie kontenera z obrazu kontenera analizy tonacji
* Przydziela jednego rdzenia Procesora i 8 gigabajtów (GB) pamięci
* Uwidacznia TCP port 5000 i przydziela pseudo-TTY kontenera
* Po wychodzi są automatycznie usuwane kontenera

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> `Eula`, `Billing`, I `ApiKey` należy określić opcje wiersza polecenia do wystąpienia kontenera; w przeciwnym razie nie uruchamia się kontener.  Aby uzyskać więcej informacji, zobacz [rozliczeń](#billing).

Po utworzeniu można wywołać operacji z kontenera za pomocą kontenera hosta identyfikatora URI. Na przykład następujący host identyfikatora URI reprezentuje kontener analiza tonacji, który został utworzony w poprzednim przykładzie:

```http
http://localhost:5000/
```

> [!TIP]
> Możesz uzyskać dostęp [specyfikacji interfejsu OpenAPI](https://swagger.io/docs/specification/about/) (dawniej specyfikacją struktury Swagger), opisujący operacje obsługiwane przez kontener wystąpień z `/swagger` względny identyfikator URI dla tego kontenera. Na przykład następujący identyfikator URI zapewnia dostęp do specyfikacji interfejsu OpenAPI dla kontenera analiza tonacji, do którego został utworzony w poprzednim przykładzie:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Możesz albo [wywoływanie operacji interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) dostępne z kontenera lub użyj [Azure Cognitive Services tekstu Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) biblioteki klienta do wywołania tych operacji.  
> [!IMPORTANT]
> Konieczne jest posiadanie Azure Cognitive Services tekstu analizy zestawu SDK w wersji 2.1.0 lub nowszej, aby za pomocą kontenera za pomocą biblioteki klienta.

Jedyną różnicą między wywoływanie operacji z kontenera, a wywołanie tej samej operacji z odpowiednich usług na platformie Azure to czy użyjesz host identyfikatora URI kontenera, a nie host identyfikatora URI z regionu platformy Azure, aby wywołać operację. Na przykład jeśli chcesz użyć instancji analizy tekstu działa w regionie zachodnie stany USA Azure, możesz wywołać następującej operacji interfejsu API REST:

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Jeśli chcesz użyć kontenera klucza frazy, uruchomione na maszynie lokalnej w konfiguracji domyślnej, możesz wywołać następującej operacji interfejsu API REST:

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Rozliczenia

Kontenery analizy tekstu wysyłanie informacji dotyczących rozliczeń na platformie Azure, przy użyciu odpowiedniego zasobu analizy tekstu na Twoim koncie platformy Azure. Następujące opcje wiersza polecenia są używane przez kontenery analizy tekstu na potrzeby rozliczeń:

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API analizy tekstu zasobu, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji, musi być równa klucza interfejsu API dla aprowizowanego zasobu analizy tekstu w usłudze Azure określone w `Billing`. |
| `Billing` | Punkt końcowy zasobu analizy tekstu, używane do śledzenia informacji dotyczących rozliczeń.<br/>Wartość ta opcja musi być równa punkt końcowy identyfikatora URI zasobu Azure analizy tekstu elastycznie.|
| `Eula` | Wskazuje, zaakceptowane licencji dla kontenera.<br/>Wartość ta opcja musi być równa `accept`. |

> [!IMPORTANT]
> Prawidłowe wartości należy określić wszystkie trzy opcje, lub uruchomić kontenera.

Aby uzyskać więcej informacji o tych opcjach, zobacz [skonfigurować kontenery](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Podsumowanie

W tym artykule przedstawiono pojęcia i przepływ pracy na potrzeby pobierania, instalowania i uruchamiania kontenerów analizy tekstu. Podsumowanie:

* Analiza tekstu zawiera trzy kontenery systemu Linux dla platformy Docker, zawieranie, wyodrębnianie kluczowych fraz, wykrywanie języka i analiza opinii.
* Obrazy kontenerów są pobierane z rejestru kontenerów firmy Microsoft (MCR) na platformie Azure.
* Obrazy kontenera Uruchom na platformie Docker.
* Można użyć interfejsu API REST lub zestawu SDK do wywoływania operacji w kontenerach analizy tekstu, określając host identyfikatora URI kontenera.
* Należy określić informacje rozliczeniowe, podczas tworzenia wystąpienia kontenera.

> [!IMPORTANT]
> Kontenery usługi cognitive Services nie są licencjonowane do uruchomienia bez połączenia z platformy Azure do zbierania danych. Klienci muszą włączyć kontener, aby komunikować informacje rozliczeniowe usłudze zliczania przez cały czas. Kontenery usługi cognitive Services nie wysyłaj danych klientów (np. obraz lub tekst, który jest analizowana) do firmy Microsoft.

## <a name="next-steps"></a>Kolejne kroki

* Przegląd [skonfigurować kontenery](../text-analytics-resource-container-config.md) ustawień konfiguracji
* Przegląd [przegląd analizy tekstu](../overview.md) Aby dowiedzieć się więcej na temat wykrywania kluczowych fraz, wykrywanie języka i analiza tonacji  
* Zapoznaj się [interfejsu API analizy tekstu](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) szczegółowe informacje na temat metod obsługiwanych przez kontener.
* Zapoznaj się [— często zadawane pytania (FAQ)](../text-analytics-resource-faq.md) Aby rozwiązać problemy związane z funkcji przetwarzania obrazów.