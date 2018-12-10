---
title: Omówienie usługi Azure Time Series Insights | Microsoft Docs
description: Omówienie usługi Azure Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/03/2018
ms.openlocfilehash: 83513ab9f7bee5c51fc909e5f4246105b6f145ca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876135"
---
# <a name="azure-time-series-insights-overview"></a>Omówienie usługi Azure Time Series Insights

Usługa Azure Time Series Insights (TSI) to kompleksowa platforma jako usługa do pozyskiwania, przetwarzania i magazynowania wysoce skontekstualizowanych, zoptymalizowanych pod kątem szeregów czasowych danych w skali środowiska IoT i wykonywania względem nich zapytań. Usługa Azure TSI idealnie nadaje się do eksplorowania danych ad hoc oraz do analizy operacyjnej. Usługa TSI to unikatowo rozszerzalna, niestandardowa oferta, która spełnia szeroki zakres potrzeb związanych z przemysłowymi wdrożeniami IoT.

## <a name="what-is-iot-data"></a>Co to są dane IoT?

Dane IoT to każde dane „przemysłowe” dostępne w organizacjach intensywnie korzystających z zasobów. Dane IoT są często wysoce nieustrukturyzowane, ponieważ są przesyłane z szerokiego zakresu zasobów, które służą do rejestrowania dość „zanieczyszczonych” pomiarów, takich jak temperatura, ruch i wilgotność. Ponadto te strumienie danych często charakteryzują się znacznymi lukami, uszkodzonymi komunikatami i fałszywymi odczytami. Dane z tych strumieni należy oczyścić, zanim będzie można przeprowadzić na nich jakąkolwiek analizę. Dane IoT często mają znaczenie tylko w kontekście dodatkowych danych pochodzących z własnych (np. systemy CRM i ERP) lub zewnętrznych źródeł danych (np. pogoda lub lokalizacja).

W wyniku tylko niewielka część tych danych jest wykorzystywana do celów operacyjnych i biznesowych. Takie dane zapewniają spójne, kompleksowe, bieżące i prawidłowe informacje na potrzeby raportów i analiz biznesowych. Z tego względu przekształcenie zgromadzonych danych IoT na szczegółowe informacje umożliwiające podjęcie działań wymaga kilku kluczowych funkcji:

* Przetwarzanie danych w celu oczyszczenia, przefiltrowania, interpolacji, przekształcenia i przygotowania danych do analizy
* Struktura do nawigowania po danych i ich interpretacji (czyli do normalizacji i ustalenia kontekstu danych)
* Ekonomiczny magazyn do długoterminowego/nieskończonego przechowywania przetworzonych (uzyskanych), jak i pierwotnych danych z dziesiątków lat

Typowy przepływ danych IoT można przedstawić w następujący sposób:

  ![Przepływ danych IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Usługa Azure Time Series Insights dla przemysłowego Internetu rzeczy

Obecny krajobraz Internetu rzeczy jest zróżnicowany. Obejmuje on klientów z branży produkcyjnej, motoryzacyjnej, paliwowej, energetycznej i usług komunalnych, inteligentnego budownictwa i konsultingowej. Scenariusze obejmują eksplorowanie danych ad hoc, gdzie forma danych jest nieznana, oraz analizę operacyjną danych o określonym schemacie (jawnie modelowanych) w celu uzyskania wydajności operacyjnej. Te scenariusze na ogół istnieją obok siebie i obsługują różne przypadki użycia. Funkcje platformy, takie jak magazyn wielowarstwowy (ciepły i zimny), możliwość przechowywania danych szeregów czasowych z kilkudziesięciu lat oraz możliwość jawnego modelowania i optymalizowania zapytań na potrzeby analizy operacyjnej opartej na zasobach, stają się kluczem do sukcesu przedsiębiorstw przemysłowego IoT i ich rewolucji cyfrowej.

Usługa Azure TSI jest kompleksową ofertą platformy jako usługi, która spełnia wymagania zarówno eksplorowania danych IoT, jak i uzyskiwania szczegółowych informacji operacyjnych. Usługa Time Series Insights oferuje w pełni zarządzaną usługę w chmurze do analizowania danych szeregów czasowych w skali IoT.

Klienci mogą przechowywać dane pierwotne w magazynie w pamięci bez schematu i wykonywać interaktywne zapytania ad-hoc za pośrednictwem aparatu zapytań rozproszonych i interfejsu API, a także korzystać z naszego zaawansowanego środowiska obsługi użytkownika do wizualizowania miliardów zdarzeń w ciągu kilku sekund. Dowiedz się więcej o naszych [funkcjach eksplorowania danych](./time-series-insights-overview.md).

Usługa TSI oferuje również funkcje dotyczące szczegółowych informacji operacyjnych, obecnie w publicznej wersji zapoznawczej. W połączeniu z interakcyjnym eksplorowaniem danych i analizą operacyjną usługa Time Series Insights umożliwia klientom uzyskanie większej wartości z danych zgromadzonych z zasobów IoT. Oferta publicznej wersji zapoznawczej obsługuje następujące kluczowe funkcje:

* Skalowalny, zoptymalizowany pod kątem wydajności i kosztów magazyn danych szeregów czasowych, który umożliwia określenie w ciągu kilku sekund trendów danych szeregów czasowych z wielu lat przy użyciu chmurowego rozwiązania IoT.
* Obsługa modelu semantycznego w celu opisania domeny i metadanych skojarzonych z pochodnymi i niepochodnymi sygnałami z zasobów i urządzeń.
* Ulepszone środowisko obsługi użytkownika, które łączy szczegółowe informacje o danych oparte na zasobach z rozbudowanymi funkcjami analizy danych ad-hoc w celu przeprowadzania analiz biznesowych i operacyjnych.
* Integracja z zaawansowanymi narzędziami do uczenia maszynowego i analizy, takimi jak m.in. Azure Databricks, Apache Spark, Azure Machine Learning, notesy programu Jupyter i usługa Power BI, które ułatwiają klientom wykonywanie zadań związanych z danymi szeregów czasowych i zwiększanie wydajności operacyjnej.

Funkcje dotyczące szczegółowych informacji operacyjnych i eksplorowania danych są oferowane razem w prostym modelu płatności zgodnie z rzeczywistym użyciem na potrzeby przetwarzania danych, magazynowania i wysyłania zapytań, zapewniając klientom o wiele bardziej skalowalny model, który można dopasować do zmieniających się potrzeb biznesowych.

Poniżej znajduje się ogólny diagram przepływu danych, na którym przedstawiono zaktualizowane funkcje:

  ![Najważniejsze możliwości][2]

Dzięki wprowadzeniu tych najważniejszych możliwości przemysłowego IoT usługa Azure TSI oferuje następujące kluczowe korzyści.

* Wielowarstwowy magazyn dla danych szeregów czasowych w skali IoT

  * Dzięki wspólnemu potokowi przetwarzania danych do pozyskiwania danych klienci mogą przechowywać dane w ciepłym magazynie na potrzeby interaktywnych zapytań i/lub w zimnym magazynie na potrzeby przechowywania dużych ilości danych i korzystać z zalet wysoce wydajnych zapytań opartych na zasobach.

  * Routing dynamiczny między warstwami magazynu będzie dostępny wkrótce.

* Model szeregu czasowego na potrzeby określania kontekstu nieprzetworzonych danych telemetrycznych i uzyskiwania szczegółowych danych opartych na zasobach

  * Klienci mogą określać kontekst dla nieprzetworzonych danych telemetrycznych za pomocą opisowego modelu szeregu czasowego i wykonywać zaawansowane analizy operacyjne za pomocą wysoce zoptymalizowanych pod kątem wydajności i kosztów zapytań opartych na urządzeniach.

* Bezproblemowa integracja z innymi rozwiązaniami do obsługi danych
  
  * Dane w usłudze Azure Time Series Insights są przechowywane w plikach open source Apache Parquet, dlatego klienci z łatwością mogą zintegrować tę usługę z innymi rozwiązaniami do obsługi danych (własnymi lub zewnętrznymi) na potrzeby kompleksowych scenariuszy obejmujących analizę biznesową, zaawansowane uczenie maszynowe, analizę predykcyjną itd.

* Eksploracja danych w czasie niemal rzeczywistym

  * Środowisko użytkownika eksploratora usługi Azure Time Series Insights udostępnia wizualizacje dla wszystkich danych przesyłanych strumieniowo za pośrednictwem potoku pozyskiwania. Wkrótce po połączeniu źródła zdarzeń klienci mogą wyświetlać i eksplorować dane i wysyłać względem nich zapytania na potrzeby sprawdzania, czy urządzenie generuje dane zgodnie z oczekiwaniami, i monitorowania zasobu IoT pod kątem kondycji, wydajności i ogólnej efektywności.

* Analizowanie głównych przyczyn problemów i wykrywanie anomalii

  * Eksplorator usługi Azure Time Series Insights obsługuje wzorce i widoki perspektyw, które pozwalają przeprowadzać i zapisywać wieloetapowe analizy głównych przyczyn. Łącząc usługę Time Series Insights z usługą Azure Stream Analytics klienci mogą wykrywać alerty i anomalie w czasie zbliżonym do rzeczywistego.

* Tworzenie aplikacji niestandardowych na platformie usługi Time Series Insights

  * Usługa Azure Time Series Insights obsługuje zestaw SDK języka JavaScript za pomocą zaawansowanych kontrolek i uproszczonego dostępu do zapytań, dzięki czemu klienci mogą tworzyć niestandardowe aplikacje IoT na platformie usługi Time Series Insights, aby dostosować je do potrzeb poszczególnych firm.
  * Klienci mogą również bezpośrednio korzystać z interfejsów API zapytań usługi Time Series Insights, aby kierować dane do niestandardowych aplikacji IoT.

## <a name="next-steps"></a>Następne kroki

Teraz możesz rozpocząć pracę z usługą Azure TSI (wersja zapoznawcza):

> [!div class="nextstepaction"]
> [Zapoznaj się z przewodnikiem Szybki start](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png