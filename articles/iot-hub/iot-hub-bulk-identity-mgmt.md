---
title: Importowanie/eksportowanie tożsamości urządzeń usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Jak używać usługi Azure IoT SDK do uruchamiania operacji zbiorczych w rejestrze tożsamości do importowania i eksportowania tożsamości urządzeń. Operacje importu umożliwiają zbiorcze tworzenie, aktualizowanie i usuwanie tożsamości urządzeń.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371583"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Zbiorcze importowanie i eksportowanie tożsamości urządzeń usługi IoT Hub

Każdy centrum IoT ma rejestr tożsamości, którego można użyć do tworzenia zasobów na urządzenie w usłudze. Rejestr tożsamości umożliwia również kontrolowanie dostępu do punktów końcowych skierowanych do urządzenia. W tym artykule opisano sposób zbiorczego importowania i eksportowania tożsamości urządzeń do i z rejestru tożsamości. Aby wyświetlić działającą próbkę w języku C# i dowiedzieć się, jak używać tej funkcji podczas klonowania koncentratora do innego regionu, zobacz [Jak sklonować centrum IoT Hub](iot-hub-how-to-clone.md).

> [!NOTE]
> Usługa IoT Hub niedawno dodała obsługę sieci wirtualnej w ograniczonej liczbie regionów. Ta funkcja zabezpiecza operacje importu i eksportu oraz eliminuje konieczność przekazywania kluczy do uwierzytelniania.  Początkowo obsługa sieci wirtualnej jest dostępna tylko w tych regionach: *WestUS2*, *EastUS*i *SouthCentralUS*. Aby dowiedzieć się więcej na temat obsługi sieci wirtualnej i wywołań interfejsu API w celu jej zaimplementowania, zobacz [Obsługa usługi IoT Hub dla sieci wirtualnych](virtual-network-support.md).

Operacje importowania i eksportowania odbywają się w kontekście *zadań,* które umożliwiają wykonywanie operacji usługi zbiorczej względem centrum IoT hub.

**RegistryManager** Klasa zawiera **ExportDevicesAsync** i **ImportDevicesAsync** metody, które używają **struktury zadania.** Te metody umożliwiają eksportowanie, importowanie i synchronizowanie całego rejestru tożsamości centrum IoT.

W tym temacie omówiono przy użyciu **RegistryManager** klasy i systemu **zadań** do wykonywania importu zbiorczego i eksportu urządzeń do iz rejestru tożsamości centrum IoT hub. Można również użyć usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub, aby włączyć obsługę obsługi administracyjnej typu "tylko w czasie" typu "zero-touch" dla jednego lub kilku centrów IoT bez konieczności interwencji międzyludzkiej. Aby dowiedzieć się więcej, zobacz [dokumentację usługi inicjowania obsługi administracyjnej](/azure/iot-dps).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>Co to są miejsca pracy?

Operacje rejestru tożsamości używają systemu **zadań,** gdy operacja:

* Ma potencjalnie długi czas wykonywania w porównaniu do standardowych operacji w czasie wykonywania.

* Zwraca dużą ilość danych do użytkownika.

Zamiast pojedynczego wywołania interfejsu API oczekiwania lub blokowania w wyniku operacji, operacja asynchronicznie tworzy **Zadanie** dla tego centrum IoT. Operacja następnie natychmiast zwraca **JobProperties** obiektu.

Poniższy fragment kodu języka C# pokazuje, jak utworzyć zadanie eksportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Aby użyć **RegistryManager** klasy w kodzie języka C#, dodaj **microsoft.Azure.Devices** NuGet pakiet do projektu. **RegistryManager** Klasa znajduje się w obszarze nazw **Microsoft.Azure.Devices.**

Klasy **RegistryManager** można użyć do wykonywania zapytań o stan **zadania** przy użyciu zwróconych metadanych **JobProperties.** Aby utworzyć wystąpienie **klasy RegistryManager,** należy użyć metody **CreateFromConnectionString.**

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Aby znaleźć parametry połączenia dla centrum IoT hub, w witrynie Azure portal:

- Przejdź do centrum IoT Hub.

- Wybierz pozycję **Zasady dostępu współdzielonego**.

- Wybierz zasadę, biorąc pod uwagę potrzebne uprawnienia.

- Skopiuj sznurek połączenia z panelu po prawej stronie ekranu.

Poniższy fragment kodu języka C# pokazuje, jak sondować co pięć sekund, aby sprawdzić, czy zadanie zostało zakończone wykonywanie:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

> [!NOTE]
> Jeśli twoje konto magazynu ma konfiguracje zapory, które ograniczają łączność usługi IoT Hub, należy rozważyć użycie [zaufanego wyjątku firmy Microsoft (dostępnego](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) w wybranych regionach dla centrów IoT z tożsamością usługi zarządzanej).


## <a name="device-importexport-job-limits"></a>Limity zadań importowania/eksportowania urządzeń

Tylko 1 aktywne zadanie importu lub eksportu urządzeń jest dozwolone w czasie dla wszystkich warstw Usługi IoT Hub. Centrum IoT hub ma również limity dla szybkości operacji zadań. Aby dowiedzieć się więcej, zobacz [Odwołanie — przydziały i ograniczanie przepustowości usługi IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Eksportowanie urządzeń

Metoda **ExportDevicesAsync** służy do eksportowania całego rejestru tożsamości centrum IoT do kontenera obiektów blob usługi Azure Storage przy użyciu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji na temat podpisów dostępu współdzielonego, zobacz [Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatur dostępu współdzielonego (SAS).](../storage/common/storage-sas-overview.md)

Ta metoda umożliwia tworzenie niezawodnych kopii zapasowych informacji o urządzeniu w kontenerze obiektów blob, który można kontrolować.

Metoda **ExportDevicesAsync** wymaga dwóch parametrów:

* *Ciąg,* który zawiera identyfikator URI kontenera obiektu blob. Ten identyfikator URI musi zawierać token sygnatury dostępu współdzielonego, który udziela dostępu do zapisu do kontenera. Zadanie tworzy blokowy obiekt blob w tym kontenerze do przechowywania danych urządzenia eksportu szeregowego. Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Wartość *logiczna* wskazująca, czy chcesz wykluczyć klucze uwierzytelniania z danych eksportu. Jeśli **false**, klucze uwierzytelniania są uwzględniane w danych wyjściowych eksportu. W przeciwnym razie klucze są eksportowane jako **null**.

Poniższy fragment kodu języka C# pokazuje, jak zainicjować zadanie eksportu, które zawiera klucze uwierzytelniania urządzenia w danych eksportu, a następnie sondować do ukończenia:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Zadanie przechowuje swoje dane wyjściowe w dostarczonym kontenerze obiektów blob jako blokowy obiekt blob o nazwie **devices.txt**. Dane wyjściowe składają się z szeregowanych danych urządzenia JSON, z jednym urządzeniem na linię.

W poniższym przykładzie przedstawiono dane wyjściowe:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Jeśli urządzenie ma dane bliźniacze, dane bliźniaczej reprezentacji są również eksportowane razem z danymi urządzenia. Poniższy przykład pokazuje ten format. Wszystkie dane z linii "twinETag" do końca są dane bliźniacze.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Jeśli potrzebujesz dostępu do tych danych w kodzie, można łatwo deserialize tych danych przy użyciu **ExportImportDevice** klasy. Poniższy fragment kodu języka C# pokazuje, jak odczytać informacje o urządzeniu, który został wcześniej wyeksportowany do bloku obiektu blob:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importowanie urządzeń

Metoda **ImportDevicesAsync** w klasie **RegistryManager** umożliwia wykonywanie zbiorczych operacji importowania i synchronizacji w rejestrze tożsamości centrum IoT. Podobnie jak **ExportDevicesAsync** metoda **ImportDevicesAsync** metoda używa **struktury zadania.**

Należy uważać przy użyciu **ImportDevicesAsync** metody, ponieważ oprócz inicjowania obsługi administracyjnej nowych urządzeń w rejestrze tożsamości, można również zaktualizować i usunąć istniejące urządzenia.

> [!WARNING]
> Nie można cofnąć operacji importowania. Zawsze wykonać zapasową istniejących danych przy użyciu **ExportDevicesAsync** metody do innego kontenera obiektu blob przed wprowadzeniem zbiorczych zmian w rejestrze tożsamości.

Metoda **ImportDevicesAsync** przyjmuje dwa parametry:

* *Ciąg,* który zawiera identyfikator URI kontenera obiektów blob [usługi Azure Storage](../storage/index.yml) do użycia jako dane *wejściowe* do zadania. Ten identyfikator URI musi zawierać token sygnatury dostępu współdzielonego, który udziela dostępu do odczytu do kontenera. Ten kontener musi zawierać obiekt blob o nazwie **devices.txt,** który zawiera szeregowane dane urządzenia do zaimportowania do rejestru tożsamości. Dane importu muszą zawierać informacje o urządzeniu w tym samym formacie JSON, który jest używany przez zadanie **ExportImportDevice** podczas tworzenia obiektu blob **device.txt.** Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* *Ciąg,* który zawiera identyfikator URI kontenera obiektów blob [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) do użycia jako dane *wyjściowe* z zadania. Zadanie tworzy blokowy obiekt blob w tym kontenerze do przechowywania wszelkich informacji o błędzie z ukończonego **zadania**importu . Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Dwa parametry mogą wskazywać ten sam kontener obiektów blob. Oddzielne parametry po prostu włączyć większą kontrolę nad danymi, jak kontener danych wyjściowych wymaga dodatkowych uprawnień.

Poniższy fragment kodu języka C# pokazuje, jak zainicjować zadanie importowania:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Ta metoda może również służyć do importowania danych dla bliźniaczej reprezentacji urządzenia. Format wprowadzania danych jest taki sam jak format pokazany w sekcji **ExportDevicesAsync.** W ten sposób można ponownie zaimportować wyeksportowane dane. **$metadata** jest opcjonalna.

## <a name="import-behavior"></a>Zachowanie importu

Za pomocą **metody ImportDevicesAsync** można wykonać następujące operacje zbiorcze w rejestrze tożsamości:

* Masowa rejestracja nowych urządzeń
* Masowe usuwanie istniejących urządzeń
* Zbiorcze zmiany stanu (włączanie lub wyłączanie urządzeń)
* Zbiorcze przypisywanie nowych kluczy uwierzytelniania urządzeń
* Zbiorcza automatyczna regeneracja kluczy uwierzytelniania urządzeń
* Zbiorcza aktualizacja danych bliźniaczych

Można wykonać dowolną kombinację poprzednich operacji w ramach jednego **wywołania ImportDevicesAsync.** Na przykład można zarejestrować nowe urządzenia i usunąć lub zaktualizować istniejące urządzenia w tym samym czasie. Gdy jest używana wraz z **ExportDevicesAsync** metody, można całkowicie migrować wszystkie urządzenia z jednego centrum IoT do innego.

Jeśli plik importu zawiera podwójne metadane, metadane te nadpisują istniejące metadane bliźniaczej reprezentacji. Jeśli plik importu nie zawiera metadanych `lastUpdateTime` bliźniaczej reprezentacji, tylko metadane są aktualizowane przy użyciu bieżącego czasu.

Użyj opcjonalnej właściwości **importMode** w danych seriowania importu dla każdego urządzenia, aby kontrolować proces importowania na urządzenie. Właściwość **importMode** ma następujące opcje:

| importMode | Opis |
| --- | --- |
| **createOrUpdate** |Jeśli urządzenie nie istnieje z określonym **identyfikatorem,** jest nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje są zastępowane dostarczonymi danymi wejściowymi bez względu na wartość **ETag.** <br> Użytkownik może opcjonalnie określić dane bliźniaczych reprezentacji wraz z danymi urządzenia. Etag bliźniaka, jeśli jest określony, jest przetwarzany niezależnie od etagu urządzenia. Jeśli istnieje niezgodność z istniejącym etagiem bliźniaczego, błąd jest zapisywany w pliku dziennika. |
| **Utworzyć** |Jeśli urządzenie nie istnieje z określonym **identyfikatorem,** jest nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, błąd jest zapisywany w pliku dziennika. <br> Użytkownik może opcjonalnie określić dane bliźniaczych reprezentacji wraz z danymi urządzenia. Etag bliźniaka, jeśli jest określony, jest przetwarzany niezależnie od etagu urządzenia. Jeśli istnieje niezgodność z istniejącym etagiem bliźniaczego, błąd jest zapisywany w pliku dziennika. |
| **Aktualizacji** |Jeśli urządzenie już istnieje z określonym **identyfikatorem,** istniejące informacje są zastępowane dostarczonymi danymi wejściowymi bez względu na wartość **ETag.** <br/>Jeśli urządzenie nie istnieje, błąd jest zapisywany w pliku dziennika. |
| **aktualizacjaIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikatorem,** istniejące informacje są zastępowane dostarczonymi danymi wejściowymi tylko wtedy, gdy istnieje dopasowanie **ETag.** <br/>Jeśli urządzenie nie istnieje, błąd jest zapisywany w pliku dziennika. <br/>Jeśli istnieje niezgodność **ETag,** błąd jest zapisywany w pliku dziennika. |
| **createOrUpdateIfMatchETag** |Jeśli urządzenie nie istnieje z określonym **identyfikatorem,** jest nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje są zastępowane dostarczonymi danymi wejściowymi tylko wtedy, gdy istnieje dopasowanie **ETag.** <br/>Jeśli istnieje niezgodność **ETag,** błąd jest zapisywany w pliku dziennika. <br> Użytkownik może opcjonalnie określić dane bliźniaczych reprezentacji wraz z danymi urządzenia. Etag bliźniaka, jeśli jest określony, jest przetwarzany niezależnie od etagu urządzenia. Jeśli istnieje niezgodność z istniejącym etagiem bliźniaczego, błąd jest zapisywany w pliku dziennika. |
| **Usunąć** |Jeśli urządzenie już istnieje z określonym **identyfikatorem,** jest usuwane bez względu na wartość **ETag.** <br/>Jeśli urządzenie nie istnieje, błąd jest zapisywany w pliku dziennika. |
| **deleteIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikatorem,** jest usuwane tylko wtedy, gdy istnieje dopasowanie **ETag.** Jeśli urządzenie nie istnieje, błąd jest zapisywany w pliku dziennika. <br/>Jeśli istnieje niezgodność ETag, błąd jest zapisywany w pliku dziennika. |

> [!NOTE]
> Jeśli dane serializacji nie jawnie zdefiniować **importMode** flagi dla urządzenia, domyślnie **createOrUpdate** podczas operacji importu.

## <a name="import-devices-example--bulk-device-provisioning"></a>Przykład importowanie urządzeń — aprowizacji urządzeń zbiorczych

Poniższy przykład kodu Języka C# ilustruje sposób generowania wielu tożsamości urządzeń, które:

* Dołącz klucze uwierzytelniania.
* Zapisz te informacje o urządzeniu do bloku blob.
* Zaimportuj urządzenia do rejestru tożsamości.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Przykład importu urządzeń – usuwanie zbiorcze

Poniższy przykład kodu pokazuje, jak usunąć urządzenia dodane przy użyciu poprzedniego przykładu kodu:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Pobierz identyfikator URI sygnatury dostępu Współdzielonego kontenera

Poniższy przykład kodu pokazuje, jak wygenerować [identyfikator URI](../storage/common/storage-dotnet-shared-access-signature-part-1.md) sygnatury dostępu Współdzielonego z uprawnieniami do odczytu, zapisu i usuwania kontenera obiektów blob:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak wykonywać operacje zbiorcze względem rejestru tożsamości w centrum IoT. Wiele z tych operacji, w tym sposób przenoszenia urządzeń z jednego koncentratora do drugiego, są używane w [zarządzaniu urządzeniami zarejestrowanymi w sekcji Centrum IoT w centrum Jak sklonować centrum IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Artykuł klonowania ma próbki pracy skojarzone z nim, który znajduje się w próbkach IoT C# na tej stronie: [Próbki Usługi Azure IoT dla języka C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), z projektu jest ImportExportDevicesSample. Możesz pobrać próbkę i wypróbować ją; istnieją instrukcje w [jak sklonować Centrum IoT](iot-hub-how-to-clone.md) Hub artykułu.

Aby dowiedzieć się więcej na temat zarządzania usługą Azure IoT Hub, zapoznaj się z następującymi artykułami:

* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Dzienniki usługi IoT Hub](iot-hub-monitor-resource-health.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Aby eksplorować korzystanie z usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub, aby włączyć obsługę obsługi administracyjnej typu "tylko w czasie" typu "zero-touch", zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps)
