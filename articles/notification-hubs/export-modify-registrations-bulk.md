---
title: Eksportowanie i importowanie usługi Azure Notification Hubs rejestracji zbiorczych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z pomocy technicznej zbiorcze usługi Notification Hubs, przeprowadzenie dużej liczby operacji w Centrum powiadomień, lub aby wyeksportować wszystkie rejestracje.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: d7e38e8eca58c06fc6896887522b320a797fc42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575300"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Eksportowanie i importowanie usługi Azure Notification Hubs rejestracji zbiorczej
Istnieją scenariusze, w których jest wymagany, aby utworzyć lub zmodyfikować dużą liczbę rejestracji w Centrum powiadomień. Niektóre z tych scenariuszy są aktualizacje tag następujące obliczeń wsadowych, czy migrujesz istniejącą implementację wypychania używać usługi Notification Hubs.

W tym artykule wyjaśniono, jak przeprowadzić dużą liczbę operacji w Centrum powiadomień lub aby wyeksportować wszystkie rejestracje zbiorczo.

## <a name="high-level-flow"></a>Ogólny przepływ
Obsługa usługi Batch jest przeznaczony do obsługi długotrwałych zadań, obejmujących miliony rejestracji. Do osiągnięcia tej skali, obsługę usługi batch używa usługi Azure Storage do przechowywania szczegółów zadania i dane wyjściowe. W przypadku operacji aktualizacji zbiorczej użytkownika jest wymagane do utworzenia pliku w kontenerze obiektów blob, w których zawartość znajduje się lista operacje aktualizacji operacji. Podczas uruchamiania zadania, użytkownik udostępnia adres URL do wejściowego obiektu blob oraz adres URL do katalogu wyjściowego (również w kontenerze obiektów blob). Po rozpoczęciu zadania użytkownika można sprawdzić stan, badając lokalizacji adresu URL podany podczas uruchamiania zadania. Określonego zadania można wykonać tylko operacje tworzenia określonego typu (tworzy, aktualizuje lub usuwa). Analogicznie wykonywane są operacje eksportu.

## <a name="import"></a>Import

### <a name="set-up"></a>Konfigurowanie
W tej sekcji założono, że masz następujące jednostki:

- Centrum powiadomień elastycznie.
- Kontener obiektów blob usługi Azure Storage.
- Odwołuje się do [pakietu NuGet usługi Azure Storage](https://www.nuget.org/packages/windowsazure.storage/) i [pakietu NuGet usługi Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Tworzenie pliku wejściowego i zapisz go w obiekcie blob
Plik wejściowy zawiera listę rejestracje serializowane w kodzie XML, jeden na wiersz. Przy użyciu zestawu SDK platformy Azure, w poniższym przykładzie kodu pokazano, jak do serializacji rejestracji i przekazać je do kontenera obiektów blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Powyższy kod serializuje rejestracje w pamięci, a następnie przekazuje całe strumienia do obiektu blob. Jeśli przekazano plik więcej niż kilku megabajtów, zobacz wskazówki dotyczące obiektów blob platformy Azure na temat sposobu wykonywania tych kroków; na przykład [blokowe obiekty BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Utwórz adres URL tokenów
Po przekazaniu pliku wejściowego, należy wygenerować adresy URL, aby zapewnić do Centrum powiadomień dla pliku wejściowego i katalog wyjściowy. Korzystaj z dwóch kontenerów inny obiektu blob, dane wejściowe i wyjściowe.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Przesyłanie zadania
Za pomocą dwóch danych wejściowych i wyjściowych adresy URL możesz teraz rozpocząć zadanie wsadowe.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Oprócz przychodzące i wychodzące adresy URL, w tym przykładzie jest tworzony `NotificationHubJob` obiekt, który zawiera `JobType` obiektu, który może być jedną z następujących typów:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Gdy nawiązać połączenia, zadanie jest kontynuowane przez Centrum powiadomień i mogli sprawdzać jego stan, z wywołaniem [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Po ukończeniu zadania możesz sprawdzić wyniki, analizując następujące pliki w katalogu danych wyjściowych:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Te pliki zawierają listę udane i nieudane operacje z usługi batch. Format pliku jest `.cvs`, w którym każdy wiersz zawiera numer wiersza, oryginalnym pliku wejściowego, a wynik operacji (zazwyczaj opis utworzone lub zaktualizowane rejestracji).

### <a name="full-sample-code"></a>Pełny przykładowy kod
Następujący przykładowy kod importuje rejestracji do danego centrum powiadomień.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Eksportowanie
Eksportowanie rejestracji jest podobne do importowania, z następującymi różnicami:

- Wystarczy tylko adres URL danych wyjściowych.
- Możesz utworzyć NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Fragment kodu z próbki
Poniżej przedstawiono przykładowe wstawki kodu programu do eksportowania rejestracje w języku Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat rejestracji, zobacz następujące artykuły:

- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Tagi do rejestracji](notification-hubs-tags-segment-push-message.md)
- [Rejestracja szablonu](notification-hubs-templates-cross-platform-push-messages.md)
