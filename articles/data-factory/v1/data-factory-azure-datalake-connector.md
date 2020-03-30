---
title: Kopiowanie danych do i z usługi Azure Data Lake Storage Gen1
description: Dowiedz się, jak kopiować dane do i z usługi Data Lake Store przy użyciu usługi Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b6a60536bae6fbedf01eda7aa340e90ced58e004
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281602"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopiowanie danych do i z usługi Data Lake Storage Gen1 przy użyciu usługi Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-azure-datalake-connector.md)
> * [Wersja 2 (bieżąca wersja)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [łącznik usługi Azure Data Lake Storage Gen1 w wersji 2](../connector-azure-data-lake-store.md).

W tym artykule wyjaśniono, jak używać działania kopiowania w usłudze Azure Data Factory do przenoszenia danych do i z usługi Azure Data Lake Storage Gen1 (wcześniej znanego jako Usługa Azure Data Lake Store). Opiera się na [działaniach przenoszenia danych,](data-factory-data-movement-activities.md) omówienie przenoszenia danych z copy activity.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane z **usługi Azure Data Lake Store** można skopiować do następujących magazynów danych:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Dane z następujących magazynów danych można **skopiować do usługi Azure Data Lake Store:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Utwórz konto magazynu Usługi Data Lake Store przed utworzeniem potoku z działaniem kopiowania. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Obsługiwane typy uwierzytelniania
Łącznik magazynu usługi Data Lake store obsługuje następujące typy uwierzytelniania:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie poświadczeń użytkownika (OAuth)

Zaleca się użycie uwierzytelniania jednostkowego usługi, szczególnie w przypadku zaplanowanej kopii danych. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Aby uzyskać szczegółowe informacje o konfiguracji, zobacz sekcję [Połączone właściwości usługi.](#linked-service-properties)

## <a name="get-started"></a>Wprowadzenie
Potoku można utworzyć z działaniem kopiowania, które przenosi dane do/z magazynu usługi Azure Data Lake przy użyciu różnych narzędzi/interfejsów API.

Najprostszym sposobem utworzenia potoku do kopiowania danych jest użycie **Kreatora kopiowania**. Aby zapoznać się z samouczkiem na temat tworzenia potoku za pomocą Kreatora kopiowania, zobacz [Samouczek: Tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Do utworzenia potoku można również użyć następujących narzędzi: **Visual Studio**, **Azure PowerShell**, **szablon usługi Azure Resource Manager**, **.NET API**i REST **API**. Zobacz [Kopiowanie samouczka aktywności,](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aby uzyskać instrukcje krok po kroku, aby utworzyć potok z działaniem kopiowania.

Niezależnie od tego, czy są używane narzędzia, czy interfejsy API, wykonaj następujące kroki, aby utworzyć potok, który przenosi dane ze źródłowego magazynu danych do magazynu danych ujścia:

1. Tworzenie **fabryki danych**. Fabryka danych może zawierać jeden lub więcej potoków.
2. Tworzenie **połączonych usług** w celu połączenia magazynów danych wejściowych i wyjściowych z fabryką danych. Na przykład jeśli kopiujesz dane z magazynu obiektów blob platformy Azure do magazynu usługi Azure Data Lake Store, utworzysz dwie połączone usługi, aby połączyć konto usługi Azure storage i magazyn usługi Azure Data Lake z fabryką danych. Właściwości usługi połączone, które są specyficzne dla usługi Azure Data Lake Store, zobacz [sekcję właściwości usługi połączone.](#linked-service-properties)
2. Tworzenie **zestawów danych** do reprezentowania danych wejściowych i wyjściowych dla operacji kopiowania. W przykładzie wymienionym w ostatnim kroku utworzysz zestaw danych, aby określić kontener obiektu blob i folder zawierający dane wejściowe. I utworzyć inny zestaw danych, aby określić folder i ścieżkę pliku w magazynie Usługi Data Lake, który przechowuje dane skopiowane z magazynu obiektów blob. Właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [sekcję właściwości zestawu danych.](#dataset-properties)
3. Utwórz **potok** z działaniem kopiowania, które przyjmuje zestaw danych jako dane wejściowe i zestaw danych jako dane wyjściowe. W przykładzie wspomniano wcześniej, należy użyć BlobSource jako źródło i AzureDataLakeStoreSink jako ujście dla działania kopiowania. Podobnie, jeśli kopiujesz z usługi Azure Data Lake Store do usługi Azure Blob Storage, używasz AzureDataLakeStoreSource i BlobSink w działania kopiowania. Aby uzyskać właściwości działania kopiowania, które są specyficzne dla usługi Azure Data Lake Store, zobacz [sekcję właściwości działania kopiowania.](#copy-activity-properties) Aby uzyskać szczegółowe informacje na temat używania magazynu danych jako źródła lub ujścia, kliknij łącze w poprzedniej sekcji dla magazynu danych.

Korzystając z kreatora, definicje JSON dla tych jednostek usługi Data Factory (połączone usługi, zestawy danych i potok) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/interfejsów API (z wyjątkiem interfejsu API platformy .NET), można zdefiniować te jednostki fabryki danych przy użyciu formatu JSON. W przypadku przykładów z definicjami JSON dla jednostek usługi Data Factory, które są używane do kopiowania danych do/z magazynu usługi Azure Data Lake Store, zobacz sekcję [przykładów JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) w tym artykule.

W poniższych sekcjach znajdują się szczegółowe informacje o właściwościach JSON, które są używane do definiowania jednostek usługi Data Factory specyficznych dla magazynu Usługi Data Lake.

## <a name="linked-service-properties"></a>Połączone właściwości usługi
Połączona usługa łączy magazyn danych z fabryką danych. Utwórz połączony serwis typu **AzureDataLakeStore,** aby połączyć dane z magazynu Data Lake Store z fabryką danych. W poniższej tabeli opisano elementy JSON specyficzne dla usług połączonych usługi usługi usługi połączone z magazynem usługi Usługi Usługi usługi usługi. Można wybrać między jednostkowym i uwierzytelnianiem poświadczeń użytkownika.

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **Typu** | Właściwość typu musi być ustawiona na **AzureDataLakeStore**. | Tak |
| **dataLakeStoreUri** | Informacje o koncie usługi Azure Data Lake Store. Informacje te mają jeden z `https://[accountname].azuredatalakestore.net/webhdfs/v1` następujących `adl://[accountname].azuredatalakestore.net/`formatów: lub . | Tak |
| **Subscriptionid** | Identyfikator subskrypcji platformy Azure, do którego należy konto Usługi Data Lake Store. | Wymagane do zlewu |
| **nazwa grupy zasobów** | Nazwa grupy zasobów platformy Azure, do której należy konto Magazynu usługi Data Lake Store. | Wymagane do zlewu |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostkowe usługi (zalecane)
Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD) i udziel jej dostępu do magazynu Usługi Data Lake. Aby uzyskać szczegółowe kroki, zobacz [Uwierzytelnianie usługi do usługi](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji
* Identyfikator dzierżawy

> [!IMPORTANT]
> Upewnij się, że udzielasz jednostki usługi prawidłowe uprawnienia w usłudze Azure Data Lake Store:
>- **Aby użyć usługi Data Lake Store jako źródła,** przyznaj co najmniej uprawnienie do **odczytu + wykonywanie** dostępu do danych do listy i kopiowania zawartości folderu lub uprawnienie do **odczytu** do kopiowania pojedynczego pliku. Brak wymagań dotyczących kontroli dostępu na poziomie konta.
>- **Aby użyć usługi Data Lake Store jako ujścia,** przyznaj co najmniej uprawnienie **do zapisu + wykonywanie** dostępu do danych, aby utworzyć elementy podrzędne w folderze. A jeśli używasz usługi Azure IR, aby umożliwić kopiowanie (zarówno źródło, jak i ujście znajdują się w chmurze), aby umożliwić fabryce danych wykrywanie regionu usługi Data Lake Store, przyznaj co najmniej rolę **czytelnika** w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli IAM, [określ executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją magazynu Usługi Data Lake w działaniu kopiowania.
>- Jeśli **używasz Kreatora kopiowania do tworzenia potoków,** przyznaj co najmniej rolę **czytelnika** w kontroli dostępu do konta (IAM). Ponadto przyznaj co najmniej **uprawnienia do odczytu + Wykonaj** do katalogu głównego magazynu Data Lake Store ("/") i jego podstawowych podstawowych uprawnień. W przeciwnym razie może zostać wyświetlony komunikat "Podane poświadczenia są nieprawidłowe".

Użyj uwierzytelniania głównego usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Tak |
| **servicePrincipalKey** | Określ klucz aplikacji. | Tak |
| **Dzierżawy** | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak |

**Przykład: uwierzytelnianie jednostki usługi**
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

### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
Alternatywnie można użyć uwierzytelniania poświadczeń użytkownika do kopiowania z lub do magazynu Usługi Data Lake, określając następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **Autoryzacji** | Kliknij przycisk **Autoryzuj** w Edytorze fabryki danych i wprowadź poświadczenia, które przypisuje do tej właściwości automatyczniegenerowany adres URL autoryzacji. | Tak |
| **Sessionid** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie podczas korzystania z Edytora fabryki danych. | Tak |

> [!IMPORTANT]
> Upewnij się, że udzielisz użytkownikowi odpowiednich uprawnień w usłudze Azure Data Lake Store:
>- **Aby użyć usługi Data Lake Store jako źródła,** przyznaj co najmniej uprawnienie do **odczytu + wykonywanie** dostępu do danych do listy i kopiowania zawartości folderu lub uprawnienie do **odczytu** do kopiowania pojedynczego pliku. Brak wymagań dotyczących kontroli dostępu na poziomie konta.
>- **Aby użyć usługi Data Lake Store jako ujścia,** przyznaj co najmniej uprawnienie **do zapisu + wykonywanie** dostępu do danych, aby utworzyć elementy podrzędne w folderze. A jeśli używasz usługi Azure IR, aby umożliwić kopiowanie (zarówno źródło, jak i ujście znajdują się w chmurze), aby umożliwić fabryce danych wykrywanie regionu usługi Data Lake Store, przyznaj co najmniej rolę **czytelnika** w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli IAM, [określ executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją magazynu Usługi Data Lake w działaniu kopiowania.
>- Jeśli **używasz Kreatora kopiowania do tworzenia potoków,** przyznaj co najmniej rolę **czytelnika** w kontroli dostępu do konta (IAM). Ponadto przyznaj co najmniej **uprawnienia do odczytu + Wykonaj** do katalogu głównego magazynu Data Lake Store ("/") i jego podstawowych podstawowych uprawnień. W przeciwnym razie może zostać wyświetlony komunikat "Podane poświadczenia są nieprawidłowe".

**Przykład: uwierzytelnianie poświadczeń użytkownika**
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
Kod autoryzacji generowany za pomocą przycisku **Autoryzuj** wygasa po pewnym czasie. Następujący komunikat oznacza, że token uwierzytelniania wygasł:

Błąd operacji poświadczeń: invalid_grant — AADSTS70002: Błąd sprawdzania poprawności poświadczeń. AADSTS70008: Udzielona dotacja dostępu wygasła lub została odwołana. Identyfikator śledzenia: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be 6-4e02-8d69-a776d2ffefd7 Sygnatura czasowa: 2015-12-15 21-09-31Z.

W poniższej tabeli przedstawiono czasy wygaśnięcia różnych typów kont użytkowników:

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta *użytkowników,* które nie są zarządzane @hotmail.com przez @live.comusługę Azure Active Directory (na przykład lub ) |12 godz. |
| Konta użytkowników zarządzane przez usługę Azure Active Directory |14 dni po ostatnim uruchomieniu plasterka <br/><br/>90 dni, jeśli plasterek oparty na połączonej usłudze opartej na UAuth działa co najmniej raz na 14 dni |

Jeśli zmienisz hasło przed czasem wygaśnięcia tokenu, token wygaśnie natychmiast. Zostanie wyświetlony komunikat wymieniony wcześniej w tej sekcji.

Można ponownie autoryzować konto za pomocą przycisku **Autoryzuj** po wygaśnięciu tokenu, aby ponownie wdrożyć usługę połączoną. Można również programowo wygenerować wartości właściwości **sessionId** i **authorization** przy użyciu następującego kodu:


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
Aby uzyskać szczegółowe informacje na temat klas fabryki danych używanych w kodzie, zobacz [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)i [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematy. Dodaj odwołanie do `2.9.10826.1824` `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` wersji `WindowsFormsWebAuthenticationDialog` dla klasy używanej w kodzie.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objaw:** Podczas kopiowania danych **do** usługi Azure Data Lake Store, jeśli działanie kopiowania nie powiedzie się z następującym błędem:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Główna przyczyna:** Istnieją 2 możliwe powody:

1. I/lub `resourceGroupName` `subscriptionId` określone w usłudze połączonej usługi Usługi Azure Data Lake Store są niepoprawne;
2. Użytkownik lub podmiot usługi nie ma wymaganych uprawnień.

**Rozdzielczość:**

1. Upewnij `subscriptionId` się, `resourceGroupName` że i określisz w połączonej usłudze `typeProperties` są rzeczywiście te, które należy do konta data lake.

2. Upewnij się, że przyznać co najmniej **rolę czytelnika** do użytkownika lub jednostki usługi na koncie usługi usługi. Oto jak to zrobić:

    1. Przejdź do witryny Azure portal - > konto usługi Data Lake Store
    2. Kliknij **pozycję Kontrola dostępu (IAM)** na bloku magazynu Data Lake
    3. Kliknij **pozycję Dodaj przypisanie roli**
    4. Ustaw **rolę** jako **czytnik**i wybierz użytkownika lub jednostkę usługi używaną do kopiowania w celu udzielenia dostępu

3. Jeśli nie chcesz przyznać roli **programu Reader** użytkownikowi lub podmiotowi usługi, alternatywą jest jawne określenie lokalizacji wykonywania w działaniu [kopiowania](data-factory-data-movement-activities.md#global) z lokalizacją magazynu Usługi Data Lake. Przykład:

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
Aby określić zestaw danych do reprezentowania danych wejściowych w magazynie Usługi Data Lake Store, należy ustawić właściwość **typu** zestawu danych na **AzureDataLakeStore**. Ustaw właściwość **linkedServiceName** zestawu danych na nazwę połączonej usługi Data Lake Store. Aby uzyskać pełną listę sekcji json i właściwości dostępnych do definiowania zestawów danych, zobacz [tworzenie zestawów danych](data-factory-create-datasets.md) artykułu. Sekcje zestawu danych w JSON, takie jak **struktura,** **dostępność**i **zasady,** są podobne dla wszystkich typów zestawów danych (na przykład baza danych Sql Azure Azure, obiekt blob i tabela platformy Azure). Sekcja **typeProperties** jest inna dla każdego typu zestawu danych i zawiera informacje, takie jak lokalizacja i format danych w magazynie danych.

Sekcja **typeProperties** dla zestawu danych typu **AzureDataLakeStore** zawiera następujące właściwości:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| **folderPath** |Ścieżka do kontenera i folderu w magazynie usługi Data Lake Store. |Tak |
| **fileName** |Nazwa pliku w usłudze Azure Data Lake Store. Właściwość **fileName** jest opcjonalna i rozróżniana wielkość liter. <br/><br/>Jeśli określisz **fileName**, działanie (w tym Kopiuj) działa na określonym pliku.<br/><br/>Gdy **nazwa pliku** nie jest określona, Copy zawiera wszystkie pliki w **folderPath** w wejściowym zestawie danych.<br/><br/>Jeśli **nazwa pliku** nie jest określona dla wyjściowego zestawu danych i **preserveHierarchy** nie jest określony `Data._Guid_.txt`w ujściu działania, nazwa wygenerowanego pliku jest w formacie . Na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nie |
| **partitionedBy** |Właściwość **partitionedBy** jest opcjonalna. Można go użyć do określenia ścieżki dynamicznej i nazwy pliku dla danych szeregów czasowych. Na przykład **folderPath** może być sparametryzowany dla każdej godziny danych. Aby uzyskać szczegółowe informacje i przykłady, zobacz PartitionedBy właściwości. |Nie |
| **Formacie** | Obsługiwane są następujące typy formatów: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**i **ParquetFormat**. Ustaw właściwość **typu** w **formacie** na jedną z tych wartości. Aby uzyskać więcej informacji, zobacz [sekcje Format tekstu,](data-factory-supported-file-and-compression-formats.md#text-format) [Format JSON,](data-factory-supported-file-and-compression-formats.md#json-format) [Format Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [FORMAT ORC](data-factory-supported-file-and-compression-formats.md#orc-format)i [Format parkietu](data-factory-supported-file-and-compression-formats.md#parquet-format) w [formatach plików i kompresji obsługiwanych przez usługę Azure Data Factory.](data-factory-supported-file-and-compression-formats.md) <br><br> Jeśli chcesz skopiować pliki "tak jak jest" między magazynami `format` opartymi na plikach (kopia binarna), pomiń sekcję w definicjach danych wejściowych i wyjściowych. |Nie |
| **Kompresji** | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**i **ZipDeflate**. Obsługiwane poziomy są **optymalne** i **najszybsze.** Aby uzyskać więcej informacji, zobacz [Formaty plików i kompresji obsługiwane przez usługę Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="the-partitionedby-property"></a>Właściwość partitionedBy
Właściwości **folderu dynamicznegoPath** i **fileName** dla danych szeregów czasowych można określić za pomocą właściwości **partitionedBy,** funkcji fabryki danych i zmiennych systemowych. Aby uzyskać szczegółowe informacje, zobacz [usługę Azure Data Factory — funkcje i zmienne systemowe](data-factory-functions-variables.md) artykułu.


W poniższym `{Slice}` przykładzie zastępuje się wartością zmiennej `SliceStart` systemowej`yyyyMMddHH`Fabryka danych w określonym formacie ( ). Nazwa `SliceStart` odnosi się do czasu rozpoczęcia plasterka. Właściwość `folderPath` jest inna dla każdego `wikidatagateway/wikisampledataout/2014100103` `wikidatagateway/wikisampledataout/2014100104`wycinka, jak w lub .

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W poniższym przykładzie rok, miesiąc, dzień `SliceStart` i godzina są wyodrębniane do `folderPath` `fileName` oddzielnych zmiennych, które są używane przez i właściwości:
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
Aby uzyskać więcej informacji na temat zestawów danych szeregów czasowych, planowania i wycinków, zobacz [zestawy danych w witrynie Azure Data Factory](data-factory-create-datasets.md) i data factory planowania i [wykonywania](data-factory-scheduling-and-execution.md) artykułów.


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Aby uzyskać pełną listę sekcji i właściwości dostępnych do definiowania działań, zobacz [Tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w sekcji **typeProperties** działania różnią się w zależności od typu działania. W przypadku działania kopiowania różnią się one w zależności od typów źródeł i pochłaniacze.

**AzureDataLakeStoreSource** obsługuje następującą właściwość w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| **Cykliczne** |Wskazuje, czy dane są odczytywane cyklicznie z podfolderów, czy tylko z określonego folderu. |Prawda (wartość domyślna), Fałsz |Nie |

**AzureDataLakeStoreSink** obsługuje następujące właściwości w sekcji **typeProperties:**

| Właściwość | Opis | Dozwolone wartości | Wymagany |
| --- | --- | --- | --- |
| **copyBehavior** |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: Zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest identyczna ze względną ścieżką pliku docelowego do folderu docelowego.<br/><br/><b>SpłaszczHierarchii:</b>Wszystkie pliki z folderu źródłowego są tworzone na pierwszym poziomie folderu docelowego. Pliki docelowe są tworzone przy obliczu automatycznie generowanych nazw.<br/><br/><b>MergeFiles</b>: Scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli nazwa pliku lub obiektu blob jest określona, scalona nazwa pliku jest określoną nazwą. W przeciwnym razie nazwa pliku jest automatyczniegenerowana. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>przykłady rekursywne i copyBehavior
W tej sekcji opisano wynikowe zachowanie operacji Kopiowanie dla różnych kombinacji wartości cyklicznych i copyBehavior.

| Cykliczne | copyBehavior | Wynikowe działanie |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z taką samą strukturą jak źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5. |
| true |spłaszczyćHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku5 |
| true |scalanie plików |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy1 jest tworzony z następującą strukturą: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 zawartość są scalane w jeden plik z automatycznie wygenerowaną nazwą pliku |
| false |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |spłaszczyćHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatycznie wygenerowana nazwa pliku2<br/><br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |
| false |scalanie plików |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik5<br/><br/>folder docelowy Folder1 jest tworzony z następującą strukturą<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Zawartość file1 + File2 jest scalana w jeden plik o automatycznie wygenerowanej nazwie pliku. automatycznie wygenerowana nazwa pliku1<br/><br/>Podfolder1 z plikami File3, File4 i File5 nie są pobierane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać szczegółowe informacje, zobacz [formaty plików i kompresji w usłudze Azure Data Factory](data-factory-supported-file-and-compression-formats.md) artykułu.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Przykłady JSON do kopiowania danych do i z usługi Data Lake Store
Poniższe przykłady zawierają przykładowe definicje JSON. Za pomocą tych przykładowych definicji można utworzyć potok przy użyciu [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) lub programu Azure [PowerShell.](data-factory-copy-activity-tutorial-using-powershell.md) Przykłady pokazują, jak skopiować dane do i z usługi Data Lake Store i magazynu obiektów Blob platformy Azure. Jednak dane mogą być kopiowane _bezpośrednio_ z dowolnego źródła do dowolnego z obsługiwanych pochłaniacze. Aby uzyskać więcej informacji, zobacz sekcję "Obsługiwane magazyny i formaty danych" w [artykule Przenoszenie danych przy użyciu artykułu Aktywność kopiowania.](data-factory-data-movement-activities.md)

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Przykład: Kopiowanie danych z usługi Azure Blob Storage do usługi Azure Data Lake Store
Przykładowy kod w tej sekcji pokazuje:

* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Połączona usługa typu [AzureDataLakeStore](#linked-service-properties).
* Wejściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Wyjściowy zestaw danych](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureDataLakeStoreSink](#copy-activity-properties).

Przykłady pokazują, jak dane szeregów czasowych z usługi Azure Blob Storage są kopiowane do magazynu usługi Data Lake Co godzinę.

**Usługa połączona usługi Usługi Azure Storage**

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

**Usługa połączona z usługą Azure Data Lake Store**

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
> Aby uzyskać szczegółowe informacje o konfiguracji, zobacz sekcję [Połączone właściwości usługi.](#linked-service-properties)
>

**Wejściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie dane są pobierane z`"frequency": "Hour", "interval": 1`nowego obiektu blob co godzinę ( ). Ścieżka folderu i nazwa pliku obiektu blob są dynamicznie oceniane na podstawie czasu rozpoczęcia wycinka, który jest przetwarzany. Ścieżka folderu używa roku, miesiąca i dnia części czasu rozpoczęcia. Nazwa pliku używa części godziny godziny rozpoczęcia. Ustawienie `"external": true` informuje usługę Data Factory, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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

**Zestaw danych wyjściowych usługi Azure Data Lake Store**

Poniższy przykład kopiuje dane do magazynu Usługi Data Lake Store. Nowe dane są kopiowane do usługi Data Lake Store co godzinę.

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

**Kopiowanie działania w potoku ze źródłem obiektu blob i ujściem magazynu usługi Data Lake Store**

W poniższym przykładzie potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON `source` potoku typ `BlobSource`jest `sink` ustawiony na `AzureDataLakeStoreSink`, a typ jest ustawiony na .

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Przykład: kopiowanie danych z usługi Azure Data Lake Store do obiektu blob platformy Azure
Przykładowy kod w tej sekcji pokazuje:

* Połączona usługa typu [AzureDataLakeStore](#linked-service-properties).
* Połączona usługa typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* [Wejściowy zestaw danych](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Wyjściowy [zestaw danych](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* [Potok](data-factory-create-pipelines.md) z działaniem kopiowania, który używa [AzureDataLakeStoreSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kod kopiuje dane szeregów czasowych z usługi Data Lake Store do obiektu blob platformy Azure co godzinę.

**Usługa połączona z usługą Azure Data Lake Store**

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
> Aby uzyskać szczegółowe informacje o konfiguracji, zobacz sekcję [Połączone właściwości usługi.](#linked-service-properties)
>

**Usługa połączona usługi Usługi Azure Storage**

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
**Zestaw danych wejściowych usługi Azure Data Lake**

W tym przykładzie ustawienie `"external"` `true` informuje usługę Fabryka danych, że tabela jest zewnętrzna dla fabryki danych i nie jest wytwarzana przez działanie w fabryce danych.

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
**Zestaw danych wyjściowych obiektów blob platformy Azure**

W poniższym przykładzie dane są zapisywane w`"frequency": "Hour", "interval": 1`nowym obiekcie blob co godzinę ( ). Ścieżka folderu obiektu blob jest dynamicznie oceniana na podstawie czasu rozpoczęcia przetwarzanego plasterka. Ścieżka folderu używa części godziny rozpoczęcia, miesiąca, dnia i godziny.

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

**Działanie kopiowania w potoku z źródłem usługi Azure Data Lake Store i ujściem obiektu blob**

W poniższym przykładzie potok zawiera działanie kopiowania, które jest skonfigurowane do używania wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane do uruchomienia co godzinę. W definicji JSON `source` potoku typ `AzureDataLakeStoreSource`jest `sink` ustawiony na `BlobSink`, a typ jest ustawiony na .

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

W definicji działania kopiowania można również mapować kolumny z źródłowego zestawu danych na kolumny w zestawie danych ujścia. Aby uzyskać szczegółowe informacje, zobacz [Mapowanie kolumn zestawu danych w usłudze Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Aby dowiedzieć się więcej o czynnikach wpływających na wydajność działania kopiowania i o tym, jak ją zoptymalizować, zobacz artykuł [przewodnika o wydajności działania kopiowania i dostrajaniu.](data-factory-copy-activity-performance.md)
