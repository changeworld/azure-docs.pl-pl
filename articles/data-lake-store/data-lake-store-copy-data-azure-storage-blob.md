---
title: Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1
description: Kopiowanie danych z obiektów Blob usługi Azure Storage Do usługi Azure Data Lake Storage Gen1 za pomocą narzędzia AdlCopy
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ad408df140be49da2e50ef810285dd850e9da6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638872"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów blob usługi Azure Storage do usługi Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 udostępnia narzędzie wiersza [polecenia, AdlCopy,](https://www.microsoft.com/download/details.aspx?id=50358)aby skopiować dane z następujących źródeł:

* Z obiektów blob usługi Azure Storage do usługi Data Lake Storage Gen1. Nie można użyć AdlCopy do kopiowania danych z usługi Data Lake Storage Gen1 do obiektów blob usługi Azure Storage.
* Między dwoma kontami Data Lake Storage Gen1.

Ponadto można użyć narzędzia AdlCopy w dwóch różnych trybach:

* **Standalone**, gdzie narzędzie używa zasobów Data Lake Storage Gen1 do wykonania zadania.
* **Korzystanie z konta Usługi Data Lake Analytics,** na którym jednostki przypisane do twojego konta Usługi Data Lake Analytics są używane do wykonywania operacji kopiowania. Można użyć tej opcji, gdy chcesz wykonać zadania kopiowania w przewidywalny sposób.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* Kontener **obiektów BLOB usługi Azure Storage** z pewnymi danymi.
* **Konto Data Lake Storage Gen1**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz Wprowadzenie do [usługi Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Konto usługi Data Lake Analytics (opcjonalnie)** — zobacz [Wprowadzenie do usługi Azure Data Lake Analytics,](../data-lake-analytics/data-lake-analytics-get-started-portal.md) aby uzyskać instrukcje dotyczące tworzenia konta usługi Data Lake Analytics.
* **AdlCopy narzędzie**. Zainstaluj [narzędzie AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Składnia narzędzia AdlCopy

Użyj następującej składni, aby pracować z narzędziem AdlCopy

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Parametry w składni są opisane poniżej:

| Opcja | Opis |
| --- | --- |
| Element źródłowy |Określa lokalizację danych źródłowych w obiekcie blob magazynu platformy Azure. Źródłem może być kontener obiektów blob, obiekt blob lub inne konto Usługi Data Lake Storage Gen1. |
| Dest |Określa miejsce docelowe gen1 magazynu danych lake do skopiowania. |
| Klawisz SourceKey |Określa klucz dostępu do magazynu dla źródła obiektów blob magazynu platformy Azure. Jest to wymagane tylko wtedy, gdy źródłem jest kontener obiektu blob lub obiekt blob. |
| Konto |**Opcjonalnie**. Użyj tego, jeśli chcesz użyć konta usługi Azure Data Lake Analytics do uruchomienia zadania kopiowania. Jeśli używasz /Konto opcji w składni, ale nie określisz konta Usługi Data Lake Analytics, AdlCopy używa konta domyślnego do uruchomienia zadania. Ponadto jeśli używasz tej opcji, należy dodać źródło (Azure Storage Blob) i miejsce docelowe (Usługi Azure Data Lake Storage Gen1) jako źródła danych dla konta usługi Data Lake Analytics. |
| Jednostki |Określa liczbę jednostek usługi Data Lake Analytics, które będą używane dla zadania kopiowania. Ta opcja jest obowiązkowa, jeśli użyjesz opcji **/Account,** aby określić konto Usługi Data Lake Analytics. |
| Wzorce |Określa wzorzec wyrażenia regularnego, który wskazuje, które obiekty BLOB lub pliki mają być kopiowane. AdlCopy używa dopasowania z uwzględnieniem wielkości liter. Domyślny szyk, gdy nie określono wzorca, polega na skopiowaniu wszystkich elementów. Określanie wielu wzorców plików nie jest obsługiwane. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Kopiowanie danych z obiektu blob usługi Azure Storage za pomocą programu AdlCopy (jako autonomicznego)

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zazwyczaj `%HOMEPATH%\Documents\adlcopy`jest zainstalowany program AdlCopy.
1. Uruchom następujące polecenie, aby skopiować określony obiekt blob z kontenera źródłowego do folderu Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >Powyższa składnia określa plik, który ma zostać skopiowany do folderu na koncie Data Lake Storage Gen1. Narzędzie AdlCopy tworzy folder, jeśli określona nazwa folderu nie istnieje.

    Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto Data Lake Storage Gen1. Zostanie wyświetlenie wyjścia podobnego do następującego:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Można również skopiować wszystkie obiekty blob z jednego kontenera do konta Usługi Data Lake Storage Gen1 przy użyciu następującego polecenia:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Przykład:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Jeśli kopiujesz z konta usługi Azure Blob Storage, może być ograniczona podczas kopiowania po stronie magazynu obiektów blob. Spowoduje to obniżenie wydajności zadania kopiowania. Aby dowiedzieć się więcej o ograniczeniach usługi Azure Blob Storage, zobacz Limity usługi Azure Storage w [limitach subskrypcji i usług platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Kopiowanie danych z innego konta Data Lake Storage Gen1 za pomocą programu AdlCopy (jako autonomicznego)

Za pomocą programu AdlCopy można również kopiować dane między dwoma kontami Usługi Data Lake Storage Gen1.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zazwyczaj `%HOMEPATH%\Documents\adlcopy`jest zainstalowany program AdlCopy.
1. Uruchom następujące polecenie, aby skopiować określony plik z jednego konta Data Lake Storage Gen1 do innego.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Przykład:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Powyższa składnia określa plik, który ma zostać skopiowany do folderu na docelowym koncie Data Lake Storage Gen1. Narzędzie AdlCopy tworzy folder, jeśli określona nazwa folderu nie istnieje.
   >
   >

    Zostanie wyświetlony monit o wprowadzenie poświadczeń dla subskrypcji platformy Azure, w ramach której masz konto Data Lake Storage Gen1. Zostanie wyświetlenie wyjścia podobnego do następującego:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. Następujące polecenie kopiuje wszystkie pliki z określonego folderu w źródłowym koncie Data Lake Storage Gen1 do folderu na docelowym koncie Data Lake Storage Gen1.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas korzystania z AdlCopy jako narzędzie autonomiczne, kopia jest uruchamiana na udostępnionych zasobów zarządzanych przez platformę Azure. Wydajność, którą można uzyskać w tym środowisku, zależy od obciążenia systemu i dostępnych zasobów. Ten tryb jest najlepiej używany do małych transferów na zasadzie ad hoc. Nie trzeba dostroić żadnych parametrów podczas korzystania z AdlCopy jako samodzielnego narzędzia.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Kopiowanie danych za pomocą programu AdlCopy (z kontem Data Lake Analytics)

Można również użyć konta usługi Data Lake Analytics do uruchomienia zadania AdlCopy do kopiowania danych z obiektów blob magazynu platformy Azure do usługi Data Lake Storage Gen1. Zazwyczaj należy użyć tej opcji, gdy dane do przeniesienia znajduje się w zakresie gigabajtów i terabajtów i chcesz lepszą i przewidywalną przepływność wydajności.

Aby użyć konta usługi Data Lake Analytics za pomocą usługi AdlCopy do kopiowania z obiektu blob usługi Azure Storage, źródło (obiekt blob usługi Azure Storage) musi zostać dodane jako źródło danych dla konta usługi Data Lake Analytics. Aby uzyskać instrukcje dotyczące dodawania dodatkowych źródeł danych do konta Usługi Data Lake Analytics, zobacz [Zarządzanie źródłami danych konta Usługi Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Jeśli kopiujesz z konta usługi Azure Data Lake Storage Gen1 jako źródło przy użyciu konta usługi Data Lake Analytics, nie musisz kojarzyć konta Data Lake Storage Gen1 z kontem usługi Data Lake Analytics. Wymaganie skojarzenia magazynu źródłowego z kontem usługi Data Lake Analytics jest tylko wtedy, gdy źródłem jest konto usługi Azure Storage.
>
>

Uruchom następujące polecenie, aby skopiować z obiektu blob usługi Azure Storage do konta Data Lake Storage Gen1 przy użyciu konta usługi Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Przykład:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Podobnie uruchom następujące polecenie, aby skopiować wszystkie pliki z określonego folderu w źródłowym koncie Data Lake Storage Gen1 do folderu na docelowym koncie Data Lake Storage Gen1 przy użyciu konta Usługi Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Podczas kopiowania danych w zakresie terabajtów, przy użyciu AdlCopy z własnym kontem usługi Azure Data Lake Analytics zapewnia lepszą i bardziej przewidywalną wydajność. Parametr, który powinien być dostrojony jest liczba jednostek analizy usługi Azure Data Lake do użycia dla zadania kopiowania. Zwiększenie liczby jednostek zwiększy wydajność zadania kopiowania. Każdy plik do skopiowania może używać maksymalnie jednej jednostki. Określenie większej liczby jednostek niż liczba kopiowanych plików nie zwiększy wydajności.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Kopiowanie danych za pomocą programu AdlCopy przy użyciu dopasowywania wzorców

W tej sekcji dowiesz się, jak używać AdlCopy do kopiowania danych ze źródła (w naszym przykładzie poniżej używamy obiektu blob usługi Azure Storage) do docelowego konta usługi Data Lake Storage Gen1 przy użyciu dopasowania wzorca. Na przykład można użyć poniższych kroków, aby skopiować wszystkie pliki z rozszerzeniem csv ze źródłowego obiektu blob do miejsca docelowego.

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym zazwyczaj `%HOMEPATH%\Documents\adlcopy`jest zainstalowany program AdlCopy.
1. Uruchom następujące polecenie, aby skopiować wszystkie pliki z rozszerzeniem *.csv z określonego obiektu blob z kontenera źródłowego do folderu Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Przykład:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Rozliczenia

* Jeśli używasz narzędzia AdlCopy jako autonomiczne, opłaty będą naliczane za koszty ruchu danych, jeśli źródłowe konto usługi Azure Storage nie znajduje się w tym samym regionie co konto Data Lake Storage Gen1.
* Jeśli korzystasz z narzędzia AdlCopy na swoim koncie Data Lake Analytics, będą obowiązywać standardowe [stawki rozliczeniowe Data Lake Analytics.](https://azure.microsoft.com/pricing/details/data-lake-analytics/)

## <a name="considerations-for-using-adlcopy"></a>Uwagi dotyczące korzystania z AdlCopy

* AdlCopy (dla wersji 1.0.5), obsługuje kopiowanie danych ze źródeł, które łącznie mają więcej niż tysiące plików i folderów. Jeśli jednak wystąpią problemy z kopiowaniem dużego zestawu danych, można rozpowszechniać pliki/foldery w różnych podfolderach i używać ścieżki do tych podfolderów jako źródła.

## <a name="performance-considerations-for-using-adlcopy"></a>Zagadnienia dotyczące wydajności dotyczące korzystania z AdlCopy

AdlCopy obsługuje kopiowanie danych zawierających tysiące plików i folderów. Jeśli jednak wystąpią problemy z kopiowaniem dużego zestawu danych, można je rozpowszechniać w mniejszych podfolderach. AdlCopy został zbudowany dla kopii ad hoc. Jeśli próbujesz kopiować dane na zasadzie cyklicznej, należy rozważyć użycie [usługi Azure Data Factory,](../data-factory/connector-azure-data-lake-store.md) która zapewnia pełne zarządzanie wokół operacji kopiowania.

## <a name="release-notes"></a>Informacje o wersji

* 1.0.13 — jeśli kopiujesz dane do tego samego konta usługi Azure Data Lake Storage Gen1 za pomocą wielu poleceń adlcopy, nie trzeba ponownie wdawać poświadczeń dla każdego uruchomienia. Adlcopy będzie teraz buforować te informacje w wielu przebiegach.

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
