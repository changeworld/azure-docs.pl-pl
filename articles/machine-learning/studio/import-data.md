---
title: Importowanie danych do usługi Machine Learning Studio | Dokumentacja firmy Microsoft
description: Jak zaimportować dane do usługi Azure Machine Learning Studio z różnych źródeł danych. Dowiedz się, jakie typy danych i formatów danych są obsługiwane.
keywords: Importowanie danych, formatu danych, typy danych, źródła danych, dane szkoleniowe
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: a5750555802489b41b007831164767beb953ebc4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837467"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio z różnych źródeł danych
Aby użyć własnych danych w usłudze Machine Learning Studio do opracowywania i uczenia rozwiązania analizy predykcyjnej, można: 

* przekazywanie danych z **pliku lokalnego** wcześniejsze z dysku twardego do utworzenia modułu zestawu danych w obszarze roboczym
* dostęp do danych z jednego z kilku **źródeł danych w trybie online** eksperymentu jest uruchomiona za pomocą [i zaimportuj dane] [ import-data] modułu 
* Użyj danych z innej usługi Azure Machine learning **eksperymentu** zapisane jako zestawu danych
* Użyj danych z lokalnego **bazy danych programu SQL Server**

Każdej z tych opcji jest opisany w tematach menu poniżej. W tych tematach opisano, jak zaimportować dane z tych różnych źródeł danych do użycia w usłudze Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Wiele przykładowych zestawów danych są dostępne w usłudze Machine Learning Studio używanej dla danych szkoleniowych. Aby uzyskać informacje o tych, zobacz [użyj przykładowych zestawów danych w usłudze Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

W tym temacie wprowadzające również omówiono sposób przygotowania danych do użycia w usłudze Machine Learning Studio oraz opis, które formaty danych i typy danych są obsługiwane. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Przygotowanie danych do użycia w usłudze Azure Machine Learning Studio
Usługa Machine Learning Studio jest przeznaczona do pracy z danymi prostokątne lub tabelarycznych, takich jak dane tekstowe, rozdzielany lub strukturę danych z bazy danych, chociaż w niektórych sytuacjach może być używany nieregularnych danych.

Najlepiej danych jest stosunkowo czysty. Oznacza to, że należy zajmie się problemy, takie jak parametry bez cudzysłowów przed przekazać dane do eksperymentu.

Jednak Brak modułów dostępnych w usłudze Machine Learning Studio umożliwiające niektórych manipulacji danymi w ramach eksperymentu. W zależności od algorytmów, którego będziesz używać uczenia maszynowego, konieczne może być zdecydować, w jaki sposób będzie obsługiwać danych strukturalnych problemy, takie jak brak wartości i rozrzedzone danych, a istnieją modułów, które mogą pomóc, z których. Szukaj w **transformacji danych** części palety modułów dla modułów, które wykonywanie tych funkcji.

W dowolnym momencie w eksperymencie można wyświetlić lub pobrać dane, które jest generowany przez moduł, klikając port wyjściowy. W zależności od modułu mogą być pobierania różne opcje dostępne lub może być niemożliwe do wizualizacji danych w przeglądarce sieci web w usłudze Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Dane formaty i typy danych obsługiwane
Można je zaimportować do eksperymentu wiele typów danych, w zależności od tego, jaki mechanizm służy do importowania danych i której pochodzi od:

* Zwykły tekst (txt)
* Wartości rozdzielanych przecinkami (CSV) z nagłówkiem (.csv) lub bez (. nh.csv)
* Tabulatorem tabulatorami (TSV z nagłówkiem (.tsv) lub bez) (. nh.tsv)
* Plik programu Excel
* Tabela platformy Azure
* Tabelę programu hive
* Tabela bazy danych SQL
* Wartości OData
* Dane SVMLight (.svmlight) (zobacz [definicji SVMLight](http://svmlight.joachims.org/) dla informacji o formacie)
* Atrybut danych relacji File Format (ARFF) (.arff) (zobacz [definicji ARFF](http://weka.wikispaces.com/ARFF) dla informacji o formacie)
* Plik zip (.zip)
* Plik obiektu lub obszar roboczy R (. RData)

W przypadku importowania danych w formacie, takich jak ARFF zawierający metadane, Machine Learning Studio wykorzystuje te metadane do definiowania nagłówek i typ danych każdej kolumny.

Po zaimportowaniu danych, takich jak format TSV lub CSV, który nie zawiera metadanych usługi Machine Learning Studio wnioskuje typ danych dla każdej kolumny poprzez próbkowanie danych. Jeśli dane również nie ma nagłówki kolumn, Machine Learning Studio udostępnia domyślne nazwy.

Można jawnie określ lub zmień nagłówki i typy danych kolumn za pomocą [edytowanie metadanych][edit-metadata].

Następujące **typy danych** są rozpoznawane przez Machine Learning Studio:

* Ciąg
* Liczba całkowita
* O podwójnej precyzji
* Wartość logiczna
* DateTime
* Zakres czasu

Usługa Machine Learning Studio używa typu danych wewnętrznych o nazwie ***tabeli danych*** do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych za pomocą [przekonwertować zestawu danych] [ convert-to-dataset] modułu.

Każdy moduł akceptującego formatów innych niż tabela danych przekonwertuje dane do tabeli danych dyskretnie przed przekazaniem go do następnego modułu.

Jeśli to konieczne, możesz przekonwertować format tabeli danych do woluminu CSV, TSV, ARFF lub format SVMLight przy użyciu innych modułów konwersji.
Szukaj w **konwersje Format danych** części palety modułów dla modułów, które wykonywanie tych funkcji.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
