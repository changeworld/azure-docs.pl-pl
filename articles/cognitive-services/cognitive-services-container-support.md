---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak kontenery platformy Docker mogą przybliżyć usługi Cognitive Services do danych.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219413"
---
# <a name="container-support-in-azure-cognitive-services"></a>Obsługa kontenerów w usługach Azure Cognitive Services

Obsługa kontenerów w usłudze Azure Cognitive Services umożliwia deweloperom korzystanie z tych samych bogatych interfejsów API, które są dostępne na platformie Azure, i umożliwia elastyczność w miejscu wdrażania i hostowania usług, które są dostępne z [kontenerami platformy Docker.](https://www.docker.com/what-container) Obsługa kontenerów jest obecnie dostępna dla podzbioru usług Azure Cognitive Services, w tym części:

> [!div class="checklist"]
> * [Narzędzie do wykrywania anomalii][ad-containers]
> * [Wizja komputerowa][cv-containers]
> * [Rozpoznawanie twarzy][fa-containers]
> * [Rozpoznawanie formularzy][fr-containers]
> * [Rozumienie języka (LUIS)][lu-containers]
> * [Interfejs API usługi rozpoznawania mowy][sp-containers]
> * [Analiza tekstu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Konteneryzacja jest podejście do dystrybucji oprogramowania, w którym aplikacja lub usługa, w tym jego zależności & konfiguracji, jest spakowany razem jako obraz kontenera. Przy niewielkiej lub żadnej modyfikacji obraz kontenera można wdrożyć na hoście kontenera. Kontenery są odizolowane od siebie i podstawowy system operacyjny, z mniejszym rozmiarem niż maszyna wirtualna. Kontenery można utworzyć wystąpienia z obrazów kontenerów dla zadań krótkoterminowych i usunąć, gdy nie są już potrzebne.

Zasoby usług Cognitive Services są dostępne na [platformie Microsoft Azure](https://azure.microsoft.com). Zaloguj się do [witryny Azure Portal,](https://portal.azure.com/) aby utworzyć i eksplorować zasoby platformy Azure dla tych usług.

## <a name="features-and-benefits"></a>Funkcje i korzyści

- **Niezmienna infrastruktura:** Włącz zespoły DevOps, aby wykorzystać spójny i niezawodny zestaw znanych parametrów systemu, jednocześnie będąc w stanie dostosować się do zmian. Kontenery zapewniają elastyczność obracania się w przewidywalnym ekosystemie i unikają dryfu konfiguracji.
- **Kontrola nad danymi:** Zezwalaj klientom na wybór miejsca, w którym te usługi Cognitive Services przetwarzają swoje dane. Jest to niezbędne dla klientów, którzy nie mogą wysyłać danych do chmury, ale potrzebują dostępu do technologii usług Cognitive Services. Obsługa spójności w środowiskach hybrydowych — między danymi, zarządzaniem, tożsamością i zabezpieczeniami.
- **Kontrola nad aktualizacjami modelu:** Zapewnij klientom elastyczność w zakresie przechowywania wersji i aktualizowania modeli wdrożonych w ich rozwiązaniach.
- **Architektura przenośna:** Włącz tworzenie architektury aplikacji przenośnych, które można wdrożyć na platformie Azure, lokalnie i na krawędzi. Kontenery można wdrażać bezpośrednio w [usłudze Azure Kubernetes](../aks/index.yml)Service, [instancjach kontenerów platformy Azure](../container-instances/index.yml)lub w klastrze [Kubernetes](https://kubernetes.io/) wdrożonym w [usłudze Azure Stack.](/azure-stack/operator) Aby uzyskać więcej informacji, zobacz [Wdrażanie sieci Kubernetes w usłudze Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Wysoka przepustowość / małe opóźnienia:** Zapewnij klientom możliwość skalowania dla wymagań o wysokiej przepływności i małych opóźnieniach, umożliwiając usługi Cognitive Services fizyczne uruchamianie blisko logiki i danych aplikacji. Kontenery nie cap transakcji na sekundę (TPS) i mogą być wykonane w celu skalowania zarówno w górę i na zewnątrz do obsługi popytu, jeśli zapewniają niezbędne zasoby sprzętowe.
- **Skalowalność**: Wraz z coraz większą popularnością oprogramowania do konteneryzacji i aranżacji kontenerów, takich jak Kubernetes; skalowalność jest w czołówce osiągnięć technologicznych. Opierając się na skalowalnej podstawie klastra, tworzenie aplikacji zapewnia wysoką dostępność.

## <a name="containers-in-azure-cognitive-services"></a>Kontenery w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services zawierają następujący zestaw kontenerów platformy Docker, z których każdy zawiera podzbiór funkcji z usług w usłudze Azure Cognitive Services:

| Usługa | Obsługiwana warstwa cenowa | Kontener | Opis |
|---------|----------|----------|-------------|
|[Wykrywacz anomalii][ad-containers] |F0, S0|**Wykrywacz anomalii** |Interfejs API detektora anomalii umożliwia monitorowanie i wykrywanie nieprawidłowości w danych szeregów czasowych za pomocą uczenia maszynowego.<br>[Żądanie dostępu](https://aka.ms/adcontainer)|
|[Wizja komputerowa][cv-containers] |F0, S1|**Odczyt** |Wyodrębnia drukowany tekst z obrazów różnych obiektów o różnych powierzchniach i tłach, takich jak paragony, plakaty i wizytówki. Odczyt kontenera wykrywa również *odręczny tekst* w obrazach i zapewnia obsługę pdf/TIFF/wielostronicową.<br/><br/>**Ważne:** Odczyt kontenera obecnie działa tylko w języku angielskim.|
|[Rozpoznawanie twarzy][fa-containers] |F0, S0|**Rozpoznawanie twarzy** |Wykrywa ludzkie twarze na obrazach i identyfikuje atrybuty, w tym punkty orientacyjne twarzy (takie jak nosy i oczy), płeć, wiek i inne cechy twarzy przewidywane maszynowo. Oprócz wykrywania funkcja Face może sprawdzić, czy dwie ściany na tym samym obrazie lub na różnych obrazach są takie same, używając wyniku zaufania, lub porównać twarze z bazą danych, aby sprawdzić, czy ma już podobną lub identyczną twarz. Może również organizować podobne twarze w grupy, używając wspólnych cech wizualnych.<br>[Żądanie dostępu](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Aparat rozpoznawania formularzy][fr-containers] |F0, S0|**Rozpoznawanie formularzy** |Form Understanding stosuje technologię uczenia maszynowego do identyfikowania i wyodrębniania par klucza wartości i tabel z formularzy.<br>[Żądanie dostępu](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**USŁUGA LUIS** ([obraz](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Ładuje przeszkolony lub opublikowany model zrozumienia języka, znany również jako aplikacja usługi LUIS, do kontenera platformy docker i zapewnia dostęp do prognoz kwerend z punktów końcowych interfejsu API kontenera. Można zbierać dzienniki zapytań z kontenera i przekazać je z powrotem do [portalu usługi LUIS,](https://www.luis.ai) aby zwiększyć dokładność przewidywania aplikacji.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-stt] |F0, S0|**Zamiana mowy na tekst** |Przekształca w czasie rzeczywistym ciągłą mowę na tekst.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-cstt] |F0, S0|**Niestandardowa zamiana mowy na tekst** |Transkrybuje ciągłą mowę w czasie rzeczywistym na tekst przy użyciu modelu niestandardowego.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-tts] |F0, S0|**Zamiana tekstu na mowę** |Konwertuje tekst na naturalnie brzmiącą mowę.|
|[Interfejs API usługi rozpoznawania mowy][sp-containers-ctts] |F0, S0|**Niestandardowy tekst na mowę** |Konwertuje tekst na naturalnie brzmiącą mowę przy użyciu modelu niestandardowego.|
|[Analiza tekstu][ta-containers-keyphrase] |F0, S|**Wyodrębnianie fraz kluczowych** [(obraz)](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409) |Wyodrębnia kluczowe frazy w celu zidentyfikowania głównych punktów. Na przykład dla tekstu wejściowego „Jedzenie było pyszne, a serwowała je doskonała obsługa” interfejs API zwraca główne tematy wypowiedzi: „jedzenie” i „doskonała obsługa”. |
|[Analiza tekstu][ta-containers-language]|F0, S|**Wykrywanie języka** [(obraz)](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409) |W przypadku maksymalnie 120 języków wykrywa język, w którym jest napisany tekst wejściowy, i zgłoś jeden kod języka dla każdego dokumentu przesłanego na żądanie. Kod języka jest powiązany z oceną, co wskazuje siłę oceny. |
|[Analiza tekstu][ta-containers-sentiment]|F0, S|**Analiza tonacji** [(obraz)](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409) |Analizuje nieprzetworzony tekst pod kątem wskazówek dotyczących pozytywnych lub negatywnych nastrojów. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, przy czym 1 oznacza najbardziej pozytywną tonację. Modele analizy są wstępnie przeszkolone przy użyciu obszernej treści technologii tekstu i języka naturalnego firmy Microsoft. W przypadku [wybranych języków](./text-analytics/language-support.md) interfejs API może przeanalizować i ocenić dowolny podany nieprzetworzony tekst, zwracając wyniki bezpośrednio do aplikacji wywołującej. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Ponadto niektóre kontenery są obsługiwane w programach [**all-in-one**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) usług Cognitive Services oferujących klucze zasobów. Można utworzyć jeden pojedynczy zasób all-in-one usług Cognitive Services i użyć tego samego klucza rozliczeniowego w obsługiwanych usługach dla następujących usług:

* Przetwarzanie obrazów
* Rozpoznawanie twarzy
* LUIS
* Analiza tekstu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostępność kontenerów w usługach Azure Cognitive Services

Kontenery usługi Azure Cognitive Services są publicznie dostępne za pośrednictwem subskrypcji platformy Azure, a obrazy kontenerów platformy Docker można pobierać z rejestru kontenerów firmy Microsoft lub centrum platformy Docker. Za pomocą polecenia [ściągania docker](https://docs.docker.com/engine/reference/commandline/pull/) można pobrać obraz kontenera z odpowiedniego rejestru.

> [!IMPORTANT]
> Obecnie należy ukończyć proces rejestracji, aby uzyskać dostęp do następujących kontenerów, w których można wypełnić i przesłać kwestionariusz z pytaniami o Ciebie, firmy i przypadku użycia, dla którego chcesz zaimplementować kontenery. Po udzieleniu dostępu i poświadczeniu poświadczenia, można następnie wyciągnąć obrazy kontenerów z rejestru kontenerów prywatnych obsługiwanych przez usługę Azure Container Registry.
> * [Wykrywacz anomalii](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Rozpoznawanie twarzy](Face/face-how-to-install-containers.md)
> * [Rozpoznawanie formularzy](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Odczyt](computer-vision/computer-vision-how-to-install-containers.md)
> * [Zamiana mowy na tekst i zamiana tekstu na mowę](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem kontenerów usług Azure Cognitive Services należy spełnić następujące wymagania wstępne:

**Aparat platformy Docker:** Aparat platformy Docker musi być zainstalowany lokalnie. Docker udostępnia pakiety, które konfigurują środowisko platformy Docker w [systemach macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)i [Windows](https://docs.docker.com/docker-for-windows/). W systemie Windows program Docker musi być skonfigurowany do obsługi kontenerów systemu Linux. Kontenery platformy Docker można również wdrożyć bezpośrednio w [usłudze Azure Kubernetes](../aks/index.yml) lub [wystąpieniach kontenerów platformy Azure.](../container-instances/index.yml)

Platforma Docker musi być skonfigurowana tak, aby zezwalała kontenerom na łączenie się z danymi rozliczeń i wysyłanie ich na platformę Azure.

**Znajomość rejestru kontenerów firmy Microsoft i platformy Docker:** Należy mieć podstawową wiedzę na temat pojęć rejestru kontenerów firmy Microsoft i `docker` platformy Docker, takich jak rejestry, repozytoria, kontenery i obrazy kontenerów, a także znajomość podstawowych poleceń.

Aby uzyskać podstawowe informacje na temat platformy Docker i kontenerów, zapoznaj się z artykułem [Docker overview](https://docs.docker.com/engine/docker-overview/) (Przegląd platformy Docker).

Poszczególne kontenery mogą mieć własne wymagania, jak również, w tym wymagania dotyczące alokacji serwera i pamięci.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [recepturach kontenerów,](containers/container-reuse-recipe.md) których można używać w usługach Cognitive Services.

Instalowanie i eksplorowanie funkcji udostępnianych przez kontenery w usługach Azure Cognitive Services:

* [Kontenery wykrywające anomalię][ad-containers]
* [Kontenery widzenia komputerowego][cv-containers]
* [Pojemniki na twarz][fa-containers]
* [Kontenery aparatu rozpoznawania formularzy][fr-containers]
* [Kontenery rozumienia języka (LUIS)][lu-containers]
* [Kontenery interfejsu API usługi mowy][sp-containers]
* [Kontenery analizy tekstu][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment