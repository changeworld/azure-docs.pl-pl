---
title: Kopiowanie danych do i z usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane do i z usługi Data Lake Store przy użyciu fabryki danych Azure
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d83e71efa89746d7744893281ffe0ac1169fa08b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621476"
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Kopiowanie danych do i z usługi Data Lake Store przy użyciu fabryki danych
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-azure-datalake-connector.md)
> * [Wersja 2 — wersja zapoznawcza](../connector-azure-data-lake-store.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [łącznika usługi Azure Data Lake Store w wersji 2](../connector-azure-data-lake-store.md).

W tym artykule opisano sposób użycia działanie kopiowania w fabryce danych Azure do przenoszenia danych do i z usługi Azure Data Lake Store. Opiera się na [działań przepływu danych](data-factory-data-movement-activities.md) artykuł Omówienie przenoszenia danych z działania kopiowania.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Dane należy skopiować **z usługi Azure Data Lake Store** do następujących danych przechowuje:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Możesz skopiować dane z następujących baz danych **do usługi Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Przed utworzeniem potoku z działania kopiowania, należy utworzyć konto usługi Data Lake Store. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Typy obsługiwane uwierzytelniania
Łącznik usługi Data Lake Store obsługuje następujące typy uwierzytelniania:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie użytkownika poświadczeń (OAuth) 

Firma Microsoft zaleca korzystanie z uwierzytelniania głównej usługi, zwłaszcza w przypadku kopiowania danych zaplanowane. Zachowanie wygaśnięcia tokenu może wystąpić przy użyciu uwierzytelniania poświadczeń użytkownika. Szczegółowe informacje dotyczące konfiguracji, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji.

## <a name="get-started"></a>Rozpoczęcie pracy
Można utworzyć potok z działania kopiowania, który przenosi dane z usługi Azure Data Lake Store za pomocą różnych narzędzi/interfejsów API.

Najprostszym sposobem tworzenia potoku, aby skopiować dane, jest użycie **kreatora kopiowania**. Samouczek dotyczący tworzenia potoku za pomocą Kreatora kopiowania, zobacz [samouczek: tworzenie potoku za pomocą Kreatora kopiowania](data-factory-copy-data-wizard-tutorial.md).

Umożliwia także następujące narzędzia do tworzenia potoku: **portalu Azure**, **programu Visual Studio**, **programu Azure PowerShell**, **szablonu usługi Azure Resource Manager**, **interfejs API .NET**, i **interfejsu API REST**. Zobacz [samouczek działania kopiowania](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) instrukcje krok po kroku utworzyć potok z działaniem kopiowania.

Czy można użyć narzędzia i interfejsy API, należy wykonać następujące kroki, aby utworzyć potok, który przenosi dane z magazynu danych źródła do ujścia magazynu danych:

1. Utwórz **fabryki danych**. Fabryka danych może zawierać co najmniej jeden potoków. 
2. Utwórz **połączone usługi** Aby połączyć dane wejściowe i wyjściowe są przechowywane w fabryce danych. Jeśli kopiujesz danych z magazynu obiektów blob platformy Azure do usługi Azure Data Lake Store, na przykład utworzyć dwa połączone usługi, aby połączyć Twoje konto magazynu Azure i usługi Azure Data Lake store z fabryką danych. Dla właściwości połączonej usługi, które są specyficzne dla usługi Azure Data Lake Store, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji. 
2. Utwórz **zestawów danych** do reprezentowania danych wejściowych i wyjściowych operacji kopiowania. W tym przykładzie wymienionych w ostatnim kroku tworzenia zestawu danych, aby określić folder, który zawiera dane wejściowe i kontener obiektów blob. I utwórz inny zestaw danych do określenia folderu i ścieżka pliku w usłudze Data Lake store, która przechowuje dane skopiowane z magazynu obiektów blob. Dla właściwości zestawu danych, które są specyficzne dla usługi Azure Data Lake Store, zobacz [właściwości zestawu danych](#dataset-properties) sekcji.
3. Utwórz **potoku** aktywnością kopiowania zestawu danych jako dane wejściowe i zestawu danych jako dane wyjściowe. W przykładzie wspomniano wcześniej używasz BlobSource jako źródło i AzureDataLakeStoreSink jako zbiorniku dla działania kopiowania. Podobnie usługa Azure Data Lake Store są kopiowane do magazynu obiektów Blob Azure, należy użyć AzureDataLakeStoreSource i BlobSink w przypadku działania kopiowania. Właściwości działania kopiowania, które są specyficzne dla usługi Azure Data Lake Store, zobacz [skopiować właściwości działania](#copy-activity-properties) sekcji. Aby uzyskać szczegółowe informacje dotyczące sposobu używania magazynu danych jako źródło lub zbiorniku kliknij łącze w poprzedniej sekcji dla magazynu danych.  

Korzystając z kreatora, definicje JSON do tych jednostek fabryki danych (połączone usługi, zestawy danych i potoki) są tworzone automatycznie dla Ciebie. Korzystając z narzędzi/API (z wyjątkiem interfejs API .NET), należy zdefiniować tych jednostek fabryki danych w formacie JSON.  Aby uzyskać przykłady z definicji JSON dla jednostek fabryki danych, które są używane do kopiowania danych do/z usługi Azure Data Lake Store, zobacz [przykłady JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) sekcji tego artykułu.

Poniższe sekcje zawierają szczegółowe informacje o właściwości JSON, które są używane do definiowania jednostek fabryki danych określonej do usługi Data Lake Store.

## <a name="linked-service-properties"></a>Połączona usługa właściwości
Połączona usługa łączy magazynu danych z fabryki danych. Tworzenie połączonej usługi typu **AzureDataLakeStore** połączyć dane z usługi Data Lake Store z fabryką danych. W poniższej tabeli opisano specyficzne dla usługi Data Lake Store połączone elementy JSON. Można wybrać usługę podmiotu zabezpieczeń i uwierzytelniania poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **type** | Właściwość type musi mieć ustawioną **AzureDataLakeStore**. | Yes |
| **dataLakeStoreUri** | Informacje o koncie usługi Azure Data Lake Store. Informacja ta ma jeden z następujących formatów: `https://[accountname].azuredatalakestore.net/webhdfs/v1` lub `adl://[accountname].azuredatalakestore.net/`. | Yes |
| **Identyfikator subskrypcji** | Identyfikator subskrypcji platformy Azure, do której należy konto usługi Data Lake Store. | Wymagany dla odbiorcy |
| **resourceGroupName** | Nazwa grupy zasobów platformy Azure, do której należy konto usługi Data Lake Store. | Wymagany dla odbiorcy |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie główna usługi (zalecane)
Aby używać uwierzytelniania głównej usługi, Zarejestruj podmiot aplikacji w usłudze Azure Active Directory (Azure AD) i przyznać jej dostęp do usługi Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [do usługi uwierzytelniania](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zwróć uwagę na następujące wartości, które służą do definiowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

> [!IMPORTANT]
> Upewnij się, że można przydzielić usługi głównej odpowiednie uprawnienia w usłudze Azure Data Lake Store:
>- **Do użycia usługi Data Lake Store jako źródło**, przyznaj co najmniej **Odczyt i wykonywanie** uprawnienia do listy, a następnie skopiuj zawartość folderu, dostępu do danych lub **odczytu** uprawnień do kopiowania pojedynczy plik. Nie jest wymagany na kontroli dostępu na poziomie konta.
>- **Do użycia usługi Data Lake Store jako obiekt sink**, przyznaj co najmniej **zapisu i wykonywania** uprawnienia do tworzenia elementów podrzędnych w folderze dostępu do danych. I użycie Azure IR dla kopiowania (źródłowy i odbiorczy znajdują się w chmurze), aby umożliwić fabryki danych wykrywania region Data Lake Store, przyznaj co najmniej **czytnika** roli w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli IAM [Określ executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją usługi Data Lake Store w przypadku działania kopiowania.
>- Jeśli użytkownik **tworzenie potoków za pomocą Kreatora kopiowania**, udzielić co najmniej **czytnika** roli w kontroli dostępu do konta (IAM). Ponadto udzielić co najmniej **Odczyt i wykonywanie** uprawnień do katalogu głównym usługi Data Lake Store ("/") i jego elementów podrzędnych. W przeciwnym razie można napotkać komunikat "podane poświadczenia są nieprawidłowe."

Uwierzytelnianie usługi głównej przez określenie następujących właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator aplikacji klienta. | Yes |
| **servicePrincipalKey** | Określ klucz aplikacji. | Yes |
| **Dzierżawy** | Określ informacje dzierżawy (identyfikator nazwy lub dzierżawy domeny), w którym znajduje się aplikacja. Można go pobrać, ustawiając kursor myszy w prawym górnym rogu portalu Azure. | Yes |

**Przykład: Usługa podmiotu zabezpieczeń uwierzytelniania**
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
Alternatywnie służy uwierzytelnienia poświadczeń użytkownika do kopiowania z lub do usługi Data Lake Store przez określenie następujących właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **Autoryzacji** | Kliknij przycisk **autoryzacji** przycisk Edytor fabryki danych i wprowadź Twoje poświadczenia, który przypisuje do tej właściwości adresu URL autoryzacji wygenerowana automatycznie. | Yes |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i mogą być użyte tylko raz. To ustawienie jest generowane automatycznie, gdy używasz Edytor fabryki danych. | Yes |

> [!IMPORTANT]
> Upewnij się, że można przyznać odpowiednie uprawnienia użytkownika w usłudze Azure Data Lake Store:
>- **Do użycia usługi Data Lake Store jako źródło**, przyznaj co najmniej **Odczyt i wykonywanie** uprawnienia do listy, a następnie skopiuj zawartość folderu, dostępu do danych lub **odczytu** uprawnień do kopiowania pojedynczy plik. Nie jest wymagany na kontroli dostępu na poziomie konta.
>- **Do użycia usługi Data Lake Store jako obiekt sink**, przyznaj co najmniej **zapisu i wykonywania** uprawnienia do tworzenia elementów podrzędnych w folderze dostępu do danych. I użycie Azure IR dla kopiowania (źródłowy i odbiorczy znajdują się w chmurze), aby umożliwić fabryki danych wykrywania region Data Lake Store, przyznaj co najmniej **czytnika** roli w kontroli dostępu do konta (IAM). Jeśli chcesz uniknąć tej roli IAM [Określ executionLocation](data-factory-data-movement-activities.md#global) z lokalizacją usługi Data Lake Store w przypadku działania kopiowania.
>- Jeśli użytkownik **tworzenie potoków za pomocą Kreatora kopiowania**, udzielić co najmniej **czytnika** roli w kontroli dostępu do konta (IAM). Ponadto udzielić co najmniej **Odczyt i wykonywanie** uprawnień do katalogu głównym usługi Data Lake Store ("/") i jego elementów podrzędnych. W przeciwnym razie można napotkać komunikat "podane poświadczenia są nieprawidłowe."

**Przykład: Użytkownik poświadczeń uwierzytelniania**
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

#### <a name="token-expiration"></a>Wygaśnięcia tokenu
Kod autoryzacji, który można wygenerować za pomocą **autoryzacji** przycisk wygasa po określonym czasie. Następujący komunikat o błędzie oznacza, że token uwierzytelniania wygasł:

Poświadczeń błąd operacji: invalid_grant - AADSTS70002: błąd podczas sprawdzania poprawności poświadczeń. AADSTS70008: Udzielone prawa dostępu jest wygasnąć lub zostać odwołane. Identyfikator śledzenia: Identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: sygnatura czasowa fac30a0c-6be6-4e02-8d69-a776d2ffefd7: 2015-12-15 21-09-31Z.

W poniższej tabeli przedstawiono czas wygaśnięcia różnych typów kont użytkowników:

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników *nie* zarządzane przez usługę Azure Active Directory (na przykład @hotmail.com lub @live.com) |12 godzin |
| Konta użytkowników zarządzanych przez usługę Azure Active Directory |Uruchom 14 dni od ostatniego wycinek <br/><br/>90 dni, jeśli wycinek oparte na podstawie OAuth połączonej usługi jest uruchamiana co najmniej raz na 14 dni |

Jeśli zmienisz hasło przed upływem terminu wygaśnięcia tokenu, token jest ważny od razu. Zostanie wyświetlony komunikat, o których wspomniano wcześniej w tej sekcji.

Można ponownie autoryzować konta przy użyciu **autoryzacji** przycisku token jest ważny przez ponowne wdrożenie połączonej usługi. Można również tworzyć wartości **sessionId** i **autoryzacji** właściwości programowo przy użyciu następującego kodu:


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
Aby uzyskać szczegółowe informacje o klasach fabryki danych używana w kodzie, zobacz [klasy AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [klasy AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), i [klasy AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tematów. Dodaj odwołanie do wersji `2.9.10826.1824` z `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` dla `WindowsFormsWebAuthenticationDialog` klasa używana w kodzie.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

**Objaw:** podczas kopiowania danych **do** Azure Data Lake Store, jeśli aktywności kopiowania nie powiedzie się z powodu następującego błędu:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Główny powód:** istnieją 2 możliwe przyczyny:

1. `resourceGroupName` I/lub `subscriptionId` określony w usłudze Azure Data Lake Store połączone usługi są niepoprawne;
2. Użytkownik lub nazwy głównej usługi nie ma odpowiedniego uprawnienia.

**Rozwiązanie:**

1. Upewnij się, że `subscriptionId` i `resourceGroupName` określić w połączonej usłudze `typeProperties` są rzeczywiście te, które należy do Twojego konta usługi data lake.

2. Upewnij się, że co najmniej udzielasz "**czytnika**" rolę dla użytkownika lub nazwy głównej usługi na konta usługi data lake. Poniżej przedstawiono sposób było to:

    1. Przejdź do portalu Azure -> Twoje konto usługi Data Lake Store
    2. Kliknij przycisk "dostęp do formantu (IAM)" w bloku Data Lake Store
    3. Kliknij przycisk "Dodaj" w bloku z "dostęp do formantu (IAM)"
    4. Ustaw "Rola" jako "Czytnika", a następnie wybierz użytkownika lub nazwy głównej usługi, używanej do skopiowania udzielenia dostępu

3. Jeśli nie chcesz udzielić "Czytnika" roli użytkownika lub nazwy głównej usługi, jest alernative [jawnie określić lokalizację wykonywania](data-factory-data-movement-activities.md#global) w activitywith kopiowania lokalizacji usługi Data Lake Store. Przykład:

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
Aby określić zestaw danych do reprezentowania danych wejściowych w Data Lake Store, należy ustawić **typu** właściwości zestawu danych na **AzureDataLakeStore**. Ustaw **linkedServiceName** właściwości zestawu danych do nazwy usługi Data Lake Store połączonej usługi. Aby uzyskać pełną listę właściwości JSON sekcje i dostępne do definiowania zestawów danych, zobacz [Tworzenie zbiorów danych](data-factory-create-datasets.md) artykułu. Sekcje zestawu danych w formacie JSON, takich jak **struktury**, **dostępności**, i **zasad**, są podobne dla wszystkich typów obiektów dataset (Azure SQL database, obiektów blob platformy Azure i tabeli platformy Azure, na przykład). **TypeProperties** sekcja jest różne dla każdego typu zestawu danych i udostępnia informacje, takie jak lokalizacja i formatowanie danych w magazynie danych. 

**TypeProperties** sekcja dla zestawu danych typu **AzureDataLakeStore** zawiera następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **folderPath** |Ścieżka do kontenera i folderu w usłudze Data Lake Store. |Yes |
| **fileName** |Nazwa pliku w usłudze Azure Data Lake Store. **FileName** właściwość jest opcjonalna i z uwzględnieniem wielkości liter. <br/><br/>Jeśli określisz **fileName**, działania (w tym kopiowania) działa na określonego pliku.<br/><br/>Gdy **fileName** nie zostanie określony, kopia zawiera wszystkie pliki w **folderPath** w zestawie danych wejściowych.<br/><br/>Gdy **fileName** dla wyjściowego zestawu danych nie został określony i **preserveHierarchy** nie została określona w zbiornika działania nazwę wygenerowanego pliku ma format danych. _Identyfikator GUID_txt ". Na przykład: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nie |
| **partitionedBy** |**PartitionedBy** właściwość jest opcjonalna. Służy on do określ dynamiczne ścieżkę i nazwę pliku dla dane szeregów czasowych. Na przykład **folderPath** mogą nadać parametry dla każdej godziny danych. Aby uzyskać szczegółowe informacje i przykłady, zobacz [właściwości partitionedBy](#using-partitionedby-property). |Nie |
| **Format** | Obsługiwane są następujące typy format: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, i **ParquetFormat**. Ustaw **typu** właściwości w **format** do jednej z tych wartości. Aby uzyskać więcej informacji, zobacz [formacie tekstowym](data-factory-supported-file-and-compression-formats.md#text-format), [formatu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro format](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC format](data-factory-supported-file-and-compression-formats.md#orc-format), i [Parquet Format](data-factory-supported-file-and-compression-formats.md#parquet-format) sekcje w [formatów plików i ich kompresji, obsługiwanych przez usługi fabryka danych Azure](data-factory-supported-file-and-compression-formats.md) artykułu. <br><br> Jeśli chcesz skopiować pliki "jako — jest" między opartych na plikach magazynów (kopia binarnego), Pomiń `format` sekcji w obu definicji zestawu danych wejściowych i wyjściowych. |Nie |
| **Kompresja** | Określ typ i poziom kompresji danych. Obsługiwane typy to **GZip**, **Deflate**, **BZip2**, i **ZipDeflate**. Obsługiwane poziomy są **optymalna** i **najszybciej**. Aby uzyskać więcej informacji, zobacz [formatów plików i ich kompresji, obsługiwanych przez usługi fabryka danych Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Nie |

### <a name="the-partitionedby-property"></a>Właściwość partitionedBy
Można określić dynamiczne **folderPath** i **fileName** właściwości dane szeregów czasowych z **partitionedBy** właściwości, funkcje fabryki danych i zmienne systemowe. Aby uzyskać więcej informacji, zobacz [fabryki danych Azure — funkcje i zmienne systemu](data-factory-functions-variables.md) artykułu.


W poniższym przykładzie `{Slice}` zostanie zastąpiony wartością zmiennej systemowej fabryki danych `SliceStart` w formacie określonym (`yyyyMMddHH`). Nazwa `SliceStart` odwołuje się do czasu uruchomienia wycinka. `folderPath` Właściwość jest różne dla każdego wycinka jako w `wikidatagateway/wikisampledataout/2014100103` lub `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

W poniższym przykładzie, rok, miesiąc, dzień i czas `SliceStart` są wyodrębniane do oddzielnych zmiennych, które są używane przez `folderPath` i `fileName` właściwości:
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
Więcej szczegółów na zestawy danych szeregu czasowego, planowanie i wycinków, zobacz [zestawów danych w fabryce danych Azure](data-factory-create-datasets.md) i [fabryki danych planowania i wykonywania](data-factory-scheduling-and-execution.md) artykułów. 


## <a name="copy-activity-properties"></a>Właściwości działania kopiowania
Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [tworzenie potoków](data-factory-create-pipelines.md) artykułu. Właściwości, takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasady są dostępne dla wszystkich typów działań.

Właściwości dostępne w **typeProperties** sekcji działanie zależy od każdego typu działania. Dla działania kopiowania różnią się w zależności od typów źródeł i sink.

**AzureDataLakeStoreSource** obsługuje następującą właściwość w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **Cykliczne** |Wskazuje, czy dane są odczytywane rekursywnie z podfoldery lub tylko określonego folderu. |TRUE, False (wartość domyślna) |Nie |


**AzureDataLakeStoreSink** obsługuje następujące właściwości w **typeProperties** sekcji:

| Właściwość | Opis | Dozwolone wartości | Wymagane |
| --- | --- | --- | --- |
| **copyBehavior** |Określa zachowanie kopiowania. |<b>PreserveHierarchy</b>: zachowuje hierarchię plików w folderze docelowym. Względna ścieżka pliku źródłowego do folderu źródłowego jest taka sama jak ścieżka względna docelowego pliku do folderu docelowego.<br/><br/><b>FlattenHierarchy</b>: wszystkie pliki z folderu źródłowego są tworzone w pierwszy poziom folderu docelowego. Pliki docelowe są tworzone z automatycznie wygenerowaną nazwy.<br/><br/><b>MergeFiles</b>: scala wszystkie pliki z folderu źródłowego do jednego pliku. Jeśli zostanie określona nazwa pliku lub obiektu blob, nazwa scalony plik jest określona nazwa. W przeciwnym razie nazwa pliku zostanie wygenerowana automatycznie. |Nie |

### <a name="recursive-and-copybehavior-examples"></a>Przykłady cyklicznego i copyBehavior
W tej sekcji opisano efekty operacji kopiowania dla różnych kombinacji wartości cyklicznej i copyBehavior.

| Cykliczne | copyBehavior | Efekty |
| --- | --- | --- |
| true |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony z tej samej struktury jako źródło<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie File5 |
| true |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>element docelowy Folder1, utworzono o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 + plik3 + File4 + pliku 5 zawartości są scalane w jeden plik o nazwie generowanych automatycznie |
| false |preserveHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |
| false |flattenHierarchy |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie Plik1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nazwa wygenerowana automatycznie plik2<br/><br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |
| false |mergeFiles |Dla folderu źródłowego Folder1 o następującej strukturze:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Plik3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>folder docelowy Folder1 jest tworzony o następującej strukturze<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Plik1 + plik2 zawartości są scalane w jeden plik o nazwie wygenerowany automatycznie. Nazwa wygenerowana automatycznie Plik1<br/><br/>Subfolder1 plik3, File4 i File5 nie są odczytywane. |

## <a name="supported-file-and-compression-formats"></a>Obsługiwane formaty plików i kompresji
Aby uzyskać więcej informacji, zobacz [formaty plików i kompresji w fabryce danych Azure](data-factory-supported-file-and-compression-formats.md) artykułu.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Przykłady JSON kopiowania danych do i z usługi Data Lake Store
Poniższe przykłady zapewniają definicje JSON. Te przykładowe definicje umożliwia tworzenie potoku przy użyciu [portalu Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [programu Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), lub [programu Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). W przykładach pokazano, jak skopiować dane do i z magazynu usługi Data Lake Store i obiektów Blob platformy Azure. Jednak dane mogą być kopiowane _bezpośrednio_ z dowolnego źródła do żadnego z obsługiwanych sink. Aby uzyskać więcej informacji, zobacz sekcję "obsługiwane magazyny danych i formaty" w [przenoszenia danych za pomocą działania kopiowania](data-factory-data-movement-activities.md) artykułu.  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Przykład: Kopiowanie danych z magazynu obiektów Blob Azure do usługi Azure Data Lake Store
Przykład kodu w tej sekcji przedstawiono:

* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Połączonej usługi typu [AzureDataLakeStore](#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) i [AzureDataLakeStoreSink](#copy-activity-properties).

W przykładach pokazano, jak szeregów czasowych dane z magazynu obiektów Blob platformy Azure są kopiowane do usługi Data Lake Store co godzinę. 

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

**Azure Data Lake Store połączona usługa**

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
> Szczegółowe informacje dotyczące konfiguracji, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji.
>

**Wejściowy zestaw danych obiektów blob platformy Azure**

W poniższym przykładzie danych jest pobierana z obiektu blob nowe co godzinę (`"frequency": "Hour", "interval": 1`). Nazwa i ścieżka pliku folder dla obiektu blob dynamicznie są oceniane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc i dzień część czas rozpoczęcia. Nazwa pliku korzysta z części godzina czas rozpoczęcia. `"external": true` Ustawienie usługi fabryka danych informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

Poniższy przykładowy kod kopiuje danych do usługi Data Lake Store. Nowe dane są kopiowane do usługi Data Lake Store co godzinę.

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


**Działanie kopiowania w potoku źródła obiektów blob i ujście usługi Data Lake Store**

W poniższym przykładzie potoku zawiera działanie kopiowania, który jest skonfigurowany do używania danych wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane co godzinę. W definicji JSON potoku `source` ustawiono typ `BlobSource`i `sink` ustawiono typ `AzureDataLakeStoreSink`.

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

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Przykład: Kopiowanie danych z usługi Azure Data Lake Store do obiektów blob platformy Azure
Przykład kodu w tej sekcji przedstawiono:

* Połączonej usługi typu [AzureDataLakeStore](#linked-service-properties).
* Połączonej usługi typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Dane wejściowe [dataset](data-factory-create-datasets.md) typu [AzureDataLakeStore](#dataset-properties).
* Dane wyjściowe [dataset](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* A [potoku](data-factory-create-pipelines.md) z działania kopiowania, która używa [AzureDataLakeStoreSource](#copy-activity-properties) i [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Kod kopiuje dane szeregów czasowych z usługi Data Lake Store do obiektów blob platformy Azure co godzinę. 

**Azure Data Lake Store połączona usługa**

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
> Szczegółowe informacje dotyczące konfiguracji, zobacz [połączona usługa właściwości](#linked-service-properties) sekcji.
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
**Usługa Azure Data Lake wejściowy zestaw danych**

W tym przykładzie ustawienie `"external"` do `true` usługi fabryka danych informuje, że w tabeli zewnętrznej dla fabryki danych i nie jest generowany przez działanie w fabryce danych.

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

W poniższym przykładzie dane są zapisywane do nowego obiektu blob co godzinę (`"frequency": "Hour", "interval": 1`). Ścieżka folderu dla obiekt blob jest dynamicznie obliczane na podstawie czasu rozpoczęcia wycinek, który jest przetwarzana. Ścieżka folderu używa rok, miesiąc, dzień i godziny część czas rozpoczęcia.

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

**Działanie kopiowania w potoku ze źródłem Azure Data Lake Store i ujście obiektów blob**

W poniższym przykładzie potoku zawiera działanie kopiowania, który jest skonfigurowany do używania danych wejściowych i wyjściowych zestawów danych. Działanie kopiowania jest zaplanowane co godzinę. W definicji JSON potoku `source` ustawiono typ `AzureDataLakeStoreSource`i `sink` ustawiono typ `BlobSink`.

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

W definicji działania kopiowania można również mapować kolumn z zestawu źródła danych do kolumn w zestawie ujścia danych. Aby uzyskać więcej informacji, zobacz [mapowania kolumnach dataset w fabryce danych Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Informacje na temat czynniki wpływające na wydajność działania kopiowania i jak zoptymalizować go, zobacz [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md) artykułu.
