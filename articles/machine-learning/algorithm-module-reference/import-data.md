---
title: 'Importuj dane: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu importu danych w usłudze Azure Machine Learning do ładowania danych do eksperymentu usługi machine learning, z istniejącymi usługami danych w chmurze.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028308"
---
# <a name="import-data-module"></a>Zaimportuj moduł danych

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do ładowania danych do eksperymentu usługi machine learning, z istniejącymi usługami danych w chmurze.  
Moduł zawiera teraz kreatora, aby pomóc Ci opcję magazynu, a następnie wybierz istniejące subskrypcje i konta, aby szybko skonfigurować wszystkie opcje. Należy zmodyfikować istniejące połączenie z danymi? Żaden problem; Kreator ładuje wszystkie poprzednie szczegółów konfiguracji, dzięki czemu nie trzeba ponownie uruchomić od podstaw. 
  
Po zdefiniowaniu dane i połączyć się ze źródłem [importu danych](./import-data.md) wnioskuje typ danych w każdej kolumnie na podstawie wartości zawiera i służy do ładowania danych do obszaru roboczego usługi Azure Machine Learning. Dane wyjściowe [importu danych](./import-data.md) jest zestawem danych, które mogą być używane z dowolnym eksperymentu.

  
Jeśli źródło danych zmienia się, możesz odświeżyć zestaw danych i dodawać nowe dane, uruchamiając [importu danych](./import-data.md). Jeśli nie chcesz ponownie odczytywana ze źródła z każdym przy uruchamianiu eksperymentu, jednak wybrać **użycia pamięci podręcznej wyniki** opcji na wartość TRUE. Po wybraniu tej opcji moduł sprawdza, czy eksperymentu zostało uruchomione poprzednio przy użyciu tego samego źródła i te same opcje danych wejściowych. Jeśli zostanie znaleziony poprzedniego uruchomienia, dane w pamięci podręcznej jest używany, zamiast ponownie załadowanie danych ze źródła.
 

## <a name="data-sources"></a>Źródła danych

Modułu importu danych obsługuje następujące źródła danych. Kliknij łącza, aby uzyskać szczegółowe instrukcje i przykłady użycia każdego źródła danych. 
 
Jeśli nie masz pewności, jak i gdzie należy przechowywać dane, zobacz ten przewodnik do typowych scenariuszy danych w procesie nauki o danych:  [Scenariusze zaawansowanej analizy w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Źródło danych| Za pomocą|
|-----------|-----------|  
|[Adres URL sieci Web za pośrednictwem protokołu HTTP](./import-from-web-url-via-http.md)|Pobieranie danych, który znajduje się na adres URL sieci web używającej protokołu HTTP i że zostało podane w formacie CSV, TSV, ARFF lub SvmLight|  
|[Importuj z usługi Azure Blob Storage](./import-from-azure-blob-storage.md) |Pobierz dane są przechowywane w usłudze obiektów blob platformy Azure|  

## <a name="how-to-use-import-data"></a>Jak używać importu danych
 
1. Dodaj **importu danych** modułu do eksperymentu. Możesz znaleźć tego modułu w **danych wejściowych i wyjściowych** kategorii w interfejsie.

2. Kliknij przycisk **Uruchom Kreatora importu danych** Konfigurowanie źródła danych przy użyciu kreatora.

    Kreator pobiera nazwę konta i poświadczenia i skonfigurować inne opcje pomocy. Jeśli edytujesz istniejącej konfiguracji ładuje bieżące wartości najpierw.

3. Jeśli nie chcesz używać kreatora, kliknij przycisk **źródła danych**i wybierz typ podczas odczytu z magazynu w chmurze. 

    Dodatkowe ustawienia są zależne od typu magazynu, możesz wybrać, i czy magazyn jest zabezpieczony, czy nie. Należy podać nazwę konta, typu pliku lub poświadczenia. Niektóre źródła nie wymagają uwierzytelniania dla innych użytkowników może być konieczne znać nazwę konta, klucza lub nazwę kontenera.

4. Wybierz **użycia pamięci podręcznej wyniki** opcję, jeśli chcesz buforować zestawu danych do ponownego użycia w kolejnych przebiegów.

    Przy założeniu, że nie było żadnych innych zmian, aby parametry modułu, eksperymentu ładuje czasu danych tylko pierwszy moduł jest uruchamiany, a następnie używa zbuforowaną wersję zestawu danych.

    Wyłącz tę opcję, jeśli chcesz załadować dane za każdym razem, gdy uruchomienie eksperymentu.

5. Uruchom eksperyment.

    Podczas importowania danych służy do ładowania danych w interfejsie, jego wnioskuje typ danych w każdej z kolumn na podstawie wartości w nim wartości liczbowych lub podzielonych na kategorie.

    - Jeśli istnieje nagłówek, nagłówek jest używany do nazywania kolumny zestawu danych wyjściowych.

    - W przypadku istniejących nagłówków kolumn w danych, nowych nazw kolumn są generowane przy użyciu col1 format, col2... , coln *.

## <a name="results"></a>Wyniki

Po zakończeniu importowania, kliknij wyjściowy zestaw danych i wybierz pozycję **Visualize** aby zobaczyć, jeśli dane zostały pomyślnie zaimportowane.

Jeśli chcesz zapisać dane do ponownego użycia, zamiast importowania nowy zestaw danych przy każdym uruchomieniu eksperymentu, kliknij prawym przyciskiem myszy dane wyjściowe, a następnie wybierz **Zapisz jako zestaw danych**. Wybierz nazwę dla zestawu danych. Zapisany zestaw danych zachowuje te dane w czasie zapisywania i danych nie jest aktualizowany podczas ponownego uruchomienia eksperymentu, nawet w przypadku zmiany zestawu danych w eksperymencie. Może to być przydatne do rzucenia migawki danych.

Po zaimportowaniu danych, może być konieczne pewne dodatkowe przygotowania do modelowania i analiz:


- Użyj [edytować metadane](./edit-metadata.md) można zmienić nazwy kolumn, do obsługi kolumnę jako innego typu danych lub do wskazania, że niektóre kolumny są etykiet lub funkcji.

- Użyj [Select Columns in Dataset](./select-columns-in-dataset.md) można wybrać podzestaw kolumn, aby przekształcić lub użyj w modelowania. Po przekształceniu lub usuniętych kolumn można łatwo zostać ponownie przyłączone do oryginalnego zestawu danych za pomocą [Dodaj kolumny](./add-columns.md) modułu.  

- Użyj [partycja i próbka](./partition-and-sample.md) dzielenia zestawu danych, przeprowadzić próbkowania lub uzyskaj pierwszych n wierszy.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 