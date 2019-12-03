---
title: Dołączanie niestandardowego kontenera magazynu w systemie Linux
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy do kontenera systemu Linux w Azure App Service. Udostępnianie plików między aplikacjami, zdalne zarządzanie zawartością statyczną oraz uzyskiwanie dostępu lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 00c60edeefa5fd8d1304aa5fc301a3b0304f5ca3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671787"
---
# <a name="attach-azure-storage-containers-to-linux-containers"></a>Dołącz kontenery usługi Azure Storage do kontenerów systemu Linux

W tym przewodniku pokazano, jak dołączyć udziały sieciowe do App Service w systemie Linux przy użyciu [usługi Azure Storage](/azure/storage/common/storage-introduction). Korzyści obejmują bezpieczną zawartość, przenośność zawartości, magazyn trwały, dostęp do wielu aplikacji i wiele metod transferu.

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejąca aplikacja sieci Web (App Service w systemie Linux lub Web App for Containers).
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszy).

## <a name="create-azure-storage"></a>Tworzenie usługi Azure Storage

> [!NOTE]
> Usługa Azure Storage jest magazynem innym niż domyślne i rozliczane oddzielnie, a nie z aplikacją sieci Web.
>
> Używanie własnego magazynu nie jest obsługiwane przy użyciu konfiguracji zapory magazynu z powodu ograniczeń infrastruktury.
>

Utwórz konto usługi Azure [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Przekazywanie plików do usługi Azure Storage

Aby przekazać katalog lokalny do konta magazynu, użyj polecenia [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) , jak w poniższym przykładzie:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Łączenie magazynu z aplikacją internetową (wersja zapoznawcza)

> [!CAUTION]
> Połączenie istniejącego katalogu w aplikacji internetowej z kontem magazynu spowoduje usunięcie zawartości katalogu. Jeśli migrujesz pliki dla istniejącej aplikacji, Utwórz kopię zapasową aplikacji i jej zawartość przed rozpoczęciem.
>

Aby zainstalować konto magazynu w katalogu w aplikacji App Service, użyj polecenia [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) . Typem magazynu może być AzureBlob lub migracji pamięci. Używasz AzureBlob dla tego kontenera.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Należy to zrobić dla wszystkich innych katalogów, które mają być połączone z kontem magazynu.

## <a name="verify"></a>Weryfikuj

Po połączeniu kontenera magazynu z aplikacją internetową można to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Używanie magazynu niestandardowego w Docker Compose

Usługę Azure Storage można zainstalować za pomocą aplikacji wielokontenerowych przy użyciu identyfikatora niestandardowego. Aby wyświetlić nazwę niestandardowego ID, uruchom [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

W pliku *Docker-Compose. yml* zamapuj opcję `volumes`, aby `custom-id`. Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj aplikacje sieci Web w Azure App Service](../configure-common.md).
