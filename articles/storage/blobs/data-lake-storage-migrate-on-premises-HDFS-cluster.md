---
title: Użyj usługi Azure Data Box migracji danych z środowiska lokalnego systemu plików HDFS magazynu do usługi Azure Storage
description: Migracja danych z magazynu lokalnego systemu plików HDFS do usługi Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595394"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Użyj usługi Azure Data Box do migracji danych z lokalnego systemu plików HDFS magazynu do usługi Azure Storage

Za pomocą urządzenia Data Box, można migrować dane z lokalnego systemu plików HDFS magazynu klastra usługi Hadoop w usłudze Azure Storage (magazynie obiektów blob lub Data Lake Storage Gen2). Możesz wybrać z urządzenia Data Box 80 TB lub duże pole 770 TB danych.

Ten artykuł pomoże Ci wykonać następujące zadania:

> [!div class="checklist"]
> * Przygotowanie do migracji danych.
> * Skopiuj dane do urządzenia Data Box lub duże pole danych urządzenia.
> * Odesłanie urządzenia do firmy Microsoft.
> * Przenieś dane do Data Lake Storage Gen2.

## <a name="prerequisites"></a>Wymagania wstępne

Należy te rzeczy, aby ukończyć migrację.

* Dwa konta magazynu; który ma hierarchicznej przestrzeni nazw, włączone na nim, a jeden, które nie.

* W środowisku lokalnym klastrze usługi Hadoop, która zawiera dane źródłowe.

* [Urządzenie Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Zamówienie usługi Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) lub [Data Box ciężkich](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Podczas zamawiania urządzenia, pamiętaj, aby wybrać konto magazynu, który **nie** obejmuje hierarchicznej przestrzeni nazw, włączone na nim. Jest to spowodowane urządzenia Data Box nie jest jeszcze obsługiwany bezpośrednie przyjęciu usługi Azure Data Lake Storage Gen2. Należy skopiować do konta magazynu, a następnie wykonaj drugą kopię na konto usługi Azure Data Lake Store Gen2. Odpowiednie instrukcje są podane w poniższych krokach.

  * Podłączanie kabli i połącz swoje [urządzenia Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) lub [duże pole danych](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) z siecią lokalną.

Jeśli wszystko jest gotowe, Zacznijmy.

## <a name="copy-your-data-to-a-data-box-device"></a>Skopiuj dane do urządzenia Data Box

Jeśli dane mieści się w jedno urządzenie Data Box, dane będą kopiowane do urządzenia Data Box. 

Jeśli rozmiar danych przekracza pojemność urządzenie Data Box, należy użyć [opcjonalna procedurze, aby podzielić dane na wielu urządzeniach urządzenia Data Box](#appendix-split-data-across-multiple-data-box-devices) , a następnie wykonaj ten krok. 

Aby skopiować dane z magazynu lokalnego systemu plików HDFS do urządzenia Data Box, można będzie skonfigurować kilka rzeczy, a następnie użyj [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) narzędzia.

Wykonaj następujące kroki, aby skopiować dane za pośrednictwem usługi REST API elementu/obiektu Blob storage do urządzenia Data Box. Interfejs REST API spowoduje, że urządzenia są traktowane jako magazyn systemu plików HDFS do klastra.

1. Przed przystąpieniem do kopiowania danych za pośrednictwem interfejsu REST, należy zidentyfikować podstawowych zabezpieczeń i połączenia, połączyć się z interfejsu REST na urządzenie Data Box lub duże pole danych. Zaloguj się do lokalnego internetowego interfejsu użytkownika urządzenia Data Box i przejdź do **Połącz i skopiuj** strony. Względem usługi Azure storage konta dla danego urządzenia, w obszarze **ustawienia dostępu**Znajdź i zaznacz **REST**.

    ![Strona "Nawiązywanie połączenia i kopiowanie"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. W ramach dostępu do konta magazynu i przekazywania danych okna dialogowego, skopiuj **punkt końcowy usługi Blob** i **klucza konta magazynu**. Z punktu końcowego usługi blob, należy pominąć `https://` i końcowy ukośnik.

    W tym przypadku jest punkt końcowy: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Częścią hosta tego identyfikatora URI, który będzie potrzebny jest: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Aby uzyskać przykład, zobacz instrukcje [nawiązywanie połączenia z REST za pośrednictwem protokołu http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Okno dialogowe "Dostęp do konta magazynu i Przekaż dane"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Dodawanie punktu końcowego i adres IP węzła urządzenia Data Box lub duże pole danych do `/etc/hosts` w każdym węźle.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Jeśli używasz innego mechanizmu dla serwera DNS, możesz upewnij się, że punkt końcowy może być rozpoznany urządzenie Data Box.

4. Ustawianie zmiennej powłoki `azjars` do lokalizacji `hadoop-azure` i `azure-storage` pliki jar. Możesz znaleźć te pliki w katalogu instalacyjnym usługi Hadoop.

    Aby ustalić, czy te pliki istnieją, użyj następującego polecenia: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Zastąp `<hadoop_install_dir>` zastępczego ścieżkę do katalogu, w którym zainstalowano usługi Hadoop. Należy użyć w pełni kwalifikowanej ścieżki.

    Przykłady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar``azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Utwórz kontener magazynu, którego chcesz użyć do kopiowania danych. Należy także określić katalog docelowy jako część tego polecenia. Może to być katalog docelowy fikcyjnego, w tym momencie.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` nazwą dla punktu końcowego usługi blob service.

    * Zastąp `<account_key>` symbolu zastępczego kluczem dostępu do Twojego konta.

    * Zastąp `<container-name>` symbol zastępczy nazwy kontenera.

    * Zastąp `<destination_directory>` nazwą katalogu, który chcesz skopiować dane.

6. Uruchom polecenie listy, aby upewnić się, że kontenera i katalogu zostały utworzone.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Zastąp `<blob_service_endpoint>` nazwą dla punktu końcowego usługi blob service.

   * Zastąp `<account_key>` symbolu zastępczego kluczem dostępu do Twojego konta.

   * Zastąp `<container-name>` symbol zastępczy nazwy kontenera.

7. Kopiowanie danych z HDFS, Hadoop do magazynu obiektów Blob pole danych, do kontenera, który został utworzony wcześniej. Jeśli są kopiowane do katalogu nie zostanie znaleziony, polecenie automatycznie go utworzy.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` nazwą dla punktu końcowego usługi blob service.

    * Zastąp `<account_key>` symbolu zastępczego kluczem dostępu do Twojego konta.

    * Zastąp `<container-name>` symbol zastępczy nazwy kontenera.

    * Zastąp `<exlusion_filelist_file>` zastępczego dla nazwy pliku, który zawiera listę wykluczeń plików.

    * Zastąp `<source_directory>` nazwą katalogu, który zawiera dane, które mają zostać skopiowane.

    * Zastąp `<destination_directory>` nazwą katalogu, który chcesz skopiować dane.

    `-libjars` Opcja umożliwia `hadoop-azure*.jar` i zależnych od ustawień lokalnych `azure-storage*.jar` plików `distcp`. Może to nastąpić już w przypadku niektórych klastrów.

    W poniższym przykładzie pokazano sposób, w jaki `distcp` polecenie służy do kopiowania danych.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Aby zwiększyć szybkość kopii:

    * Spróbuj zmienić liczbę liczby maperów. (W powyższym przykładzie użyto `m` = 4 liczby maperów.)

    * Spróbuj uruchomić wiele `distcp` równolegle.

    * Należy pamiętać, lepiej niż małych plików wykonaj dużych plików.

## <a name="ship-the-data-box-to-microsoft"></a>Dostarczaj pola danych do firmy Microsoft

Wykonaj następujące kroki, aby przygotujemy i wyślemy urządzenie Data Box do firmy Microsoft.

1. Po pierwsze, [przygotowywanie do wysłania na urządzenie Data Box lub duże pole danych](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Po zakończeniu przygotowywania urządzenia pobierają pliki BOM. Będzie używać tych BOM lub manifest pliki później, aby sprawdzić dane przekazywane do platformy Azure.

3. Zamknij urządzenie i Usuń kable.

4. Zaplanować odbioru UPS.

    * Dla urządzenia Data Box, zobacz [dostarczaj swoje urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * W przypadku urządzeń duże pole danych, zobacz [dostarczaj swoje duże pole danych](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Po firma Microsoft odbierze urządzenia, jest ona połączona z siecią centrum danych i dane są przekazywane do konta magazynu określony (hierarchiczne przestrzenie nazw, wyłączona) po użytkownik złożył zamówienie urządzenia. Sprawdź względem plików BOM, że wszystkie dane została przekazana do platformy Azure. Te dane mogą teraz przejść do konta magazynu Data Lake Storage Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Przenoszenia danych do usługi Azure Data Lake Storage Gen2

Masz już dane na koncie usługi Azure Storage. Teraz będzie skopiować dane do konta usługi Azure Data Lake storage i stosowanie uprawnień dostępu do plików i katalogów.

> [!NOTE]
> Ten krok jest niezbędny, jeśli używasz usługi Azure Data Lake Storage Gen2 jako magazyn danych. Jeśli używasz tylko konto magazynu obiektów blob bez hierarchicznej przestrzeni nazw jako magazyn danych, możesz pominąć tę sekcję.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Kopiowanie danych do konta usługi Azure Data Lake Storage Gen 2

Można skopiować danych, za pomocą usługi Azure Data Factory lub przy użyciu klastra Hadoop oparte na platformie Azure.

* Aby użyć usługi Azure Data Factory, zobacz [usługi Azure Data Factory, aby przenieść dane do usługi ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Upewnij się określić **usługi Azure Blob Storage** jako źródło.

* Aby korzystać z klastrem Hadoop oparte na platformie Azure, uruchom następujące polecenie DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Zastąp `<source_account>` i `<dest_account>` symbole zastępcze nazwą konta magazynu źródłowego i docelowego.

    * Zastąp `<source_container>` i `<dest_container>` symbole zastępcze przy użyciu nazwy kontenerów źródłowym i docelowym.

    * Zastąp `<source_path>` i `<dest_path>` symbole zastępcze przy użyciu ścieżki katalogu źródłowego i docelowego.

    * Zastąp `<source_account_key>` symbolu zastępczego z kluczem dostępu konta magazynu, który zawiera dane.

    To polecenie powoduje skopiowanie danych i metadanych z konta usługi storage na konto magazynu usługi Data Lake Storage Gen2.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Tworzenie jednostki usługi dla konta usługi Azure Data Lake Storage Gen2

Aby utworzyć nazwę główną usługi, zobacz [jak: używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi w celu uzyskiwania dostępu do zasobów](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) tego artykułu, upewnij się, że przypisano rolę **Współautor danych obiektu blob magazynu** do jednostki usługi.

* Wykonując kroki opisane w [pobieranie wartości do logowania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) części artykułu, identyfikator aplikacji i wartości klucza tajnego klienta do pliku tekstowego. Wkrótce będą potrzebne.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generowanie listy kopiowanych plików wraz z ich uprawnieniami

Z klastra usługi Hadoop w środowisku lokalnym uruchom następujące polecenie:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

To polecenie generuje listę kopiowanych plików wraz z ich uprawnieniami.

> [!NOTE]
> W zależności od liczby plików w systemie plików HDFS to polecenie może zająć dużo czasu do uruchomienia.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Wygeneruj listę tożsamości i zamapować je do tożsamości usługi Azure Active Directory (Dodaj)

1. Pobierz `copy-acls.py` skryptu. Zobacz [Pobierz skrypty pomocnika i skonfiguruj Twój węzeł krawędzi, aby uruchomić je](#download-helper-scripts) dalszej części tego artykułu.

2. Uruchom następujące polecenie, aby wygenerować listę unikatowych tożsamości.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ten skrypt generuje plik o nazwie `id_map.json` zawierający tożsamości, wymagających do mapowania tożsamości oparte na dodatku.

3. Otwórz `id_map.json` plik w edytorze tekstów.

4. Dla każdego obiektu JSON, który pojawia się w pliku, należy zaktualizować `target` atrybut AAD główną nazwę użytkownika (UPN) lub identyfikator obiektu (OID) z odpowiednią mapowane tożsamości. Po wykonaniu tych czynności należy zapisać plik. Należy tego pliku w następnym kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Stosowanie uprawnień do kopiowanych plików i stosuje się mapowania tożsamości

Uruchom następujące polecenie, aby zastosować uprawnienia do danych, który został skopiowany na konto usługi Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` symbol zastępczy nazwy kontenera.

* Zastąp `<application-id>` i `<client-secret>` symbole zastępcze przy użyciu aplikacji identyfikator i klucz tajny klienta zebranych podczas tworzenia nazwy głównej usługi.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Dodatek: Podział danych na wielu urządzeniach urządzenia Data Box

Przed przeniesieniem danych na urządzenie Data Box, musisz pobrać niektóre skrypty pomocnika, upewnij się, że dane są organizowane, aby zmieściły się na urządzenie Data Box i wykluczone niepotrzebne pliki.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Pobierz skrypty pomocnika i skonfiguruj Twój węzeł krawędzi, aby uruchomić je

1. Krawędzi lub węzłem głównym klastra Hadoop w środowisku lokalnym uruchom następujące polecenie:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   To polecenie klonuje repozytorium GitHub, który zawiera skrypty pomocnika.

2. Upewnij się, że [jq](https://stedolan.github.io/jq/) pakiet na komputerze lokalnym.

   ```bash
   
   sudo apt-get install jq
   ```

3. Zainstaluj [żądań](http://docs.python-requests.org/en/master/) pakiet języka python.

   ```bash
   
   pip install requests
   ```

4. Ustaw uprawnienia do wykonywania na wymagane skrypty.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Upewnij się, że Twoje dane są organizowane, aby zmieściły się na urządzenie Data Box

Jeśli rozmiar danych przekracza rozmiar pojedynczego urządzenia Data Box, można podzielić pliki grup, które mogą być przechowywane na wielu urządzeniach urządzenia Data Box.

Jeśli dane nie przekracza rozmiaru wystąpieniu urządzenie Data Box, możesz przejść do następnej sekcji.

1. Z podwyższonym poziomem uprawnień uruchom `generate-file-list` skrypt, który został pobrany, postępując zgodnie ze wskazówkami w poprzedniej sekcji.

   Poniżej przedstawiono opis parametrów poleceń:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Wygenerowany plik listy systemu plików HDFS, aby były dostępne dla kopiowania [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) zadania.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Wykluczone niepotrzebne pliki

Należy wykluczyć niektóre katalogi z zadania DisCp. Na przykład wykluczyć katalogi, które zawierają informacje o stanie zachowujących klastra z systemem.

W klastrze platformy Hadoop w środowisku lokalnym, gdzie planujesz zainicjować zadanie narzędzia DistCp należy utworzyć plik, który określa listę katalogów, które chcesz wykluczyć.

Oto przykład:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Data Lake Storage Gen2 działa z klastrami HDInsight. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
