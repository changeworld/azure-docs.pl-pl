---
title: Jak używać magazynu obiektów (Blob) z dla systemu iOS — Azure | Dokumentacja firmy Microsoft
description: Przechowuj dane niestrukturalne w chmurze za pomocą Magazynu obiektów blob Azure.
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: 1ab799ef7eb9d7c591e76ab9180d4e3f4ba6ba59
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122745"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak używać magazynu obiektów Blob z poziomu systemu iOS

W tym artykule pokazano, jak realizować typowe scenariusze usługi Microsoft Azure Blob storage. Przykłady są zapisywane w języku Objective-C i użyj [biblioteki klienta usługi Azure Storage dla systemu iOS](https://github.com/Azure/azure-storage-ios). Omówione scenariusze obejmują przekazywanie, wyświetlanie listy, pobieranie i usuwanie obiektów blob. Aby uzyskać więcej informacji na temat obiektów blob, zobacz [następne kroki](#next-steps) sekcji. Możesz również pobrać [przykładową aplikację](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) szybko sprawdzić użycie usługi Azure Storage w aplikacji systemu iOS.

Aby dowiedzieć się więcej o usłudze Blob storage, zobacz [wprowadzenie do usługi Azure Blob storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importowanie biblioteki z systemem iOS usługi Azure Storage w aplikacji
Możesz zaimportować biblioteki usługi Azure Storage z systemem iOS do aplikacji za pomocą [CocoaPod magazynu Azure](https://cocoapods.org/pods/AZSClient) lub importując **Framework** pliku. CocoaPod jest zalecany, ponieważ ułatwia integrowanie było prostsze, ale importowania z pliku framework jest płynniejsza dla istniejącego projektu biblioteki.

Aby użyć tej biblioteki, potrzebne są następujące elementy:
- iOS 8+
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Jeśli nie zrobiono tego wcześniej [zainstalować zasobniki CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) na komputerze, otwierając okno terminalu i uruchom następujące polecenie
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Następnie w katalogu projektu (katalog zawierający plik xcodeproj) Utwórz nowy plik o nazwie _plik Podfile_(bez rozszerzenia). Dodaj następujące polecenie, aby _plik Podfile_ i Zapisz.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. W oknie terminalu przejdź do katalogu projektu, a następnie uruchom następujące polecenie

    ```shell    
    pod install
    ```

4. Jeśli Twoje xcodeproj jest otwarty w programie Xcode, zamknij go. W katalogu projektu otwórz plik nowo utworzonego projektu, które będą miały rozszerzenie .xcworkspace. Jest to plik, który będziesz pracować z dla teraz na.

## <a name="framework"></a>Framework
Inny sposób, aby używać biblioteki jest ręcznie tworzyć ramach:

1. Najpierw pobierz lub sklonuj [repozytorium azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Przejdź do *azure-storage-ios* -> *Lib* -> *biblioteki klienta usługi Azure Storage*, a następnie otwórz `AZSClient.xcodeproj` w środowisku Xcode.
3. W lewym górnym Xcode należy zmienić schemat active "Biblioteki klienta usługi Azure Storage" do "Framework".
4. Skompiluj projekt (⌘ + B). Spowoduje to utworzenie `AZSClient.framework` plik na pulpicie.

Następnie można zaimportować plik framework do aplikacji, wykonując następujące czynności:

1. Utwórz nowy projekt lub Otwórz istniejący projekt w programie Xcode.
2. Przeciąganie i upuszczanie `AZSClient.framework` do Nawigatora projektu Xcode.
3. Wybierz *skopiuj elementy w razie potrzeby*i kliknij pozycję *Zakończ*.
4. Kliknij projekt w obszarze nawigacji po lewej stronie, a następnie kliknij przycisk *ogólne* kartę w górnej części edytora projektu.
5. W obszarze *połączone struktury i biblioteki* sekcji i kliknij przycisk Dodaj (+).
6. Na liście bibliotek poprawiał Wyszukaj `libxml2.2.tbd` i dodaj go do projektu.

## <a name="import-the-library"></a>Importuj biblioteki 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Jeśli używasz języka Swift, konieczne będzie Utwórz nagłówek mostkowania i zaimportuj < AZSClient/AZSClient.h > Brak:

1. Utwórz plik nagłówka `Bridging-Header.h`i Dodaj instrukcję importu powyżej.
2. Przejdź do *ustawieniach kompilacji* kartę i wyszukaj *nagłówków mostkowania języka Objective-C*.
3. Kliknij dwukrotnie pole *nagłówków mostkowania języka Objective-C* i Dodaj ścieżkę do pliku nagłówka: `ProjectName/Bridging-Header.h`
4. Skompiluj projekt (⌘ + B), aby sprawdzić, czy nagłówek mostkowania zostało odebrane przez Xcode.
5. Rozpoczynanie korzystania z biblioteki bezpośrednio w dowolnym pliku Swift, nie ma potrzeby dla instrukcje importowania.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operacje asynchroniczne
> [!NOTE]
> Wszystkie metody, które wykonują żądanie względem usługi są operacji asynchronicznych. W przykładach kodu będzie okazać, że te metody są procedury obsługi zakończenia. Zostanie uruchomiony kod wewnątrz procedury obsługi zakończenia **po** ukończyć żądania. Kod po uruchomieniu procedury obsługi zakończenia będzie **podczas** odbywa się w żądaniu.
> 
> 

## <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w usłudze Azure Storage musi znajdować się w kontenerze. Poniższy przykład pokazuje, jak utworzyć kontener o nazwie *newcontainer*, w ramach konta magazynu, jeśli jeszcze nie istnieje. Wybierając nazwę kontenera, należy zachować ostrożność, reguł nazewnictwa wymienionych powyżej.

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

Możesz potwierdzić, że to działa, analizując [Microsoft Azure Storage Explorer](https://storageexplorer.com) i sprawdzając, czy *newcontainer* znajduje się na liście kontenerów na koncie magazynu.

## <a name="set-container-permissions"></a>Ustaw uprawnienia do kontenera
Kontener uprawnienia są skonfigurowane dla **prywatnej** dostępu domyślnie. Jednak kontenery zapewniają kilka różnych opcji dla dostępu do kontenera:

* **Prywatne**: Dane kontenera i obiektów blob mogą być odczytywane tylko właściciel konta.
* **Obiekt blob**: Danych obiektów blob w ramach tego kontenera, można je odczytać za pomocą żądania od użytkowników anonimowych, ale kontenera dane są niedostępne. Klienci nie można wyliczyć obiektów blob w kontenerze za pomocą żądania od użytkowników anonimowych.
* **kontener**: Dane kontenera i obiektów blob mogą być odczytywane za pośrednictwem żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze za pomocą żądania od użytkowników anonimowych, ale nie można wyliczyć kontenerów na koncie magazynu.

Poniższy przykład pokazuje jak utworzyć kontener z **kontenera** dostęp do uprawnień, które pozwoli na dostęp publiczny, tylko do odczytu dla wszystkich użytkowników w Internecie:

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
Jak wspomniano w sekcji pojęcia dotyczące usługi Blob, Blob Storage oferuje trzy różne typy obiektów blob: blokowe obiekty BLOB, uzupełnialne obiekty BLOB i stronicowe obiekty BLOB. Biblioteka platformy iOS usługi Azure Storage obsługuje wszystkie trzy typy obiektów blob. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Poniższy przykład pokazuje, jak przekazać blokowy obiekt blob z NSString. Jeśli obiekt blob z tej samej nazwie już istnieje w tym kontenerze, zawartość tego obiektu blob zostanie zastąpiony.

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

Możesz potwierdzić, że to działa, analizując [Microsoft Azure Storage Explorer](https://storageexplorer.com) i sprawdzając, czy kontener, *containerpublic*, zawiera obiekt blob, *sampleblob*. W tym przykładzie użyliśmy publicznego kontenera, dzięki czemu można również sprawdzić, czy ta aplikacja zadziałała, przechodząc do identyfikatora URI obiektów blob:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Oprócz przekazywanie blokowego obiektu blob z NSString, podobne metody istnieje NSData, NSInputStream lub pliku lokalnego.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze
Poniższy przykład ilustruje sposób wyświetlenia listy wszystkich obiektów blob w kontenerze. Podczas wykonywania tej operacji, należy je na uwadze następujące parametry:     

* **continuationToken** — reprezentuje token kontynuacji, gdzie ma się rozpocząć operację wyświetlania listy. Jeśli token nie zostanie podany, spowoduje wyświetlenie listy obiektów BLOB od samego początku. Mogą być wyświetlane dowolną liczbę obiektów blob, od zera aż Maksymalny zestaw. Nawet jeśli ta metoda zwraca zero wyniki, jeśli `results.continuationToken` nie jest zerowy, może istnieć więcej obiektów blob w usłudze, które nie zostały wymienione.
* **prefiks** — można określić prefiks używany dla listy obiektów blob. Zostaną wyświetlone tylko te obiekty BLOB, które zaczynają się od tego prefiksu.
* **useFlatBlobListing** — zgodnie z opisem w [nazewnictwo i odwoływanie się do kontenerów i obiektów blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) sekcji, chociaż usługi obiektów Blob jest schemat prostego magazynu można utworzyć wirtualnego hierarchii za pomocą nazw obiektów blob przy użyciu ścieżki informacje. Jednak bez płaskiej listy nie jest obecnie obsługiwane. Ta funkcja będzie dostępna wkrótce. Na razie ta wartość powinna być **tak**.
* **blobListingDetails** — można określić elementów do uwzględnienia podczas wyświetlania listy obiektów blob
  * _AZSBlobListingDetailsNone_: Wyświetlanie listy jedynie zatwierdzone obiektów blob, a nie zwracają metadane obiektu blob.
  * _AZSBlobListingDetailsSnapshots_: Wyświetlanie listy obiektów blob zatwierdzone i migawek obiektów blob.
  * _AZSBlobListingDetailsMetadata_: Pobierz metadane obiektu blob dla każdego obiektu blob są zwracane na liście.
  * _AZSBlobListingDetailsUncommittedBlobs_: Wyświetlanie listy obiektów blob zatwierdzone i niezatwierdzone.
  * _AZSBlobListingDetailsCopy_: Właściwości kopiowania są uwzględniane na liście.
  * _AZSBlobListingDetailsAll_: Lista wszystkich dostępnych zatwierdzone obiektów blob, niezatwierdzone obiektów blob i migawek i zwracać wszystkie stany metadanych i kopii tych obiektów blob.
* **maxResults** — maksymalna liczba wyników do zwrócenia dla tej operacji. Użyj wartości -1 nie ustawić limit.
* **completionHandler** — blok kodu do wykonania z wynikami Operacja tworzenia listy.

W tym przykładzie jest używany do metody pomocnika cyklicznie, wywołanie listy obiektów blob metoda, za każdym razem, gdy zwracany jest token kontynuacji.

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
Poniższy przykład pokazuje, jak pobrać obiekt blob do obiektu NSString.

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
Poniższy przykład pokazuje, jak można usunąć obiektu blob.

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

## <a name="delete-a-blob-container"></a>Usuń kontener obiektów blob
Poniższy przykład pokazuje, jak można usunąć kontenera.

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

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wyjaśniono sposób użycia usługi Blob Storage z systemem iOS, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat biblioteki z systemem iOS i usługi Storage.

* [Biblioteka klienta usługi Azure Storage dla systemu iOS](https://github.com/azure/azure-storage-ios)
* [Usługa Azure Storage z systemem iOS dokumentację referencyjną](https://azure.github.io/azure-storage-ios/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](https://blogs.msdn.com/b/windowsazurestorage)

Jeśli masz pytania dotyczące tej biblioteki możesz publikować na naszym [forum MSDN usługi Azure](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) lub [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Jeśli masz propozycje dotyczące funkcji dla usługi Azure Storage, opublikuj wpis na [opinii magazynu Azure](https://feedback.azure.com/forums/217298-storage/).

