---
title: 'Importuj dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Informacje dotyczące ładowania danych do potoku uczenia maszynowego z istniejących usług danych w chmurze przy użyciu modułu Importuj dane w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693149"
---
# <a name="import-data-module"></a>Importuj moduł danych

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do ładowania danych do potoku uczenia maszynowego z istniejących usług danych w chmurze.  

Najpierw wybierz typ magazynu opartego na chmurze, z którego czytasz i Zakończ dodatkowe ustawienia. Po zdefiniowaniu danych, które chcesz połączyć ze źródłem, [Importowanie danych](./import-data.md) powoduje, że dane są uwzględniane w poszczególnych kolumnach, na podstawie wartości, które zawiera, i ładowania danych do obszaru roboczego Azure Machine Learning. Dane wyjściowe [importowania](./import-data.md) są zestawem danych, który może być używany z dowolnym potokiem.

  
Jeśli dane źródłowe zmienią się, można odświeżyć zestaw danych i dodać nowe dane przez ponowne uruchomienie [importowania danych](./import-data.md). Jeśli jednak nie chcesz powtarzać odczytu ze źródła przy każdym uruchomieniu potoku, wybierz opcję **Użyj wyników z pamięci podręcznej** na wartość true. Gdy ta opcja jest zaznaczona, moduł sprawdza, czy potok został wcześniej uruchomiony przy użyciu tych samych danych źródłowych i tych samych opcji. W przypadku znalezienia poprzedniego przebiegu dane w pamięci podręcznej są używane zamiast ponownego ładowania danych ze źródła.
 

## <a name="data-sources"></a>Źródła danych

Moduł Importuj dane obsługuje następujące źródła danych. Kliknij linki, aby uzyskać szczegółowe instrukcje i przykłady użycia każdego źródła danych. 
 
Jeśli nie masz pewności, jak lub gdzie należy przechowywać dane, zapoznaj się z tym przewodnikiem, aby zapoznać się z typowymi scenariuszami dotyczącymi danych w procesie analizy danych: [scenariusze zaawansowanej analizy w Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Źródło danych| Używanie z|
|-----------|-----------|  
|[Adres URL sieci Web za pośrednictwem protokołu HTTP](./import-from-web-url-via-http.md)|Pobierz dane hostowane w adresie URL sieci Web, który używa protokołu HTTP i który został udostępniony w formatach CSV, TSV, ARFF lub SvmLight|  
|[Importuj z usługi Azure Blob Storage](./import-from-azure-blob-storage.md) |Pobieranie danych przechowywanych w usłudze Azure Blob Service|  
|[Importuj z Azure SQL Database](./import-from-azure-sql-database.md) |Pobierz dane z Azure SQL Database|

## <a name="how-to-configure-import-data"></a>Jak skonfigurować Importowanie danych
 
1. Dodaj moduł **Import danych** do potoku. Ten moduł można znaleźć w kategorii dane **wejściowe i wyjściowe** w interfejsie.

1. Kliknij pozycję **Źródło danych**i wybierz typ magazynu opartego na chmurze, z którego się czytasz. 

    Dodatkowe ustawienia zależą od wybranego typu magazynu i od tego, czy magazyn jest zabezpieczony, czy nie. Może być konieczne podanie nazwy konta, typu pliku lub poświadczeń. Niektóre źródła nie wymagają uwierzytelniania; dla innych osób może być konieczne poznanie nazwy konta, klucza lub nazwy kontenera.

1. Wybierz opcję **Użyj zbuforowanych wyników** , jeśli chcesz buforować zestaw danych do ponownego użycia przy kolejnych uruchomieniach.

    Przy założeniu, że nie zostały wprowadzone żadne inne zmiany parametrów modułu, potok ładuje dane tylko przy pierwszym uruchomieniu modułu, a następnie używa buforowanej wersji zestawu danych.

    Usuń zaznaczenie tej opcji, jeśli konieczne jest ponowne załadowanie danych przy każdym uruchomieniu potoku.

1. Uruchamianie potoku.

    W przypadku zaimportowania danych do interfejsu program wnioskuje typ danych każdej kolumny w oparciu o zawarte w niej wartości, wartość numeryczną lub kategorii.

    - Jeśli nagłówek jest obecny, nagłówek jest używany do nazwy kolumn wyjściowego zestawu danych.

    - Jeśli w danych nie ma nagłówków istniejących kolumn, nowe nazwy kolumn są generowane przy użyciu formatu Kol1, Col2,... , coln*.

## <a name="results"></a>Wyniki

Po zakończeniu importu kliknij wyjściowy zestaw danych i wybierz polecenie **Wizualizuj** , aby sprawdzić, czy dane zostały zaimportowane pomyślnie.

Jeśli chcesz zapisać dane do ponownego użycia zamiast zaimportować nowy zestaw danych przy każdym uruchomieniu potoku, kliknij prawym przyciskiem myszy dane wyjściowe i wybierz polecenie **Zapisz jako zestaw danych**. Wybierz nazwę dla zestawu danych. Zapisany zestaw danych zachowuje dane podczas zapisywania, a dane nie są aktualizowane po ponownym uruchomieniu potoku, nawet jeśli zestaw danych w potoku ulegnie zmianie. Może to być przydatne do tworzenia migawek danych.

Po zaimportowaniu danych może zajść potrzeba przeprowadzenia pewnych dodatkowych przygotowań do modelowania i analizy:


- Użyj opcji [Edytuj metadane](./edit-metadata.md) , aby zmienić nazwy kolumn, obsłużyć kolumnę jako inny typ danych lub wskazać, że niektóre kolumny są etykietami lub funkcjami.

- Użyj [opcji wybierz kolumny w zestawie danych](./select-columns-in-dataset.md) , aby wybrać podzbiór kolumn do przekształcenia lub użycia podczas modelowania. Kolumny przekształcone lub usunięte można łatwo ponownie przyłączyć do oryginalnego zestawu danych przy użyciu modułu [Dodaj kolumny](./add-columns.md) .  

- Użyj [partycji i przykładu](./partition-and-sample.md) , aby podzielić zestaw danych, wykonać próbkowanie lub pobrać pierwsze n wierszy.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 