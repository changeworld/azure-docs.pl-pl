---
title: Zbiorcze eksportowanie i importowanie rejestracji usługi Azure Notification Hubs | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać obsługi zbiorczej Centrów powiadomień do wykonywania dużej liczby operacji w centrum powiadomień lub eksportowania wszystkich rejestracji.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212404"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Zbiorcze eksportowanie i importowanie rejestracji usługi Azure Notification Hubs
Istnieją scenariusze, w których jest wymagane do tworzenia lub modyfikowania dużej liczby rejestracji w centrum powiadomień. Niektóre z tych scenariuszy są aktualizacje tagów po obliczeń wsadowych lub migracji istniejącej implementacji wypchnięcia do korzystania z Centrum powiadomień.

W tym artykule wyjaśniono, jak wykonać dużą liczbę operacji w centrum powiadomień lub wyeksportować wszystkie rejestracje zbiorczo.

## <a name="high-level-flow"></a>Przepływ wysokiego poziomu
Obsługa wsadowa jest przeznaczona do obsługi długotrwałych zadań obejmujących miliony rejestracji. Aby osiągnąć tę skalę, obsługa wsadowa używa usługi Azure Storage do przechowywania szczegółów zadań i danych wyjściowych. W przypadku operacji aktualizacji zbiorczej użytkownik jest zobowiązany do utworzenia pliku w kontenerze obiektów blob, którego zawartość jest listą operacji aktualizacji rejestracji. Podczas uruchamiania zadania użytkownik udostępnia adres URL do wejściowego obiektu blob wraz z adresem URL do katalogu wyjściowego (również w kontenerze obiektów blob). Po rozpoczęciu zadania użytkownik może sprawdzić stan, odpytując lokalizację adresu URL pod warunkiem, że rozpocznie się zadanie. Określone zadanie może wykonywać tylko operacje określonego rodzaju (tworzy, aktualizuje lub usuwa). Operacje eksportu są wykonywane analogicznie.

## <a name="import"></a>Import

### <a name="set-up"></a>Konfiguruj
W tej sekcji założono, że masz następujące jednostki:

- Centrum powiadomień aprowizowanych.
- Kontener obiektów blob usługi Azure Storage.
- Odwołania do [pakietu NuGet usługi Azure Storage i pakietu](https://www.nuget.org/packages/windowsazure.storage/) [NuGet centrum powiadomień.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

### <a name="create-input-file-and-store-it-in-a-blob"></a>Tworzenie pliku wejściowego i przechowywanie go w obiekcie blob
Plik wejściowy zawiera listę rejestracji szeregowanych w formacie XML, po jednym w wierszu. Za pomocą narzędzia Azure SDK, w poniższym przykładzie kodu pokazano, jak serializować rejestracji i przekazać je do kontenera obiektów blob.

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
> Poprzedni kod serializuje rejestracje w pamięci, a następnie przekazuje cały strumień do obiektu blob. Jeśli prześlesz plik o rozmiarze więcej niż kilka megabajtów, zobacz wskazówki dotyczące obiektów blob platformy Azure dotyczące wykonywania tych kroków; na przykład [blokować obiekty blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Tworzenie tokenów adresów URL
Po przekazaniu pliku wejściowego wygeneruj adresy URL, aby udostępnić do centrum powiadomień zarówno plik wejściowy, jak i katalog wyjściowy. Można użyć dwóch różnych kontenerów obiektów blob dla danych wejściowych i wyjściowych.

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
Za pomocą dwóch wejściowych i wyjściowych adresów URL można teraz uruchomić zadanie wsadowe.

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

Oprócz wejściowych i wyjściowych adresów URL `NotificationHubJob` w tym `JobType` przykładzie tworzy obiekt zawierający obiekt, który może być jednym z następujących typów:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Po zakończeniu połączenia zadanie jest kontynuowane przez centrum powiadomień i można sprawdzić jego stan za pomocą wywołania [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Po zakończeniu zadania można sprawdzić wyniki, patrząc na następujące pliki w katalogu wyjściowym:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Pliki te zawierają listę udanych i nieudanych operacji z partii. Format pliku `.cvs`jest , w którym każdy wiersz ma numer wiersza oryginalnego pliku wejściowego i dane wyjściowe operacji (zwykle utworzony lub zaktualizowany opis rejestracji).

### <a name="full-sample-code"></a>Pełny przykładowy kod
Poniższy przykładowy kod importuje rejestracje do centrum powiadomień.

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
Rejestracja eksportu jest podobna do importu, z następującymi różnicami:

- Potrzebny jest tylko wyjściowy adres URL.
- Tworzenie NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Przykładowy fragment kodu
Oto przykładowy fragment kodu do eksportowania rejestracji w języku Java:

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

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o rejestracjach, zobacz następujące artykuły:

- [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md)
- [Tagi do rejestracji](notification-hubs-tags-segment-push-message.md)
- [Rejestracja szablonu](notification-hubs-templates-cross-platform-push-messages.md)
