---
title: Importowanie danych do usługi Machine Learning Studio | Dokumentacja firmy Microsoft
description: Jak zaimportować dane do usługi Azure Machine Learning Studio z różnych źródeł danych. Dowiedz się, jakie typy danych i formatów danych są obsługiwane.
keywords: Importowanie danych, formatu danych, typów danych, źródeł danych, dane szkoleniowe
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 733441620d48e99d2f1ef67d1258b7968b026840
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2018
ms.locfileid: "52262986"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importowanie danych szkoleniowych do usługi Azure Machine Learning Studio z różnych źródeł danych

Aby skorzystać z własnych danych w usłudze Machine Learning Studio, tworzenie i uczenie rozwiązania do analizy predykcyjnej, można używać danych z: 

* A [ **lokalnego pliku** ](import-data-from-local-file.md) -ładowanie danych lokalnych, które wcześniej z dysku twardego do tworzenia modułu zestawu danych w obszarze roboczym
* [**Źródła danych online** ](import-data-from-online-sources.md) — użyj [importu danych] [ import-data] modułu dostępu do danych z jednego z kilku źródeł online po uruchomieniu eksperymentu
* [**Usługi Machine Learning Studio eksperymentu** ](import-data-from-an-experiment.md) — korzystanie z danych, który został zapisany jako zestaw danych w usłudze Machine Learning Studio
* [**Baza danych programu SQL Server w środowisku lokalnym** ](use-data-from-an-on-premises-sql-server.md) -użyte dane z lokalnej bazy danych programu SQL Server, bez konieczności ręcznego kopiowania danych

> [!NOTE]
> Wiele przykładowych zestawów danych są dostępne w usłudze Machine Learning Studio, można użyć danych szkoleniowych. Aby uzyskać informacje na ten temat, zobacz [użyj przykładowych zestawów danych w usłudze Azure Machine Learning Studio](use-sample-datasets.md)).
> 
> 

Ten artykuł wprowadzający również w tym artykule omówiono sposób przygotowania danych do użycia w usłudze Machine Learning Studio i w tym artykule opisano, które formaty danych i typy danych są obsługiwane.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Pobieranie danych gotowe do użycia w usłudze Azure Machine Learning Studio
Usługa Machine Learning Studio jest przeznaczony do pracy z prostokątne lub tabelaryczne dane, takie jak dane tekstowe, rozdzielonych lub ze strukturą danych z bazy danych, chociaż w niektórych sytuacjach może użyć danych innych niż prostokątne.

Najlepiej Twoje dane są stosunkowo czyste. Oznacza to należy zadbać o problemy, takie jak ciągi bez cudzysłowów, aby przekazać dane do eksperymentu.

Istnieją moduły dostępne w usłudze Machine Learning Studio pozwalających na korzystanie z niektórych manipulacji danych w ramach eksperymentu. W zależności od algorytmów, których będziesz korzystać z uczenia maszynowego, konieczne może być zdecydować, jak będzie obsługiwać danych strukturalnych problemów, takich jak brakujące wartości i danych rozrzedzonych, wiąże się z modułów, które mogą pomóc przy użyciu których. Szukaj w **przekształcania danych** części palety modułów dla modułów, które wykonują te funkcje.

W dowolnym momencie w eksperymencie można wyświetlić lub pobrać dane, które są generowane przez moduł przy kliknij port wyjściowy. W zależności od modułu może być opcje pobierania inny dostępny, lub można wizualizować dane w przeglądarce sieci web w usłudze Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Dane formaty i typy danych obsługiwane
Importujesz wiele typów danych do eksperymentu, w zależności od tego, jakie mechanizm służy do importowania danych i gdzie pochodzi z:

* Zwykły tekst (txt)
* Wartości rozdzielanych przecinkami (CSV) przy użyciu nagłówka (.csv) lub bez (. nh.csv)
* Wartości rozdzielane tabulatorami (TSV) przy użyciu nagłówka (tsv) lub bez (. nh.tsv)
* Plik programu Excel
* Tabela platformy Azure
* Tabela programu hive
* Tabela bazy danych SQL
* Wartości OData
* Dane SVMLight (.svmlight) (zobacz [definicji SVMLight](http://svmlight.joachims.org/) dla informacji o formacie)
* Atrybut danych relacji pliku Format (ARFF) (.arff) (zobacz [definicji ARFF](http://weka.wikispaces.com/ARFF) dla informacji o formacie)
* Plik zip (.zip)
* Plik obiektu lub obszaru roboczego R (. Dane_rekordu)

Po zaimportowaniu danych w formacie, takich jak ARFF, który zawiera metadane usługi Machine Learning Studio używa tych metadanych do definiowania, nagłówka i typu danych każdej kolumny.

Po zaimportowaniu danych, takie jak format TSV lub CSV, który nie zawiera metadanych usługi Machine Learning Studio wnioskuje typ danych dla każdej kolumny przez próbkowanie danych. Jeśli dane nie ma również nagłówki kolumn, Machine Learning Studio udostępnia domyślne nazwy.

Można jawnie określ lub zmień nagłówki i typy danych dla kolumn przy użyciu [edytować metadane][edit-metadata].

Następujące **typy danych** są rozpoznawane przez usługi Machine Learning Studio:

* Ciąg
* Liczba całkowita
* Podwójne
* Wartość logiczna
* DateTime
* Przedział czasu

Wewnętrzny typ danych o nazwie korzysta z usługi Machine Learning Studio ***tabeli danych*** do przekazywania danych między modułami. Można jawnie przekonwertować danych w tabeli danych, używając formatu [przekonwertować zestawu danych] [ convert-to-dataset] modułu.

Wszelkie moduł, który akceptuje formatach niż tabela danych przekonwertuje dane tabeli danych dyskretnie przed przekazaniem go do następnego modułu.

Jeśli to konieczne, możesz przekonwertować format tabeli danych z powrotem do pliku CSV, TSV, ARFF SVMLight format lub przy użyciu innych modułów konwersji.
Szukaj w **konwersje formatu danych** części palety modułów dla modułów, które wykonują te funkcje.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
