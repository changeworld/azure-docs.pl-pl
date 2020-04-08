---
title: Zasoby platformy Azure — QnA Maker
description: Program QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane indywidualnie, pozwala zaplanować i wybrać właściwą warstwę cenową lub wiedzieć, kiedy zmienić warstwę cenową. Zrozumienie, jak są one używane w połączeniu pozwala znaleźć i rozwiązać problemy, gdy wystąpią.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 1bd491ecbd878cb7bb05a7eaa5712c75653f2cba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804303"
---
# <a name="azure-resources-for-qna-maker"></a>Zasoby platformy Azure dla programu QnA Maker

Program QnA Maker używa kilku źródeł platformy Azure, z których każdy ma inny cel. Zrozumienie, w jaki sposób są one używane indywidualnie, pozwala zaplanować i wybrać właściwą warstwę cenową lub wiedzieć, kiedy zmienić warstwę cenową. Zrozumienie, jak są one używane _w połączeniu_ pozwala znaleźć i rozwiązać problemy, gdy wystąpią.

## <a name="resource-planning"></a>Planowanie zasobów

Podczas pierwszego opracowania bazy wiedzy QnA Maker, w fazie prototypu, często masz jeden zasób QnA Maker do testowania i produkcji.

Po przejściu do fazy rozwoju projektu należy wziąć pod uwagę:

* ile języków będzie posiadać system bazy wiedzy
* ile regionów potrzebujesz swojej bazy wiedzy, aby być dostępne w/z
* ile dokumentów w każdej domenie będzie przechowywać system

Zaplanuj, aby pojedynczy zasób programu QnA Maker posiadał wszystkie bazy wiedzy, które mają ten sam język, ten sam region i tę samą kombinację domeny tematu.

## <a name="pricing-tier-considerations"></a>Zagadnienia dotyczące warstwy cenowej

Zazwyczaj istnieją trzy parametry, które należy wziąć pod uwagę:

* **Przepustowość, której potrzebujesz z usługi:**
    * Wybierz odpowiedni [plan aplikacji](https://azure.microsoft.com/pricing/details/app-service/plans/) dla usługi aplikacji w zależności od potrzeb. Aplikację można [skalować w górę](https://docs.microsoft.com/azure/app-service/manage-scale-up) lub w dół.
    * Powinno to również mieć wpływ na wybór jednostki SKU usługi Azure **Cognitive Search,** zobacz więcej szczegółów [tutaj](https://docs.microsoft.com/azure/search/search-sku-tier). Ponadto może być konieczne dostosowanie [pojemności](../../../search/search-capacity-planning.md) wyszukiwania poznawczego za pomocą replik.

* **Rozmiar i liczba baz wiedzy:** Wybierz odpowiednią jednostkę [SKU wyszukiwania platformy Azure](https://azure.microsoft.com/pricing/details/search/) dla danego scenariusza. Zazwyczaj decydujesz o liczbie potrzebnych baz wiedzy na podstawie liczby różnych domen tematycznych. Kiedy domena tematu (dla jednego języka) powinna znajdować się w jednej bazie wiedzy.

    Można opublikować n-1 baz wiedzy w określonej warstwie, gdzie N jest maksymalna liczba indeksów dozwolonych w warstwie. Sprawdź również maksymalny rozmiar i liczbę dokumentów dozwolonych na warstwę.

    Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty indeks jest używany dla wszystkich baz wiedzy do tworzenia i testowania.

* **Liczba dokumentów jako źródeł**: Bezpłatna jednostka SKU usługi zarządzania QnA Maker ogranicza liczbę dokumentów, którymi można zarządzać za pośrednictwem portalu i interfejsów API do 3 (po 1 MB). Standardowa jednostka SKU nie ma ograniczeń co do liczby dokumentów, którymi można zarządzać. Zobacz więcej szczegółów [tutaj](https://aka.ms/qnamaker-pricing).

W poniższej tabeli przedstawiono kilka wskazówek wysokiego poziomu.

|                        | Zarządzanie producentami QnA | App Service | Azure Cognitive Search | Ograniczenia                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Eksperymentowanie        | Bezpłatna jednostka SKU             | Warstwa bezpłatna   | Warstwa bezpłatna    | Opublikuj do 2 KB, rozmiar 50 MB  |
| Środowisko deweloperów/testów   | Standardowy SKU         | Udostępnione      | Podstawowy        | Opublikuj do 14 KB, rozmiar 2 GB    |
| Środowisko produkcyjne | Standardowy SKU         | Podstawowy       | Standardowa     | Opublikuj do 49 KB, rozmiar 25 GB |

## <a name="when-to-change-a-pricing-tier"></a>Kiedy zmienić warstwę cenową

|Uaktualnienie|Przyczyna|
|--|--|
|[Uaktualnienie](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Usługa SKU zarządzania QnA Maker|Chcesz mieć więcej par QnA lub źródeł dokumentów w swojej bazie wiedzy.|
|[Uaktualnienie](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) Usługa App Service SKU i sprawdź warstwę Wyszukiwania poznawczego i [utwórz repliki wyszukiwania funkcji Cognitive Search](../../../search/search-capacity-planning.md)|Baza wiedzy musi obsługiwać więcej żądań z aplikacji klienckiej, takich jak czat bot.|
|[Uaktualnienie](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Usługa Azure Cognitive Search|Planujesz mieć wiele baz wiedzy.|

Pobierz najnowsze aktualizacje środowiska uruchomieniowego, [aktualizując usługę app service w witrynie Azure portal](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Zagadnienia dotyczące nazewnictwa zasobów

Nazwa zasobu QnA Maker, na `qna-westus-f0-b`przykład , jest również używana do nadawanie nazw innym zasobom.

Okno tworzenia witryny Azure Portal umożliwia utworzenie zasobu programu QnA Maker i wybranie warstw cenowych dla innych zasobów.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal do tworzenia zasobów programu QnA Maker](../media/concept-azure-resource/create-blade.png)

Po utworzeniu zasobów mają taką samą nazwę, z wyjątkiem opcjonalnego zasobu usługi Application Insights, który umieszcza znaki postpendy do nazwy.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę zasobów witryny Azure portal](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Utwórz nową grupę zasobów podczas tworzenia zasobu programu QnA Maker. Dzięki temu można zobaczyć wszystkie zasoby skojarzone z zasobem QnA Maker podczas wyszukiwania według grupy zasobów.

> [!TIP]
> Konwencja nazewnictwa służy do wskazywania warstw cenowych w nazwie zasobu lub grupy zasobów. Po otrzymaniu błędów podczas tworzenia nowej bazy wiedzy lub dodawania nowych dokumentów, limit warstwy cenowej wyszukiwania funkcji Poznawczych jest częstym problemem.

## <a name="resource-purposes"></a>Cele zasobów

Każdy zasób platformy Azure utworzony za pomocą programu QnA Maker ma określony cel:

* Zasób QnA Maker
* Zasób wyszukiwania poznawczego
* App Service
* Usługa planu aplikacji
* Usługa application insights


### <a name="cognitive-search-resource"></a>Zasób wyszukiwania poznawczego

[Zasób Wyszukiwania poznawczego](../../../search/index.yml) jest używany do:

* Przechowywanie par QnA
* Podaj wstępny ranking (ranker #1) par QnA w czasie wykonywania

#### <a name="index-usage"></a>Użycie indeksu

Zasób zachowuje jeden indeks do działania jako indeks testu, a pozostałe indeksy korelują z jedną opublikowaną bazą wiedzy.

Zasób wyceniony na 15 indeksów, będzie posiadał 14 opublikowanych baz wiedzy, a jeden indeks jest używany do testowania wszystkich baz wiedzy. Ten indeks testu jest podzielony na partycje według bazy wiedzy, dzięki czemu kwerenda przy użyciu interaktywnego okienka testu użyje indeksu testu, ale zwraca tylko wyniki z określonej partycji skojarzonej z określoną bazą wiedzy.

#### <a name="language-usage"></a>Użycie języka

Pierwsza baza wiedzy utworzona w zasobie QnA Maker służy do określania _zestawu pojedynczego_ języka dla zasobu wyszukiwania funkcji Poznawczych i wszystkich jego indeksów. Dla usługi QnA Maker można mieć tylko _jeden zestaw językowy._

### <a name="qna-maker-resource"></a>Zasób QnA Maker

Zasób QnA Maker zapewnia dostęp do interfejsów API tworzenia i publikowania, a także do drugiej warstwy rankingu (ranker #2) par QnA w czasie wykonywania.

Drugi ranking stosuje inteligentne filtry, które mogą zawierać metadane i monity uzupełniające.

#### <a name="qna-maker-resource-configuration-settings"></a>Ustawienia konfiguracji zasobów programu QnA Maker

Podczas tworzenia nowej bazy wiedzy w [portalu QnA Maker](https://qnamaker.ai)ustawienie **Język** jest jedynym ustawieniem stosowanym na poziomie zasobu. Język należy wybrać podczas tworzenia pierwszej bazy wiedzy dla zasobu.

### <a name="app-service-and-app-service-plan"></a>Usługa aplikacji i plan usługi aplikacji

[Usługa aplikacji](../../../app-service/index.yml) jest używana przez aplikację kliencką, aby uzyskać dostęp do opublikowanych baz wiedzy za pośrednictwem punktu końcowego środowiska wykonawczego.

Aby zbadać opublikowaną bazę wiedzy, wszystkie opublikowane bazy wiedzy używają tego samego punktu końcowego adresu URL, ale określają **identyfikator bazy wiedzy** w ramach trasy.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../../../azure-monitor/app/app-insights-overview.md) służy do zbierania dzienników czatu i telemetrii. Zapoznaj się z [typowymi zapytaniami Kusto,](../how-to/get-analytics-knowledge-base.md) aby uzyskać informacje na temat usługi.

## <a name="share-services-with-qna-maker"></a>Udostępnianie usług qna maker

Program QnA Maker tworzy kilka zasobów platformy Azure. Aby ograniczyć zarządzanie i korzystać z podziału kosztów, skorzystaj z poniższej tabeli, aby dowiedzieć się, co możesz, a czego nie możesz udostępnić:

|Usługa|Udostępnij|Przyczyna|
|--|--|--|
|Cognitive Services|X|Niemożliwe z założenia|
|Plan usługi App Service|✔|Poprawiono miejsce na dysku przydzielone dla planu usługi aplikacji. Jeśli inne aplikacje, które współużytkują ten sam plan usługi App Service używać znacznego miejsca na dysku, QnAMaker App Service wystąpienie wystąpi problemy.|
|App Service|X|Niemożliwe z założenia|
|Application Insights|✔|Możliwość udostępniania|
|Search Service|✔|1. `testkb` jest zastrzeżoną nazwą usługi QnAMaker; nie może być używany przez innych.<br>2. Mapa synonimów `synonym-map` według nazwy jest zarezerwowana dla usługi QnAMaker.<br>3. Liczba opublikowanych baz wiedzy jest ograniczona przez warstwę usługi wyszukiwania. Jeśli dostępne są bezpłatne indeksy, inne usługi mogą z nich korzystać.|

### <a name="using-a-single-cognitive-search-service"></a>Korzystanie z jednej usługi wyszukiwania funkcji Cognitive Search

Jeśli utworzysz usługę QnA i jej zależności (takie jak wyszukiwanie) za pośrednictwem portalu, usługa wyszukiwania zostanie utworzona dla Ciebie i połączona z usługą QnA Maker. Po utworzeniu tych zasobów można zaktualizować ustawienie usługi App Service, aby użyć wcześniej istniejącej usługi wyszukiwania i usunąć właśnie utworzoną usługę.

Dowiedz [się, jak skonfigurować](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) program QnA Maker do używania innego zasobu usługi Cognitive Service niż ten utworzony w ramach procesu tworzenia zasobów programu QnA Maker.

## <a name="management-service-region"></a>Region usług zarządzania

Usługa zarządzania QnA Maker jest używana tylko dla portalu QnA Maker i do wstępnego przetwarzania danych. Ta usługa jest dostępna tylko w regionie **Zachodnie stany USA.** Żadne dane klienta nie są przechowywane w tej usłudze w zachodnie stany USA.

## <a name="keys-in-qna-maker"></a>Klawisze w QnA Maker

Usługa QnA Maker zajmuje się dwoma rodzajami kluczy: **kluczami tworzenia** i **kluczami punktu końcowego zapytań używanymi** w czasie wykonywania hostowanym w usłudze aplikacji.

Jeśli szukasz **klucza subskrypcji,** [terminologia uległa zmianie.](#subscription-keys)

Użyj tych kluczy podczas tworzenia żądań do usługi za pośrednictwem interfejsów API.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

|Nazwa|Lokalizacja|Przeznaczenie|
|--|--|--|
|Klucz tworzenia|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Te klucze są używane do uzyskiwania dostępu do [interfejsów API usługi zarządzania QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają edytowanie pytań i odpowiedzi w bazie wiedzy oraz publikowanie bazy wiedzy. Klucze te są tworzone podczas tworzenia nowej usługi QnA Maker.<br><br>Znajdź te klucze w zasobie **usług Cognitive Services** na stronie **Klucze.**|
|Klucz punktu końcowego kwerendy|[Portal usługi QnA Maker](https://www.qnamaker.ai)|Te klucze są używane do kwerendy opublikowanego punktu końcowego bazy wiedzy, aby uzyskać odpowiedź na pytanie użytkownika. Zazwyczaj używasz tego punktu końcowego kwerendy w botie czatu lub w kodzie aplikacji klienckiej, który łączy się z usługą QnA Maker. Te klucze są tworzone podczas publikowania bazy wiedzy QnA Maker.<br><br>Znajdź te klawisze na stronie **Ustawienia usługi.** Znajdź tę stronę z menu użytkownika w prawym górnym rogu strony w menu rozwijanym.|

### <a name="subscription-keys"></a>Klucze subskrypcji

Terminy tworzenia i klucz końcowy kwerendy są terminy korygujące. Poprzedni okres był **kluczem subskrypcji**. Jeśli zostanie wyświetlona inna dokumentacja odnosząca się do kluczy subskrypcji, są one równoważne kluczom punktów końcowych tworzenia i kwerendy (używanej w czasie wykonywania).

Musisz wiedzieć, co klucz jest dostęp, zarządzanie bazą wiedzy lub badania bazy wiedzy, aby wiedzieć, który klucz należy znaleźć.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [bazie wiedzy](knowledge-base.md) QnA Maker
* Zrozumienie [cyklu życia bazy wiedzy](development-lifecycle-knowledge-base.md)
* Przegląd [limitów](../limits.md) usług i bazy wiedzy

