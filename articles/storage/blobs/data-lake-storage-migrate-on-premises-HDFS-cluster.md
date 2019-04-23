---
title: Użyj usługi Azure Data Box migracji danych z środowiska lokalnego systemu plików HDFS magazynu do usługi Azure Storage
description: Migracja danych z magazynu lokalnego systemu plików HDFS do usługi Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: d0908e9edce8efb7a378ee04b6076b61cae2d2bf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998298"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Użyj usługi Azure Data Box do migracji danych z lokalnego systemu plików HDFS magazynu do usługi Azure Storage

Za pomocą urządzenia Data Box, można migrować dane z lokalnego systemu plików HDFS magazynu klastra usługi Hadoop w usłudze Azure Storage (magazynie obiektów blob lub Data Lake Storage Gen2).

Ten artykuł pomoże Ci wykonać następujące zadania:

:heavy_check_mark: Skopiuj dane do urządzenia Data Box.

:heavy_check_mark: Odesłanie urządzenia Data Box do firmy Microsoft.

:heavy_check_mark: Przenoszenie danych na koncie magazynu Data Lake Storage Gen2.

## <a name="prerequisites"></a>Wymagania wstępne

Należy te rzeczy, aby ukończyć migrację.

* Konta usługi Azure Storage, do którego **nie** obejmuje hierarchicznej przestrzeni nazw, włączone na nim.

* Jeśli chcesz użyć konta usługi Azure Data Lake Storage Gen2 (konto magazynu, który **jest** obejmuje hierarchicznej przestrzeni nazw, włączone na nim), możesz chcieć utworzyć ją w tym momencie, a następnie.

* W środowisku lokalnym klastrze usługi Hadoop, która zawiera dane źródłowe.

* [Urządzenie Azure Data Box](https://azure.microsoft.com/services/storage/databox/). 

    - [Zamówienie usługi Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered). Podczas zamawiania usługi Box, pamiętaj, aby wybrać konto magazynu, który **nie** obejmuje hierarchicznej przestrzeni nazw, włączone na nim. Jest to spowodowane urządzenia Data Box nie obsługuje jeszcze bezpośrednie przyjęciu usługi Azure Data Lake Storage Gen2. Należy skopiować do konta magazynu, a następnie wykonaj drugą kopię na konto usługi Azure Data Lake Store Gen2. Odpowiednie instrukcje są podane w poniższych krokach.
    - [Podłączanie kabli i połącz swoje urządzenia Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) z siecią lokalną.

Jeśli wszystko jest gotowe, Zacznijmy.

## <a name="copy-your-data-to-a-data-box-device"></a>Skopiuj dane do urządzenia Data Box

Aby skopiować dane z magazynu lokalnego systemu plików HDFS do urządzenia Data Box, można będzie skonfigurować kilka rzeczy, a następnie użyj [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) narzędzia.

Ilość danych, który jest kopiowany jest większa niż pojemność pojedyncze pole danych, należy podzielić zestawu danych do rozmiarów, pasujące do Twojego pól danych.

Wykonaj następujące kroki, aby skopiować dane za pośrednictwem usługi REST API elementu/obiektu Blob storage do usługi Data Box. Urządzenie Data Box są wyświetlane jako magazyn systemu plików HDFS do klastra spowoduje, że interfejs REST API. 


1. Przed przystąpieniem do kopiowania danych za pośrednictwem interfejsu REST, należy zidentyfikować podstawowych zabezpieczeń i połączenia, połączyć się z interfejsu REST na urządzenie Data Box. Zaloguj się do lokalnego internetowego interfejsu użytkownika urządzenia Data Box i przejdź do **Połącz i skopiuj** strony. Względem usługi Azure storage konto usługi Data Box w obszarze **ustawienia dostępu**Znajdź i zaznacz **REST(Preview)**.

    ![Strona "Nawiązywanie połączenia i kopiowanie"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. W ramach dostępu do konta magazynu i przekazywania danych okna dialogowego, skopiuj **punkt końcowy usługi Blob** i **klucza konta magazynu**. Z punktu końcowego usługi blob, należy pominąć `https://` i końcowy ukośnik.

    W tym przypadku jest punkt końcowy: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Częścią hosta tego identyfikatora URI, który będzie potrzebny jest: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Aby uzyskać przykład, zobacz instrukcje [nawiązywanie połączenia z REST za pośrednictwem protokołu http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Okno dialogowe "Dostęp do konta magazynu i Przekaż dane"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Dodawanie punktu końcowego i adres IP pole danych, który `/etc/hosts` w każdym węźle.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Jeśli używasz innego mechanizmu dla serwera DNS, możesz upewnij się, że punkt końcowy może być rozpoznany urządzenie Data Box.
    
3. Ustawianie zmiennej powłoki `azjars` wskaż `hadoop-azure` i `microsoft-windowsazure-storage-sdk` pliki jar. Te pliki znajdują się w katalogu instalacyjnym usługi Hadoop (Jeśli te pliki znajdują się za pomocą tego polecenia można sprawdzić `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` gdzie `<hadoop_install_dir>` to katalog, w którym zainstalowano usługi Hadoop) Używaj pełnych ścieżek. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Kopiowanie danych z HDFS, Hadoop do magazynu obiektów Blob pola danych.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars` Opcja umożliwia `hadoop-azure*.jar` i zależnych od ustawień lokalnych `azure-storage*.jar` plików `distcp`. Może to nastąpić już w przypadku niektórych klastrów.
   
   W poniższym przykładzie pokazano sposób, w jaki `distcp` polecenie służy do kopiowania danych.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Aby zwiększyć szybkość kopii:
- Spróbuj zmienić liczbę liczby maperów. (W powyższym przykładzie użyto `m` = 4 liczby maperów.)
- Spróbuj uruchomić wiele `distcp` równolegle.
- Należy pamiętać, lepiej niż małych plików wykonaj dużych plików.       
    
## <a name="ship-the-data-box-to-microsoft"></a>Dostarczaj pola danych do firmy Microsoft

Wykonaj następujące kroki, aby przygotujemy i wyślemy urządzenie Data Box do firmy Microsoft.

1. Po zakończeniu kopiowania danych uruchom [przygotowywanie do wysłania](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest) na Twoje urządzenie Data Box. Po zakończeniu przygotowywania urządzenia pobierają pliki BOM. Będzie używać tych BOM lub manifest pliki później, aby sprawdzić dane przekazywane do platformy Azure. Zamknij urządzenie i Usuń kable. 
2.  Zaplanować odbioru z UPS [dostarczaj swoje urządzenie Data Box do usługi Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.  Po firma Microsoft odbierze urządzenia, jest ona dołączona do sieci centrum danych i dane są przekazywane do konta magazynu określony (hierarchiczne przestrzenie nazw, wyłączona) po użytkownik uporządkowane urządzenie Data Box. Sprawdź względem plików BOM, że wszystkie dane została przekazana do platformy Azure. Te dane mogą teraz przejść do konta magazynu Data Lake Storage Gen2.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Przenoszenie danych na koncie usługi Data Lake Storage Gen2 storage

Ten krok jest niezbędny, jeśli używasz usługi Azure Data Lake Storage Gen2 jako magazyn danych. Jeśli używasz tylko konto magazynu obiektów blob bez hierarchicznej przestrzeni nazw jako magazyn danych, nie musisz wykonać ten krok.

Można to zrobić na 2 sposoby. 

- Użyj [usługi Azure Data Factory, aby przenieść dane do usługi ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Musisz określić **usługi Azure Blob Storage** jako źródło.

- Użyj klastra Hadoop oparte na platformie Azure. Możesz uruchomić to polecenie DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

To polecenie powoduje skopiowanie danych i metadanych z konta usługi storage na konto magazynu usługi Data Lake Storage Gen2.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak Data Lake Storage Gen2 działa z klastrami HDInsight. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
