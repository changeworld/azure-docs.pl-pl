---
title: 'Importuj z internetowego adresu URL za pośrednictwem protokołu HTTP: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać funkcji importowania z internetowego adresu URL za pośrednictwem protokołu HTTP w usłudze Azure Machine Learning, aby czytać dane z publicznej strony internetowej na potrzeby eksperymentu uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128754"
---
# <a name="import-from-web-url-via-http-module"></a>Importuj z internetowego adresu URL za pośrednictwem modułu HTTP

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do odczytywania danych z publicznej strony internetowej na potrzeby eksperymentu uczenia maszynowego.

Następujące ograniczenia dotyczą danych opublikowanych na stronie sieci Web:

- Dane muszą być w jednym z obsługiwanych formatów: CSV, TSV, ARFF lub SvmLight. Inne dane spowodują błędy.
- Uwierzytelnianie nie jest wymagane ani obsługiwane. Dane muszą być dostępne publicznie. 

Istnieją dwa sposoby pobierania danych: Użyj kreatora, aby skonfigurować źródło danych lub skonfigurować je ręcznie.

## <a name="use-the-data-import-wizard"></a>Korzystanie z Kreatora importu danych

1. Dodaj moduł **Import danych** do eksperymentu. Moduł można znaleźć w interfejsie, w kategorii dane **wejściowe i wyjściowe** .

2. Kliknij przycisk **Uruchom Kreatora importu danych** i wybierz adres URL sieci Web za pośrednictwem protokołu HTTP.

3. Wklej w adresie URL i wybierz format danych.

4. Po zakończeniu konfiguracji.

Aby edytować istniejące połączenie danych, ponownie uruchom kreatora. Kreator ładuje wszystkie poprzednie szczegóły konfiguracji, aby nie trzeba było ich uruchamiać od początku

## <a name="manually-set-properties-in-the-import-data-module"></a>Ręczne ustawianie właściwości w module Importuj dane

W poniższych krokach opisano sposób ręcznego konfigurowania źródła importu.

1. Dodaj moduł [Import danych](import-data.md) do eksperymentu. Moduł można znaleźć w interfejsie, w kategorii dane **wejściowe i wyjściowe** .

2. W obszarze **Źródło danych**wybierz pozycję **adres URL sieci Web za pośrednictwem protokołu HTTP**.

3. W polu **adres URL**wpisz lub wklej pełny adres URL strony zawierającej dane, które chcesz załadować.

    Adres URL powinien zawierać adres URL witryny i pełną ścieżkę, z nazwą pliku i rozszerzeniem, do strony zawierającej dane do załadowania.

    Na przykład następująca strona zawiera zestaw danych Iris z repozytorium uczenia maszynowego University of California, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. W polu **Format danych**wybierz jeden z obsługiwanych formatów danych z listy.

    Zalecane jest, aby zawsze sprawdzać dane, aby określić format. Strona UC Irvine używa formatu CSV. Inne obsługiwane formaty danych to TSV, ARFF i SvmLight.

5. Jeśli dane są w formacie CSV lub TSV, użyj pliku z opcją **wiersza nagłówka** , aby wskazać, czy dane źródłowe zawierają wiersz nagłówka. Wiersz nagłówka jest używany do przypisywania nazw kolumn.

6. Wybierz opcję **Użyj buforowanych wyników** , jeśli nie spodziewasz się, że dane mają być zmieniane, lub jeśli chcesz uniknąć ponownego ładowania danych przy każdym uruchomieniu eksperymentu.

    Gdy ta opcja jest zaznaczona, eksperyment ładuje dane przy pierwszym uruchomieniu modułu, a następnie używa buforowanej wersji zestawu danych.

    Jeśli chcesz ponownie załadować zestaw danych dla każdej iteracji zestawu danych eksperymentu, usuń zaznaczenie opcji **Użyj wyników z pamięci** podręcznej. Wyniki są również ponownie ładowane, jeśli istnieją zmiany parametrów [importu danych](import-data.md).

7. Uruchom eksperyment.

## <a name="results"></a>Wyniki

Po zakończeniu kliknij wyjściowy zestaw danych i wybierz polecenie **Wizualizuj** , aby sprawdzić, czy dane zostały zaimportowane pomyślnie.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 