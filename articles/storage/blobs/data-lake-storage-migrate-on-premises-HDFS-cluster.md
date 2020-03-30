---
title: Migrowanie ze sklepu usługi HDFS w przedsprzedaży do usługi Azure Storage za pomocą usługi Azure Data Box
description: Migrowanie danych z lokalnego magazynu HDFS do usługi Azure Storage
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302004"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrowanie ze sklepu usługi HDFS w przedsprzedaży do usługi Azure Storage za pomocą usługi Azure Data Box

Dane z lokalnego magazynu HDFS klastra Hadoop można migrować do usługi Azure Storage (magazynu obiektów blob lub magazynu danych Lake Storage Gen2) przy użyciu urządzenia Data Box. Do wyboru jest skrzynka danych o pojemności 80 TB lub 770 TB data box.

Ten artykuł ułatwia wykonywanie tych zadań:

> [!div class="checklist"]
> * Przygotuj się do migracji danych.
> * Skopiuj dane do urządzenia Data Box lub Data Box Heavy.
> * Wyślij urządzenie z powrotem do firmy Microsoft.
> * Stosowanie uprawnień dostępu do plików i katalogów (tylko Data Lake Storage Gen2)

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są te rzeczy, aby zakończyć migrację.

* Konto usługi Azure Storage.

* Lokalny klaster Hadoop zawierający dane źródłowe.

* [Urządzenie usługi Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Zamów skrzynkę danych](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) lub [pole danych Grube](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Kabel i podłącz [skrzynkę danych](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) lub [urządzenie Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) do sieci lokalnej.

Jeśli jesteś gotowy, zacznijmy.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopiowanie danych do urządzenia Data Box

Jeśli dane zmieści się w jednym urządzeniu Data Box, skopiujesz dane do urządzenia Data Box. 

Jeśli rozmiar danych przekracza pojemność urządzenia Data Box, wykonaj [opcjonalną procedurę, aby podzielić dane na wiele urządzeń Data Box,](#appendix-split-data-across-multiple-data-box-devices) a następnie wykonaj ten krok. 

Aby skopiować dane z lokalnego magazynu HDFS do urządzenia Data Box, ustaw kilka czynności, a następnie użyj narzędzia [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Wykonaj następujące czynności, aby skopiować dane za pośrednictwem interfejsów API REST obiektów blob/object storage do urządzenia Data Box. Interfejs interfejsu API REST sprawi, że urządzenie będzie wyświetlane jako magazyn HDFS w klastrze.

1. Przed skopiowaniem danych za pośrednictwem rest, należy zidentyfikować zabezpieczeń i ujednolicenia połączenia, aby połączyć się z interfejsem REST na data box lub Data Box Heavy. Zaloguj się do lokalnego interfejsu użytkownika internetowego data box i przejdź do **strony Połącz i skopiuj.** Na koncie magazynu platformy Azure dla urządzenia w obszarze **Ustawienia programu Access**znajdź i wybierz POZYCJĘ **REST**.

    ![Strona "Połącz i skopaj"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. W oknie dialogowym Konteszklaowanie magazynu programu Access i przekazywania danych skopiuj **punkt końcowy usługi Blob** i **klucz konta Magazyn**. Z punktu końcowego usługi obiektu `https://` blob pomiń ukośnik i końcowe.

    W takim przypadku punktem `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`końcowym jest: . Część hosta identyfikatora URI, który `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`będzie używany, to: . Na przykład zobacz, jak [połączyć się z REST za pośrednictwem http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Okno dialogowe "Dostęp do konta magazynu i przekazywania danych"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Dodaj punkt końcowy i adres IP węzła data `/etc/hosts` box lub data box heavy do każdego węzła.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Jeśli używasz innego mechanizmu dla systemu DNS, należy upewnić się, że punkt końcowy pola danych można rozwiązać.

4. Ustaw zmienną `azjars` powłoki na `hadoop-azure` `azure-storage` położenie plików i jar. Możesz znaleźć te pliki w katalogu instalacyjnym Hadoop.

    Aby ustalić, czy te pliki istnieją, należy użyć następującego polecenia: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Zastąp `<hadoop_install_dir>` symbol zastępczy ścieżką do katalogu, w którym zainstalowano hadoop. Pamiętaj, aby korzystać z w pełni kwalifikowanych ścieżek.

    Przykłady:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Utwórz kontener magazynu, którego chcesz użyć do kopiowania danych. Należy również określić katalog docelowy jako część tego polecenia. Może to być fikcyjny katalog docelowy w tym momencie.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi obiektu blob.

    * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

    * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

    * Zastąp `<destination_directory>` symbol zastępczy nazwą katalogu, do którego chcesz skopiować dane.

6. Uruchom polecenie listy, aby upewnić się, że kontener i katalog zostały utworzone.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi obiektu blob.

   * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

   * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

7. Skopiuj dane z usługi Hadoop HDFS do magazynu obiektów blob pola danych do kontenera, który został utworzony wcześniej. Jeśli katalog, do którego kopiujesz, nie zostanie znaleziony, polecenie automatycznie go utworzy.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Zastąp `<blob_service_endpoint>` symbol zastępczy nazwą punktu końcowego usługi obiektu blob.

    * Zastąp `<account_key>` symbol zastępczy kluczem dostępu do konta.

    * Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

    * Zastąp `<exlusion_filelist_file>` symbol zastępczy nazwą pliku zawierającego listę wykluczeń plików.

    * Zastąp `<source_directory>` symbol zastępczy nazwą katalogu zawierającego dane, które chcesz skopiować.

    * Zastąp `<destination_directory>` symbol zastępczy nazwą katalogu, do którego chcesz skopiować dane.

    Opcja `-libjars` ta jest używana `hadoop-azure*.jar` do `azure-storage*.jar` udostępniania plików `distcp`zależnych i zależnych . Może to już wystąpić w przypadku niektórych klastrów.

    W poniższym przykładzie pokazano, `distcp` jak polecenie jest używane do kopiowania danych.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Aby zwiększyć szybkość kopiowania:

    * Spróbuj zmienić liczbę maperów. (Powyższy przykład `m` używa = 4 maperów.)

    * Spróbuj uruchomić `distcp` wiele równolegle.

    * Pamiętaj, że duże pliki działają lepiej niż małe pliki.

## <a name="ship-the-data-box-to-microsoft"></a>Wyślij skrzynkę danych do firmy Microsoft

Wykonaj następujące kroki, aby przygotować i wysłać urządzenie Data Box do firmy Microsoft.

1. Najpierw [przygotuj się do wysyłki na polu danych lub data box heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Po zakończeniu przygotowywania urządzenia pobierz pliki BOM. Te pliki BOM lub manifestu użyjesz później, aby zweryfikować dane przekazane na platformę Azure.

3. Wyłącz urządzenie i wyjmij kable.

4. Zaplanuj odbiór przez firmę UPS.

    * W przypadku urządzeń Data Box zobacz [Wysyłka pola danych](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Aby zapoznać się z danymi Box Heavy, zobacz [Wysyłka urządzenia Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Po odebraniu urządzenia przez firmę Microsoft jest ono połączone z siecią centrum danych, a dane są przekazywane na konto magazynu określone podczas składania zamówienia urządzenia. Sprawdź w plikach BOM, czy wszystkie dane są przekazywane na platformę Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Stosowanie uprawnień dostępu do plików i katalogów (tylko Data Lake Storage Gen2)

Masz już dane na koncie usługi Azure Storage. Teraz zastosujesz uprawnienia dostępu do plików i katalogów.

> [!NOTE]
> Ten krok jest potrzebny tylko wtedy, gdy używasz usługi Azure Data Lake Storage Gen2 jako magazynu danych. Jeśli używasz tylko konta magazynu obiektów blob bez hierarchicznego obszaru nazw jako magazynu danych, możesz pominąć tę sekcję.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Tworzenie jednostki usługi dla konta usługi Azure Data Storage Gen2

Aby utworzyć jednostkę usługi, zobacz [Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* Wykonując kroki opisane w sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) tego artykułu, upewnij się, że przypisano rolę **Współautor danych obiektu blob magazynu** do jednostki usługi.

* Podczas wykonywania kroków w [Pobierz wartości do logowania się w](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) sekcji artykułu, zapisz identyfikator aplikacji i tajne wartości klienta w pliku tekstowym. Wkrótce będą potrzebne.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generowanie listy skopiowanych plików z ich uprawnieniami

Z lokalnego klastra Hadoop uruchom to polecenie:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

To polecenie generuje listę skopiowanych plików z ich uprawnieniami.

> [!NOTE]
> W zależności od liczby plików w hdfs, to polecenie może zająć dużo czasu, aby uruchomić.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generowanie listy tożsamości i mapowanie ich na tożsamości usługi Azure Active Directory (ADD)

1. Pobierz `copy-acls.py` skrypt. Zobacz [Pobierz skrypty pomocnika i skonfiguruj węzeł brzegowy, aby uruchomić je](#download-helper-scripts) w tym artykule.

2. Uruchom to polecenie, aby wygenerować listę unikatowych tożsamości.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Ten skrypt generuje plik `id_map.json` o nazwie, który zawiera tożsamości, które należy mapować do tożsamości opartych na ADD.

3. Otwórz `id_map.json` plik w edytorze tekstu.

4. Dla każdego obiektu JSON, który pojawia `target` się w pliku, zaktualizuj atrybut głównej nazwy użytkownika usługi AAD (UPN) lub ObjectId (OID), o odpowiednią zamapowane tożsamości. Po zakończeniu zapisz plik. Ten plik będzie potrzebny w następnym kroku.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Stosowanie uprawnień do skopiowanych plików i stosowanie mapowań tożsamości

Uruchom to polecenie, aby zastosować uprawnienia do danych skopiowanych do konta Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Zastąp symbol zastępczy `<storage-account-name>` nazwą konta magazynu.

* Zastąp `<container-name>` symbol zastępczy nazwą kontenera.

* Zastąp `<application-id>` symbole zastępcze identyfikatorem `<client-secret>` aplikacji i kluczem tajnym klienta zebranym podczas tworzenia jednostki usługi.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Dodatek: Dzielenie danych na wiele urządzeń Data Box

Przed przeniesieniem danych na urządzenie Data Box należy pobrać niektóre skrypty pomocnicze, upewnić się, że dane są zorganizowane tak, aby pasowały do urządzenia Data Box, oraz wykluczyć niepotrzebne pliki.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Pobierz skrypty pomocnika i skonfiguruj węzeł krawędzi, aby je uruchomić

1. Z węzła brzegowego lub głównego lokalnego klastra Hadoop uruchom to polecenie:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   To polecenie klonuje repozytorium GitHub, które zawiera skrypty pomocnicze.

2. Upewnij się, że na komputerze lokalnym jest zainstalowany pakiet [jq.](https://stedolan.github.io/jq/)

   ```bash
   
   sudo apt-get install jq
   ```

3. Zainstaluj pakiet [Żądaje](https://2.python-requests.org/en/master/) python.

   ```bash
   
   pip install requests
   ```

4. Ustaw uprawnienia do wykonywania wymaganych skryptów.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Upewnij się, że dane są zorganizowane tak, aby pasowały do urządzenia Data Box

Jeśli rozmiar danych przekracza rozmiar pojedynczego urządzenia Data Box, można podzielić pliki na grupy, które można przechowywać na wielu urządzeniach Data Box.

Jeśli dane nie przekraczają rozmiaru urządzenia singe Data Box, możesz przejść do następnej sekcji.

1. Z podwyższonym poziomem uprawnień uruchom pobrany `generate-file-list` skrypt, postępował zgodnie ze wskazówkami zawartymi w poprzedniej sekcji.

   Oto opis parametrów polecenia:

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

2. Skopiuj wygenerowane listy plików do systemu plików HDFS, aby były dostępne dla zadania [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Wykluczanie niepotrzebnych plików

Musisz wykluczyć niektóre katalogi z zadania DisCp. Na przykład wyklucz katalogi zawierające informacje o stanie, które utrzymują klaster uruchomiony.

W lokalnym klastrze Hadoop, w którym planujesz zainicjować zadanie DistCp, utwórz plik określający listę katalogów, które chcesz wykluczyć.

Oto przykład:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Data Lake Storage Gen2 współpracuje z klastrami HDInsight. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
