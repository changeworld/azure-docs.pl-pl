---
title: Reagowanie na zdarzenia modułu magazynu obiektów blob — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Reagowanie na zdarzenia modułu magazynu obiektów blob
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 12/13/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3360b92a1b71adcbf0364a16c197aecdab5700db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086607"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Samouczek: Reagowanie na zdarzenia magazynu obiektów blob w usłudze IoT Edge (wersja zapoznawcza)
W tym artykule pokazano, jak wdrożyć usługę Azure Blob Storage w module IoT, który działa jako wydawca usługi Event Grid do wysyłania zdarzeń w tworzeniu obiektów Blob i usuwania obiektów blob do siatki zdarzeń.  

Aby uzyskać omówienie usługi Azure Blob Storage w usłudze IoT Edge, zobacz [usługa Azure Blob Storage w usłudze IoT Edge](../../iot-edge/how-to-store-data-blob.md) i jej funkcjach.

> [!WARNING]
> Usługa Azure Blob Storage w usłudze IoT Edge integracja z siatką zdarzeń jest w wersji zapoznawczej

Aby ukończyć ten samouczek, trzeba będzie:

* **Subskrypcja platformy Azure** — utwórz [bezpłatne konto,](https://azure.microsoft.com/free) jeśli jeszcze go nie masz. 
* **Usługa Azure IoT Hub i urządzenie usługi IoT Edge** — postępuj zgodnie z instrukcjami w przewodniku Szybki start dla urządzeń [z systemem Linux](../../iot-edge/quickstart-linux.md) lub [Windows,](../../iot-edge/quickstart.md) jeśli jeszcze go nie masz.

## <a name="deploy-event-grid-iot-edge-module"></a>Wdrażanie modułu IoT Edge w siatce zdarzeń

Istnieje kilka sposobów wdrażania modułów na urządzeniu usługi IoT Edge i wszystkie z nich działają w usłudze Azure Event Grid w usłudze IoT Edge. W tym artykule opisano kroki wdrażania usługi Event Grid w usłudze IoT Edge z witryny Azure portal.

>[!NOTE]
> W tym samouczku wdrożysz moduł siatki zdarzeń bez trwałości. Oznacza to, że wszystkie tematy i subskrypcje utworzone w tym samouczku zostaną usunięte po przemieszczeniu modułu. Aby uzyskać więcej informacji na temat konfigurowania trwałości, zobacz następujące artykuły: [Utrwalić stan w systemie Linux](persist-state-linux.md) lub Persist state w systemie [Windows](persist-state-windows.md). W przypadku obciążeń produkcyjnych zaleca się zainstalowanie modułu event grid z trwałością.


### <a name="select-your-iot-edge-device"></a>Wybieranie urządzenia IoT Edge

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Przejdź do centrum IoT Hub.
1. Wybierz pozycję **IoT Edge** z menu w sekcji **Automatyczne zarządzanie urządzeniami.** 
1. Kliknij identyfikator urządzenia docelowego z listy urządzeń
1. Wybierz pozycję **Ustaw moduły**. Zachowaj otwartą stronę. Będziesz kontynuować kroki w następnej sekcji.

### <a name="configure-a-deployment-manifest"></a>Konfigurowanie manifestu wdrażania

Manifest wdrożenia to dokument JSON, który opisuje, które moduły do wdrożenia, jak przepływy danych między modułami i żądane właściwości bliźniaczych reprezentacji modułu. Portal Azure ma kreatora, który przeprowadzi Cię przez tworzenie manifestu wdrożenia, zamiast ręcznego tworzenia dokumentu JSON.  Ma trzy kroki: **Dodaj moduły,** **Określ trasy**i **Przejrzyj wdrożenie**.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj**
1. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
1. Podaj nazwę, obraz, opcje tworzenia kontenera:

   * **Nazwa**: eventgridmodule
   * **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Opcje tworzenia kontenera:**

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Kliknij **przycisk Zapisz**
 1. Przejdź do następnej sekcji, aby dodać moduł subskrybenta usługi Azure Event Grid przed ich wdrożeniem razem.

    >[!IMPORTANT]
    > W tym samouczku dowiesz się wdrożyć moduł siatki zdarzeń, aby umożliwić zarówno http/HTTPs żądań, uwierzytelnianie klienta wyłączone. W przypadku obciążeń produkcyjnych zaleca się włączenie tylko żądań HTTP i subskrybentów z włączonym uwierzytelnianiem klienta. Aby uzyskać więcej informacji na temat bezpiecznego konfigurowania modułu event grid, zobacz [Zabezpieczenia i uwierzytelnianie](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Wdrażanie modułu usługi IoT Edge subskrybenta usługi Event Grid

W tej sekcji pokazano, jak wdrożyć inny moduł IoT, który będzie działać jako program obsługi zdarzeń, do których zdarzenia mogą być dostarczane.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj** ponownie. 
1. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
1. Podaj opcje tworzenia nazwy, obrazu i kontenera kontenera:

   * **Nazwa**: subskrybent
   * **Identyfikator URI obrazu**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Opcje tworzenia kontenera:** Brak
1. Kliknij **przycisk Zapisz**
1. Przejdź do następnej sekcji, aby dodać moduł usługi Azure Blob Storage

## <a name="deploy-azure-blob-storage-module"></a>Wdrażanie modułu usługi Azure Blob Storage

W tej sekcji pokazano, jak wdrożyć moduł usługi Azure Blob Storage, który działa jako wydawca usługi Event Grid publikowania obiektów Blob utworzonych i usuniętych zdarzeń.

### <a name="add-modules"></a>Dodaj moduły

1. W sekcji **Moduły wdrażania** wybierz pozycję **Dodaj**
2. Z typów modułów z listy rozwijanej wybierz opcję **Moduł krawędzi IoT**
3. Podaj opcje tworzenia nazwy, obrazu i kontenera kontenera:

   * **Nazwa**: azureblobstorageoniotedge
   * **Identyfikator URI obrazu**: mcr.microsoft.com/azure-blob-storage:latest
   * **Opcje tworzenia kontenera:**

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Moduł magazynu obiektów Blob może publikować zdarzenia przy użyciu protokołu HTTPS i HTTP. 
   > - Jeśli włączono uwierzytelnianie oparte na kliencie dla EventGrid, upewnij się, że aktualizujesz wartość `EVENTGRID_ENDPOINT=https://<event grid module name>:4438`EVENTGRID_ENDPOINT, aby umożliwić https, w ten sposób: .
   > - Dodaj również inną `AllowUnknownCertificateAuthority=true` zmienną środowiskową do powyższego Json. Podczas rozmowy z EventGrid za pośrednictwem protokołu HTTPS, **AllowUnknownCertificateAuthority** umożliwia modułowi magazynu ufanie certyfikatom serwera EventGrid z podpisem własnym.

4. Zaktualizuj skopiowany JSON, aby uzyskać następujące informacje:

   - Zastąp `<your storage account name>` nazwą, którą zapamiętasz. Nazwy kont powinny mieć od 3 do 24 znaków, z małych liter i cyfr. Brak spacji.

   - Wymień `<your storage account key>` na 64-bajtowy klucz base64. Klucz można wygenerować za pomocą narzędzi takich jak [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Te poświadczenia będą używane do uzyskiwania dostępu do magazynu obiektów blob z innych modułów.

   - Zamień `<event grid module name>` na nazwę modułu Siatki zdarzeń.
   - Wymień `<storage mount>` zgodnie z systemem operacyjnym kontenera.
     - W przypadku kontenerów systemu Linux **mój wolumin:/blobroot**
     - W przypadku kontenerów systemu Windows**mój wolumin:C:/BlobRoot**

5. Kliknij **przycisk Zapisz**
6. Kliknij **przycisk Dalej,** aby przejść do sekcji Trasy

    > [!NOTE]
    > Jeśli używasz maszyny Wirtualnej platformy Azure jako urządzenia brzegowego, dodaj regułę portu przychodzącego, aby zezwolić na ruch przychodzący na portach hosta używanych w tym samouczku: 4438, 5888, 8080 i 11002. Aby uzyskać instrukcje dotyczące dodawania reguły, zobacz [Jak otworzyć porty na maszynie Wirtualnej](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Ustawianie tras

Zachowaj trasy domyślne, a następnie wybierz pozycję **Dalej,** aby przejść do sekcji recenzji

### <a name="review-deployment"></a>Przegląd wdrożenia

1. Sekcja przeglądu zawiera manifest wdrożenia JSON, który został utworzony na podstawie wybranych w poprzedniej sekcji. Upewnij się, że widzisz następujące cztery moduły: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **subskrybent** i **azureblobstorageoniotedge,** które są wdrażane.
2. Przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Prześlij**.

## <a name="verify-your-deployment"></a>Weryfikowanie wdrożenia

1. Po przesłaniu wdrożenia powrócisz do strony usługi IoT Edge w centrum IoT Hub.
2. Wybierz **urządzenie usługi IoT Edge,** które są przeznaczone dla wdrożenia, aby otworzyć jego szczegóły.
3. W szczegółach urządzenia sprawdź, czy moduły eventgridmodule, subskrybenta i azureblobstorageoniotedge są wymienione zarówno jako **określone we wdrożeniu,** jak i **zgłoszone przez urządzenie**.

   Może upłynąć kilka chwil, aby moduł został uruchomiony na urządzeniu, a następnie zgłoszony z powrotem do usługi IoT Hub. Odśwież stronę, aby wyświetlić zaktualizowany stan.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publikowanie zdarzeń BlobCreated i BlobDeleted

1. Ten moduł automatycznie tworzy temat **MicrosoftStorage**. Sprawdź, czy istnieje
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Przykładowe dane wyjściowe:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem klucza Sygnatury dostępu Współdzielonego, klucz sygnatury dostępu Współdzielonego określony wcześniej powinien zostać dodany jako nagłówek. W związku z tym żądanie curl będzie:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zwijania będzie następujące:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Subskrybenci mogą rejestrować się w przypadku zdarzeń opublikowanych w temacie. Aby otrzymać dowolne zdarzenie, musisz utworzyć subskrypcję usługi Event Grid dla tematu **MicrosoftStorage.**
    1. Utwórz plik blobsubscription.json z następującą zawartością. Szczegółowe informacje na temat ładunku można znaleźć w [dokumentacji interfejsu API](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Właściwość **endpointType** określa, że subskrybent jest elementem **Webhook**.  **EndpointUrl** określa adres URL, pod którym subskrybent nasłuchuje zdarzeń. Ten adres URL odpowiada przykładowi funkcji platformy Azure wdrożonym wcześniej.

    2. Uruchom następujące polecenie, aby utworzyć subskrypcję dla tematu. Upewnij się, że widzisz `200 OK`kod stanu HTTP jest .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem klucza Sygnatury dostępu Współdzielonego, klucz sygnatury dostępu Współdzielonego określony wcześniej powinien zostać dodany jako nagłówek. W związku z tym żądanie curl będzie:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zwijania będzie następujące:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Uruchom następujące polecenie, aby sprawdzić subskrypcję został utworzony pomyślnie. Kod stanu HTTP 200 OK powinny być zwracane.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Przykładowe dane wyjściowe:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem klucza Sygnatury dostępu Współdzielonego, klucz sygnatury dostępu Współdzielonego określony wcześniej powinien zostać dodany jako nagłówek. W związku z tym żądanie curl będzie:`curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - W przypadku przepływu HTTPS, jeśli uwierzytelnianie klienta jest włączone za pośrednictwem certyfikatu, żądanie zwijania będzie następujące:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Pobierz [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) i podłącz go do magazynu [lokalnego](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Weryfikowanie dostarczania zdarzeń

### <a name="verify-blobcreated-event-delivery"></a>Weryfikowanie dostarczania zdarzeń blobCreated

1. Przekaż pliki jako blokowe obiekty blob do magazynu lokalnego z Eksploratora usługi Azure Storage, a moduł automatycznie opublikuje zdarzenia tworzenia. 
2. Zapoznaj się z dziennikami subskrybentów dla zdarzenia create. Postępuj zgodnie z instrukcjami, aby [zweryfikować dostarczanie zdarzeń](pub-sub-events-webhook-local.md#verify-event-delivery)

    Przykładowe wyjście:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Weryfikowanie dostarczania zdarzeń blobDeleted

1. Usuń obiekty blob z magazynu lokalnego przy użyciu Eksploratora usługi Azure Storage, a moduł automatycznie opublikuje zdarzenia usuwania. 
2. Zapoznaj się z dziennikami subskrybenta, aby usunąć zdarzenie. Postępuj zgodnie z instrukcjami, aby [zweryfikować dostarczanie zdarzeń](pub-sub-events-webhook-local.md#verify-event-delivery)

    Przykładowe wyjście:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Gratulacje! Ukończono samouczek. W poniższych sekcjach podano szczegółowe informacje na temat właściwości zdarzenia.

### <a name="event-properties"></a>Właściwości zdarzenia

Oto lista obsługiwanych właściwości zdarzeń oraz ich typów i opisów. 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| api | ciąg | Operacja, która wyzwoliła zdarzenie. Może to być jedna z następujących wartości: <ul><li>BlobCreated - dozwolone wartości `PutBlob` to: i`PutBlockList`</li><li>BlobDeleted - dozwolone `DeleteBlob`wartości `DeleteAfterUpload` `AutoDelete`są i . <p>Zdarzenie `DeleteAfterUpload` jest generowane, gdy obiekt blob jest automatycznie usuwany, ponieważ deleteAfterUpload żądana właściwość jest ustawiona na true. </p><p>`AutoDelete`zdarzenie jest generowany, gdy obiekt blob jest automatycznie usuwany, ponieważ deleteAfterMinutes żądana wartość właściwości wygasła.</p></li></ul>|
| identyfikator żądania klienta | ciąg | identyfikator żądania dostarczonego przez klienta dla operacji interfejsu API magazynu. Ten identyfikator może służyć do skorelowania z dziennikami diagnostycznymi usługi Azure Storage przy użyciu pola "client-request-id" w dziennikach i może być dostarczany w żądaniach klientów przy użyciu nagłówka "x-ms-client-request-id". Aby uzyskać szczegółowe informacje, zobacz [Format dziennika](/rest/api/storageservices/storage-analytics-log-format). |
| Requestid | ciąg | Identyfikator żądania wygenerowanego przez usługę dla operacji interfejsu API magazynu. Może służyć do skorelowania z dziennikami diagnostycznymi usługi Azure Storage przy użyciu pola "request-id-header" w dziennikach i jest zwracany z inicjowania wywołania interfejsu API w nagłówku "x-ms-request-id". Zobacz [Format dziennika](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | ciąg | Wartość, której można użyć do wykonywania operacji warunkowo. |
| Contenttype | ciąg | Typ zawartości określony dla obiektu blob. |
| Contentlength | liczba całkowita | Rozmiar obiektu blob w bajtach. |
| typ obiektu blob | ciąg | Typ obiektu blob. Prawidłowe wartości to "BlockBlob" lub "PageBlob". |
| url | ciąg | Ścieżka do obiektu blob. <br>Jeśli klient używa interfejsu API REST obiektów Blob, adres URL ma tę strukturę: * \<nazwa\>magazynu-account .blob.core.windows.net/\<nazwa\>/\<pliku\>kontenera*. <br>Jeśli klient używa interfejsu API REST magazynu usługi Data Lake, adres URL ma tę strukturę: * \<nazwa konta magazynu\>.dfs.core.windows.net/ nazwa\<pliku file-system-name\>/\<\>*. |


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły z dokumentacji magazynu obiektów blob:

- [Filtrowanie zdarzeń magazynu obiektów blob](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Zalecane praktyki dotyczące korzystania ze zdarzeń magazynu obiektów Blob](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

W tym samouczku opublikowano zdarzenia, tworząc lub usuwając obiekty blob w magazynie obiektów Blob platformy Azure. Zobacz inne samouczki, aby dowiedzieć się, jak przesyłać dalej zdarzenia do chmury (Usługa Azure Event Hub lub usługa Azure IoT Hub): 

- [Przesyłanie dalej zdarzeń do usługi Azure Event Grid](forward-events-event-grid-cloud.md)
- [Przesyłanie dalej zdarzeń do usługi Azure IoT Hub](forward-events-iothub.md)
