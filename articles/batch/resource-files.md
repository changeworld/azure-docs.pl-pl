---
title: Tworzenie i używanie plików zasobów — usługa Azure Batch
description: Dowiedz się, jak tworzyć pliki zasobów usługi Batch z różnych źródeł wejściowych. W tym artykule opisano kilka typowych metod tworzenia i umieszczania ich na maszynie wirtualnej.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531145"
---
# <a name="creating-and-using-resource-files"></a>Tworzenie i używanie plików zasobów

Zadanie usługi Azure Batch często wymaga jakiejś formy danych do przetworzenia. Pliki zasobów są sposobem dostarczania tych danych do maszyny wirtualnej batch (VM) za pośrednictwem zadania. Wszystkie typy zadań obsługują pliki zasobów: zadania, uruchamianie zadań, zadania związane z przygotowaniem zadań, zadania związane z zwalnianiem zadań itp. W tym artykule opisano kilka typowych metod tworzenia plików zasobów i umieszczania ich na maszynie Wirtualnej.  

Pliki zasobów umieścić dane na maszynie Wirtualnej w partii, ale typ danych i jak jest używany jest elastyczny. Istnieją jednak pewne typowe przypadki użycia:

1. Aprowizuj typowe pliki na każdej maszynie wirtualnej przy użyciu plików zasobów w zadaniu początkowym
1. Aprowizuj dane wejściowe do przetworzenia przez zadania

Typowe pliki mogą być na przykład plikami w zadaniu startowym używanym do instalowania aplikacji uruchamianych zadaniem. Dane wejściowe mogą być nieprzetworzonymi danymi obrazu lub wideo lub wszelkimi informacjami, które mają być przetwarzane przez partię.

## <a name="types-of-resource-files"></a>Typy plików zasobów

Istnieje kilka różnych opcji do generowania plików zasobów. Proces tworzenia plików zasobów różni się w zależności od tego, gdzie są przechowywane oryginalne dane.

Opcje tworzenia pliku zasobów:

- [Adres URL kontenera magazynu:](#storage-container-url)generuje plik zasobu z dowolnego kontenera magazynu na platformie Azure
- [Nazwa kontenera magazynu:](#storage-container-name)Generuje plik zasobu na podstawie nazwy kontenera na koncie magazynu platformy Azure połączonym z usługą Batch
- [Punkt końcowy sieci Web:](#web-endpoint)Generuje plik zasobu z dowolnego prawidłowego adresu URL HTTP

### <a name="storage-container-url"></a>Adres URL kontenera magazynu

Przy użyciu adresu URL kontenera magazynu oznacza, z poprawnymi uprawnieniami, można uzyskać dostęp do plików w dowolnym kontenerze magazynu na platformie Azure. 

W tym przykładzie języka C# pliki zostały już przekazane do kontenera magazynu platformy Azure jako magazynu obiektów blob. Aby uzyskać dostęp do danych potrzebnych do utworzenia pliku zasobów, najpierw musimy uzyskać dostęp do kontenera magazynu.

Utwórz identyfikator URI podpisu dostępu udostępnionego (SAS) z odpowiednimi uprawnieniami dostępu do kontenera magazynu. Ustaw czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego. W takim przypadku nie określono godziny rozpoczęcia, więc sygnatury dostępu Współdzielonego staje się prawidłowy natychmiast i wygasa dwie godziny po jego wygenerowaniu.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> W przypadku dostępu do `Read` kontenera musisz mieć zarówno uprawnienia, `List` podczas `Read` gdy z dostępem do obiektu blob potrzebujesz tylko uprawnień.

Po skonfigurowaniu uprawnień utwórz token sygnatury dostępu Współdzielonego i sformatować adres URL sygnatury dostępu współdzielonego w celu uzyskania dostępu do kontenera magazynu. Za pomocą sformatowanego adresu URL sygnatury dostępu Współdzielonego dla kontenera magazynu wygeneruj plik zasobu za pomocą programu [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet).

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

Alternatywą dla generowania adresu URL sygnatury dostępu współdzielonego jest włączenie anonimowego, publicznego dostępu do odczytu do kontenera i jego obiektów blob w magazynie obiektów Blob platformy Azure. W ten sposób można udzielić dostępu tylko do odczytu do tych zasobów bez udostępniania klucza konta i bez konieczności sygnatury dostępu Współdzielonego. Publiczny dostęp do odczytu jest zwykle używany w scenariuszach, w których niektóre obiekty blob mają być zawsze dostępne dla anonimowego dostępu do odczytu. Jeśli ten scenariusz odpowiada twojemu rozwiązaniu, zobacz [anonimowy dostęp do obiektów blob](../storage/blobs/storage-manage-access-to-resources.md) artykuł, aby dowiedzieć się więcej na temat zarządzania dostępem do danych obiektów blob.

### <a name="storage-container-name"></a>Nazwa kontenera magazynu

Zamiast konfigurowania i tworzenia adresu URL sygnatury dostępu Współdzielonego, można użyć nazwy kontenera magazynu platformy Azure, aby uzyskać dostęp do danych obiektów blob. Używany kontener magazynu musi znajdować się na koncie magazynu platformy Azure połączonym z kontem usługi Batch. To konto magazynu jest znane jako konto autoprzeswoje. Za pomocą kontenera autoprzetworzenia umożliwia ominięcie konfigurowania i tworzenia adresu URL sygnatury dostępu współdzielonego w celu uzyskania dostępu do kontenera magazynu.

W tym przykładzie zakładamy, że dane, które mają być używane do tworzenia pliku zasobów jest już na koncie usługi Azure Storage połączone z kontem usługi Batch. Jeśli nie masz konta autoprzeswojenia, zobacz kroki w [części Tworzenie konta usługi Batch,](batch-account-create-portal.md) aby uzyskać szczegółowe informacje na temat tworzenia i łączenia konta.

Korzystając z połączonego konta magazynu, nie trzeba tworzyć i konfigurować adresu URL sygnatury dostępu Współdzielonego do kontenera magazynu. Zamiast tego podaj nazwę kontenera magazynu na połączonym koncie magazynu.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Punkt końcowy sieci Web

Dane, które nie są przekazywane do usługi Azure Storage nadal mogą być używane do tworzenia plików zasobów. Można określić dowolny prawidłowy adres URL HTTP zawierający dane wejściowe. Adres URL jest dostarczany do interfejsu API partii, a następnie dane są używane do tworzenia pliku zasobów.

W poniższym przykładzie języka C# dane wejściowe są hostowane w fikcyjnym punkcie końcowym GitHub. Interfejs API pobiera plik z prawidłowego punktu końcowego sieci web i generuje plik zasobów, który ma zostać wykorzystany przez zadanie. W tym scenariuszu nie są potrzebne żadne poświadczenia.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>Porady i sugestie

Każde zadanie usługi Azure Batch używa plików inaczej, dlatego usługa Batch ma dostępne opcje zarządzania plikami w zadaniach. Poniższe scenariusze nie mają być kompleksowe, ale zamiast tego obejmują kilka typowych sytuacji i zawierają zalecenia.

### <a name="many-resource-files"></a>Wiele plików zasobów

Zadanie wsadowe może zawierać kilka zadań, które używają tych samych, typowych plików. Jeśli typowe pliki zadań są współużytkowane przez wiele zadań, lepszym rozwiązaniem może być użycie pakietu aplikacji zawierającego pliki zamiast plików zasobów. Pakiety aplikacji zapewniają optymalizację szybkości pobierania. Ponadto dane w pakietach aplikacji są buforowane między zadaniami, więc jeśli pliki zadań nie zmieniają się często, pakiety aplikacji mogą być odpowiednie dla rozwiązania. W przypadku pakietów aplikacji nie trzeba ręcznie zarządzać kilkoma plikami zasobów ani generować adresów URL SYGNATUR, aby uzyskać dostęp do plików w usłudze Azure Storage. Usługa Batch działa w tle z usługą Azure Storage do przechowywania i wdrażania pakietów aplikacji w węzłach obliczeniowych.

Jeśli każde zadanie ma wiele plików unikatowych dla tego zadania, pliki zasobów są najlepszym rozwiązaniem, ponieważ zadania korzystające z unikatowych plików często wymagają aktualizacji lub zastąpienia, co nie jest tak łatwe do wykonania w przypadku zawartości pakietów aplikacji. Pliki zasobów zapewniają dodatkową elastyczność w zakresie aktualizowania, dodawania lub edytowania pojedynczych plików.

### <a name="number-of-resource-files-per-task"></a>Liczba plików zasobów na zadanie

Jeśli w zadaniu określono kilkaset plików zasobów, usługa Batch może odrzucić zadanie jako zbyt duże. Najlepiej zachować małe zadania, minimalizując liczbę plików zasobów w samym zadaniu.

Jeśli nie ma sposobu, aby zminimalizować liczbę plików, których potrzebuje zadanie, można zoptymalizować zadanie, tworząc pojedynczy plik zasobu, który odwołuje się do kontenera magazynu plików zasobów. Aby to zrobić, umieść pliki zasobów w kontenerze usługi Azure Storage i użyj różnych [metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods) "kontenera" dla plików zasobów. Opcje prefiksu obiektu blob można określić kolekcje plików do pobrania dla zadań.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [pakietach aplikacji](batch-application-packages.md) jako alternatywie dla plików zasobów.
- Aby uzyskać więcej informacji na temat używania kontenerów dla plików zasobów, zobacz [Obciążenia kontenerów](batch-docker-container-workloads.md).
- Aby dowiedzieć się, jak zbierać i zapisywać dane wyjściowe z zadań, zobacz [Utrwalanie zadania i zadania wyjściowego](batch-task-output.md).
- Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
