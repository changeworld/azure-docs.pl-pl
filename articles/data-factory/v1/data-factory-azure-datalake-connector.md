---
title: Kopiowanie danych do i z usługi Azure Data Lake Storage Gen1 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do i z Data Lake Store za pomocą usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d8637a2711c0301d9e9f409e169ed04fb3d65783
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839540"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiowanie danych do i z Data Lake Storage Gen1 przy użyciu usługi fabryka danych
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-azure-datalake-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [łącznika usługi Azure Data Lake Storage Gen1 w wersji 2](../connector-azure-data-lake-store.md).

W tym artykule wyjaśniono, jak użyć działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do i z usługi Azure Data Lake magazynu Gen1 (wcześniej znane jako usługi Azure Data Lake Store). Opiera się na [działania przenoszenia danych](data-factory-data-movement-activities.md) artykuł z omówieniem przenoszenie danych za pomocą działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane można kopiować **z usługi Azure Data Lake Store** się następujące dane są przechowywane:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących magazynów danych **do usługi Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Tworzenie konta Data Lake Store, przed utworzeniem potoku za pomocą działania kopiowania. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typy obsługiwane uwierzytelnianie
Łącznik programu Data Lake Store obsługuje następujące typy uwierzytelniania:
* Uwierzytelnianie jednostki usługi
* Uwierzytelniania poświadczeń (OAuth) użytkownika

Zaleca się, że używasz uwierzytelniania jednostki usługi, szczególnie w przypadku kopiowania danych według harmonogramu. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Szczegółowe informacje dotyczące konfiguracji, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.

## <a name="get-started"></a>Wprowadzenie
Utworzysz potok z działaniem kopiowania, które przenosi dane z usługi Azure Data Lake Store przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku w celu kopiowania danych jest użycie **kreatora kopiowania**. Samouczek dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczka: Tworzenie potoku przy użyciu Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Aby utworzyć potok umożliwia także następujących narzędzi: **Program Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejsu API platformy .NET**, i **interfejsu API REST**. Zobacz [samouczka działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy używasz narzędzi lub interfejsów API, należy wykonać poniższe kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródłowych do magazynu danych ujścia:

1. Tworzenie **usługi data factory**. Fabryka danych może zawierać jeden lub wiele potoków.
2. Tworzenie **połączonych usług** połączyć dane wejściowe i wyjściowe przechowywane z fabryką danych. Na przykład jeśli kopiujesz dane z usługi Azure blob storage do usługi Azure Data Lake Store, utworzysz dwie połączone usługi, aby połączyć swoje konto usługi Azure storage i Azure Data Lake store z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
2. Tworzenie **zestawów danych** do reprezentowania dane wejściowe i wyjściowe operacji kopiowania. W tym przykładzie wymienione w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektów blob oraz folder, który zawiera dane wejściowe. I utwórz inny zestaw danych do określenia folderu i ścieżka pliku w magazynie usługi Data Lake, która przechowuje dane skopiowane z magazynu obiektów blob. Aby uzyskać właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
3. Tworzenie **potoku** za pomocą działania kopiowania, która przyjmuje jako dane wejściowe zestawu danych i zestaw danych jako dane wyjściowe. W przykładzie, o których wspomniano wcześniej możesz użyć BlobSource jako źródła i AzureDataLakeStoreSink jako obiekt sink dla działania kopiowania. Podobnie Azure Data Lake Store są kopiowane do usługi Azure Blob Storage, należy użyć AzureDataLakeStoreSource i BlobSink w działaniu kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości działania kopiowania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje na temat korzystania z magazynu danych jako źródła lub ujścia kliknij link w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dotyczące tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), należy zdefiniować te jednostki usługi Data Factory przy użyciu formatu JSON. Aby uzyskać przykłady przy użyciu definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych z usługi Azure Data Lake Store, zobacz [JSON przykłady](#json-examples-for-copying-data-to-and-from-data-lake-store) dalszej części tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi fabryka danych określonej do Data Lake Store.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej
Połączona usługa łączy magazyn danych do usługi data factory. Tworzenie połączonej usługi typu **AzureDataLakeStore** połączyć dane usługi Data Lake Store z fabryką danych. W poniższej tabeli opisano specyficzne dla usługi Data Lake Store, połączone elementy JSON. Można wybrać nazwy głównej usługi i uwierzytelnienia poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **type** | Właściwość type musi być równa **AzureDataLakeStore**. | Yes |
| **dataLakeStoreUri** | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jedną z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Tak |
| **subscriptionId** | Identyfikator subskrypcji platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |
| **resourceGroupName** | Nazwa grupy zasobów platformy Azure, do której należy konto Data Lake Store. | Wymagane dla ujścia |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostki aplikacji w usłudze Azure Active Directory (Azure AD), a następnie przyznać jej dostęp do programu Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [Service-to-service authentication](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji
* Identyfikator dzierżawy

> [!IMPORTANT]
> Upewnij się, że można przyznać usługi głównej odpowiednie uprawnienia w usłudze Azure Data Lake Store:
>- **Aby użyć Data Lake Store jako źródła**, przyznać co najmniej **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj zawartość folderu, dostępu do danych lub **odczytu** uprawnień do kopiowania pojedynczy plik. Nie wymagań dotyczących kontroli dostępu na poziomie konta.
>- **Aby użyć Data Lake Store jako obiekt sink**, przyznać co najmniej **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze dostępu do danych. I jeśli używasz środowiska Azure IR przeznaczonych dla kopii (zarówno źródła i ujścia znajdują się w chmurze), aby umożliwiają usłudze Data Factory wykryć regionu Data Lake Store, co najmniej udzielić **czytnika** roli konta kontroli dostępu (IAM). Jeśli chcesz uniknąć tej roli zarządzania tożsamościami i Dostępem [określić wartość elementu executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją usługi Data Lake Store, w działaniu kopiowania.
>- Jeśli użytkownik **tworzenie potoków za pomocą Kreatora kopiowania**, przyznaj co najmniej **czytnika** roli konta kontroli dostępu (IAM). Ponadto udzielić co najmniej **odczytu i wykonania** uprawnień do katalogu głównego usługi Data Lake Store ("/") i jego elementów podrzędnych. W przeciwnym razie być może zobaczysz komunikat "podane poświadczenia są nieprawidłowe."

Użyj uwierzytelniania jednostki usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Tak |
| **servicePrincipalKey** | Określ klucz aplikacji. | Yes |
| **dzierżawy** | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można je pobrać, ustawiając kursor myszy w prawym górnym rogu witryny Azure portal. | Yes |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Uwierzytelnienia poświadczeń użytkownika
Alternatywnie można uwierzytelnienia poświadczeń użytkownika kopiowanie Data Lake Store, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **Autoryzacja** | Kliknij przycisk **Autoryzuj** znajdujący się w edytorze fabryki danych i wprowadź swoje poświadczenia, które przypisuje adres URL autoryzacji wygenerowany automatycznie do tej właściwości. | Yes |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowany automatycznie, korzystając z edytora fabryki danych. | Tak |

> [!IMPORTANT]
> Upewnij się, że można przyznać odpowiednie uprawnienia użytkownika w usłudze Azure Data Lake Store:
>- **Aby użyć Data Lake Store jako źródła**, przyznać co najmniej **odczytu i wykonania** uprawnienia do listy, a następnie skopiuj zawartość folderu, dostępu do danych lub **odczytu** uprawnień do kopiowania pojedynczy plik. Nie wymagań dotyczących kontroli dostępu na poziomie konta.
>- **Aby użyć Data Lake Store jako obiekt sink**, przyznać co najmniej **zapisu i wykonania** uprawnień, aby tworzyć elementy podrzędne w folderze dostępu do danych. I jeśli używasz środowiska Azure IR przeznaczonych dla kopii (zarówno źródła i ujścia znajdują się w chmurze), aby umożliwiają usłudze Data Factory wykryć regionu Data Lake Store, co najmniej udzielić **czytnika** roli konta kontroli dostępu (IAM). Jeśli chcesz uniknąć tej roli zarządzania tożsamościami i Dostępem [określić wartość elementu executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją usługi Data Lake Store, w działaniu kopiowania.
>- Jeśli użytkownik **tworzenie potoków za pomocą Kreatora kopiowania**, przyznaj co najmniej **czytnika** roli konta kontroli dostępu (IAM). Ponadto udzielić co najmniej **odczytu i wykonania** uprawnień do katalogu głównego usługi Data Lake Store ("/") i jego elementów podrzędnych. W przeciwnym razie być może zobaczysz komunikat "podane poświadczenia są nieprawidłowe."

**Przykład: Uwierzytelnienia poświadczeń użytkownika**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji, które generują przy użyciu **Autoryzuj** przycisk wygasa po upływie określonego czasu. Następujący komunikat o błędzie oznacza, że token uwierzytelniania wygasł:

Błąd operacji dotyczącej poświadczeń: invalid_grant - AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS70008: Udzielenie dostępu podany jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21-09-31Z.

W poniższej tabeli przedstawiono czas wygaśnięcia różnych rodzajów kont użytkowników:

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników *nie* zarządzane przez usługę Azure Active Directory (na przykład @hotmail.com lub @live.com) |12 godz. |
| Konta użytkowników zarządzanych przez usługę Azure Active Directory |Uruchom 14 dni od ostatniego wycinka <br/><br/>90 dni, jeśli wycinek na połączonej usługi OAuth na podstawie działa co najmniej raz na 14 dni |

Jeśli zmienisz hasło przed upływem czasu wygaśnięcia tokenu, token jest ważny od razu. Zostanie wyświetlony komunikat, o których wspomniano wcześniej w tej sekcji.

Można ponownie autoryzować konto przy użyciu **Autoryzuj** przycisk po wygaśnięciu ważności tokenu, można wdrożyć ponownie połączoną usługę. Możesz również generować wartości **sessionId** i **autoryzacji** właściwości programowo, używając następującego kodu:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Aby uzyskać szczegółowe informacje na temat klas usługi Data Factory używane w kodzie, zobacz [klasy AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [klasy AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), i [ Klasa AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematów. Dodaj odwołanie do wersji `2.9.10826.1824` z `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` dla `WindowsFormsWebAuthenticationDialog` klasy używane w kodzie.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objaw:** Podczas kopiowania danych **do** usługi Azure Data Lake Store, jeśli działanie kopiowania zakończy się niepowodzeniem z powodu następującego błędu:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Główna przyczyna:** Istnieją 2 możliwe przyczyny:

1. `resourceGroupName` I/lub `subscriptionId` określone w usługi Azure Data Lake Store, połączone usługi są niepoprawne;
2. Użytkownik lub ta jednostka usługi nie ma odpowiedniego uprawnienia.

**Rozwiązanie:**

1. Upewnij się, że `subscriptionId` i `resourceGroupName` określisz w połączonej usłudze `typeProperties` są w rzeczywistości te, które należy do Twojego konta usługi data lake.

2. Upewnij się, można przyznać co najmniej **czytnika** roli do użytkownika lub nazwa główna usługi na konta usługi data lake. Oto jak:

    1. Przejdź do witryny Azure portal wybierz kolejno opcje konta usługi Data Lake Store
    2. Kliknij przycisk **kontrola dostępu (IAM)** do bloku Data Lake Store
    3. Kliknij przycisk **Dodaj przypisanie roli**
    4. Ustaw **roli** jako **czytnika**, a następnie wybierz użytkownika lub jednostki usługi użyj związanym z kopiowaniem do udzielania dostępu

3. Jeśli nie chcesz udzielić **czytnika** ma rolę użytkownika lub nazwa główna usługi, alternatywnych [jawnie określić lokalizację wykonania](data-factory-data-movement-activities.md#global) w działaniu kopiowania z lokalizacją usługi Data Lake Store. Przykład:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Właściwości zestawu danych
Aby określić zestaw danych reprezentujący dane wejściowe w Data Lake Store, należy ustawić **typu** właściwości zestawu danych na **AzureDataLakeStore**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy Data Lake Store połączoną usługę. Aby uzyskać pełną listę sekcje JSON i właściwości dostępne Definiowanie zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Części zestawu danych w formacie JSON, takich jak **struktury**, **dostępności**, i **zasad**, są podobne dla wszystkich typów w zestawie danych (bazy danych Azure SQL, obiektów blob platformy Azure i usługi Azure table dla np.). **TypeProperties** sekcji różni się dla każdego typu zestawu danych i udostępnia informacje, takie jak lokalizacja i format danych w magazynie danych.

**TypeProperties** sekcji dla zestawu danych typu **AzureDataLakeStore** zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **folderPath** |Ścieżka do kontenera i folderu w Data Lake Store. |Yes |
| **fileName** |Nazwa pliku w usłudze Azure Data Lake Store. **FileName** właściwość jest opcjonalna i wielkość liter. <br/><br/>Jeśli określisz **fileName**, aktywności (w tym kopiowania) działa na określonego pliku.<br/><br/>Gdy **fileName** nie zostanie określony, kopia uwzględnia wszystkie pliki w **folderPath** w zestawie danych wejściowych.<br/><br/>Gdy **fileName** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie została określona w ujścia działania nazwę wygenerowanego pliku jest w formacie `Data._Guid_.txt`. Na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nie |
| **partitionedBy** |**PartitionedBy** właściwość jest opcjonalna. Służy do określania dynamiczne ścieżkę i nazwę pliku dla danych szeregów czasowych. Na przykład **folderPath** mogą być parametryzowane za każdą godzinę danych. Aby uzyskać szczegółowe informacje i przykłady Zobacz właściwość partitionedBy. |Nie |
| **Format** | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [format tekstu](data-factory-supported-file-and-compression-formats.md#text-format), [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC format](data-factory-supported-file-and-compression-formats.md#orc-format), i [formatu Parquet ](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje w [formaty plików i kompresji, które są obsługiwane przez usługę Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykułu. <br><br> Jeśli chcesz skopiować pliki "jako — jest" między opartych na plikach magazynów (kopia binarna), Pomiń `format` sekcji w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| **compression** | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Są obsługiwane poziomy **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji, które są obsługiwane przez usługę Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="the-partitionedby-property"></a>Właściwość partitionedBy
Można określić dynamiczny **folderPath** i **fileName** właściwości danych szeregów czasowych za pomocą **partitionedBy** właściwości funkcji usługi fabryka danych i zmiennych systemowych. Aby uzyskać więcej informacji, zobacz [usługi Azure Data Factory — funkcje i zmienne systemowe](data-factory-functions-variables.md) artykułu.


W poniższym przykładzie `{Slice}` jest zastępowana wartością zmiennej systemowej usługi Data Factory `SliceStart` w formacie określonym (`yyyyMMddHH`). Nazwa `SliceStart` odnosi się do czasu rozpoczęcia wycinka. `folderPath` Właściwość różni się dla każdego wycinka jak `wikidatagateway/wikisampledataout/2014100103` lub `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W poniższym przykładzie, rok, miesiąc, dzień i godzina `SliceStart` są wyodrębniane do oddzielnych zmiennych, które są używane przez `folderPath` i `fileName` właściwości:
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
Aby uzyskać szczegółowe informacje na temat zestawów danych szeregów czasowych, planowanie i wycinków, zobacz [zestawów danych w usłudze Azure Data Factory](data-factory-create-datasets.md) i [usługi Data Factory planowania i wykonywania](data-factory-scheduling-and-execution.md) artykułów.


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [tworzenia potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** różnią się w sekcji działania za pomocą poszczególnych typów działań. Działanie kopiowania różnią w zależności od typów źródła i ujścia.

**AzureDataLakeStoreSource** obsługuje następującą właściwość w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **cykliczne** |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów lub tylko z określonego folderu. |TRUE, False (wartość domyślna) |Nie |

**AzureDataLakeStoreSink** obsługuje następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **copyBehavior** |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: Zachowuje hierarchii plików w folderze docelowym. Ścieżka względna pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: Wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z nazwami wygenerowany automatycznie.<br/><br/><b>MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określony, nazwa pliku scalonego jest określonej nazwy. W przeciwnym razie nazwa pliku jest generowana automatycznie. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>przykładów rekurencyjnych i copyBehavior
W tej sekcji opisano wynikowe zachowania operacji kopiowania różne kombinacje wartości cyklicznych i copyBehavior.

| recursive | copyBehavior | Wynikowe zachowania |
| --- | --- | --- |
| true |preserveHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony przy użyciu tej samej struktury jako źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę File5 |
| true |mergeFiles |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1 jest tworzony o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + 5 plików zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie plik |
| false |preserveHierarchy |Do folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |flattenHierarchy |Do folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Wygenerowany automatycznie nazwę plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;wygenerowany automatycznie nazwę plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |
| false |mergeFiles |Do folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Wygenerowany automatycznie nazwę plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są pobierane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykułu.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Przykłady JSON do kopiowania danych Data Lake Store
W poniższych przykładach udostępniono przykładowe definicji JSON. Te definicje umożliwia tworzenie potoku za pomocą [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładach pokazano, jak kopiować dane do i z usługi Data Lake Store i Azure Blob storage. Jednak dane mogą być kopiowane _bezpośrednio_ z dowolnego źródła do dowolnej obsługiwanej wychwytywanie. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny danych i formatów" w [przenoszenie danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) artykułu.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Przykład: Kopiowanie danych z usługi Azure Blob Storage do usługi Azure Data Lake Store
Przykładowy kod w tej sekcji przedstawiono:

* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Połączonej usługi typu [AzureDataLakeStore](#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureDataLakeStoreSink](#copy-activity-properties).

W przykładach pokazano, jak szeregów czasowych dane z usługi Azure Blob Storage są kopiowane do Data Lake Store, co godzinę.

**Połączona usługa Azure Storage**

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

**Usługę połączoną usługi Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Szczegółowe informacje dotyczące konfiguracji, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
>

**Wejściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie data jest pobierana z nowy obiekt blob co godzinę (`"frequency": "Hour", "interval": 1`). Folder ścieżkę i nazwę dla obiektu blob są dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa rok, miesiąc i część dotyczącą dnia, godziny rozpoczęcia. Nazwa pliku używa część dotyczącą godziny z czas rozpoczęcia. `"external": true` Ustawienie usługi Data Factory informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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

**Azure Data Lake Store wyjściowy zestaw danych**

Poniższy przykład kopiuje dane do programu Data Lake Store. Nowe dane są kopiowane, do Data Lake Store, co godzinę.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Działanie kopiowania w potoku za pomocą obiektu blob źródła i ujścia Data Lake Store**

W poniższym przykładzie potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania danych wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok `source` ustawiono typ `BlobSource`i `sink` ustawiono typ `AzureDataLakeStoreSink`.

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure Data Lake Store do obiektu blob platformy Azure
Przykładowy kod w tej sekcji przedstawiono:

* Połączonej usługi typu [AzureDataLakeStore](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) za pomocą działania kopiowania, która używa [AzureDataLakeStoreSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kod kopiuje dane szeregów czasowych z Data Lake Store do obiektu blob platformy Azure co godzinę.

**Usługę połączoną usługi Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Szczegółowe informacje dotyczące konfiguracji, zobacz [właściwości usługi połączonej](#linked-service-properties) sekcji.
>

**Połączona usługa Azure Storage**

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
**Wejściowy zestaw danych usługi Azure Data Lake**

W tym przykładzie ustawienie `"external"` do `true` usługi Data Factory informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w usłudze data factory.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
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
**Wyjściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie dane są zapisywane do nowego obiektu blob na godzinę (`"frequency": "Hour", "interval": 1`). Ścieżka folderu dla obiektu blob jest dynamicznie obliczana na podstawie czasu rozpoczęcia wycinek, który jest przetwarzany. Ścieżka folderu używa roku, miesiąca, dnia i godziny część czas rozpoczęcia.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Działania kopiowania w potoku za pomocą z usługi Azure Data Lake Store źródła i ujścia obiektu blob**

W poniższym przykładzie potoku zawierającego działanie kopiowania, który jest skonfigurowany do używania danych wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia na godzinę. W definicji JSON potok `source` ustawiono typ `AzureDataLakeStoreSource`i `sink` ustawiono typ `BlobSink`.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

W definicji działania kopiowania można również mapować kolumny z zestawu danych źródłowych do kolumn w zestawie danych ujścia. Aby uzyskać więcej informacji, zobacz [mapowanie kolumny zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Aby dowiedzieć się więcej na temat czynników wpływających na wydajność działania kopiowania i jak ją zoptymalizować, zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md) artykułu.
