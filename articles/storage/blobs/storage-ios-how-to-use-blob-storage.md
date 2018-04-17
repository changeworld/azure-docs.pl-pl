---
title: Jak używać magazynu obiektów (Blob) z systemem iOS - Azure | Dokumentacja firmy Microsoft
description: Przechowuj dane niestrukturalne w chmurze za pomocą Magazynu obiektów blob Azure.
services: storage
documentationcenter: ios
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: a15ba7409b4c5f75729b1b40cd2f333c44ae0368
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak używać magazynu obiektów Blob z systemem iOS

W tym artykule przedstawiono sposób wykonywania typowych scenariuszy przy użyciu magazynu obiektów Blob Microsoft Azure. Przykłady są napisane w języku Objective C i użyj [biblioteki klienta magazynu Azure dla systemu iOS](https://github.com/Azure/azure-storage-ios). Omówione scenariusze obejmują przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów blob. Aby uzyskać więcej informacji dotyczących obiektów blob, zobacz [następne kroki](#next-steps) sekcji. Możesz również pobrać [Przykładowa aplikacja](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) szybko wyświetlić korzystanie z usługi Azure Storage w aplikacji systemu iOS.

## <a name="what-is-blob-storage"></a>Co to jest magazyn obiektów Blob?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importowanie biblioteki z systemem iOS magazynu Azure do aplikacji
Możesz zaimportować biblioteki z systemem iOS magazynu Azure do aplikacji przy użyciu [CocoaPod magazynu Azure](https://cocoapods.org/pods/AZSClient) lub importując **Framework** pliku. CocoaPod jest zalecany, ponieważ ułatwia integrowanie łatwiejsze, jednak importowania z pliku framework jest ta opcja jest mniej pożądana dla istniejącego projektu biblioteki.

Aby użyć tej biblioteki, są potrzebne następujące elementy:
- System iOS 8 i nowsze
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Jeśli użytkownik jeszcze tego nie zrobiono, [instalacji programu CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) na komputerze, otwierając okno terminalu i uruchom następujące polecenie
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Następnie w katalogu projektu (katalog zawierający plik .xcodeproj), Utwórz nowy plik o nazwie _Podfile_(bez rozszerzenia pliku). Dodaj następujący kod do _Podfile_ i Zapisz.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. W oknie terminalu przejdź do katalogu projektu i uruchom następujące polecenie

    ```shell    
    pod install
    ```

4. Jeśli Twoje .xcodeproj jest otwarty w programie Xcode, należy go zamknąć. W katalogu projektu otwórz plik nowo utworzonego projektu, który ma rozszerzenie .xcworkspace. Jest to plik, który będzie skorzystać z dla teraz.

## <a name="framework"></a>Framework
Innym sposobem korzystania z biblioteki jest skompilować platformę ręcznie:

1. Najpierw należy pobrać lub sklonować [repozytorium azure magazynu ios](https://github.com/azure/azure-storage-ios).
2. Przejdź do *systemu ios magazynu azure* -> *Lib* -> *biblioteki klienta magazynu Azure*, a następnie otwórz `AZSClient.xcodeproj` w środowisku Xcode.
3. W lewym górnym Xcode należy zmienić schemat active "Biblioteki klienta magazynu Azure" do "Framework".
4. Kompilacji projektu (⌘ + B). Spowoduje to utworzenie `AZSClient.framework` plik na pulpicie.

Następnie można zaimportować plik framework w aplikacji, wykonując następujące czynności:

1. Tworzenie nowego projektu lub otworzenie istniejącego projektu w środowisku Xcode.
2. Przeciągnij i upuść `AZSClient.framework` do Nawigatora projektu Xcode.
3. Wybierz *skopiować elementy w razie potrzeby*i kliknij przycisk *Zakończ*.
4. Kliknij projekt w obszarze nawigacji po lewej stronie, a następnie kliknij przycisk *ogólne* kartę w górnej części edytora projektu.
5. W obszarze *połączone struktury i biblioteki* sekcji, kliknij przycisk Dodaj (+).
6. Na liście bibliotek już dostarczona, wyszukaj `libxml2.2.tbd` i dodaj go do projektu.

## <a name="import-the-library"></a>Importuj biblioteki 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Jeśli używasz Swift, konieczne będzie Utwórz nagłówek mostkowania i zaimportuj < AZSClient/AZSClient.h > istnieje:

1. Utwórz plik nagłówka `Bridging-Header.h`i Dodaj powyżej instrukcję import.
2. Przejdź do *ustawieniach kompilacji* , a następnie wyszukaj *nagłówków mostkowania języka Objective-C*.
3. Kliknij dwukrotnie w zakresie *nagłówków mostkowania języka Objective-C* i Dodaj ścieżkę do pliku nagłówka: `ProjectName/Bridging-Header.h`
4. Kompilacji projektu (⌘ + B), aby sprawdzić, czy nagłówek mostkowania została pobrana przez Xcode.
5. Rozpoczynanie korzystania z biblioteki bezpośrednio w żadnym pliku Swift, nie istnieje potrzeba dla instrukcje importu.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operacje asynchroniczne
> [!NOTE]
> Wszystkie metody, które wykonują żądania z usługą są operacji asynchronicznych. W przykładach kodu znajdziesz, że te metody ma obsługi zakończenia. Uruchomi kodu wewnątrz obsługi uzupełniania **po** ukończyć żądania. Kod po zakończeniu obsługi zostanie uruchomiony **podczas** odbywa się żądanie.
> 
> 

## <a name="create-a-container"></a>Tworzenie kontenera
Każdy obiekt blob w magazynie Azure musi znajdować się w kontenerze. Poniższy przykład przedstawia sposób tworzenia kontenera o nazwie *newcontainer*, na Twoim koncie magazynu, jeśli jeszcze nie istnieje. W przypadku wybrania nazwy użytkownika kontenera, należy zachować ostrożność, reguły nazewnictwa wymienionych powyżej.

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

Można potwierdzić, że to działa, analizując [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com) i sprawdzić, czy *newcontainer* znajduje się na liście kontenery konta magazynu.

## <a name="set-container-permissions"></a>Ustaw uprawnienia do kontenera
Kontener uprawnienia są skonfigurowane dla **prywatnej** dostępu domyślnie. Kontenery zapewnia jednak kilka różnych opcji dla dostępu do kontenera:

* **Prywatne**: obiektów blob i kontenera danych mogą być odczytywane tylko właściciel konta.
* **Obiekt blob**: dane obiektów Blob w tym kontenerze mogą być odczytywane za pomocą żądania od użytkowników anonimowych, ale nie są dostępne dane kontenera. Klienci nie można wyliczyć obiektów blob w kontenerze, za pomocą żądania od użytkowników anonimowych.
* **Kontener**: obiektów blob i kontenera danych mogą być odczytywane za pomocą żądania od użytkowników anonimowych. Klientów można wyliczyć obiektów blob w kontenerze, za pomocą żądania od użytkowników anonimowych, ale nie można wyliczyć kontenery w ramach konta magazynu.

Poniższy przykład przedstawia sposób tworzenia kontenera o **kontenera** dostępu uprawnienia, które pozwoli na dostęp publiczny, tylko do odczytu dla wszystkich użytkowników w Internecie:

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
Jak wspomniano w [pojęcia dotyczące usługi Blob](#blob-service-concepts) sekcji magazynu obiektów Blob udostępnia trzy typy obiektów blob: blokowe obiekty BLOB, uzupełnialnych obiektów blob i stronicowe. Biblioteka systemu iOS usługi Azure Storage obsługuje wszystkie trzy typy obiektów blob. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Poniższy przykład pokazuje, jak przekazać obiekt blob blokowy z NSString. Jeśli obiektu blob o takiej samej nazwie już istnieje w tym kontenerze, zawartość tego obiektu blob zostaną zastąpione.

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

Można potwierdzić, że to działa, analizując [Eksploratora usługi Microsoft Azure Storage](http://storageexplorer.com) i sprawdzić, czy kontener, *containerpublic*, zawiera obiekt blob, *sampleblob*. W tym przykładzie użyliśmy publicznego kontenera, można również sprawdzić, czy tej aplikacji zadziałała, przechodząc do obiektów blob identyfikatora URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Oprócz przekazywania blokowych obiektów blob z NSString, istnieją podobne metody NSData, NSInputStream lub pliku lokalnego.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze
Poniższy przykład przedstawia sposób wyświetlania wszystkich obiektów blob w kontenerze. Po wykonaniu tej operacji można mając na uwadze następujące parametry:     

* **continuationToken** -reprezentuje token kontynuacji gdzie powinien rozpocząć operację wyświetlania listy. Jeśli token nie zostanie podany, będzie zawierała listę obiektów BLOB od początku. Dowolną liczbę obiektów blob może być wymieniona od zera do maksymalnej zestaw. Nawet jeśli ta metoda zwraca wyników, jeśli `results.continuationToken` nie jest tokenem nil, może być więcej obiektów blob w usłudze, które nie zostały wymienione.
* **prefiks** — można określić prefiks do użycia na potrzeby listę obiektów blob. Zostaną wyświetlone tylko te obiekty BLOB, zaczynające się z tym prefiksem.
* **useFlatBlobListing** — jak wspomniano w [nazewnictwa i odwołuje się do kontenerów i obiektów blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) sekcji, mimo że usługa Blob jest schemat płaskiej magazynu można utworzyć wirtualnego hierarchii za pomocą nazw obiektów blob ze ścieżką informacje. Jednak lista-flat nie jest obecnie obsługiwane. Ta funkcja będzie dostępna wkrótce. Obecnie ta wartość powinna być **tak**.
* **blobListingDetails** — można określić elementów do uwzględnienia podczas wyświetlania obiektów blob
  * _AZSBlobListingDetailsNone_: wyświetlanie tylko zatwierdzone obiektów blob, a nie zwracać metadane obiektu blob.
  * _AZSBlobListingDetailsSnapshots_: wyświetlanie obiektów blob zatwierdzone i migawki obiektu blob.
  * _AZSBlobListingDetailsMetadata_: zwrócony pobrać metadane obiektu blob dla każdego obiektu blob na liście.
  * _AZSBlobListingDetailsUncommittedBlobs_: wyświetlanie zatwierdzonej i niezatwierdzone obiektów blob.
  * _AZSBlobListingDetailsCopy_: obejmują kopiowania właściwości na liście.
  * _AZSBlobListingDetailsAll_: wyświetlić listę wszystkich dostępnych obiektów blob zatwierdzone, niezatwierdzone obiekty BLOB i migawki, a następnie wróć wszystkich metadanych i kopia stanu dla tych obiektów blob.
* **maxResults** — maksymalna liczba wyników do zwrócenia do wykonania tej operacji. Użyj wartości -1 nie ustawić limit.
* **completionHandler** — blok kodu do wykonania z wynikami operację wyświetlania listy.

W tym przykładzie jest używane do metody pomocnika rekursywnie wywołanie listy obiektów blob — metoda, za każdym razem, gdy token kontynuacji jest zwracany.

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
Poniższy przykład przedstawia sposób pobierania obiektu blob do obiektu NSString.

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

## <a name="delete-a-blob-container"></a>Usunięcie kontenera obiektów blob
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
Teraz, kiedy znasz, jak używać magazynu obiektów Blob z systemem iOS, skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat biblioteki z systemem iOS i usługi magazynu.

* [Biblioteka klienta usługi Azure Storage dla systemu iOS](https://github.com/azure/azure-storage-ios)
* [IOS magazynu Azure dokumentacji](http://azure.github.io/azure-storage-ios/)
* [Interfejs API REST usług Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog zespołu odpowiedzialnego za usługę Azure Storage](http://blogs.msdn.com/b/windowsazurestorage)

Jeśli masz pytania dotyczące tej biblioteki, możesz do wysłania do naszej [forum MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) lub [przepełnienie stosu](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Jeśli masz sugestie funkcji usługi Azure Storage, Opublikuj do [opinii magazynu Azure](https://feedback.azure.com/forums/217298-storage/).

