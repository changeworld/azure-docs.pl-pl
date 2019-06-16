---
title: 'Importuj z adresu URL sieci Web za pośrednictwem protokołu HTTP: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć Importuj z adresu URL sieci Web za pomocą modułu HTTP w usłudze Azure Machine Learning można odczytać danych z publicznej strony internetowej do użycia w eksperymentu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bff913efb38c9e5589c795386dfbbc480d799a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411444"
---
# <a name="import-from-web-url-via-http-module"></a>Importuj z adresu URL sieci Web za pośrednictwem modułu HTTP

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do odczytywania danych z publicznej strony internetowej do użycia w eksperymentu uczenia maszynowego.

Poniższe ograniczenia mają zastosowanie do danych na stronie sieci web:

- Dane muszą być w jednym z obsługiwanych formatów: CSV, TSV, ARFF lub SvmLight. Inne dane będą powodować błędy.
- Uwierzytelnianie nie jest wymagane lub obsługiwany. Dane muszą być dostępne publicznie. 

Istnieją dwa sposoby, aby uzyskać dane: Konfigurowanie źródła danych za pomocą kreatora lub ręcznie skonfigurować.

## <a name="use-the-data-import-wizard"></a>Użyj Kreatora importu danych

1. Dodaj **importu danych** modułu do eksperymentu. Moduł w interfejsie, można znaleźć w **danych wejściowych i wyjściowych** kategorii.

2. Kliknij przycisk **Uruchom Kreatora importu danych** i wybierz adres URL sieci Web za pośrednictwem protokołu HTTP.

3. Wklej adres URL, a następnie wybierz format danych.

4. Jeśli konfiguracja zostanie zakończona.

Aby edytować istniejące połączenie z danymi, uruchom ponownie kreatora. Kreator ładuje wszystkie poprzednie szczegółów konfiguracji, dzięki czemu nie trzeba ponownie uruchomić od podstaw

## <a name="manually-set-properties-in-the-import-data-module"></a>Ręcznie Ustaw właściwości modułu importu danych

Poniżej opisano jak ręcznie skonfigurować źródło importu.

1. Dodaj [importu danych](import-data.md) modułu do eksperymentu. Moduł w interfejsie, można znaleźć w **danych wejściowych i wyjściowych** kategorii.

2. Aby uzyskać **źródła danych**, wybierz opcję **adres URL sieci Web za pośrednictwem protokołu HTTP**.

3. Aby uzyskać **adresu URL**wpisz lub Wklej pełny adres URL strony, która zawiera dane, których chcesz załadować.

    Adres URL powinien zawierać pełną ścieżkę, z nazwą pliku i rozszerzenie do strony, która zawiera dane, które można załadować i adresem URL witryny.

    Na przykład następująca strona zawiera zestaw danych irysów z repozytorium uniwersytet kalifornijski Irvine uczenia maszynowego:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Aby uzyskać **format danych**, wybierz jedną z obsługiwanych danych formatów z listy.

    Firma Microsoft zaleca, Zawsze sprawdzaj wcześniej dane do określenia formatu. Na stronie UC Irvine używa formatu CSV. Inne formaty obsługiwane dane są TSV, ARFF i SvmLight.

5. Jeśli dane są w formacie CSV lub TSV, użyj **plik ma wiersz nagłówka** opcję, aby wskazać, czy źródło danych zawiera wiersz nagłówka. Wiersz nagłówka służy do przypisywania nazw kolumn.

6. Wybierz **użycia pamięci podręcznej wyniki** opcji, jeśli nie będziesz już dane, aby zmienić wiele lub jeśli chcesz uniknąć ponownego ładowania danych każdego czasu uruchamiania eksperymentu.

    Gdy ta opcja jest zaznaczona, eksperymentu ładuje czasu danych pierwszy moduł jest uruchamiany, a następnie używa zbuforowaną wersję zestawu danych.

    Jeśli chcesz ponownie załadować zestaw danych w każdej iteracji eksperymentu zestawu danych, wyłącz opcję **użycia pamięci podręcznej wyniki** opcji. Wyniki są również ponownie załadowany w przypadku zmiany parametrów [importu danych](import-data.md).

7. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu kliknij wyjściowy zestaw danych i wybierz pozycję **Visualize** aby zobaczyć, jeśli dane zostały pomyślnie zaimportowane.


## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 