---
title: Obsługa zawartości z usługi Azure Storage do kontenerów systemu Linux
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy do kontenera systemu Linux w usłudze Azure App Service. Udostępniaj pliki między aplikacjami, zarządzaj zawartością statyczną zdalnie i uzyskuj dostęp lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297921"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Obsługa zawartości z usługi Azure Storage w usłudze App Service w systemie Linux

> [!NOTE]
> Ten artykuł dotyczy kontenerów systemu Linux. Aby wdrożyć w niestandardowych kontenerach systemu Windows, zobacz [Konfigurowanie plików platformy Azure w kontenerze systemu Windows w usłudze App Service](../configure-connect-to-azure-storage.md). Usługa Azure Storage w usłudze App Service w systemie Linux jest funkcją **w wersji zapoznawczej.** Ta funkcja nie jest **obsługiwana w scenariuszach produkcyjnych**.
>

W tym przewodniku pokazano, jak dołączyć usługę Azure Storage do usługi App Service w systemie Linux. Korzyści obejmują zabezpieczoną zawartość, przenośność zawartości, trwałą pamięć masową, dostęp do wielu aplikacji i wiele metod przenoszenia.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszym).
- Istniejąca [usługa app service w aplikacji Linux](https://docs.microsoft.com/azure/app-service/containers/).
- [Konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Udział plików i katalog na platformie Azure](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Ograniczenia usługi Azure Storage z usługą App Service

- Usługa Azure Storage with App Service jest **w wersji zapoznawczej** dla usługi App Service w systemie Linux i aplikacji sieci Web dla kontenerów. Nie jest **obsługiwany** dla **scenariuszy produkcji**.
- Usługa Azure Storage z usługą App Service obsługuje **instalowanie kontenerów plików Azure** (odczyt/ zapis) i **kontenerów obiektów blob platformy Azure** (tylko do odczytu)
- Usługa Azure Storage z **usługą** App Service nie obsługuje konfiguracji **Zapory magazynu** z powodu ograniczeń infrastruktury.
- Usługa Azure Storage z usługą App Service umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pośrednictwem punktów końcowych FTP/FTPs usługi App Service. Użyj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
- Usługa Azure Storage nie jest **dołączona** do aplikacji sieci web i rozliczana oddzielnie. Dowiedz się więcej o [cenach usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Konfiguracje usługi App Service przy użyciu usługi Azure Blob Storage staną się odczytane tylko w lutym 2020 r. [Dowiedz się więcej](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurowanie aplikacji za pomocą usługi Azure Storage

Po utworzeniu [konta usługi Azure Storage, udziału plików i katalogu](#prerequisites)możesz teraz skonfigurować aplikację za pomocą usługi Azure Storage.

Aby zainstalować konto magazynu w katalogu w aplikacji usługi [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) App Service, należy użyć polecenia. Typem magazynu może być AzureBlob lub AzureFiles. AzureFiles jest używany w tym przykładzie.


> [!CAUTION]
> Katalog określony jako ścieżka instalacji w aplikacji sieci web powinna być pusta. Wszelkie treści przechowywane w tym katalogu zostaną usunięte po dodaniu instalacji zewnętrznej. Jeśli przeprowadzasz migrację plików dla istniejącej aplikacji, przed rozpoczęciem należy wykonać kopię zapasową aplikacji i jej zawartości.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Należy to zrobić dla innych katalogów, które mają być połączone z kontem magazynu.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Weryfikowanie łącza usługi Azure Storage do aplikacji sieci Web

Gdy kontener magazynu jest połączony z aplikacją sieci web, można to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Używanie usługi Azure Storage w comonowaniu doładowania

Usługa Azure Storage można montować za pomocą aplikacji z wieloma kontenerami przy użyciu identyfikatora niestandardowego. Aby wyświetlić nazwę niestandardowego [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)identyfikatora, uruchom program .

W pliku *docker-compose.yml* `volumes` mapuj `custom-id`opcję na . Przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie aplikacji sieci Web w usłudze Azure App Service](../configure-common.md).

