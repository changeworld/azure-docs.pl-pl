---
title: Omówienie analizy tekstu — usług Azure Cognitive Services | Dokumentacja firmy Microsoft
description: Analiza tekstu w usłudze Azure Cognitive Services do analizy tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i łączenie podmiotów.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: a514618713afe2306b6fb99b2f8c038aeac56009
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092207"
---
# <a name="what-is-text-analytics"></a>Co to jest analiza tekstu?

Usługi Text Analytics oferuje zaawansowane przetwarzanie języka naturalnego w pierwotnych tekstu bez struktury. Obejmuje cztery główne funkcje: analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i łączenie podmiotów.

## <a name="analyze-sentiment"></a>Analiza tonacji

[Dowiedz się,](how-tos/text-analytics-how-to-sentiment-analysis.md) co klienci myślą o Twojej marki lub wybranego tematu, analizując nieprzetworzony tekst dla wskazówek dotyczących opinii dodatnia lub ujemna. Ten interfejs API zwraca ocenę tonacji od 0 do 1 dla każdego dokumentu, gdzie 1 oznacza najbardziej dodatnią.<br />
Modele analizy są wstępnie przetrenowane przy użyciu rozbudowane treści, tekstu i języka naturalnego technologii firmy Microsoft. Aby uzyskać [wybrane języki](text-analytics-supported-languages.md), interfejs API można analizować i oceniać nieprzetworzony tekst, który należy podać.

## <a name="extract-key-phrases"></a>Wyodrębnianie kluczowych fraz

Automatycznie [wyodrębnianie kluczowych fraz](how-tos/text-analytics-how-to-keyword-extraction.md) można szybko identyfikować jego główne punkty. Na przykład, biorąc pod uwagę tekst wejściowy "żywności został wyborna i wystąpiły personelu wspaniała", usługa analizy tekstu zwróci głównego najważniejsze punkty: "jedzenie" i "personel wspaniała".

## <a name="detect-language"></a>Wykrywanie języka

W przypadku maksymalnie 120 języków [wykryć](how-tos/text-analytics-how-to-language-detection.md) które tekst wejściowy został napisany w języku i kod jeden język, dla każdego dokumentu Przesłano żądanie raportu. Kod języka jest powiązany z wynikiem wskazujący siłę wynik.

## <a name="identify-linked-entities-preview"></a>Identyfikowanie połączonej jednostki (wersja zapoznawcza)

[Identyfikowanie](how-tos/text-analytics-how-to-entity-linking.md) dobrze znanych jednostek w tekst i link do szczegółowych informacji w sieci web. Łączenie podmiotów rozpoznaje i pozwala odróżnić od siebie, gdy termin jest używany jako jeden z oddzielnie rozróżnialnych jednostek, zleceń i inne formy programu word.

## <a name="typical-workflow"></a>Typowy przepływ pracy

Przepływ pracy jest proste: przesyłanie danych do analizy i obsługują dane wyjściowe w kodzie. Analizatory są używane jako — to bez dodatkowej konfiguracji lub dostosowywania.

1. [Zarejestruj](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) dla [klucz dostępu](how-tos/text-analytics-how-to-access-key.md). Klucz musi zostać przekazany na każde żądanie.

2. [Utwórz żądanie](how-tos/text-analytics-how-to-call-api.md#json-schema) w formacie JSON, które zawiera dane jako nieprzetworzony tekst bez struktury.

3. Opublikuj żądanie do punktu końcowego ustalone podczas tworzenia konta, dodając interfejsu API, który chcesz wybrać: analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka lub identyfikator jednostki.

4. Stream ani nie przechowują odpowiedź lokalnie. W zależności od żądania wyniki są wynikiem tonacji, zbiór frazy kluczowe wyodrębnione lub kod języka.

Dane wyjściowe zostaną zwrócone jako pojedynczy dokument JSON z wynikami dla każdego dokumentu tekstowego, który opublikowana, na podstawie identyfikatora. Można następnie analizować, Wizualizuj lub kategoryzowanie wyniki do podejmowania określonych działań analizy.

Operacje wykonywane przez usługę Analiza tekstu są bezstanowe. Dane nie są przechowywane na koncie.

<a name="data-limits"></a>

## <a name="specifications"></a>Specyfikacje

### <a name="supported-languages"></a>Obsługiwane języki

Zobacz [obsługiwane języki w analizy tekstu](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Limity danych

Wszystkie punkty końcowe usługi Text Analytics akceptować dane nieprzetworzony tekst. Aktualne ograniczenie wynosi 5000 znaków dla każdego dokumentu; Jeśli potrzebujesz przeanalizować większych dokumentów, możesz je Podziel na mniejsze części. Jeśli nadal potrzebujesz wyższego limitu [skontaktuj się z nami](https://azure.microsoft.com/overview/sales-number/) tak, aby omówimy Twoje wymagania.

| Limit | Wartość |
|------------------------|---------------|
| Maksymalny rozmiar pojedynczego dokumentu | maksymalnie 5000 znaków, gdyż jest mierzone przez `String.Length`. |
| Maksymalny rozmiar całego żądania | 1 MB |
| Maksymalna liczba dokumentów w żądaniu | 1000 dokumentów |

Ograniczanie liczby wywołań jest 100 wywołań na minutę. Należy pamiętać, że można przesyłać duże ilości dokumentów w jednym wywołaniu (maksymalnie 1000 dokumentów).

### <a name="unicode-encoding"></a>Kodowanie Unicode

Usługi analizy tekstu używa kodowania Unicode reprezentację tekstową i obliczenia liczby znaków. Możesz przesłać żądania w formacie UTF-8 lub UTF-16 z żadnych mierzalne różnic w liczby znaków. Jeśli używasz `String.Length` można pobrać liczby znaków, używasz tej samej metody, które są używane do mierzenia rozmiaru danych.

## <a name="next-steps"></a>Kolejne kroki

Najpierw zainstaluj [interaktywna demonstracja](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Wklej wprowadzanie tekstu (maks. 5000 znaków), aby wykryć język (maksymalnie 120), obliczania wyniku tonacji, wyodrębnianie kluczowych fraz lub Zidentyfikuj połączonych jednostek. Rejestracji nie jest konieczne.

Gdy możesz bezpośrednio wywołać usługę analizy tekstu:

+ [Zarejestruj](how-tos/text-analytics-how-to-signup.md) uzyskać dostęp do kluczy i przejrzyj kroki związane z [wywołania interfejsu API](how-tos/text-analytics-how-to-call-api.md).

+ [Przewodnik Szybki Start](quickstarts/csharp.md) jest przewodnik po interfejsie API REST wywołuje napisane w języku C#. Dowiedz się, jak przesłać tekstu, wybierz analizę i wyświetlić wyniki z minimalną ilością kodu.

+ [Dokumentacja interfejsu API](//go.microsoft.com/fwlink/?LinkID=759346) zawiera dokumentacja techniczna, interfejsy API REST. Dokumentacja obsługuje osadzone wywołań, można więc wywoływać interfejs API z każdej strony dokumentacji.

+ [Zewnętrzne & sekcji Zawartość społeczności](text-analytics-resource-external-community.md) zawiera listę wpisów w blogu i filmy wideo ukazujące sposób korzystanie z analizy tekstu z innymi narzędziami i technologiami.

## <a name="see-also"></a>Zobacz także

 [Cognitive stronę dokumentacji usługi](https://docs.microsoft.com/azure/cognitive-services/)
