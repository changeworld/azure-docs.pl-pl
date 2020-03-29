---
title: Jak używać magazynu obiektów (Blob) z systemu iOS — Azure | Dokumenty firmy Microsoft
description: Przechowuj dane niestrukturalne w chmurze za pomocą Magazynu obiektów blob Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726406"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak korzystać z magazynu obiektów Blob z systemu iOS

W tym artykule pokazano, jak wykonywać typowe scenariusze przy użyciu magazynu obiektów Blob platformy Microsoft Azure. Przykłady są zapisywane w języku Objective-C i używają [biblioteki klienta usługi Azure Storage dla systemu iOS.](https://github.com/Azure/azure-storage-ios) Scenariusze obejmują przekazywanie, wystawianie, pobieranie i usuwanie obiektów blob. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [sekcję Następne kroki.](#next-steps) Możesz również pobrać [przykładową aplikację,](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) aby szybko wyświetlić użycie usługi Azure Storage w aplikacji dla systemu iOS.

Aby dowiedzieć się więcej o magazynie obiektów Blob, zobacz [Wprowadzenie do magazynu obiektów Blob platformy Azure.](storage-blobs-introduction.md)

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importowanie biblioteki systemu iOS usługi Azure Storage do aplikacji

Bibliotekę systemu iOS usługi Azure Storage można zaimportować do aplikacji za pomocą [cocoaPod usługi Azure Storage](https://cocoapods.org/pods/AZSClient) lub importując plik **Framework.** CocoaPod jest zalecanym sposobem, ponieważ ułatwia integrację biblioteki, jednak importowanie z pliku ramowego jest mniej inwazyjne dla istniejącego projektu.

Aby korzystać z tej biblioteki, potrzebne są następujące elementy:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) na swoim komputerze, otwierając okno terminala i uruchamiając następujące polecenie

    ```shell
    sudo gem install cocoapods
    ```

2. Następnie w katalogu projektu (katalogu zawierającym plik .xcodeproj) utwórz nowy plik o nazwie _Podfile_(bez rozszerzenia pliku). Dodaj następujące elementy do _podfile_ i zapisz.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. W oknie terminala przejdź do katalogu projektu i uruchom następujące polecenie

    ```shell
    pod install
    ```

4. Jeśli plik .xcodeproj jest otwarty w kodzie Xcode, zamknij go. W katalogu projektu otwórz nowo utworzony plik projektu, który będzie miał rozszerzenie .xcworkspace. Jest to plik, od którego będziesz teraz pracować.

## <a name="framework"></a>Framework

Innym sposobem użycia biblioteki jest ręczne tworzenie struktury:

1. Najpierw pobierz lub sklonuj [repozytorium azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Przejdź do*Lib* -> *biblioteki klienta*usługi Azure Azure Storage `AZSClient.xcodeproj` *w usłudze Azure-storage i* -> otwórz w usłudze Xcode.
3. W lewym górnym rogu xcode zmień aktywny schemat z "Biblioteka klienta usługi Azure Storage" na "Framework".
4. Zbuduj projekt (♠+B). Spowoduje to `AZSClient.framework` utworzenie pliku na pulpicie.

Następnie można zaimportować plik framework do aplikacji, wykonując następujące czynności:

1. Utwórz nowy projekt lub otwórz istniejący projekt w programie Xcode.
2. Przeciągnij i `AZSClient.framework` upuść go do nawigatora projektu Xcode.
3. W razie potrzeby wybierz *pozycję Kopiuj elementy*i kliknij przycisk *Zakończ*.
4. Kliknij projekt w nawigacji po lewej stronie i kliknij kartę *Ogólne* u góry edytora projektów.
5. W sekcji *Połączone struktury i biblioteki* kliknij przycisk Dodaj (+).
6. Na liście już dostarczonych bibliotek `libxml2.2.tbd` wyszukaj i dodaj je do projektu.

## <a name="import-the-library"></a>Importowanie biblioteki

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Jeśli używasz Swift, musisz utworzyć nagłówek pomostowy i \<importować AZSClient/AZSClient.h> tam:

1. Utwórz plik `Bridging-Header.h`nagłówka i dodaj powyższą instrukcję importu.
2. Przejdź do karty *Ustawienia kompilacji* i wyszukaj *nagłówek mostkowania Objective-C*.
3. Kliknij dwukrotnie pole *nagłówka mostkowego Objective-C* i dodaj ścieżkę do pliku nagłówka:`ProjectName/Bridging-Header.h`
4. Zbuduj projekt (♠+B), aby sprawdzić, czy nagłówek pomostowy został pobrany przez Xcode.
5. Rozpocznij korzystanie z biblioteki bezpośrednio w dowolnym pliku Swift, nie ma potrzeby importowania instrukcji.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operacje asynchroniczne

> [!NOTE]
> Wszystkie metody, które wykonują żądanie względem usługi są operacje asynchroniczne. W przykładach kodu okaże się, że te metody mają program obsługi zakończenia. Kod wewnątrz programu obsługi zakończenia zostanie uruchomiony **po** zakończeniu żądania. Kod po zakończeniu obsługi zostanie uruchomiony **podczas** żądania jest wprowadzana.

## <a name="create-a-container"></a>Tworzenie kontenera

Każdy obiekt blob w usłudze Azure Storage musi znajdować się w kontenerze. W poniższym przykładzie pokazano, jak utworzyć kontener, o nazwie *newcontainer*, na koncie magazynu, jeśli jeszcze nie istnieje. Wybierając nazwę kontenera, należy pamiętać o regułach nazewnictwa wymienionych powyżej.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Można potwierdzić, że to działa, patrząc na [Microsoft Azure Storage Explorer](https://storageexplorer.com) i sprawdź, czy *nowykontener* znajduje się na liście kontenerów dla konta magazynu.

## <a name="set-container-permissions"></a>Ustawianie uprawnień kontenera

Uprawnienia kontenera są domyślnie skonfigurowane dla dostępu **prywatnego.** Jednak kontenery zapewniają kilka różnych opcji dostępu do kontenera:

- **Prywatne**: Dane kontenera i obiektu blob mogą być odczytywane tylko przez właściciela konta.
- **Obiekt blob:** dane obiektów blob w tym kontenerze mogą być odczytywane za pośrednictwem żądania anonimowego, ale dane kontenera nie są dostępne. Klienci nie mogą wyliczyć obiektów blob w kontenerze za pośrednictwem żądania anonimowego.
- **Kontener:** Dane kontenera i obiektu blob można odczytać za pośrednictwem anonimowego żądania. Klienci mogą wyliczać obiekty BLOB w kontenerze za pośrednictwem żądania anonimowego, ale nie można wyliczyć kontenerów w ramach konta magazynu.

W poniższym przykładzie pokazano, jak utworzyć kontener z uprawnieniami dostępu do **kontenera,** które umożliwią publiczny dostęp tylko do odczytu dla wszystkich użytkowników w Internecie:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Jak wspomniano w sekcji pojęcia o usługach obiektów Blob, magazyn obiektów Blob oferuje trzy różne typy obiektów blob: blokowe obiekty blob, dołączanie obiektów blob i stronicowe obiekty blob. Biblioteka usługi Azure Storage dla systemu iOS obsługuje wszystkie trzy typy obiektów blob. W większości przypadków zalecane jest użycie blokowych obiektów blob.

W poniższym przykładzie pokazano, jak przekazać blok obiektu blob z NSString. Jeśli obiekt blob o tej samej nazwie już istnieje w tym kontenerze, zawartość tego obiektu blob zostanie zastąpiona.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Można potwierdzić, że to działa, patrząc na [Microsoft Azure Storage Explorer](https://storageexplorer.com) i sprawdzając, czy kontener, *containerpublic*, zawiera obiekt blob, *sampleblob*. W tym przykładzie użyliśmy kontenera publicznego, dzięki czemu można również sprawdzić, czy ta aplikacja działa, przechodząc do identyfikatora URI obiektów blob:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Oprócz przekazywania bloku obiektu blob z NSString, podobne metody istnieją dla NSData, NSInputStream lub pliku lokalnego.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

W poniższym przykładzie pokazano, jak wyświetlić listę wszystkich obiektów blob w kontenerze. Podczas wykonywania tej operacji należy pamiętać o następujących parametrach:

- **continuationToken** — token kontynuacji reprezentuje, gdzie należy rozpocząć operację aukcji. Jeśli nie token jest podana, będzie lista obiektów blob od początku. Można wymienić dowolną liczbę obiektów blob, od zera do ustawionego maksimum. Nawet jeśli ta metoda zwraca `results.continuationToken` zero wyników, jeśli nie jest zerowa, może być więcej obiektów blob w usłudze, które nie zostały wymienione.
- **prefiks** — można określić prefiks do użycia dla listy obiektów blob. Zostaną wyświetlone tylko obiekty BLOB, które zaczynają się od tego prefiksu.
- **useFlatBlobListing** — Jak wspomniano w [sekcji Nazewnictwo i odwoływanie się do kontenerów i obiektów blob,](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) chociaż usługa obiektów Blob jest schematem magazynu płaskiego, można utworzyć hierarchię wirtualną, nawołując obiekty BLOB do informacji o ścieżce. Jednak aukcja niezrydowa nie jest obecnie obsługiwana. Ta funkcja już wkrótce. Na razie ta wartość powinna być **TAK**.
- **blobListingDetails** — można określić, które elementy mają być uwzględniane podczas wyświetlania obiektów blob
  - _AZSBlobListingDetailsNone_: Lista tylko zatwierdzonych obiektów blob i nie zwracać metadanych obiektów blob.
  - _AZSBlobListingDetailsSnapshots_: Lista zatwierdzonych obiektów blob i migawek obiektów blob.
  - _AZSBlobListingDetailsMetadata_: Pobierz metadane obiektów blob dla każdego obiektu blob zwróconego w aukcji.
  - _AZSBlobListingDetailsUncommittedBlobs_: Lista zatwierdzonych i niezaangażowanych obiektów blob.
  - _AZSBlobListingDetailsCopy_: Uwzględnij właściwości kopiowania w aukcji.
  - _AZSBlobListingDetailsAll_: Lista wszystkich dostępnych zatwierdzonych obiektów blob, niezaangażowanych obiektów blob i migawek i zwraca wszystkie metadane i stan kopiowania dla tych obiektów blob.
- **maxResults** - Maksymalna liczba wyników do zwrócenia dla tej operacji. Użyj -1, aby nie ustawić limitu.
- **completionHandler** — blok kodu do wykonania z wynikami operacji aukcji.

W tym przykładzie metoda pomocnika jest używana do cyklicznego wywoływania metody obiektów blob listy za każdym razem, gdy zwracany jest token kontynuacji.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

W poniższym przykładzie pokazano, jak pobrać obiekt blob do obiektu NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

W poniższym przykładzie pokazano, jak usunąć obiekt blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Usuwanie kontenera obiektu blob

W poniższym przykładzie pokazano, jak usunąć kontener.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z magazynu obiektów Blob z systemu iOS, skorzystaj z tych łączy, aby dowiedzieć się więcej o bibliotece systemu iOS i usłudze magazynu.

- [Biblioteka klienta usługi Azure Storage dla systemu iOS](https://github.com/azure/azure-storage-ios)
- [Dokumentacja referencyjna usługi Azure Storage dla systemu iOS](https://azure.github.io/azure-storage-ios/)
- [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage)

Jeśli masz pytania dotyczące tej biblioteki, możesz opublikować post na naszym [forum MSDN Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Jeśli masz sugestie dotyczące funkcji usługi Azure Storage, opublikuj opinię o [usłudze Azure Storage.](https://feedback.azure.com/forums/217298-storage/)
