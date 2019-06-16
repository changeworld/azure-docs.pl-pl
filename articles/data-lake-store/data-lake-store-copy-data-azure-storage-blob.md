---
title: Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1 za pomocą narzędzia AdlCopy
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be66fd51b37c0e62b2b757a88ee1db9319b2093a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878838"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 udostępnia narzędzia wiersza polecenia, [AdlCopy](https://aka.ms/downloadadlcopy), aby skopiować dane z następujących źródeł:

* Z usługi Azure Storage BLOB do Gen1 Lake magazynu danych. Nie można skopiować dane z Data Lake Storage Gen1 do obiektów blob usługi Azure Storage za pomocą narzędzia AdlCopy.
* Dwóch kont usługi Azure Data Lake Storage Gen1.

Ponadto możesz użyć narzędzia AdlCopy w dwóch różnych trybach:

* **Autonomiczny**, gdzie korzysta z narzędzia Data Lake Storage Gen1 zasobów do wykonania zadania.
* **Przy użyciu konta usługi Data Lake Analytics**, gdzie jednostek przypisane do Twojego konta usługi Data Lake Analytics są używane do wykonywania operacji kopiowania. Można użyć tej opcji, gdy potrzebujesz wykonać zadania kopiowania w przewidywalny sposób.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Usługa Azure Storage Blobs** kontenera, z pewnymi danymi.
* **Konto usługi Azure Data Lake Storage Gen1**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **(Opcjonalnie) konta usługi Azure Data Lake Analytics** — zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) instrukcje dotyczące sposobu tworzenia konta usługi Data Lake Analytics.
* **Narzędzia AdlCopy**. Zainstaluj narzędzia AdlCopy z [ https://aka.ms/downloadadlcopy ](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Składnia narzędzia AdlCopy
Użyj następującej składni, aby pracować z narzędzia AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Poniżej opisano parametrów w składni:

| Opcja | Opis |
| --- | --- |
| source |Określa lokalizację źródła danych w usłudze Azure storage blob. Źródło może być kontener obiektów blob, obiekt blob lub innego konta Data Lake Storage Gen1. |
| docelowy |Określa Data Lake Storage Gen1 miejsce docelowe kopiowania. |
| SourceKey |Określa klucz dostępu do magazynu dla źródłowego obiektu blob magazynu platformy Azure. Jest to wymagane, tylko wtedy, gdy źródłem jest kontener obiektów blob lub obiektu blob. |
| Konto |**Opcjonalnie**. Użyj, jeżeli chcesz użyć konta usługi Azure Data Lake Analytics, aby uruchomić zadanie kopiowania. Jeśli opcja/Account w składni, ale nie określaj na koncie usługi Data Lake Analytics, narzędzia AdlCopy używa domyślnego konta do uruchomienia zadania. Ponadto użycie tej opcji, należy dodać źródłowy (Azure Storage Blob) i docelowy (Azure Data Lake Storage Gen1) jako źródła danych dla konta usługi Data Lake Analytics. |
| Jednostki |Określa liczbę jednostek usługi Data Lake Analytics, które będą używane dla zadania kopiowania. Ta opcja jest obowiązkowa, jeśli używasz **/konta** opcję, aby określić konto usługi Data Lake Analytics. |
| Wzorce |Określa wzorzec wyrażenia regularnego, która wskazuje, które obiekty BLOB i pliki do skopiowania. AdlCopy używa dopasowywania uwzględniana wielkość liter. Domyślny wzorzec używany, gdy określona jest wzorzec nie jest kopiowanie wszystkich elementów. Określanie wielu wzorców pliku nie jest obsługiwane. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Aby skopiować dane z obiektu blob usługi Azure Storage za pomocą narzędzia AdlCopy (jako autonomiczne)
1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowane jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować konkretny obiekt blob z kontenera źródłowego do folderu Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Na przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >Powyższej przykładowej składni Określa plik, które mają być kopiowane do folderu, w ramach konta Data Lake Storage Gen1. Narzędzia AdlCopy tworzy folder, jeśli nazwa określony folder nie istnieje.

    Zostanie wyświetlony monit wprowadź poświadczenia dla subskrypcji platformy Azure, w którym masz konto usługi Data Lake Storage Gen1. Pojawią się dane wyjściowe podobne do następujących:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Można również kopiować wszystkie obiekty BLOB z jednego kontenera, do konta Data Lake Storage Gen1, używając następującego polecenia:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Na przykład:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas kopiowania z konta usługi Azure Blob Storage może być ograniczona podczas kopiowania po stronie magazynu obiektów blob. To spowoduje zmniejszenie wydajności zadania kopiowania. Aby dowiedzieć się więcej o limitach usługi Azure Blob Storage, zobacz limity usługi Azure Storage w [limity usług i subskrypcji platformy Azure](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Aby skopiować dane z innego konta Data Lake Storage Gen1 za pomocą narzędzia AdlCopy (jako autonomiczne)
Za pomocą narzędzia AdlCopy do skopiowania danych między dwa konta Data Lake Storage Gen1.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowane jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować określony plik z jednego konta Data Lake Storage Gen1 do innego.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Na przykład:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Powyższej przykładowej składni Określa plik, które mają być kopiowane do folderu w miejscu docelowym konta Data Lake Storage Gen1. Narzędzia AdlCopy tworzy folder, jeśli nazwa określony folder nie istnieje.
   >
   >

    Zostanie wyświetlony monit wprowadź poświadczenia dla subskrypcji platformy Azure, w którym masz konto usługi Data Lake Storage Gen1. Pojawią się dane wyjściowe podobne do następujących:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Następujące polecenie kopiuje wszystkie pliki z określonego folderu w źródle konta Data Lake Storage Gen1 do folderu w miejscu docelowym konta Data Lake Storage Gen1.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Korzystając z narzędzia AdlCopy jako samodzielnego narzędzia, kopia jest uruchamiany na udostępnionym, Azure zarządzanych zasobów. Wydajności, które mogą wystąpić w tym środowisku, zależy od obciążenia systemu i dostępnych zasobów. W tym trybie najlepiej nadaje się do małych transferów na zasadzie ad hoc. Bez parametrów muszą być dostosowane, korzystając z narzędzia AdlCopy jako samodzielnego narzędzia.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Kopiowanie danych za pomocą narzędzia AdlCopy (przy użyciu konta usługi Data Lake Analytics)
Twoje konto usługi Data Lake Analytics umożliwia również Uruchom zadanie AdlCopy, aby skopiować dane z usługi Azure storage BLOB do Data Lake Storage Gen1. Zazwyczaj można Użyj tej opcji, gdy przeniesienie danych znajduje się w zakresie gigabajtów i terabajty i chcesz, aby lepiej wysoką i przewidywalną wydajność, przepływność.

Do używania konta usługi Data Lake Analytics za pomocą narzędzia AdlCopy kopiowanie z obiektu Blob magazynu platformy Azure, należy dodać źródła (Azure Storage Blob) jako źródło danych dla konta usługi Data Lake Analytics. Aby uzyskać instrukcje dotyczące dodawania dodatkowych źródeł danych na koncie usługi Data Lake Analytics, zobacz [źródłami danych konta Zarządzanie Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Jeśli kopiujesz z konta usługi Azure Data Lake Storage Gen1 jako źródła przy użyciu konta usługi Data Lake Analytics, nie musisz skojarzyć konto Data Lake Storage Gen1 z kontem usługi Data Lake Analytics. Wymaganie, aby skojarzyć magazynu źródłowego przy użyciu konta usługi Data Lake Analytics jest tylko wtedy, gdy źródłem jest konto usługi Azure Storage.
>
>

Uruchom następujące polecenie, aby skopiować z obiektu blob usługi Azure Storage do konta Data Lake Storage Gen1 przy użyciu konta usługi Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Na przykład:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobnie uruchom następujące polecenie, aby skopiować wszystkie pliki z określonego folderu w źródle konta Data Lake Storage Gen1 do folderu, w ramach konta Data Lake Storage Gen1 docelowego przy użyciu konta usługi Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas kopiowania danych w zakresie terabajtów, korzystanie z narzędzia AdlCopy przy użyciu konta usługi Azure Data Lake Analytics zapewnia lepszą i bardziej przewidywalna wydajność. Parametr, który powinien być dostosowane jest liczba jednostek analizy jezioro danych Azure na potrzeby zadania kopiowania. Zwiększenie liczby jednostek zwiększy wydajność zadania kopiowania. Każdy plik do skopiowania można użyć maksymalna jedną jednostkę. Określanie jednostki więcej niż liczba plików kopiowanych nie spowoduje zwiększenia wydajności.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Kopiowanie danych za pomocą dopasowywania do wzorca za pomocą narzędzia AdlCopy
W tej sekcji opisano sposób kopiowania danych ze źródła za pomocą narzędzia AdlCopy (w naszym przykładzie poniżej używamy rozszerzenia Azure Storage Blob) do konta docelowego Data Lake Storage Gen1 za pomocą dopasowywania do wzorca. Na przykład można użyć czynności do skopiowania wszystkich plików z rozszerzeniem CSV źródłowy obiekt blob do miejsca docelowego.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zainstalowane jest AdlCopy, zwykle `%HOMEPATH%\Documents\adlcopy`.
2. Uruchom następujące polecenie, aby skopiować wszystkie pliki z rozszerzeniem *.csv z określonego obiektu blob z kontenera źródłowego do folderu Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Na przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Rozliczenia
* Jeśli używasz narzędzia AdlCopy jako autonomiczny będzie rozliczenie dla ruchu wychodzącego kosztów związanych z przenoszeniem danych, jeśli źródło konta usługi Azure Storage nie znajduje się w tym samym regionie co konto usługi Data Lake Storage Gen1.
* Jeśli za pomocą narzędzia AdlCopy za pomocą usługi Data Lake Analytics konta, standardowe [stawek rozliczeń usługi Data Lake Analytics](https://azure.microsoft.com/pricing/details/data-lake-analytics/) zostaną zastosowane.

## <a name="considerations-for-using-adlcopy"></a>Zagadnienia dotyczące korzystania z narzędzia AdlCopy
* AdlCopy (dla wersji 1.0.5) obsługuje kopiowanie danych ze źródeł, które wspólnie mieć więcej niż tysięcy plików i folderów. Jednak jeśli wystąpią problemy dotyczące kopiowania duży zestaw danych, możesz dystrybuować plików/folderów w różnych podfolderach i do tych podfolderów jako źródło w zamian użyć ścieżki.

## <a name="performance-considerations-for-using-adlcopy"></a>Zagadnienia związane z wydajnością dotyczące korzystania z narzędzia AdlCopy

AdlCopy obsługuje kopiowanie danych zawierający tysiące plików i folderów. Jednak jeśli wystąpią problemy dotyczące kopiowania duży zestaw danych, możesz dystrybuować plików/folderów na mniejsze podfoldery. AdlCopy została stworzona z myślą kopie ad-hoc. Jeśli chcesz skopiować dane cyklicznie, należy rozważyć użycie [usługi Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) zapewniający pełnego zarządzania wokół operacji kopiowania.

## <a name="release-notes"></a>Informacje o wersji
* 1.0.13 — Jeśli kopiujesz dane do tego samego konta usługi Azure Data Lake Storage Gen1 między kilka poleceń narzędzia adlcopy, nie trzeba ponownie wprowadzić swoje poświadczenia dla każdego uruchomienia już. Adlcopy teraz będzie buforować tych informacji wielu uruchomień.

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
