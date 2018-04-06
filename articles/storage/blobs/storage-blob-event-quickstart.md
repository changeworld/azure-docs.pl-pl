---
title: Trasy do punktu końcowego sieci web niestandardowego zdarzenia magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Zasubskrybuj zdarzenia usługi Blob Storage przy użyciu usługi Azure Event Grid.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: f0764ebc423cfb5323f2b634ce5a5ecbe075135c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="route-blob-storage-events-to-a-custom-web-endpoint-with-azure-cli"></a>Zdarzenia magazynu obiektów Blob trasy do punktu końcowego niestandardowe sieci web z wiersza polecenia platformy Azure

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule omówiono subskrybowanie zdarzeń usługi Blob Storage i wyzwalanie zdarzenia w celu wyświetlenia wyniku za pomocą interfejsu wiersza polecenia platformy Azure. 

Zazwyczaj wysyła się zdarzenia do punktu końcowego, który na nie reaguje, takiego jak element webhook lub funkcja platformy Azure. Aby uprościć przykład w tym artykule, omówimy wysłanie zdarzeń na adres URL, który tylko zbiera komunikaty. Ten adres URL jest tworzony w narzędziu innej firmy przy użyciu narzędzia [Hookbin](https://hookbin.com/).

> [!NOTE]
> **Hookbin** nie jest przeznaczony do użycia z wysokiej przepływności. Zastosowanie tego narzędzia ma charakter czysto poglądowy. W przypadku wypchnięcia więcej niż jednego zdarzenia w tym samym czasie w narzędziu mogą nie być widoczne wszystkie zdarzenia.

Po wykonaniu czynności opisanych w tym artykule dane powinny zostać wysłane do punktu końcowego.

[!INCLUDE [quickstarts-free-trial-note.md](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w najnowszej wersji (2.0.24 lub nowszej). Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

Jeśli nie korzystasz z usługi Cloud Shell, musisz się najpierw zalogować za pomocą polecenia `az login`.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). 

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `<resource_group_name>` w lokalizacji *westcentralus*.  Zamień `<resource_group_name>` na unikatową nazwę grupy zasobów.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Aby używać zdarzenia magazynu obiektów Blob, należy albo [kontem magazynu obiektów Blob](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) lub [konta magazynu ogólnego przeznaczenia v2](../common/storage-account-options.md#general-purpose-v2). **Ogólnego przeznaczenia v2 (GPv2)** są konta magazynu, które obsługują wszystkie funkcje dla wszystkich usług magazynu, w tym obiekty BLOB, plików, kolejek i tabel. A **kontem magazynu obiektów Blob** to specjalne konto magazynu do przechowywania danych bez struktury jako obiekty BLOB (obiekty) w usłudze Azure Storage. Konta magazynu obiektów blob są podobne do kont magazynu ogólnego przeznaczenia i udostępniać wszystkie trwałości, dostępności, skalowalności i wydajności zalety że używane obecnie, łącznie z 100% spójnością interfejsu API dla blokowych obiektów blob i uzupełnialnych obiektów blob. W przypadku aplikacji wymagających tylko magazynu obiektów blokowych lub uzupełnialnych obiektów blob zalecamy używanie kont usługi Blob Storage. 

> [!NOTE]
> Dostępność dla magazynu zdarzenia jest powiązany z siatki zdarzeń [dostępności](../../event-grid/overview.md) i będą dostępne w różnych regionach, tak jak w przypadku zdarzeń siatki.

Zamień `<storage_account_name>` na unikatową nazwę konta magazynu oraz `<resource_group_name>` na wcześniej utworzoną grupę zasobów.

```azurecli-interactive
az storage account create \
  --name <storage_account_name> \
  --location westcentralus \
  --resource-group <resource_group_name> \
  --sku Standard_LRS \
  --kind BlobStorage \
  --access-tier Hot
```

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zamiast pisać kod w celu zareagowania na zdarzenie, utwórzmy punkt końcowy, który będzie zbierać komunikaty, aby można było je wyświetlić. Hookbin to narzędzie innego producenta, które umożliwia utworzenie punktu końcowego i wyświetlanie wysyłanych do niego żądań. Przejdź do narzędzia [Hookbin](https://hookbin.com/) i kliknij pozycję **Create New Endpoint** (Utwórz nowy punkt końcowy).  Skopiuj adres URL pojemnika, ponieważ będzie on potrzebny podczas subskrybowania tematu.

## <a name="subscribe-to-your-storage-account"></a>Subskrypcja konta magazynu

Subskrybowanie tematu ma poinformować usługę Event Grid o tym, które zdarzenia chcesz śledzić. Poniższy przykład subskrybuje konta magazynu utworzone i przekazuje adres URL z Hookbin jako punkt końcowy powiadomienia o zdarzeniach. Zamień `<event_subscription_name>` na unikatową nazwę subskrypcji zdarzeń oraz `<endpoint_URL>` na wartość z poprzedniej sekcji. Dzięki określeniu punktu końcowego podczas subskrybowania usługa Event Grid obsługuje kierowanie zdarzeń do tego punktu końcowego. Jako parametrów `<resource_group_name>` i `<storage_account_name>` użyj utworzonych wcześniej wartości.  

```azurecli-interactive
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="trigger-an-event-from-blob-storage"></a>Wyzwalanie zdarzenia z usługi Blob Storage

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Po pierwsze umożliwia konfigurowanie nazwy i klucza konta magazynu, a następnie możemy utworzyć kontener, Utwórz i przekaż plik. Jako parametrów `<storage_account_name>` i `<resource_group_name>` ponownie użyj utworzonych wcześniej wartości.

```azurecli-interactive
export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY="$(az storage account keys list --account-name <storage_account_name> --resource-group <resource_group_name> --query "[0].value" --output tsv)"

az storage container create --name testcontainer

touch testfile.txt
az storage blob upload --file testfile.txt --container-name testcontainer --name testfile.txt
```

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Przejdź do adresu URL punktu końcowego utworzonego wcześniej. Ewentualnie kliknij przycisk Refresh (Odśwież) w otwartej przeglądarce. Zobaczysz właśnie wysłane zdarzenie. 

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "text/plain",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/testcontainer/testblob1.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli zamierzasz kontynuować pracę z tym kontem magazynu i tą subskrypcją zdarzeń, nie usuwaj zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

Zamień `<resource_group_name>` na utworzoną powyżej grupę zasobów.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Kolejne kroki

Wiesz już, jak tworzyć tematy i subskrypcje zdarzeń. Dowiedz się więcej na temat tego, co mogą Ci ułatwić zdarzenia usługi Blob Storage i usługa Event Grid:

- [Reagowanie na zdarzenia usługi Blob Storage](storage-blob-event-overview.md)
- [Event Grid — informacje](../../event-grid/overview.md)
