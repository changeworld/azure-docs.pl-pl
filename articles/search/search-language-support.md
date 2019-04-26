---
title: Wiele języków, indeksowanie dla innej niż angielska wyszukiwania zapytania — usługa Azure Search
description: Usługa Azure Search obsługuje 56 językach, wykorzystując analizatorów języka — Lucene i przetwarzania języka naturalnego technologii firmy Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: a198fa7fe5e1ed81e30987990359f9ecedbe225b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197298"
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Tworzenie indeksu dla dokumentów w wielu językach w usłudze Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Uwalnianie potencjału funkcji analizatory języka jest równie proste jak ustawienie jedną właściwością w polu możliwym do przeszukania w definicji indeksu. Teraz możesz wykonać ten krok w portalu.

Poniżej przedstawiono zrzuty ekranu przedstawiające bloków w witrynie Azure Portal dla usługi Azure Search, które umożliwiają użytkownikom Definiowanie schematu indeksu. W tym bloku użytkownicy mogą tworzyć, wszystkie pola i ustaw właściwość analizator dla każdego z nich.

> [!IMPORTANT]
> Można ustawić tylko analizatora języków podczas definicji pola, podobnie jak podczas tworzenia nowego indeksu od podstaw w górę lub podczas dodawania nowe pole do istniejącego indeksu. Upewnij się, że w pełni określić wszystkich atrybutów, w tym analizator, podczas tworzenia pola. Nie można edytować atrybuty, lub zmień typ analizatora, po zapisaniu zmian.
>
>

## <a name="define-a-new-field-definition"></a>Zdefiniuj nową definicję pola
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) , a następnie otwórz blok usługi usługi search.
2. Kliknij przycisk **Dodaj indeks** na pasku poleceń u góry pulpitu nawigacyjnego usługi, aby uruchomić nowy indeks lub Otwórz istniejący indeks, aby ustawić analizator na nowe pola, które dodajesz do istniejącego indeksu.
3. Zostanie wyświetlony blok pola, opcjami do definiowania schematu indeksu, w tym karty analizator używany do wybierania analizatora języka.
4. W polu Uruchom definicję pola, podając nazwę, wybierając typ danych i ustawienie atrybuty należy zaznaczyć pole jako pełnotekstowe wyszukiwanie, pobieranie w wynikach wyszukiwania, można używać w strukturach nawigacji zestawu reguł, sortowanie i tak dalej.
5. Przed przejściem do następnego pola, należy otworzyć **analizatora** kartę.

![][1]
*Aby wybrać analizator, kliknij kartę analizatora bloku pola*

## <a name="choose-an-analyzer"></a>Wybierz Analizator
1. Przewiń do pola, które są definiowane.
2. Jeśli nie została oznaczona jako wyszukiwanie, kliknij pole wyboru, teraz, aby oznaczyć go jako **z możliwością wyszukiwania**.
3. Kliknij obszar analizatora, aby wyświetlić listę dostępnych analizatorów.
4. Wybierz odpowiedni analizator, którego chcesz użyć.

![][2]
*Wybierz jedną z obsługiwanych analizatory dla każdego pola*

Domyślnie wszystkie pola z możliwością wyszukiwania używają [analizator Lucene standardowa](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) który jest niezależny od języka. Aby wyświetlić pełną listę obsługiwanych analizatorów, zobacz [Obsługa języków w usłudze Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Po wybraniu analizatora języków dla pola będą używane przy każdym żądaniu indeksowanie i wyszukiwanie dla tego pola. Gdy zapytanie jest wystawiony na podstawie wielu pól za pomocą różnych analizatorów, zapytania będą przetwarzane niezależnie przez prawo analizatory dla każdego pola.

Wiele aplikacji internetowych i mobilnych służą użytkowników na całym świecie używający innych języków. Istnieje możliwość definiowania indeksu dla tego typu scenariusza, tworząc pole dla każdego z obsługiwanych języków.

![][3]
*Definicja indeksu pole Opis każdego z języków obsługiwanych*

Jeśli język agenta zapytania jest znany, żądanie wyszukiwania może należeć do zakresu za pomocą określonego pola **searchFields** parametr zapytania. Następujące zapytanie, pojawi się wyłącznie w odniesieniu do opisu Polski:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2017-11-11`

Możesz zbadać indeksu w portalu przy użyciu **Eksploratora wyszukiwania** wkleić w zapytaniu podobny do przedstawionego powyżej. Eksplorator wyszukiwania jest dostępne z poziomu paska poleceń w bloku usługi. Zobacz [zapytania w indeksie usługi Azure Search w portalu](search-explorer.md) Aby uzyskać szczegółowe informacje.

Czasami język agenta zapytania nie jest znany, w którym to przypadku zapytania mogą być wystawiane względem wszystkich pól jednocześnie. Jeśli to konieczne, preferencji wyników w niektórych językach mogą być definiowane za pomocą [profile oceniania](https://msdn.microsoft.com/library/azure/dn798928.aspx). W poniższym przykładzie dopasowań w opisie w języku angielskim będą oceniane wyższej względem dopasowań w Polski i francuskim:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2017-11-11`

Jeśli jesteś deweloperem platformy .NET, pamiętaj, że możesz skonfigurować użyciu przez analizatory języka [zestawu .NET SDK usługi Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search). Najnowsza wersja zapewnia obsługę również analizatory języka firmy Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
