---
title: Udostępnianie zawartości z usługi Azure Storage w systemie Linux — usługa App Service
description: Jak konfigurować i obsługiwać zawartość z usługi Azure Storage w usłudze Azure App Service w systemie Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 6b4e145a693aabbf1a00d732e2fd602e7c887a03
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956013"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Udostępnianie zawartości z usługi Azure Storage w usłudze App Service w systemie Linux

Ten przewodnik pokazuje, jak udostępniać zawartość statyczną w usłudze App Service w systemie Linux przy użyciu [usługi Azure Storage](/azure/storage/common/storage-introduction). Korzyści to m.in. zabezpieczonej zawartości, zawartość przenośności, dostęp do wielu aplikacji i na wiele sposobów przesyłania. 

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejącej aplikacji sieci web (usługa App Service w systemie Linux lub Web App for Containers).
- [Interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszej).

## <a name="create-azure-storage"></a>Tworzenie usługi Azure Storage

> [!NOTE]
> Usługa Azure Storage jest magazynem innych niż domyślne i rozliczane oddzielnie, nie jest dołączony do aplikacji sieci web.
>

Tworzenie platformy Azure [konta usługi Azure storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Przekazywanie plików do usługi Azure Storage

Przekaż do katalogu lokalnego do konta magazynu, należy użyć [ `az storage blob upload-batch` ](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) polecenia jak w poniższym przykładzie:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Magazyn Link do aplikacji sieci web (wersja zapoznawcza)

> [!CAUTION]
> Łączenie istniejącego katalogu, w aplikacji internetowej z kontem magazynu spowoduje usunięcie zawartości katalogu. Jeśli jesteś migrowanie plików dla istniejącej aplikacji, należy wykonać kopię zapasową aplikacji i jej zawartość przed przystąpieniem do wykonywania.
>

Aby zainstalować konta magazynu do katalogu, w aplikacji usługi app Service, należy użyć [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) polecenia. Typ magazynu można określić obiektu blob platformy Azure lub migracji. Możesz użyć obiektu blob platformy Azure dla tego kontenera.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Należy to zrobić w przypadku innych katalogów, który ma być połączony z kontem magazynu.

## <a name="verify"></a>Weryfikuj

Gdy kontener magazynu jest połączony z aplikacji sieci web, można to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Użyj magazynu niestandardowego w narzędzia Docker Compose

Usługa Azure Storage mogą być instalowane przy użyciu aplikacji obsługującej wiele kontenerów przy użyciu identyfikatora niestandardowe. Aby wyświetlić nazwę niestandardowego id, uruchom [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

W swojej *docker-compose.yml* plików, mapowanie `volumes` opcję `custom-id`. Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie aplikacji sieci web w usłudze Azure App Service](../configure-common.md).
