---
title: Mapowanie aplikacji w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie topologii złożonych aplikacji z planem aplikacji
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 4b95cd947cd977208dee32d18fbc6a1fb099ff3b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="application-map-triage-distributed-applications"></a>Mapowanie aplikacji: Aplikacje rozproszone klasyfikowanie
Mapowanie aplikacji ułatwia wąskich gardeł wydajności dodatkowych lub błąd hotspotami dotyczące wszystkich składników aplikacji rozproszonej. Każdy węzeł na mapie reprezentuje składnika aplikacji lub jego zależności; ma kondycji kluczowy wskaźnik wydajności i alerty stanu. Możesz kliknąć za pośrednictwem z każdego składnika, do bardziej szczegółowych diagnostyki, takich jak zdarzenia usługi Application Insights. Jeśli aplikacja korzysta z usług Azure, możesz również kliknąć za pośrednictwem do diagnostyki Azure, takich jak zalecenia doradcy bazy danych SQL.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie można wdrożyć części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacji mają widoczność na poziomie kodu lub dostępu do danych telemetrycznych generowane przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, EventHub itp., które zespół/organizacji nie mogą mieć dostęp do (kod lub dane telemetryczne).
* Składniki uruchamiane na dowolnej liczbie wystąpień kontenera server/roli.
* Składniki mogą być osobne klucze Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role podlegające jednego klucza Instrumentacji usługi Application Insights. Środowisko mapy Podgląd przedstawiono składniki, niezależnie od tego, jak ustawić.

## <a name="composite-application-map-preview"></a>Mapowanie złożonych aplikacji (wersja zapoznawcza)
*To jest podgląd wczesne i dodamy większą liczbą funkcji na tej mapie. Chętnie pobrać swoją opinię na temat nowego rozwiązania. Można przełączać się między Podgląd i klasycznego środowiska łatwe.*

Włącz "Złożonych aplikacji mapy" z [listy wersji zapoznawczych](app-insights-previews.md), lub kliknij na "Wersja zapoznawcza mapy" Przełącz w prawym górnym rogu. Ten przełącznik, można użyć, aby przełączyć się do klasycznego środowiska.
![Włącz podgląd mapy](media/app-insights-app-map/preview-from-classic.png)

>[!Note]
Ta wersja zapoznawcza zastępuje wcześniejszych wersji zapoznawczej "Mapowanie wielu roli aplikacji". W tej chwili służy do wyświetlenia całego topologii na wielu poziomach zależności składnika aplikacji. Prześlij nam opinię, dodamy więcej funkcje podobne do obsługuje klasyczny mapy.

Topologia pełnej aplikacji jest widoczny na wielu poziomach składników aplikacji powiązanych. Składniki wynikać z różnych zasobów usługi Application Insights lub różnych ról w pojedynczym zasobie. Mapa aplikacji znajduje składniki według następujących wywołania zależności HTTP między serwerami z zestawem SDK Insights aplikacji, które zostały zainstalowane. 

To środowisko rozpoczyna się od progresywnego odnajdywania składników. Podczas ładowania najpierw podglądu, zbiór zapytania są wyzwalane odnajdywania składników związanych z tego składnika. Liczba składników aplikacji przycisk w lewym górnym rogu zostanie zaktualizowany zgodnie z ich odnalezieniu. 
![Podgląd mapy](media/app-insights-app-map/preview.png)

Po kliknięciu przycisku "Składników mapy aktualizacji", mapy są odświeżane ze wszystkimi składnikami wykryte do momentu na tym etapie.
![Podgląd załadować mapy](media/app-insights-app-map/components-loaded-hierarchical.png)

Jeśli wszystkie składniki są role w ramach pojedynczego zasobu usługi Application Insights, ten krok odnajdywania nie jest wymagane. Ładowania początkowego dla takich aplikacji będzie miał wszystkich jego składników.

Jest jednym z celów klucza z nowego środowiska można zwizualizować złożonej topologii z setkami składników. Nowe środowisko obsługuje powiększanie i dodaje szczegółów, jak możesz powiększania. Użytkownik może pomniejszenia Aby wyświetlić więcej składników w skrócie i nadal dodatkowych składników z awariami wyższy. 

![Poziom powiększenia](media/app-insights-app-map/zoom-levels.png)

Polecenie każdego składnika, aby zobaczyć powiązane informacje na temat technologii i przejdź do wydajność i środowisko klasyfikacji błąd dla tego składnika.

![Wysuwane okno](media/app-insights-app-map/preview-flyout.png)


## <a name="classic-application-map"></a>Mapowanie aplikacji klasycznych

Pokazuje mapy:

* Testy dostępności
* Składnik po stronie klienta (monitorowane przy użyciu zestawu SDK JavaScript)
* Składnik po stronie serwera
* Zależności składniki klienta i serwera

![Mapa aplikacji](./media/app-insights-app-map/02.png)

Można zwijać i rozwijać grupy łącze zależności:

![zwiń](./media/app-insights-app-map/03.png)

Jeśli masz wiele zależności jednego typu (SQL, HTTP itp.) są zgrupowane. 

![zależności grupowanych](./media/app-insights-app-map/03-2.png)

## <a name="spot-problems"></a>Dodatkowych problemów
Każdy węzeł ma odpowiednie wskaźniki, jak szybkości ładowania, wydajności i błąd dla tego składnika. 

Ikonami ostrzeżenia zaznacz możliwych problemów. Pomarańczowe ostrzeżenie oznacza, że błędy występują w żądaniach, wyświetleń strony lub wywołania zależności. Czerwony oznacza współczynnik awaryjności powyżej 5%. Jeśli chcesz dostosować tych progów, Otwórz opcje.

![ikony błędu](./media/app-insights-app-map/04.png)

Aktywne alerty również Pokaż zapasową: 

![aktywne alerty](./media/app-insights-app-map/05.png)

Jeśli używasz usług SQL Azure jest ikonę, która pokazuje, kiedy są zalecenia, w jaki sposób można poprawić wydajność. 

![zalecenie Azure](./media/app-insights-app-map/06.png)

Kliknij dowolną ikonę, aby uzyskać więcej szczegółów:

![zalecenie Azure](./media/app-insights-app-map/07.png)

## <a name="diagnostic-click-through"></a>Kliknij diagnostyczne za pośrednictwem
Każdego z węzłów na mapie oferuje docelowe kliknij za pomocą diagnostyki. Opcje się różnić w zależności od typu węzła.

![Opcje serwera](./media/app-insights-app-map/09.png)

Dla składników, które są hostowane na platformie Azure obejmują bezpośrednich łączy do nich.

## <a name="filters-and-time-range"></a>Filtry i zakres czasu
Domyślnie mapy znajduje się podsumowanie wszystkich danych dostępna dla wybranego zakresu czasu. Ale odfiltrowaniu może zawierać tylko nazwy operacji lub zależności.

* Nazwa operacji: dotyczy zarówno wyświetleń strony i typy żądania po stronie serwera. Po wybraniu tej opcji mapy zawiera wskaźnik KPI na węźle serwera/klienta tylko wybranej operacji. Przedstawia on wywoływany w kontekście tych określonych operacji zależności.
* Nazwa podstawowa zależności: dotyczy to również zależności przeglądarki AJAX i zależności po stronie serwera. Jeśli raport dane telemetryczne zależności niestandardowych przy użyciu interfejsu API TrackDependency pojawią się również w tym miejscu. Możesz wybrać zależności do wyświetlenia na mapie. Obecnie to pole wyboru nie filtruje żądania po stronie serwera lub wyświetleń strony po stronie klienta.

![Ustaw filtry](./media/app-insights-app-map/11.png)

## <a name="save-filters"></a>Zapisz filtry
Aby zapisać filtry zostały zastosowane, przypiąć widok filtrowany na [pulpitu nawigacyjnego](app-insights-dashboards.md).

![Przypnij do pulpitu nawigacyjnego](./media/app-insights-app-map/12.png)

## <a name="error-pane"></a>Okienko błędu
Po kliknięciu węzła w mapie okienko błędu jest wyświetlana po prawej stronie podsumowania błędów dla tego węzła. Awarie są najpierw pogrupowane według Identyfikatora operacji i pogrupowane według identyfikatora problemu.

![Okienko błędu](./media/app-insights-app-map/error-pane.png)

Kliknięcie awarii przejście do ostatniej wystąpienia tego błędu.

## <a name="resource-health"></a>Kondycja zasobów
W przypadku niektórych typów zasobów kondycja zasobu jest wyświetlany w górnej części okienka błędu. Na przykład kliknięcie węzła SQL spowoduje wyświetlenie kondycji bazy danych i alerty, które mają być uruchamiane.

![Kondycja zasobów](./media/app-insights-app-map/resource-health.png)

Można kliknąć nazwę zasobu, aby wyświetlić przegląd standardowe metryki dla tego zasobu.

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Opinia
Prześlij opinię za pośrednictwem portalu opinie.

![Obraz MapLink-1](./media/app-insights-app-map/13.png)


## <a name="next-steps"></a>Kolejne kroki

* [Azure Portal](https://portal.azure.com)
