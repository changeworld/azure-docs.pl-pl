---
title: Indeksowanie w wielu językach dla zapytań wyszukiwania w języku nieanglojęzycznym
titleSuffix: Azure Cognitive Search
description: Usługa Azure Cognitive Search obsługuje 56 języków, wykorzystując analizatory języka z lucene i technologii przetwarzania języka naturalnego firmy Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793590"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Jak utworzyć indeks dla wielu języków w usłudze Azure Cognitive Search

Indeksy mogą zawierać pola zawierające zawartość z wielu języków, na przykład tworzenie poszczególnych pól dla ciągów specyficznych dla języka. Aby uzyskać najlepsze wyniki podczas indeksowania i wykonywania zapytań, przypisz analizator języka, który zawiera odpowiednie reguły językowe. 

Usługa Azure Cognitive Search oferuje duży wybór analizatorów języka z Lucene i Microsoft, które można przypisać do poszczególnych pól przy użyciu właściwości Analizator. Można również określić analizator języka w portalu, zgodnie z opisem w tym artykule.

## <a name="add-analyzers-to-fields"></a>Dodawanie analizatorów do pól

Analizator języka jest określony podczas tworzenia pola. Dodanie analizatora do istniejącej definicji pola wymaga zastąpienia (i ponownego załadowania) indeksu lub utworzenia nowego pola identycznego z oryginalnym, ale z przypisaniem analizatora. Następnie można usunąć nieużywane pole w dogodnym dla ciebie momencie.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i znajdź usługę wyszukiwania.
1. Kliknij **przycisk Dodaj indeks** na pasku poleceń u góry pulpitu nawigacyjnego usługi, aby rozpocząć nowy indeks, lub otwórz istniejący indeks, aby ustawić analizator dla nowych pól dodawanych do istniejącego indeksu.
1. Rozpocznij definicję pola, podając nazwę.
1. Wybierz typ danych Edm.String. Tylko pola ciągów można przeszukiwać w pełnym tekście.
1. Ustaw atrybut **z wyszukuj,** aby włączyć właściwość Analizator. Pole musi być oparte na tekście, aby korzystać z analizatora języka.
1. Wybierz jeden z dostępnych analizatorów. 

![Przypisywanie analizatorów języka podczas definiowania pola](media/search-language-support/select-analyzer.png "Przypisywanie analizatorów języka podczas definiowania pola")

Domyślnie wszystkie pola z wyszukujem używają [analizatora Standardowego Lucene,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) który jest niezależny od języka. Aby wyświetlić pełną listę obsługiwanych analizatorów, zobacz [Dodawanie analizatorów języka do indeksu usługi Azure Cognitive Search.](index-add-language-analyzers.md)

W portalu analizatory są przeznaczone do użycia w stanie— jest. Jeśli wymagane jest dostosowanie lub określonej konfiguracji filtrów i tokenizatorów, należy [utworzyć analizatora niestandardowego](index-add-custom-analyzers.md) w kodzie. Portal nie obsługuje wybierania lub konfigurowania analizatorów niestandardowych.

## <a name="query-language-specific-fields"></a>Kwerenda pól specyficznych dla języka

Po wybraniu analizatora języka dla pola będzie on używany z każdym żądaniem indeksowania i wyszukiwania dla tego pola. Gdy kwerenda jest wystawiana dla wielu pól przy użyciu różnych analizatorów, kwerenda będzie przetwarzana niezależnie przez przypisane analizatory dla każdego pola.

Jeśli język agenta wystawiającego kwerendę jest znany, żądanie wyszukiwania może być ograniczone do określonego pola przy użyciu parametru zapytania **searchFields.** Poniższa kwerenda zostanie wystawiona tylko w języku polskim:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Można zbadać indeks z portalu, za pomocą [**Eksploratora wyszukiwania**](search-explorer.md) wklejać w kwerendzie podobnej do pokazanej powyżej.

## <a name="boost-language-specific-fields"></a>Zwiększanie pól specyficznych dla języka

Czasami język agenta wystawiającego kwerendę nie jest znany, w którym to przypadku kwerenda może być wystawiona dla wszystkich pól jednocześnie. W razie potrzeby preferencje dla wyników w określonym języku można zdefiniować za pomocą [profili oceniania](index-add-scoring-profiles.md). W poniższym przykładzie mecze znalezione w opisie w języku angielskim będą oceniane wyżej w stosunku do meczów w języku polskim i francuskim:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Następne kroki

Jeśli jesteś deweloperem platformy .NET, należy pamiętać, że można skonfigurować analizatory języka przy użyciu [usługi Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) i [Analysiser](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) właściwości. 