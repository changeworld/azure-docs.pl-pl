---
title: Import Eksport tożsamości urządzenia Azure IoT Hub | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT zestawu SDK w celu wykonania operacji zbiorczej przed rejestru tożsamości w celu importowania i eksportowania tożsamości urządzenia. Operacje importowania umożliwiają tworzenie, aktualizowanie i usuwanie tożsamości urządzenia w partii.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 63e7fd5807f0cf6d05d81af138d649b75024d9bb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634026"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Zarządzanie tożsamościami urządzenia IoT Hub zbiorcze

Każdy Centrum IoT ma rejestru tożsamości używanej do tworzenia zasobów na urządzenie w usłudze. W rejestrze tożsamości umożliwia kontrolowanie dostępu do urządzeń połączonych z punktów końcowych. W tym artykule opisano, jak importować i eksportować tożsamości urządzenia w trybie zbiorczym do i z rejestru tożsamości.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Importowanie i eksportowanie działania mają miejsce w kontekście *zadania* umożliwiające wykonanie operacji usługi zbiorczego z Centrum IoT.

**RegistryManager** klasa zawiera **ExportDevicesAsync** i **ImportDevicesAsync** metody, które używają **zadania** framework. Te metody umożliwiają eksportowanie, importowania i synchronizacji całości rejestru tożsamości Centrum IoT.

W tym temacie omówiono przy użyciu **RegistryManager** klasy i **zadania** systemu, aby wykonać zbiorczego importu i eksportu urządzeń do i z rejestru tożsamości Centrum IoT. Usługa udostępniania urządzeń Centrum IoT Azure umożliwia również włączyć bezobsługową, just-in-time alokacji dla co najmniej jeden centra IoT bez udziału człowieka. Aby dowiedzieć się więcej, zobacz [inicjowania obsługi usługi dokumentacji][lnk-dps].


## <a name="what-are-jobs"></a>Co to są zadania?

Użyj operacji rejestru tożsamości **zadania** systemu podczas operacji:

* Jest potencjalnie długi czas wykonywania jest porównywany standardowych operacji środowiska wykonawczego.
* Zwraca dużej ilości danych do użytkownika.

Zamiast jednego wywołania interfejsu API Oczekiwanie lub blokowania na wynik operacji, tworzy asynchronicznie operację **zadania** dla tego Centrum IoT. Operacja następnie natychmiast zwraca **JobProperties** obiektu.

Poniższy fragment kodu C# pokazano, jak utworzyć zadanie eksportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Aby użyć **RegistryManager** klasy w kodzie C#, Dodaj **Microsoft.Azure.Devices** pakiet NuGet do projektu. **RegistryManager** klasa znajduje się w **Microsoft.Azure.Devices** przestrzeni nazw.

Można użyć **RegistryManager** klasy do sprawdzania stanu **zadania** przy użyciu zwróconego **JobProperties** metadanych. Aby utworzyć wystąpienie **RegistryManager** klasy, należy użyć **CreateFromConnectionString** metody:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Aby określić parametry połączenia Centrum IoT w portalu Azure:

- Przejdź do centrum IoT Hub.
- Wybierz **zasady dostępu współużytkowanego**.
- Wybierz zasady, biorąc pod uwagę uprawnienia, które są potrzebne.
- Skopiuj parametry połączenia z panelu po prawej stronie ekranu.

Poniższy fragment kodu C# pokazano, jak wykonać sondowanie co pięć sekund, aby zobaczyć, czy zadanie zostało zakończone, wykonywania:

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

## <a name="export-devices"></a>Eksportuj urządzeń

Użyj **ExportDevicesAsync** metodę, aby wyeksportować całości rejestru tożsamości Centrum IoT w celu [usługi Azure Storage](../storage/index.yml) przy użyciu kontenera obiektów blob [sygnatura dostępu współdzielonego](../storage/common/storage-security-guide.md#data-plane-security).

Ta metoda umożliwia tworzenie niezawodnej kopii zapasowych danych użytkownika urządzenia w kontenerze obiektów blob, którą kontrolujesz.

**ExportDevicesAsync** wymaga, aby dwa parametry:

* A *ciąg* zawiera identyfikator URI kontenera obiektów blob. Ten identyfikator URI musi zawierać token sygnatury dostępu Współdzielonego, która udziela dostępu do zapisu do kontenera. To zadanie tworzy blokowych obiektów blob w tym kontenerze do przechowywania danych urządzenia eksportu serializacji. Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* A *logiczna* wskazujące, jeśli chcesz wykluczyć klucze uwierzytelniania danych eksportu. Jeśli **false**, klucze uwierzytelniania znajdują się w danych wyjściowych eksportu. W przeciwnym razie klucze są eksportowane jako **null**.

Poniższy fragment kodu C# pokazano, jak zainicjować zadanie eksportu, która zawiera klucze uwierzytelniania urządzenia w danych eksportu, a następnie sondować zakończenia:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

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

Zadania są przechowywane jego dane wyjściowe w kontenerze obiektów blob podana jako blokowych obiektów blob o nazwie **devices.txt**. Danych wyjściowych składa się z danych urządzenia Zserializowany do postaci JSON, jedno urządzenie w jednym wierszu.

W poniższym przykładzie danych wyjściowych:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Jeśli urządzenie ma dwie danych, danych dwie również zostaną wyeksportowane wraz z danych urządzenia. W poniższym przykładzie przedstawiono ten format. Wszystkie dane w wierszu "twinETag", aż do zakończenia są dwie danych.

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

Aby uzyskać dostęp do tych danych w kodzie, łatwo może wykonywać deserializację tego danych przy użyciu **ExportImportDevice** klasy. Poniższy fragment kodu C# pokazano, jak można odczytać informacji o urządzeniu, który został wcześniej wyeksportowany do blokowego obiektu blob:

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

## <a name="import-devices"></a>Importuj urządzeń

**ImportDevicesAsync** metody w **RegistryManager** klasa umożliwia wykonywanie operacji importowania i synchronizacji zbiorczego w rejestrze tożsamości Centrum IoT. Podobnie jak **ExportDevicesAsync** metody **ImportDevicesAsync** używa metody **zadania** framework.

Zajmie się przy użyciu **ImportDevicesAsync** metody ponieważ oprócz inicjowania obsługi administracyjnej nowych urządzeń w rejestrze tożsamości, można również aktualizować i usuwać istniejące urządzenia.

> [!WARNING]
> Nie można cofnąć operacji importowania. Zawsze utworzyć kopię zapasową istniejących danych przy użyciu **ExportDevicesAsync** metody do innego kontenera obiektów blob, przed wprowadzeniem zbiorcze zmiany w rejestrze Twojej tożsamości.

**ImportDevicesAsync** metoda przyjmuje dwa parametry:

* A *ciąg* zawiera identyfikator URI [usługi Azure Storage](../storage/index.yml) kontenera obiektów blob do użycia jako *wejściowych* zadania. Ten identyfikator URI musi zawierać token sygnatury dostępu Współdzielonego, która udziela dostępu do odczytu do kontenera. Ten kontener musi zawierać obiektu blob o nazwie **devices.txt** zawierający dane serializowane urządzenia do zaimportowania do rejestru tożsamości. Importuj dane musi zawierać informacje o urządzeniu, w tym samym formacie JSON **ExportImportDevice** używane podczas tworzenia **devices.txt** obiektu blob. Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* A *ciąg* zawiera identyfikator URI [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kontenera obiektów blob do użycia jako *dane wyjściowe* z zadania. To zadanie tworzy blokowych obiektów blob w tym kontenerze przechowywać informacje o błędzie z ukończone importu **zadania**. Token sygnatury dostępu Współdzielonego musi zawierać następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Dwa parametry może wskazywać tego samego kontenera obiektów blob. Oddzielne parametrów po prostu włącz większą kontrolę nad dane jako dane wyjściowe kontenera wymaga dodatkowych uprawnień.

Poniższy fragment kodu C# pokazano, jak zainicjować zadania importu:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tej metody można również importować dane dla dwie urządzenia. Format danych wejściowych jest taki sam, jak pokazano w formacie **ExportDevicesAsync** sekcji. W ten sposób można ponownie zaimportować wyeksportowane dane. **$Metadata** jest opcjonalna.

## <a name="import-behavior"></a>Zachowanie importu

Można użyć **ImportDevicesAsync** metodę, aby wykonać następujące operacje zbiorcze w rejestrze tożsamości:

* Rejestracji zbiorczej nowych urządzeń
* Zbiorczego usuwania istniejących urządzeń
* Zbiorcze zmiany stanu (Włączanie lub wyłączanie urządzenia)
* Zbiorcze przypisania nowych kluczy uwierzytelniania urządzenia
* Zbiorcze automatyczne ponowne generowanie kluczy uwierzytelniania urządzenia
* Aktualizację zbiorczą dwie danych

Może wykonywać dowolne kombinacje poprzednich operacji w jednym **ImportDevicesAsync** wywołania. Na przykład można zarejestrować nowych urządzeń i usuwania lub aktualizacji istniejących urządzeń w tym samym czasie. Gdy jest używany wraz z **ExportDevicesAsync** metody, można całkowicie migracji wszystkich urządzeń z jednego centrum IoT, do drugiego.

Jeśli plik importu zawiera dwie metadanych, te metadane zastępuje istniejące metadane dwie. Jeśli plik importu nie zawiera metadanych dwie następnie tylko `lastUpdateTime` metadanych jest aktualizowany przy użyciu bieżącego czasu.

Użyj opcjonalnego **parametrem importMode** właściwości w danych serializacji importu dla każdego urządzenia, aby kontrolować importu procesu na urządzenie. **Parametrem importMode** właściwości ma następujące opcje:

| parametrem importMode | Opis |
| --- | --- |
| **createOrUpdate** |Jeśli urządzenie nie istnieje z określonym **identyfikator**, nowo jest zarejestrowany. <br/>Jeśli urządzenie już istnieje, istniejące informacje zostały zastąpione podanych danych wejściowych bez odniesieniu do **ETag** wartość. <br> Użytkownika można opcjonalnie określić dwie danych wraz z danymi urządzenia. Dwie etag, jeśli jest określony, są przetwarzane niezależnie od urządzenia etag. Jeśli występuje niezgodność z istniejących dwie etag, błąd są zapisywane w pliku dziennika. |
| **create** |Jeśli urządzenie nie istnieje z określonym **identyfikator**, nowo jest zarejestrowany. <br/>Jeśli urządzenie już istnieje, jest on zapisywany w pliku dziennika. <br> Użytkownika można opcjonalnie określić dwie danych wraz z danymi urządzenia. Dwie etag, jeśli jest określony, są przetwarzane niezależnie od urządzenia etag. Jeśli występuje niezgodność z istniejących dwie etag, błąd są zapisywane w pliku dziennika. |
| **Aktualizacja** |Jeśli urządzenie już istnieje z określonym **identyfikator**, istniejące informacje zostały zastąpione podanych danych wejściowych bez odniesieniu do **ETag** wartość. <br/>Jeśli urządzenie nie istnieje, jest on zapisywany w pliku dziennika. |
| **updateIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikator**, istniejące informacje zostały zastąpione podanych danych wejściowych tylko wtedy, gdy istnieje **ETag** zgodne. <br/>Jeśli urządzenie nie istnieje, jest on zapisywany w pliku dziennika. <br/>W przypadku **ETag** niezgodność, błąd są zapisywane w pliku dziennika. |
| **createOrUpdateIfMatchETag** |Jeśli urządzenie nie istnieje z określonym **identyfikator**, nowo jest zarejestrowany. <br/>Jeśli urządzenie już istnieje, istniejące informacje zostały zastąpione podanych danych wejściowych tylko wtedy, gdy istnieje **ETag** zgodne. <br/>W przypadku **ETag** niezgodność, błąd są zapisywane w pliku dziennika. <br> Użytkownika można opcjonalnie określić dwie danych wraz z danymi urządzenia. Dwie etag, jeśli jest określony, są przetwarzane niezależnie od urządzenia etag. Jeśli występuje niezgodność z istniejących dwie etag, błąd są zapisywane w pliku dziennika. |
| **usuwanie** |Jeśli urządzenie już istnieje z określonym **identyfikator**, jest usuwany bez odniesieniu do **ETag** wartość. <br/>Jeśli urządzenie nie istnieje, jest on zapisywany w pliku dziennika. |
| **deleteIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikator**, jest usuwany, tylko wtedy, gdy istnieje **ETag** zgodne. Jeśli urządzenie nie istnieje, jest on zapisywany w pliku dziennika. <br/>Wystąpił błąd w przypadku element etag jest zapisany w pliku dziennika. |

> [!NOTE]
> Jeśli dane serializacji nie jawnie definiować **parametrem importMode** flagi dla urządzeń, domyślne **createOrUpdate** podczas operacji importowania.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importuj przykładowe urządzeń — zbiorcze, inicjowanie obsługi administracyjnej urządzeń

Poniższy przykład kodu C# ilustruje sposób generowania wiele tożsamości urządzenia który:

* Zawierają klucze uwierzytelniania.
* Zapisuje informacje o tego urządzenia do blokowego obiektu blob.
* Importuj urządzenia w rejestrze tożsamości.

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
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

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

## <a name="import-devices-example--bulk-deletion"></a>Przykład urządzeń importu — zbiorczego usuwania

Poniższy przykład kodu pokazuje sposób usuwania urządzenia można dodać za pomocą powyższego przykładu kodu:

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

## <a name="get-the-container-sas-uri"></a>Pobierz kontenera identyfikatora URI połączenia SAS

Poniższy przykład kodu pokazuje sposób generowania [identyfikatora URI połączenia SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) o odczytu, zapisu i usuwania uprawnień do kontenera obiektów blob:

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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób wykonania operacji zbiorczej przed w rejestrze tożsamości w Centrum IoT. Skorzystaj z poniższych linków, aby dowiedzieć się więcej o zarządzaniu Centrum IoT Azure:

* [Metryki Centrum IoT][lnk-metrics]
* [Operacje monitorowania][lnk-monitor]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Przewodnik dewelopera Centrum IoT][lnk-devguide]
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

Aby zapoznać się z usługi IoT Centrum urządzeń inicjowania obsługi administracyjnej Włącz bezobsługową, w czasie inicjowania obsługi, zobacz: 

* [Usługi inicjowania obsługi administracyjnej urządzeniu Centrum IoT Azure][lnk-dps]


[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
