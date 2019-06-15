---
title: Tworzenie i używanie plików zasobów — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć pliki zasobów usługi Azure Batch z różnych źródeł danych wejściowych.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 113faffb0ebac50a67c96ce21e0ee2c1564bb4fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65405639"
---
# <a name="creating-and-using-resource-files"></a>Tworzenie i używanie plików zasobów

Zadanie usługi Azure Batch często wymaga pewnego rodzaju dane można przetworzyć. Pliki zasobów są sposób dane do usługi Batch maszyny wirtualnej (VM) za pomocą zadania. Pliki zasobów obsługi wszystkich typów zadań: zadania, zostaną uruchomione zadania, przygotowanie zadania, zadania podrzędne zwolnienia zadania itp. W tym artykule opisano kilka typowych metod na temat sposobu tworzenia plików zasobów i umieścić je na maszynie Wirtualnej.  

Pliki zasobów są mechanizm umieszczanie danych na Maszynę wirtualną w usłudze Batch, ale typ danych i sposobu ich wykorzystywania jest elastyczny. Istnieją jednak pewne typowe przypadki użycia:

1. Inicjowanie obsługi wspólnej plików na każdej maszynie Wirtualnej przy użyciu plików zasobów dla zadania rozpoczęcia
1. Aprowizowanie dane wejściowe do przetworzenia przez zadania

Wspólne pliki można na przykład plików na zadanie podrzędne uruchamiania używane do instalowania aplikacji uruchamianych przez podzadania. Dane wejściowe można obrazu raw lub dane wideo lub żadnych informacji do przetworzenia przy użyciu usługi Batch.

## <a name="types-of-resource-files"></a>Typy plików zasobów

Istnieje kilka różnych opcji można wygenerować plików źródłowych. Proces tworzenia plików zasobów różni się w zależności od tego, gdzie są przechowywane oryginalnych danych.

Opcje tworzenia pliku zasobu:

- [Adres URL kontenera magazynu](#storage-container-url): Generuje plik zasobów z dowolnego kontenera magazynu na platformie Azure
- [Nazwa kontenera magazynu](#storage-container-name): Generuje plik zasobów na podstawie nazwy kontenera na koncie magazynu platformy Azure, połączone z usługi Batch
- [Punkt końcowy sieci Web](#web-endpoint): Generuje plik zasobów z dowolnym prawidłowym adresem URL protokołu HTTP

### <a name="storage-container-url"></a>Adres URL kontenera magazynu

Przy użyciu adresu URL kontenera magazynu oznacza, że masz dostęp plików w dowolnym kontenerze magazynu na platformie Azure z odpowiednimi uprawnieniami.

W tym C# przykład już zostały przekazane pliki do kontenera usługi Azure storage jako magazynu obiektów blob. Aby uzyskać dostęp do danych, potrzebne do utworzenia pliku zasobów, najpierw należy uzyskać dostęp do kontenera magazynu.

Tworzenie sygnatury dostępu współdzielonego (SAS) identyfikator URI przy użyciu odpowiednich uprawnień dostępu do kontenera magazynu. Ustaw czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego. W tym przypadku nie czas rozpoczęcia jest określony, więc sygnatury dostępu Współdzielonego zaczyna obowiązywać natychmiast i wygasa po dwóch godzinach od jego wygenerowaniu.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> Dostępu do kontenera, musisz mieć obie `Read` i `List` uprawnienia, natomiast dzięki dostępowi do obiektu blob, wystarczy `Read` uprawnień.

Po skonfigurowaniu uprawnień utworzyć token sygnatury dostępu Współdzielonego i sformatować adres URL SAS do uzyskiwania dostępu do kontenera magazynu. Za pomocą sformatowany adres URL sygnatury dostępu Współdzielonego kontenera magazynu wygenerować plik zasobu o [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternatywę dla generowania adresu URL sygnatury dostępu Współdzielonego jest umożliwienie anonimowe, publiczny dostęp do odczytu do kontenera i jego obiektów blob w usłudze Azure Blob storage. Dzięki temu można przyznać dostęp tylko do odczytu do tych zasobów, bez udostępniania klucz konta, a także bez konieczności sygnatury dostępu Współdzielonego. Publiczny dostęp do odczytu jest zazwyczaj używany w scenariuszach, w której chcesz niektórych obiektów blob, które zawsze będą dostępne dla anonimowy dostęp do odczytu. Jeśli w tym scenariuszu pasujące do rozwiązania, zobacz [anonimowy dostęp do obiektów blob](../storage/blobs/storage-manage-access-to-resources.md) artykuł, aby dowiedzieć się więcej o zarządzaniu dostępem do danych obiektów blob.

### <a name="storage-container-name"></a>Nazwa kontenera magazynu

Zamiast konfigurowania oraz tworzenia adresu URL sygnatury dostępu Współdzielonego, można użyć nazwa kontenera usługi Azure storage, dostęp do danych obiektów blob. Kontener magazynu używane musi koncie magazynu platformy Azure, który jest połączony z kontem usługi Batch, znane jako konto autostorage. Przy użyciu nazwy kontenera magazynu konta autostorage pozwala pominąć konfigurowanie i Tworzenie adresu URL sygnatury dostępu Współdzielonego dostępu do kontenera magazynu.

W tym przykładzie przyjęto założenie, że dane, które ma być używany do tworzenia plików zasobów jest już konta usługi Azure Storage, połączony z Twoim kontem usługi Batch. Jeśli nie masz konta autostorage, zobacz kroki w [Utwórz konto usługi Batch](batch-account-create-portal.md) Aby uzyskać szczegółowe informacje na temat sposobu tworzenia i łączenia konta usługi.

Za pomocą połączonego konta magazynu, nie trzeba tworzyć i konfigurować adres URL sygnatury dostępu Współdzielonego kontenera magazynu. Zamiast tego należy podać nazwę kontenera magazynu w połączonym koncie magazynu.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Punkt końcowy sieci Web

Dane, które nie są przekazywane do usługi Azure Storage, nadal może służyć do tworzenia plików zasobów. Możesz określić każdy prawidłowy adres HTTP URL zawierających dane wejściowe. Adres URL jest dostarczany do interfejsu API usługi Batch, a następnie dane jest używane do utworzenia pliku zasobów.

W następującym C# przykład danych wejściowych znajduje się na fikcyjnej punktu końcowego usługi GitHub. Interfejs API pobiera plik z prawidłową internetowego punktu końcowego i generuje plik zasobów do użycia przez zadanie. Żadne poświadczenia nie są wymagane dla tego scenariusza.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Porady i sugestie

Każde zadanie usługi Azure Batch używa plików inaczej, dlatego Batch ma opcjami dostępnymi na potrzeby zarządzania plikami na zadania. Następujące scenariusze nie są przeznaczone do wyczerpująca, ale zamiast tego obejmują kilka typowych sytuacjach i przedstawić zalecenia w zakresie.

### <a name="many-resource-files"></a>Wiele plików zasobów

Zadania usługi Batch mogą zawierać kilka zadań, które używają tego samego, wspólnego plików. Jeśli pliki typowych zadań są współużytkowane przez wiele zadań, za pomocą pakietu aplikacji ma zawierać pliki, zamiast korzystać z plików zasobów może być lepszym rozwiązaniem. Pakiety aplikacji zapewniają optymalizacji dla szybkość pobierania. Ponadto dane w pakiety aplikacji są buforowane między zadaniami, dlatego jeśli pliki zadanie nie zmieniają się często, pakiety aplikacji może być dobrym rozwiązaniem dla danego rozwiązania. Za pomocą pakietów aplikacji nie muszą ręcznie zarządzać kilka plików zasobów lub generowania adresów URL sygnatury dostępu Współdzielonego, aby uzyskać dostęp do plików w usłudze Azure Storage. Batch działają w tle z usługi Azure Storage do przechowywania i wdrażania pakietów aplikacji do węzłów obliczeniowych.

Jeśli każde zadanie zawiera wiele plików unikatowe dla tego zadania, pliki zasobów są głównie prawdopodobnie najlepszym rozwiązaniem. Zadania, które często używają unikatowych plików muszą zostać zaktualizowane lub zastąpiony, który nie jest łatwe do zawartości pakietów aplikacji. Pliki zasobów zapewniają większą elastyczność podczas aktualizowania, dodawania i edytowania pojedynczych plików.

### <a name="number-of-resource-files-per-task"></a>Liczba plików zasobów dla każdego zadania

W przypadku kilku plików zasobów kilkuset określony dla zadania usługi Batch może odrzucić zadanie dla niej zbyt dużego. Najlepiej Zachowywanie zadań małych, minimalizując liczbę plików zasobów w samo zadanie.

Jeśli nie ma możliwości aby zminimalizować liczbę plików zadanie wymaga, zadania można zoptymalizować, tworząc plik pojedynczy zasób, który odwołuje się do kontenera magazynu plików zasobów. W tym celu należy umieścić pliki zasobów w kontenerze usługi Azure Storage i używać różnych trybach "Container" plików zasobów. Użyj opcji prefiks obiektu blob, aby określić kolekcji plików do pobrania dla zadań podrzędnych.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [pakiety aplikacji](batch-application-packages.md) jako alternatywę do plików zasobów.
- Aby uzyskać więcej informacji na temat plików zasobów przy użyciu kontenerów, zobacz [obciążeń kontenerów](batch-docker-container-workloads.md).
- Aby dowiedzieć się, jak zebrać i zapisać dane wyjściowe z zadania, zobacz [utrwalanie danych wyjściowych zadań i zadań podrzędnych](batch-task-output.md).
- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
