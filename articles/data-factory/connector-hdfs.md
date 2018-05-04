---
title: Kopiowanie danych z systemu plików HDFS przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane ze źródła systemu plików HDFS chmurze lub lokalnie do zbiornika obsługiwanych magazynów danych za pomocą działania kopiowania w potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 688686b1d854f41bc5fa4cec5e22ca8e4994b2a5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Kopiowanie danych z systemu plików HDFS przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-hdfs-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-hdfs.md)

W tym artykule omówiono sposób używania działania kopiowania w fabryce danych Azure można skopiować danych z systemu plików HDFS. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika systemu plików HDFS w wersji 1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Obsługiwane możliwości

Można skopiować danych z systemu plików HDFS żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik systemu plików HDFS obsługuje:

- Kopiowanie plików za pomocą **Windows** (Kerberos) lub **anonimowe** uwierzytelniania.
- Kopiowanie plików za pomocą **webhdfs** protokołu lub **wbudowane narzędzia DistCp** obsługuje.
- Kopiowanie plików jako — jest lub analizowania/Generowanie plików z [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Wymagania wstępne

Można skopiować danych z systemu plików HDFS, który nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Zobacz [środowiska uruchomieniowego integracji Self-hosted](concepts-integration-runtime.md) artykuł, aby poznać szczegóły.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do systemu plików HDFS.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

System plików HDFS połączone usługi obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi mieć ustawioną: **Hdfs**. | Yes |
| url |Adres URL do systemu plików HDFS |Yes |
| Typ authenticationType | Dozwolone wartości to: **anonimowe**, lub **Windows**. <br><br> Umożliwia **uwierzytelnianie Kerberos** łącznika systemu plików HDFS, można znaleźć w temacie [w tej sekcji](#use-kerberos-authentication-for-hdfs-connector) do odpowiednio skonfigurowane w lokalnym środowisku. |Yes |
| userName |Uwierzytelnianie nazwy użytkownika dla systemu Windows. Aby uwierzytelnianie Kerberos, określ `<username>@<domain>.com`. |Tak (w przypadku uwierzytelniania systemu Windows) |
| hasło |Hasło dla uwierzytelniania systemu Windows. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (w przypadku uwierzytelniania systemu Windows) |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład: przy użyciu uwierzytelniania anonimowego**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przy użyciu uwierzytelniania systemu Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez system plików HDFS zestawu danych.

Aby skopiować dane z systemu plików HDFS, ustaw właściwość Typ zestawu danych do **FileShare**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **udziału plików** |Yes |
| folderPath | Ścieżka do folderu. Na przykład: folder/podfolder / |Yes |
| fileName |  **Nazwa lub symbol wieloznaczny filtr** dla plików w obszarze określonej "folderPath". Jeśli nie określisz wartości dla tej właściwości zestawu danych wskazuje wszystkie pliki w folderze. <br/><br/>Dla filtru, dozwolone symbole wieloznaczne są: `*` (wielu znaków) i `?` (pojedynczy znak).<br/>— Przykład 1: `"fileName": "*.csv"`<br/>— Przykład 2: `"fileName": "???20180427.txt"` |Nie |
| Format | Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych.<br/><br/>Jeśli chcesz przeanalizować pliki w określonym formacie, obsługiwane są następujące typy plików w formacie: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](supported-file-formats-and-compression-codecs.md#text-format), [formatu Json](supported-file-formats-and-compression-codecs.md#json-format), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), i [Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) sekcje. |Nie (tylko w przypadku scenariusza kopiowania binarny) |
| Kompresja | Określ typ i poziom kompresji danych. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodery-dekodery kompresji](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**.<br/>Obsługiwane poziomy: **optymalna** i **najszybciej**. |Nie |

>[!TIP]
>Aby skopiować wszystkie pliki w folderze, określ **folderPath** tylko.<br>Aby skopiować pojedynczy plik o podanej nazwie, określ **folderPath** z częścią folderu i **fileName** z nazwą pliku.<br>Aby skopiować podzestaw pliki w folderze, określ **folderPath** z częścią folderu i **fileName** filtru symbolu wieloznacznego.

**Przykład:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę obsługiwanych przez system plików HDFS źródła właściwości.

### <a name="hdfs-as-source"></a>System plików HDFS jako źródło

Aby skopiować dane z systemu plików HDFS, Ustaw typ źródła w przypadku działania kopiowania do **HdfsSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **HdfsSource** |Yes |
| Cykliczne | Wskazuje, czy dane są odczytywane rekursywnie z folderów sub lub tylko określonego folderu. Uwaga: po cykliczne ma ustawioną wartość PRAWDA, a obiekt sink jest magazynu opartych na plikach, pusty folder/podrzędne-folder nie będą kopiowane utworzone w ujścia.<br/>Dozwolone wartości to: **true** (ustawienie domyślne), **false** | Nie |
| distcpSettings | Grupy właściwości, korzystając z narzędzia DistCp systemu plików HDFS. | Nie |
| resourceManagerEndpoint | Punkt końcowy Yarn ResourceManager | Tak, jeśli za pomocą narzędzia DistCp |
| tempScriptPath | Ścieżka folderu, używany do przechowywania tymczasowego narzędzia DistCp polecenia skryptu. Plik skryptu jest generowany przez fabryki danych i zostaną usunięte po zakończeniu zadania kopiowania. | Tak, jeśli za pomocą narzędzia DistCp |
| distcpOptions | Dodatkowe opcje przekazane do narzędzia DistCp polecenia. | Nie |

**Przykład: Źródło systemu plików HDFS w przypadku działania kopiowania za pomocą zwolnienia**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

Dowiedz się więcej na temat sposobu za pomocą narzędzia DistCp można skopiować danych z systemu plików HDFS wydajnie z następnej sekcji.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopiowanie danych z systemu plików HDFS za pomocą narzędzia DistCp

[Narzędzia DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) Hadoop natywnego narzędzie wiersza polecenia do rozproszonej kopiowania klastra usługi Hadoop. Podczas uruchamiania polecenia narzędzia Distcp, najpierw zostanie lista wszystkich plików do skopiowania, Utwórz kilka zadań mapy do klastra usługi Hadoop, a każde zadanie mapy będzie wykonywać binarne kopiowania ze źródła do zbiornika.

Skopiuj działania pomocy technicznej za pomocą narzędzia DistCp, aby skopiować pliki jako — do obiektów Blob platformy Azure (w tym [przemieszczane kopiowania](copy-activity-performance.md) lub usługi Azure Data Lake Store, w takim przypadku pełni można wykorzystać zasilania klastra zamiast systemem środowiska uruchomieniowego integracji Self-hosted . Będzie on zawierał lepszą przepustowość kopiowania, zwłaszcza, jeśli klaster jest bardzo zaawansowaną. Zgodnie z konfiguracją w fabryce danych Azure, działanie kopiowania automatycznie utworzyć polecenia narzędzia distcp, przesłać do klastra usługi Hadoop i monitorować stan kopiowania.

### <a name="prerequsites"></a>Prerequsites

Aby skopiować za pomocą narzędzia DistCp plików jako — jest z systemu plików HDFS do obiektów Blob platformy Azure (w tym kopiowania przemieszczanego) lub usługi Azure Data Lake Store, upewnij się, że klaster Hadoop spełnia wymagań:

1. Usługi MapReduce i Yarn są włączone.
2. Wersja yarn to 2.5 lub nowszej.
3. Serwer systemu plików HDFS jest zintegrowany z magazynie danych docelowych - obiektów Blob platformy Azure lub usługi Azure Data Lake Store:

    - Azure Blob systemu plików jest obsługiwany natywnie od Hadoop 2.7. Należy określić ścieżkę jar w Hadoop, config env.
    - System plików usługi Azure Data Lake Store jest dostarczana, zaczynając od Hadoop 3.0.0-alpha1. Jeśli klaster Hadoop jest starsza niż ta wersja, należy ręcznie zaimportować ADLS powiązane jar pakietów (azure-datalake-store.jar) do klastra z [tutaj](https://hadoop.apache.org/releases.html)i podaj ścieżkę jar w Hadoop, config env.

4. Przygotuj folder tymczasowy w systemie plików HDFS. Ten folder tymczasowy jest używany do przechowywania narzędzia DistCp skrypt powłoki, więc będzie zajmować miejsca na poziomie KB.
5. Upewnij się, że podane konto użytkownika w systemie plików HDFS połączonej usługi ma uprawnienia do) przesyłania aplikacji w Yarn; (b) mają uprawnienia do tworzenia podfolder, odczytu/zapisu plików w obszarze powyżej folderu tymczasowego.

### <a name="configurations"></a>Konfiguracje

Poniżej przedstawiono przykładową konfigurację działania kopiowania, aby skopiować dane z systemu plików HDFS do obiektów Blob platformy Azure za pomocą narzędzia DistCp:

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uwierzytelnianie Kerberos dla łącznika systemu plików HDFS

Dostępne są dwie opcje do skonfigurowania środowiska lokalnego tak, aby korzystał z uwierzytelniania Kerberos w systemie plików HDFS łącznika. Możesz wybrać ten, który najlepiej pasuje do sprawę.
* Opcja 1: [maszyny środowiska uruchomieniowego integracji Self-hosted sprzężenia obszaru Kerberos](#kerberos-join-realm)
* Opcja 2: [włączyć wzajemnego zaufania między domeną systemu Windows i protokół Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opcja 1: Dołącz maszynę środowiska uruchomieniowego integracji Self-hosted obszaru Kerberos

#### <a name="requirements"></a>Wymagania

* Maszyna środowiska uruchomieniowego integracji Self-hosted należy dołączyć obszaru Kerberos i nie można dołączyć do dowolnej domeny systemu Windows.

#### <a name="how-to-configure"></a>Jak skonfigurować

**Na komputerze środowiska uruchomieniowego integracji Self-hosted:**

1.  Uruchom **Ksetup** narzędzie, aby skonfigurować serwer Centrum dystrybucji KLUCZY protokołu Kerberos i obszar.

    Komputer musi być skonfigurowany jako członek grupy roboczej, ponieważ obszaru Kerberos różni się od domeny systemu Windows. Można to osiągnąć przez ustawienie obszaru Kerberos i dodanie serwera Centrum dystrybucji KLUCZY w następujący sposób. Zastąp *REALM.COM* z własnych odpowiednich obszaru zgodnie z potrzebami.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Uruchom ponownie** komputer po wykonaniu tych poleceń 2.

2.  Sprawdź konfigurację z **Ksetup** polecenia. Dane wyjściowe powinny być takie jak:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**W fabryce danych Azure:**

* Skonfigurować za pomocą łącznika systemu plików HDFS **uwierzytelniania systemu Windows** wraz z nazwy głównej protokołu Kerberos i hasło, aby połączyć się ze źródłem danych systemu plików HDFS. Sprawdź [właściwości powiązanych z systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.

### <a name="kerberos-mutual-trust"></a>Opcja 2: Włączanie wzajemnego zaufania między domeną systemu Windows i protokół Kerberos

#### <a name="requirements"></a>Wymagania

*   Maszyna środowiska uruchomieniowego integracji Self-hosted musi zostać dołączony do domeny systemu Windows.
*   Wymagane jest uprawnienie można zaktualizować ustawień kontrolera domeny.

#### <a name="how-to-configure"></a>Jak skonfigurować

> [!NOTE]
> Zamień REALM.COM i AD.COM w samouczku następujące własnych odpowiednich obszaru i kontroler domeny, zgodnie z potrzebami.

**Na serwerze Centrum dystrybucji KLUCZY:**

1.  Edytuje konfigurację Centrum dystrybucji KLUCZY w **krb5.conf** pliku, aby umożliwić Centrum dystrybucji KLUCZY zaufania domeny systemu Windows, które odnoszą się do następujących szablonu konfiguracji. Domyślnie konfiguracji znajduje się w **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  **Uruchom ponownie** Usługa Centrum dystrybucji KLUCZY po konfiguracji.

2.  Przygotowanie podmiot zabezpieczeń o nazwie **krbtgt/REALM.COM@AD.COM** w Centrum dystrybucji KLUCZY serwera przy użyciu następującego polecenia:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  W **hadoop.security.auth_to_local** konfigurację usługi systemu plików HDFS plików, dodawanie `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Na kontrolerze domeny:**

1.  Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszarów:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Ustanowienia zaufania z domeny systemu Windows do obszaru protokołu Kerberos. [hasło] jest hasłem dla podmiotu zabezpieczeń **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Wybierz algorytm szyfrowania używany w protokole Kerberos.

    1. Przejdź do Menedżera serwera > Zarządzanie zasadami grupy > domeny > Obiekty zasad grupy > domyślny lub zasady domeny Active i edycji.

    2. W **Edytor zarządzania zasadami grupy** okno podręczne, przejdź do pozycji Konfiguracja komputera > zasady > Ustawienia systemu Windows > Ustawienia zabezpieczeń > Zasady lokalne > Opcje zabezpieczeń i skonfigurować **sieci zabezpieczenia: Konfigurowanie typów szyfrowania dozwolone dla protokołu Kerberos**.

    3. Wybierz algorytm szyfrowania, którego chcesz użyć, gdy nawiązać Centrum dystrybucji KLUCZY. Zazwyczaj można po prostu wybierz wszystkie opcje.

        ![Typy szyfrowania konfiguracji dla protokołu Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Użyj **Ksetup** polecenie, aby określić algorytm szyfrowania, który ma być używany dla określonego obszaru.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Utwórz mapowania między konto domeny i główna protokołu Kerberos, aby używały nazwy głównej protokołu Kerberos w domenie systemu Windows.

    1. Uruchamianie narzędzi administracyjnych > **użytkownicy usługi Active Directory i komputery**.

    2. Skonfiguruj zaawansowane funkcje, klikając **widoku** > **zaawansowanych funkcji**.

    3. Zlokalizuj konto, do którego chcesz utworzyć mapowania i kliknij prawym przyciskiem myszy, aby wyświetlić **mapowania nazw** > kliknij **nazwy protokołu Kerberos** kartę.

    4. Dodaj podmiot zabezpieczeń z obszaru.

        ![Mapowania tożsamości zabezpieczeń](media/connector-hdfs/map-security-identity.png)

**Na komputerze środowiska uruchomieniowego integracji Self-hosted:**

* Uruchom następujące polecenie **Ksetup** polecenia, aby dodać wpis obszaru.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**W fabryce danych Azure:**

* Skonfigurować za pomocą łącznika systemu plików HDFS **uwierzytelniania systemu Windows** wraz z Twojego konta domeny albo podmiot zabezpieczeń protokołu Kerberos do nawiązania połączenia ze źródłem danych systemu plików HDFS. Sprawdź [właściwości powiązanych z systemu plików HDFS](#linked-service-properties) sekcji Szczegóły konfiguracji.


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).