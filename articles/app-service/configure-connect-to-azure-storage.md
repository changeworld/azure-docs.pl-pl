---
title: Konfigurowanie magazynu przy użyciu usługi Azure Files
description: Jak skonfigurować i nawiązać połączenie z usługi Azure Files w kontenerze Windows w usłudze App Service.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789048"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurowanie usługi Azure Files w kontenerze Windows w usłudze App Service

> [!NOTE]
> Ten artykuł dotyczy niestandardowe kontenery Windows. Aby wdrożyć w usłudze App Service w systemie _Linux_, zobacz [obsługi zawartości z usługi Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Ten przewodnik pokazuje, jak dostęp do usługi Azure Storage w kontenerach Windows. Tylko [udziałów plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) i [udziałów plików w warstwie Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) są obsługiwane. Możesz użyć udziałów plików platformy Azure, w tym instruktażu. Korzyści to m.in. zabezpieczonej zawartości, zawartość przenośności, dostęp do wielu aplikacji i na wiele sposobów przesyłania.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszej).
- [Istniejącą aplikację Windows kontenera w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Tworzenie udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Przekazywanie plików do udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Usługa Azure Files jest inny niż domyślny magazyn i rozliczane oddzielnie, nie jest dołączony do aplikacji sieci web. Nie obsługuje on za pomocą konfiguracji zapory, ze względu na ograniczenia infrastruktury.
>

## <a name="link-storage-to-your-web-app-preview"></a>Magazyn Link do aplikacji sieci web (wersja zapoznawcza)

 Aby zainstalować udział plików platformy Azure do katalogu, w aplikacji usługi app Service, należy użyć [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) polecenia. Magazyn musi być typu migracji.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Należy to zrobić, aby udostępnić inne katalogi, które mają być połączone z usługą Azure Files.

## <a name="verify"></a>Weryfikuj

Gdy udział plików platformy Azure jest połączona z aplikacji sieci web, można to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Następne kroki

- [Migrowanie aplikacji ASP.NET w usłudze Azure App Service za pomocą konteneru Windows (wersja zapoznawcza)](app-service-web-tutorial-windows-containers-custom-fonts.md).