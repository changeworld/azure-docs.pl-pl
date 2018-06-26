---
title: Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store | Dokumentacja firmy Microsoft
description: Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store za pomocą narzędzia AdlCopy
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 19657030c69d9d62fbbe0a8058e50238b2afa67f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750120"
---
# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Copy data from Azure Storage Blobs to Data Lake Store (Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Store)
> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Store zapewnia narzędzia wiersza polecenia, [AdlCopy](http://aka.ms/downloadadlcopy), aby skopiować dane z następujących źródeł:

* Z obiektów blob magazynu Azure do usługi Data Lake Store. Nie można użyć AdlCopy, aby skopiować dane z usługi Data Lake Store do obiektów blob magazynu Azure.
* Dwóch kont usługi Azure Data Lake Store.

Można także użyć narzędzia AdlCopy, w dwóch różnych trybach:

* **Autonomiczny**, których narzędzie używa usługi Data Lake Store zasobów do wykonania zadania.
* **Przy użyciu konta usługi Data Lake Analytics**, gdzie jednostki przypisane do konta usługi Data Lake Analytics są używane do wykonywania operacji kopiowania. Możesz użyć tej opcji, jeśli chcesz wykonać te zadania kopiowania w sposób przewidywalny.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Obiektów blob magazynu Azure** kontener z niektórych danych.
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **(Opcjonalnie) konta usługi Azure Data Lake Analytics** — zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) instrukcje dotyczące sposobu tworzenia konta usługi Data Lake Store.
* **Narzędzie AdlCopy**. Zainstaluj narzędzie AdlCopy z [ http://aka.ms/downloadadlcopy ](http://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Składnia narzędzia AdlCopy
Należy użyć następującej składni do pracy z narzędziem AdlCopy

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Poniżej opisano parametrów w składni:

| Opcja | Opis |
| --- | --- |
| Element źródłowy |Określa lokalizację źródła danych w obiekcie blob magazynu Azure. Źródłem może być kontenera obiektów blob, obiekt blob lub innego konta usługi Data Lake Store. |
| Docelowy |Określa miejsce docelowe usługi Data Lake Store kopiowania. |
| SourceKey |Określa klucz dostępu do magazynu dla źródła obiektu blob magazynu Azure. Jest to wymagane tylko wtedy, gdy źródłem jest kontenera obiektów blob lub obiektu blob. |
| Konto |**Opcjonalnie**. Użyj, jeśli chcesz użyć konta usługi Azure Data Lake Analytics, aby uruchomić zadanie kopiowania. Jeśli opcja /Account w składni, ale nie określono konta usługi Data Lake Analytics, AdlCopy używa domyślnego konta do wykonywania zadania. Ponadto jeśli używasz tej opcji, należy dodać źródłowego (obiektu Blob magazynu Azure) i docelowego (Azure Data Lake Store) jako źródła danych dla Twojego konta usługi Data Lake Analytics. |
| Jednostki |Określa liczbę jednostek usługi Data Lake Analytics, które będą używane dla zadania kopiowania. Ta opcja jest wymagane, jeśli używasz **/konta** opcję, aby określić konto usługi Data Lake Analytics. |
| Wzorce |Określa wzorzec wyrażenia regularnego, która wskazuje, które obiekty BLOB lub plików do skopiowania. AdlCopy używa dopasowania z uwzględnieniem wielkości liter. Domyślny wzorzec używany, jeśli nie określono żadnych wzorzec jest skopiować wszystkie elementy. Nie można określać wielu wzorców pliku. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Użyj AdlCopy (jako autonomiczna), aby skopiować dane z obiektu blob magazynu Azure
1. Otwórz wiersz polecenia i przejdź do katalogu, w którym instalowany jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować konkretnego obiektu blob w kontenerze źródła do usługi Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Na przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[AZURE.NOTE] Powyższej składni Określa plik, który ma zostać skopiowany do folderu na koncie usługi Data Lake Store. Narzędzie AdlCopy tworzy folder, jeśli nazwa określony folder nie istnieje.

    Pojawi się monit o podanie poświadczeń dla subskrypcji platformy Azure, z którym masz konto usługi Data Lake Store. Zostaną wyświetlone informacje podobne do następujących:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Można także skopiować wszystkie obiekty BLOB z jednego kontenera na koncie usługi Data Lake Store za pomocą następującego polecenia:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Na przykład:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Jeśli kopiujesz z konta magazynu obiektów Blob Azure, może być ograniczony podczas kopiowania po stronie magazynu obiektów blob. To spowoduje zmniejszenie wydajności zadania kopiowania. Aby dowiedzieć się więcej na temat limitów magazynu obiektów Blob Azure, zobacz limity magazynu Azure w [subskrypcji platformy Azure i ograniczenia usługi](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>Użyj AdlCopy (jako autonomiczna), aby skopiować dane z innego konta usługi Data Lake Store
Umożliwia także AdlCopy skopiować dane z dwóch kont usługi Data Lake Store.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym instalowany jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować określonego pliku z jednego konta usługi Data Lake Store do innego.

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    Na przykład:

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Powyższej składni Określa plik, który ma zostać skopiowany do folderu na docelowy konta usługi Data Lake Store. Narzędzie AdlCopy tworzy folder, jeśli nazwa określony folder nie istnieje.
   >
   >

    Pojawi się monit o podanie poświadczeń dla subskrypcji platformy Azure, z którym masz konto usługi Data Lake Store. Zostaną wyświetlone informacje podobne do następujących:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Polecenie kopiuje wszystkie pliki z folderu określonego w źródle konta usługi Data Lake Store do folderu na docelowy konta usługi Data Lake Store.

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Używając AdlCopy jako autonomicznego narzędzia Kopia jest uruchamiane na udostępnionym, Azure zarządzanych zasobów. Wydajności, które mogą wystąpić w tym środowisku zależy od obciążenia systemu i dostępnych zasobów. W tym trybie najlepiej nadaje się do małych transferów na zasadzie ad hoc. Bez parametrów muszą być dopasowane, używając AdlCopy jako autonomicznego narzędzia.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Użyj AdlCopy (z konta usługi Data Lake Analytics), aby skopiować dane
Konto usługi Data Lake Analytics można również użyć, aby uruchamiać zadanie AdlCopy, można skopiować danych z obiektów blob magazynu Azure do usługi Data Lake Store. Tej opcji spowoduje zwykle użyć, gdy przeniesienie danych znajduje się w zakresie gigabajtów lub terabajty i chcesz przepływności lepsze, przewidywalną wydajność.

Aby korzystać z AdlCopy konta usługi Data Lake Analytics, aby skopiować z obiektu Blob magazynu Azure, źródła (obiektu Blob magazynu Azure) muszą zostać dodane jako źródła danych dla Twojego konta usługi Data Lake Analytics. Aby uzyskać instrukcje dotyczące dodawania dodatkowych źródeł danych na koncie usługi Data Lake Analytics, zobacz [źródłami danych konta zarządzania usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Jeśli jako źródło przy użyciu konta usługi Data Lake Analytics są kopiowane z konta usługi Azure Data Lake Store, nie trzeba skojarzyć konto usługi Data Lake Store z kontem usługi Data Lake Analytics. Wymaganie, aby skojarzyć magazynu źródłowego przy użyciu konta usługi Data Lake Analytics jest tylko wtedy, gdy źródłem jest konto magazynu Azure.
>
>

Uruchom następujące polecenie, aby skopiować z obiektu blob magazynu Azure z kontem usługi Data Lake Store przy użyciu konta usługi Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Na przykład:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobnie uruchom następujące polecenie, aby skopiować wszystkie pliki z określonego folderu w źródle konta usługi Data Lake Store do folderu na koncie usługi Data Lake Store docelowym przy użyciu konta usługi Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas kopiowania danych w zakresie terabajtów, AdlCopy z kontem usługi Azure Data Lake Analytics zapewnia lepszą i bardziej przewidywalna wydajność. Parametr, który powinien być dopasowane jest to liczba jednostek Azure danych Lake Analytics do użycia dla zadania kopiowania. Zwiększenie liczby jednostek spowoduje zwiększenie wydajności zadania kopiowania. Każdy plik do skopiowania można użyć maksymalną jedną jednostkę. Określanie jednostki więcej niż liczba plików kopiowanych nie spowoduje zwiększenia wydajności.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Użyj AdlCopy do skopiowania danych przy użyciu dopasowanie wzorca
W tej sekcji zostanie przedstawiony sposób AdlCopy umożliwia kopiowanie danych ze źródła (w naszym przykładzie poniżej używamy obiektu Blob magazynu Azure) do docelowego konta usługi Data Lake Store za pomocą dopasowywania do wzorca. Na przykład można czynności skopiować wszystkie pliki z rozszerzeniem CSV z obiektu blob źródła do miejsca docelowego.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym instalowany jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować wszystkie pliki z rozszerzeniem *.csv z konkretnego obiektu blob w kontenerze źródła do usługi Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Na przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Rozliczenia
* Jeśli używasz narzędzia AdlCopy jako autonomiczny opłaty będą naliczane koszty wyjście do przenoszenia danych, jeśli źródło konta magazynu Azure nie znajduje się w tym samym regionie co usługi Data Lake Store.
* Jeśli używasz narzędzia AdlCopy z usługi Data Lake Analytics konta, standardowe [rozliczeń stawki usługi Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) będą stosowane.

## <a name="considerations-for-using-adlcopy"></a>Zagadnienia dotyczące korzystania z AdlCopy
* AdlCopy (w przypadku wersji 1.0.5) obsługuje kopiowanie danych ze źródeł, które zbiorczo ma więcej niż tysięcy plików i folderów. Jednak jeśli wystąpią problemy dotyczące kopiowania dużego zestawu danych, należy rozesłać pliki/foldery w różnych podfolderach i użyć ścieżki do tych folderów podrzędnych jako źródło.

## <a name="performance-considerations-for-using-adlcopy"></a>Zagadnienia dotyczące wydajności dla przy użyciu AdlCopy

AdlCopy obsługuje kopiowanie danych zawierających tysięcy plików i folderów. Jednak jeśli wystąpią problemy dotyczące kopiowania dużego zestawu danych, można rozmieścić pliki/foldery w mniejszych podfoldery. AdlCopy został utworzony dla kopii ad hoc. Jeśli chcesz skopiować dane cyklicznie, należy rozważyć użycie [fabryki danych Azure](../data-factory/connector-azure-data-lake-store.md) zapewnia pełnego zarządzania wokół operacji kopiowania.

## <a name="release-notes"></a>Informacje o wersji
* 1.0.13 — Jeśli kopiujesz danych do tego samego konta usługi Azure Data Lake Store w wielu poleceń adlcopy, nie trzeba ponownie poświadczenia przy każdym uruchomieniu już. Adlcopy teraz będą buforowane tych informacji przez wiele przebiegów.

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
