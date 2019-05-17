---
title: Importowanie/eksportowanie tożsamości urządzeń w usłudze Azure IoT Hub | Dokumentacja firmy Microsoft
description: Jak używać zestawu SDK usługi Azure IoT, aby wykonywały operacje zbiorcze na rejestrze tożsamości w celu importowania i eksportowania tożsamości urządzeń. Operacje importowania umożliwiają tworzenie, aktualizowanie i usuwanie tożsamości urządzeń w trybie zbiorczym.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.author: robinsh
ms.openlocfilehash: 5dd93af7deec2b0c8c90f6a8586de905207ad0a6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796359"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Importowanie i eksportowanie tożsamości urządzenia usługi IoT Hub w trybie zbiorczym

Każde Centrum IoT hub zawiera rejestr tożsamości używanej do tworzenia zasobów na poziomie urządzenia w usłudze. Rejestr tożsamości umożliwia także kontrolować dostęp do punktów końcowych przeznaczonych dla urządzenia. W tym artykule opisano, jak importować i eksportować tożsamości urządzeń w trybie zbiorczym do i z rejestru tożsamości.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Importowanie i eksportowanie działania mają miejsce w kontekście *zadania* , pozwalają na wykonywanie operacji usługi zbiorcze względem usługi IoT hub.

**RegistryManager** klasa zawiera **ExportDevicesAsync** i **ImportDevicesAsync** metody, które używają **zadania** framework. Te metody umożliwiają eksportowanie, importowania i synchronizacji w całości w rejestrze tożsamości Centrum IoT.

W tym temacie omówiono przy użyciu **RegistryManager** klasy i **zadania** systemu, aby wykonywać zbiorcze przywozu i wywozu urządzeń do i z rejestru tożsamości usługi IoT hub. Azure IoT Hub Device Provisioning Service umożliwia również włączyć bezobsługowe, just-in-time aprowizacji IoT Hub, nie wymagając interwencji człowieka. Aby dowiedzieć się więcej, zobacz [inicjowania obsługi dokumentacji usługi](/azure/iot-dps).


## <a name="what-are-jobs"></a>Co to są zadania?

Użyj operacje rejestru tożsamości **zadania** systemu podczas operacji:

* Ma potencjalnie długiego czasu wykonywania w porównaniu do standardowych operacji w czasie wykonywania.

* Zwraca dużą ilość danych użytkownika.

Zamiast jednego wywołania interfejsu API oczekiwania lub blokowania na wynik operacji, tworzy asynchronicznie operację **zadania** dla tego Centrum IoT hub. Operacja, a następnie natychmiast zwraca **JobProperties** obiektu.

Poniższy fragment kodu języka C# pokazano, jak utworzyć zadanie eksportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Aby użyć **RegistryManager** klasy w kodzie C#, należy dodać **Microsoft.Azure.Devices** pakiet NuGet do projektu. **RegistryManager** klasa się zebrała **Microsoft.Azure.Devices** przestrzeni nazw.

Możesz użyć **RegistryManager** klasy, aby zbadać stan **zadania** przy użyciu zwracanego **JobProperties** metadanych. Aby utworzyć wystąpienie **RegistryManager** klasy, należy użyć **CreateFromConnectionString** metody.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Aby znaleźć parametry połączenia dla Centrum IoT, w witrynie Azure portal:

- Przejdź do centrum IoT Hub.

- Wybierz **zasady dostępu współdzielonego**.

- Wybierz zasady, biorąc pod uwagę potrzebnych uprawnień.

- Skopiuj parametry połączenia w panelu po prawej stronie ekranu.

Poniższy fragment kodu języka C# pokazuje, jak i sondowanie co pięć sekund, aby zobaczyć, jeśli zadanie zostało zakończone, wykonywania:

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

## <a name="device-importexport-job-limits"></a>Limity zadania importu/eksportu urządzeń

Można importować tylko 1 aktywnych urządzeń lub zadanie eksportu jest dozwolona na raz dla wszystkich warstw usługi IoT Hub. Usługa IoT Hub ma również limity szybkości operacji zadań. Aby dowiedzieć się więcej, zobacz [odwołanie — IoT Hub przydziałów i dławienia](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Eksportowanie urządzeń

Użyj **ExportDevicesAsync** metodę, aby wyeksportować całości w rejestrze tożsamości Centrum IoT na [usługi Azure Storage](../storage/index.yml) kontenera obiektów blob przy użyciu [sygnatura dostępu współdzielonego](../storage/common/storage-security-guide.md#data-plane-security).

Ta metoda umożliwia tworzenie niezawodnych kopii zapasowych danych urządzenia w kontenerze obiektów blob, które możesz kontrolować.

**ExportDevicesAsync** metoda wymaga dwóch parametrów:

* A *ciąg* zawierający identyfikator URI kontenera obiektów blob. Ten identyfikator URI musi zawierać token sygnatury dostępu Współdzielonego, który przyznaje uprawnienia do zapisu w kontenerze. To zadanie tworzy blokowych obiektów blob, w tym kontenerze, do przechowywania danych urządzeń Zserializowany eksportu. Token sygnatury dostępu Współdzielonego, należy uwzględnić następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* A *logiczna* oznacza to, jeśli chcesz wykluczyć klucze uwierzytelniania z Twoich danych eksportu. Jeśli **false**, klucze uwierzytelniania znajdują się w danych wyjściowych eksportu. W przeciwnym razie klucze są eksportowane jako **null**.

Poniższy fragment kodu języka C# pokazano, jak zainicjować zadanie eksportu, która zawiera klucze uwierzytelniania urządzeń w danych eksportu, a następnie sondowania pod kątem ukończenia:

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

Zadania są przechowywane dane wyjściowe w kontenerze obiektów blob podana jako blokowych obiektów blob o nazwie **devices.txt**. Dane wyjściowe składa się z danych urządzenia Zserializowany do ciągu JSON, jedno urządzenie w każdym wierszu.

Poniższy przykład przedstawia dane wyjściowe:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Jeśli urządzenie ma bliźniaczą reprezentację danych, następnie danych bliźniaczej reprezentacji eksportowane są również wraz z danymi urządzenia. Ten format można znaleźć w poniższym przykładzie. Wszystkie dane z wiersza "twinETag" do momentu zakończenia bliźniaczej reprezentacji danych.

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

Jeśli potrzebujesz dostępu do tych danych w kodzie, łatwo może wykonywać deserializację tego przy użyciu danych **ExportImportDevice** klasy. Poniższy fragment kodu języka C# pokazano, jak odczytywać informacje o urządzeniu, który został wcześniej wyeksportowany do blokowego obiektu blob:

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

## <a name="import-devices"></a>Importuj urządzenia

**ImportDevicesAsync** method in Class metoda **RegistryManager** klasa służy do wykonywania zbiorczych operacji importowania i synchronizacji w rejestrze tożsamości Centrum IoT. Podobnie jak **ExportDevicesAsync** metody **ImportDevicesAsync** metoda używa **zadania** framework.

Powinien zachować ostrożność przy użyciu **ImportDevicesAsync** metody ponieważ poza aprowizowaniem nowe urządzenia w rejestrze tożsamości, można również aktualizować i usuwać istniejące urządzenia.

> [!WARNING]
> Operacja importowania nie można cofnąć. Zawsze tworzyć kopie zapasowe z istniejącymi danymi za pomocą **ExportDevicesAsync** metody do innego kontenera obiektów blob, zanim wprowadzisz zbiorcze zmiany do rejestru tożsamości.

**ImportDevicesAsync** metoda przyjmuje dwa parametry:

* A *ciąg* zawierający identyfikator URI zestawu [usługi Azure Storage](../storage/index.yml) kontenera obiektów blob do użycia jako *wejściowych* do zadania. Ten identyfikator URI musi zawierać token sygnatury dostępu Współdzielonego, który przyznaje dostęp do odczytu do kontenera. Ten kontener może zawierać obiektu blob o nazwie **devices.txt** zawierający dane serializowane urządzenia do zaimportowania do Twojego rejestru tożsamości. Importuj dane mogą zawierać informacje o urządzeniu w taki sam JSON format **ExportImportDevice** zadanie używa podczas tworzenia **devices.txt** obiektu blob. Token sygnatury dostępu Współdzielonego, należy uwzględnić następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* A *ciąg* zawierający identyfikator URI zestawu [usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kontenera obiektów blob do użycia jako *dane wyjściowe* z zadania. To zadanie tworzy blokowych obiektów blob w tym kontenerze, aby przechowywać informacje o błędzie z zakończono import **zadania**. Token sygnatury dostępu Współdzielonego, należy uwzględnić następujące uprawnienia:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Dwa parametry może wskazywać tego samego kontenera obiektów blob. Oddzielne parametry po prostu włącz większą kontrolę nad danymi jako kontenera danych wyjściowych wymaga dodatkowych uprawnień.

Poniższy fragment kodu języka C# pokazano, jak zainicjować zadania importu:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tę metodę można również zaimportować dane do bliźniaczej reprezentacji urządzenia. Format danych wejściowych jest taka sama jak w formacie pokazanym na **ExportDevicesAsync** sekcji. W ten sposób można ponownie zaimportować wyeksportowane dane. **$Metadata** jest opcjonalne.

## <a name="import-behavior"></a>Zachowanie importu

Możesz użyć **ImportDevicesAsync** metodę, aby wykonywać następujące operacje zbiorcze w rejestrze tożsamości:

* Rejestracja zbiorcza nowych urządzeń
* Zbiorcze usuwanie istniejących urządzeń
* Zbiorcze zmiany stanu (Włączanie lub wyłączanie urządzeń)
* Przypisywanie zbiorcze nowe klucze uwierzytelniania urządzeń
* Zbiorcze automatycznie — ponowne generowanie kluczy uwierzytelniania urządzenia
* Aktualizacja zbiorcza bliźniaczej reprezentacji danych

Może wykonywać dowolne kombinacje poprzedniej operacji w obrębie pojedynczego **ImportDevicesAsync** wywołania. Na przykład można zarejestrować nowych urządzeń i usuń lub zaktualizuj istniejących urządzeń w tym samym czasie. Gdy jest używana wraz z **ExportDevicesAsync** metody, można całkowicie migracji wszystkich urządzeń z jednego centrum IoT, do innej.

Jeśli plik importu zawiera bliźniaczą reprezentację metadanych, te metadane zastępuje istniejące metadane bliźniaczej reprezentacji. Jeśli plik importu nie zawiera metadanych bliźniaczej reprezentacji następnie tylko `lastUpdateTime` metadanych jest aktualizowany przy użyciu bieżącego czasu.

Użyj opcjonalnego **parametrem importMode** właściwości w danych serializacji importu dla każdego urządzenia do kontrolowania importu procesu na urządzenia. **Parametrem importMode** właściwość ma następujące opcje:

| parametrem importMode | Opis |
| --- | --- |
| **createOrUpdate** |Jeśli nie istnieje urządzenie o określonej **identyfikator**, to nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje zostały zastąpione podane dane wejściowe bez odniesieniu do **ETag** wartość. <br> Użytkownik może określić opcjonalnie bliźniaczej reprezentacji danych wraz z danymi urządzenia. Element etag bliźniaczej reprezentacji, jeśli zostanie określony, są przetwarzane niezależnie od etag urządzenia. W przypadku niezgodności tagu etag istniejących bliźniaczej reprezentacji pliku dziennika zapisywany jest błąd. |
| **create** |Jeśli nie istnieje urządzenie o określonej **identyfikator**, to nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, do pliku dziennika zapisywany jest błąd. <br> Użytkownik może określić opcjonalnie bliźniaczej reprezentacji danych wraz z danymi urządzenia. Element etag bliźniaczej reprezentacji, jeśli zostanie określony, są przetwarzane niezależnie od etag urządzenia. W przypadku niezgodności tagu etag istniejących bliźniaczej reprezentacji pliku dziennika zapisywany jest błąd. |
| **Aktualizacja** |Jeśli urządzenie już istnieje z określonym **identyfikator**, istniejące informacje zostały zastąpione podane dane wejściowe bez odniesieniu do **ETag** wartość. <br/>Jeśli urządzenie nie istnieje, do pliku dziennika zapisywany jest błąd. |
| **updateIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikator**, istniejące informacje zostały zastąpione dane wejściowe podane tylko wtedy, gdy **ETag** zgodny. <br/>Jeśli urządzenie nie istnieje, do pliku dziennika zapisywany jest błąd. <br/>W przypadku **ETag** niezgodność zapisywany jest błąd w pliku dziennika. |
| **createOrUpdateIfMatchETag** |Jeśli nie istnieje urządzenie o określonej **identyfikator**, to nowo zarejestrowane. <br/>Jeśli urządzenie już istnieje, istniejące informacje zostały zastąpione dane wejściowe podane tylko wtedy, gdy **ETag** zgodny. <br/>W przypadku **ETag** niezgodność zapisywany jest błąd w pliku dziennika. <br> Użytkownik może określić opcjonalnie bliźniaczej reprezentacji danych wraz z danymi urządzenia. Element etag bliźniaczej reprezentacji, jeśli zostanie określony, są przetwarzane niezależnie od etag urządzenia. W przypadku niezgodności tagu etag istniejących bliźniaczej reprezentacji pliku dziennika zapisywany jest błąd. |
| **usuwanie** |Jeśli urządzenie już istnieje z określonym **identyfikator**, jest usuwany bez odniesieniu do **ETag** wartość. <br/>Jeśli urządzenie nie istnieje, do pliku dziennika zapisywany jest błąd. |
| **deleteIfMatchETag** |Jeśli urządzenie już istnieje z określonym **identyfikator**, zostanie usunięta tylko wtedy, gdy **ETag** zgodny. Jeśli urządzenie nie istnieje, do pliku dziennika zapisywany jest błąd. <br/>W przypadku niezgodności tagu ETag do pliku dziennika zapisywany jest błąd. |

> [!NOTE]
> Jeśli dane serializacji nie definiuje jawnie **parametrem importMode** flagi dla urządzenia, jego wartość domyślna to **createOrUpdate** podczas operacji importowania.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importuj przykład urządzeń — zbiorcze Inicjowanie obsługi administracyjnej urządzeń

Poniższy przykład kodu C# ilustruje sposób generowania wielu tożsamości urządzeń który:

* Obejmują klucze uwierzytelniania.
* Informacje o tym urządzeniu należy zapisać do blokowego obiektu blob.
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

## <a name="import-devices-example--bulk-deletion"></a>Importuj przykład urządzeń — zbiorczego usuwania

Poniższy przykład kodu pokazuje, jak usuwać urządzenia dodany, korzystając z poprzedniego przykładu kodu:

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

Poniższy przykładowy kod przedstawia sposób generowania [identyfikatora URI połączenia SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) odczytu, zapisu i usuwania uprawnień dla kontenera obiektów blob:

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

W tym artykule przedstawiono sposób wykonywania operacji zbiorczej dotyczących w rejestrze tożsamości w Centrum IoT hub. Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat zarządzania usługi Azure IoT Hub:

* [Metryki usługi IoT Hub](iot-hub-metrics.md)
* [Dzienniki usługi IoT Hub](iot-hub-monitor-resource-health.md)

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)
* [Wdrażanie rozwiązań SI na urządzeniach brzegowych za pomocą usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Aby zapoznać się z pomocą IoT Hub Device Provisioning Service do włączenia aprowizacji bezobsługowe, just-in-time, zobacz: 

* [Usługa Azure IoT Hub Device Provisioning](/azure/iot-dps)
