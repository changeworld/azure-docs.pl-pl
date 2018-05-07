---
title: Kopiowanie danych do/z systemem plików przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do i z lokalnego systemu plików przy użyciu fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 960a5e7a67ffaed45515e99b6aa7e64025d3167a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Kopiowanie danych do i z lokalnego systemu plików przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-onprem-file-system-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-file-system.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika systemu plików w wersji 2](../connector-file-system.md).


W tym artykule opisano sposób używania działania kopiowania w fabryce danych Azure można skopiować danych z lokalnego systemu plików. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykułu, który przedstawia ogólny przegląd przenoszenia danych z działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z lokalnego systemu plików** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **lokalnego systemu plików**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Aktywność kopiowania nie powoduje usunięcia pliku źródłowego, po pomyślnym jest kopiowany do lokalizacji docelowej. Jeśli musisz usunąć pliku źródłowego po pomyślnym kopii tworzenia działań niestandardowych do usuwania pliku i użyć działania w potoku. 

## <a name="enabling-connectivity"></a>Włączenie łączności
Fabryka danych obsługuje połączenia z lokalnego systemu plików za pośrednictwem i **brama zarządzania danymi**. Brama zarządzania danymi należy zainstalować w środowisku lokalnym dla usługi fabryka danych do nawiązania połączenia wszelkich obsługiwanych na lokalnym magazynem danych, w tym systemie plików. Aby dowiedzieć się więcej o brama zarządzania danymi i instrukcje krok po kroku dotyczące konfigurowania bramy, zobacz [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md). Oprócz brama zarządzania danymi inne pliki binarne muszą być zainstalowane do komunikowania się do i z lokalnego systemu plików. Należy zainstalować i używać bramy zarządzania danymi, nawet jeśli systemu plików jest w maszynie Wirtualnej platformy Azure IaaS. Aby uzyskać szczegółowe informacje dotyczące bramy, zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md).

Aby używać udziału plików systemu Linux, zainstaluj [Samba](https://www.samba.org/) na serwerze Linux i instalacja bramy zarządzania danymi w systemie Windows server. Instalowanie bramy zarządzania danymi na serwerze z systemem Linux nie jest obsługiwane.

## <a name="getting-started"></a>Wprowadzenie
Można utworzyć potok z działaniem kopiowania przenoszenia danych w systemie plików przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem, aby utworzyć potok jest użycie **kreatora kopiowania**. Zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md) szybkie przewodnik dotyczący tworzenia potoku za pomocą Kreatora kopiowania danych.

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Jeśli kopiujesz danych z magazynu obiektów blob platformy Azure do lokalnego systemu plików, na przykład utworzyć dwa połączone usługi, aby połączyć lokalnego systemu plików z kontem magazynu platformy Azure z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla lokalnego systemu plików, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji.
3. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić folder, który zawiera dane wejściowe i kontener obiektów blob. I Utwórz innego elementu dataset, aby określić folder i nazwę pliku (opcjonalnie) w systemie plików. Dla właściwości zestawu danych, które są specyficzne dla lokalnego systemu plików, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
4. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz BlobSource jako źródło i FileSystemSink jako zbiorniku dla działania kopiowania. Podobnie lokalnego systemu plików są kopiowane do magazynu obiektów Blob Azure, należy użyć FileSystemSource i BlobSink w przypadku działania kopiowania. Właściwości działania kopiowania, które są specyficzne dla lokalnego systemu plików, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Dla przykładów z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z systemem plików, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-file-system) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do systemu plików:

## <a name="linked-service-properties"></a>Połączona usługa właściwości
System plików lokalnych można połączyć z fabryki danych Azure z **na lokalnym serwerze plików** połączonej usługi. Poniższa tabela zawiera opisy elementy JSON, które są specyficzne dla usługi z lokalnym serwerem plików połączonych.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| type |Upewnij się, że właściwość type ma ustawioną **OnPremisesFileServer**. |Yes |
| host |Określa ścieżkę katalogu głównego folderu, który chcesz skopiować. Użyj znaku ucieczki "\" dla znaków specjalnych w ciągu. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady. |Yes |
| Nazwa użytkownika |Określ identyfikator użytkownika, który ma dostęp do serwera. |Nie (Jeśli wybierzesz encryptedCredential) |
| hasło |Określ hasło dla użytkownika (nazwa użytkownika). |Nie (Jeśli wybierzesz encryptedCredential |
| encryptedCredential |Określ zaszyfrowane poświadczenia, które można uzyskać, uruchamiając polecenie cmdlet New-AzureRmDataFactoryEncryptValue. |Nie (Jeśli wybierzesz określić identyfikator użytkownika i hasło w postaci zwykłego tekstu) |
| gatewayName |Nazwa bramy, która fabryka danych ma używać do łączenia z serwerem plików lokalnych. |Yes |


### <a name="sample-linked-service-and-dataset-definitions"></a>Przykładowe połączonej usługi i definicji zestawu danych
| Scenariusz | Host w definicji usługi połączonej | folderPath w definicji zestawu danych |
| --- | --- | --- |
| Folder lokalny na komputerze bramy zarządzania danymi: <br/><br/>Przykłady: D:\\ \* lub D:\folder\subfolder\\* |D:\\ \\ (dla danych zarządzania bramy 2.0 i nowsze wersje) <br/><br/> localhost (dla starszych niż 2.0 bramy zarządzania danych) |. \\ \\ lub folderu\\\\podfolder (dla danych zarządzania bramy 2.0 i nowsze wersje) <br/><br/>D:\\ \\ lub D:\\\\folderu\\\\podfolder (dla wersji bramy poniżej 2.0) |
| Zdalny folder udostępniony: <br/><br/>Przykłady: \\ \\MójSerwer\\udostępnianie\\ \* lub \\ \\MójSerwer\\udostępnianie\\folderu\\podfolderu\\* |\\\\\\\\MójSerwer\\\\udziału |. \\ \\ lub folderu\\\\podfolderu |

>[!NOTE]
>Podczas tworzenia za pomocą interfejsu użytkownika, nie trzeba wprowadzić podwójny ukośnik odwrotny (`\\`) aby wyjść, jak za pomocą formatu JSON, należy określić pojedynczy ukośnik odwrotny.

### <a name="example-using-username-and-password-in-plain-text"></a>Przykład: Przy użyciu nazwy użytkownika i hasła w postaci zwykłego tekstu

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Przykład: Użycie encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md). Sekcje zawierają informacje, takie jak struktury, dostępności i zasad zestawu danych JSON są podobne dla wszystkich typów w zestawie danych.

Sekcja typeProperties jest różne dla każdego typu zestawu danych. Zawiera informacje, takie jak lokalizacja i formatu danych w magazynie danych. TypeProperties sekcja dla zestawu danych typu **FileShare** ma następujące właściwości:

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| folderPath |Określa podrzędną do folderu. Użyj znaku ucieczki "\' dla znaków specjalnych w ciągu. Filtr symbolu wieloznacznego nie jest obsługiwane. Zobacz [próbki połączone definicje usługi i zestawu danych](#sample-linked-service-and-dataset-definitions) przykłady.<br/><br/>Możesz łączyć tej właściwości z **partitionBy** do folderu ścieżki oparte na wycinku rozpoczęcia/zakończenia daty i godziny. |Yes |
| fileName |Określ nazwę pliku w **folderPath** aby tabela do odwoływania się do określonego pliku w folderze. Jeśli nie określono żadnej wartości dla tej właściwości, tabela wskazuje wszystkie pliki w folderze.<br/><br/>Gdy **fileName** dla wyjściowego zestawu danych nie został określony i **preserveHierarchy** nie została określona w zbiornika działania nazwę wygenerowanego pliku znajduje się w następującym formacie: <br/><br/>`Data.<Guid>.txt` (Przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nie |
| obiektu fileFilter |Określ filtr służący do wybierania podzbioru pliki w ścieżce folderu, a nie wszystkie pliki. <br/><br/>Dozwolone wartości to: `*` (wielu znaków) i `?` (pojedynczy znak).<br/><br/>Przykład 1: "obiektu"fileFilter: "* .log"<br/>Przykład 2: "obiektu"fileFilter: 2014 - 1-?. txt"<br/><br/>Należy pamiętać, że tego obiektu fileFilter jest odpowiednie dla wejściowego zestawu danych z udziału plików. |Nie |
| partitionedBy |PartitionedBy służy do określenia dynamiczne folderPath/fileName danych szeregu czasowego. Przykładem jest folderPath sparametryzowana dla każdej godziny danych. |Nie |
| Format | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ustaw **typu** właściwości w formacie do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje. <br><br> Jeśli chcesz **skopiuj pliki jako — jest** między opartych na plikach magazynów (kopia binarnego), Pomiń sekcji format w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| Kompresja | Określ typ i poziom kompresji danych. Obsługiwane typy to: **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy: **optymalna** i **najszybciej**. zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

> [!NOTE]
> Nie można użyć nazwy pliku i obiektu fileFilter jednocześnie.

### <a name="using-partitionedby-property"></a>Za pomocą właściwości partitionedBy
Jak wspomniano w poprzedniej sekcji, można określić folderPath dynamiczne i nazwę pliku dla czasu danych serii za pomocą **partitionedBy** właściwość [funkcje fabryki danych i zmienne systemu](data-factory-functions-variables.md).

Aby poznać więcej informacji na temat zestawów danych szeregu czasowego, planowanie i wycinków, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md), [planowania i wykonywania](data-factory-scheduling-and-execution.md), i [tworzenie potoków](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Przykład 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W tym przykładzie {wycinka} zostanie zastąpiony wartością zmiennej systemowej fabryki danych SliceStart w formacie (YYYYMMDDHH). SliceStart odnosi się uruchomienie wycinka. Element folderPath jest różne dla każdego wycinka. Na przykład: wikidatagateway/wikisampledataout/2014100103 lub wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Przykład 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

W tym przykładzie rok, miesiąc, dzień i czas SliceStart są wyodrębniane do oddzielnych zmiennych, korzystających z właściwości folderPath i nazwę pliku.

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje & właściwości dostępne do definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe zestawy danych i zasady są dostępne dla wszystkich typów działań. Właściwości dostępne w **typeProperties** sekcji działania zależne od każdego typu działania.

Dla działania kopiowania różnią się w zależności od typów źródeł i sink. Jeśli przenosisz danych z lokalnego systemu plików w przypadku działania kopiowania, aby ustawić typ źródła **FileSystemSource**. Podobnie jeśli przenosisz dane do lokalnego systemu plików, należy ustawić typ ujścia w działanie kopiowania do **FileSystemSink**. Ta sekcja zawiera listę obsługiwanych przez FileSystemSource i FileSystemSink właściwości.

**FileSystemSource** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| Cykliczne |Wskazuje, czy dane są odczytywane rekursywnie z podfoldery lub tylko określonego folderu. |Wartość true, False (ustawienie domyślne) |Nie |

**FileSystemSink** obsługuje następujące właściwości:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| copyBehavior |Określa zachowanie kopiowania, gdy źródłem jest BlobSource lub systemu plików. |**PreserveHierarchy:** zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/>**FlattenHierarchy:** wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z automatycznie generowaną nazwą.<br/><br/>**MergeFiles:** scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli określono nazwę pliku nazwy/obiektów blob, nazwa scalony plik jest określona nazwa. W przeciwnym razie jest nazwa pliku wygenerowana automatycznie. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cyklicznego i copyBehavior
W tej sekcji opisano efekty operacji kopiowania dla różnych kombinacji wartości dla właściwości cyklicznego i copyBehavior.

| wartości cykliczne | wartość copyBehavior | Efekty |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tej samej struktury jako źródło:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + pliku 5 zawartości są scalane w jeden plik o nazwie pliku wygenerowana automatycznie. |
| false |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |
| false |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |
| false |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie pliku wygenerowana automatycznie.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie zostaje pobrana. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md) artykuł na temat szczegółów.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>Przykłady JSON kopiowania danych do i z systemu plików
Poniższe przykłady zapewniają definicje JSON, których można utworzyć potok przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Przedstawiają sposób kopiowania danych do i z lokalnego systemu plików i magazynu obiektów Blob platformy Azure. Można jednak skopiować dane *bezpośrednio* z dowolnego źródła do dowolnego wychwytywanie na liście [obsługiwanych źródeł i wychwytywanie](data-factory-data-movement-activities.md#supported-data-stores-and-formats) za pomocą działania kopiowania w fabryce danych Azure.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Przykład: Kopiowanie danych z lokalnego systemu plików do magazynu obiektów Blob platformy Azure
W tym przykładzie pokazano, jak można skopiować danych z lokalnego systemu plików do magazynu obiektów Blob Azure. Przykład zawiera następujące jednostek fabryki danych:

* Połączonej usługi typu [OnPremisesFileServer](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [FileShare](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działaniem kopii, która używa [FileSystemSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Poniższy przykład kopiuje dane szeregów czasowych z lokalnego systemu plików do magazynu obiektów Blob Azure co godzinę. Właściwości JSON, które są używane w te przykłady są opisane w sekcjach po próbek.

Pierwszym krokiem skonfigurować bramę zarządzania danymi zgodnie z instrukcjami wyświetlanymi w [przenoszenie danych między lokalnych źródeł i w chmurze z brama zarządzania danymi](data-factory-move-data-between-onprem-and-cloud.md).

**Serwer plików lokalnych połączonej usługi:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Firma Microsoft zaleca używanie **encryptedCredential** właściwości zamiast **userid** i **hasło** właściwości. Zobacz [serwera plików połączona usługa](#linked-service-properties) szczegółowe informacje o tej połączonej usługi.

**Połączonej usługi magazynu Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Lokalnego pliku zestawu danych wejściowych systemu:**

Dane są pobierane z nowego pliku co godzinę. Właściwości folderPath i nazwa pliku są określane na podstawie czasu rozpoczęcia wycinka.  

Ustawienie `"external": "true"` fabryki danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Magazyn obiektów Blob Azure wyjściowy zestaw danych:**

Dane są zapisywane do nowego obiektu blob co godzinę (częstotliwość: godziny, interwał: 1). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godzinę części czas rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Działanie kopiowania w potoku z systemu plików źródłowy i odbiorczy obiektów Blob:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **FileSystemSource**, i **zbiornika** ustawiono typ **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Przykład: Kopiowanie danych z bazy danych SQL Azure do lokalnego systemu plików
Poniższy przykład przedstawia:

* Połączonej usługi typu [AzureSqlDatabase.](data-factory-azure-sql-connector.md#linked-service-properties)
* Połączonej usługi typu [OnPremisesFileServer](#linked-service-properties).
* Zestaw danych wejściowych typu [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
* Wyjściowy zestaw danych typu [FileShare](#dataset-properties).
* Potok z działania kopiowania, która używa [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) i [FileSystemSink](#copy-activity-properties).

Przykład kopiuje dane szeregów czasowych z tabeli Azure SQL do lokalnego systemu plików co godzinę. Właściwości JSON, które są używane w te przykłady zostały opisane w sekcjach po próbek.

**Baza danych SQL Azure połączonej usługi:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```

**Serwer plików lokalnych połączonej usługi:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Firma Microsoft zaleca używanie **encryptedCredential** zamiast za pomocą właściwości **userid** i **hasło** właściwości. Zobacz [System plików połączona usługa](#linked-service-properties) szczegółowe informacje o tej połączonej usługi.

**Azure SQL wejściowy zestaw danych:**

Przykładzie założono, że po utworzeniu tabeli "MyTable" w języku SQL Azure i zawiera kolumnę o nazwie "timestampcolumn", szeregów czasowych danych.

Ustawienie ``“external”: ”true”`` fabryki danych informuje, czy zestaw danych jest zewnętrzne do fabryki danych i nie jest generowany przez działanie w fabryce danych.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Lokalnego pliku systemu wyjściowego zestawu danych:**

Dane zostaną skopiowane do nowego pliku co godzinę. FolderPath i nazwę pliku dla obiekt blob są określane na podstawie czasu rozpoczęcia wycinka.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Działanie kopiowania w potoku z SQL źródłowy i odbiorczy systemu plików:**

Potok zawiera działanie kopiowania, który jest skonfigurowany do używania wejściowe i wyjściowe zestawy danych i jest zaplanowane co godzinę. W definicji JSON potoku **źródła** ustawiono typ **SqlSource**i **zbiornika** ustawiono typ **FileSystemSink**. Zapytanie SQL, który jest określony dla **SqlReaderQuery** właściwości wybiera dane w ostatniej godziny do skopiowania.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


Można również mapować kolumn z zestawu źródła danych do kolumn z zestawu danych zbiornika w definicji działania kopiowania. Aby uzyskać więcej informacji, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
 Aby dowiedzieć się więcej na temat kluczowych czynników, które mają wpływ na wydajność przenoszenia danych (działanie kopiowania) w usłudze fabryka danych Azure i zoptymalizować ją na różne sposoby, zobacz [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md).
