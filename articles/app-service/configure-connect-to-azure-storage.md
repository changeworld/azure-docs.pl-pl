---
title: Dodawanie magazynu niestandardowego (kontener systemu Windows)
description: Dowiedz się, jak dołączyć niestandardowy udział sieciowy w niestandardowym kontenerze systemu Windows w usłudze Azure App Service. Udostępniaj pliki między aplikacjami, zarządzaj zawartością statyczną zdalnie i uzyskuj dostęp lokalnie itp.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120673"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurowanie plików platformy Azure w kontenerze systemu Windows w usłudze aplikacji

> [!NOTE]
> Ten artykuł dotyczy niestandardowych kontenerów systemu Windows. Aby wdrożyć w usłudze app service w _systemie Linux,_ zobacz [Obsługa zawartości z usługi Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

W tym przewodniku pokazano, jak uzyskać dostęp do usługi Azure Storage w kontenerach systemu Windows. Obsługiwane są tylko [udziały plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) i [udziały plików premium.](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) W tym wysięgowaniu usługi Azure Files Jest używany udział w tej funkcji. Korzyści obejmują zabezpieczoną zawartość, przenośność zawartości, dostęp do wielu aplikacji i wiele metod przenoszenia.

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.46 lub nowszym).
- [Istniejąca aplikacja Kontener systemu Windows w usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Tworzenie udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Przekazywanie plików do udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Usługa Azure Files jest magazynem nieobe domyślnym i rozliczane oddzielnie, nie jest dołączony do aplikacji sieci web. Nie obsługuje przy użyciu konfiguracji zapory z powodu ograniczeń infrastruktury.
>

## <a name="limitations"></a>Ograniczenia

- Usługa Azure Storage w kontenerach systemu Windows jest **w wersji zapoznawczej** i **nie jest obsługiwana** w **scenariuszach produkcyjnych.**
- Usługa Azure Storage w kontenerach systemu Windows obsługuje tylko **instalowanie kontenerów plików Azure** (odczyt/zapis).
- Usługa Azure Storage w kontenerach systemu Windows nie jest obecnie **obsługiwana** w celu wprowadzenia własnych scenariuszy kodu w planach usługi Windows App Service.
- Usługa Azure Storage w kontenerach systemu Windows **nie obsługuje** konfiguracji **Zapory magazynu** z powodu ograniczeń infrastruktury.
- Usługa Azure Storage w kontenerach systemu Windows umożliwia określenie **maksymalnie pięciu** punktów instalacji na aplikację.
- Usługa Azure Storage zainstalowana w aplikacji nie jest dostępna za pośrednictwem punktów końcowych FTP/FTPs usługi App Service. Użyj [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
- Usługa Azure Storage jest rozliczana niezależnie i **nie jest dołączona** do aplikacji sieci web. Dowiedz się więcej o [cenach usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Połącz magazyn z aplikacją internetową (wersja zapoznawcza)

 Aby zainstalować udział plików platformy Azure w katalogu w aplikacji [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) usługi App Service, należy użyć polecenia. Typem magazynu musi być AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Należy to zrobić dla innych katalogów, które mają być połączone z udziałem usługi Azure Files.

## <a name="verify"></a>Weryfikuj

Gdy udział usługi Azure Files jest połączony z aplikacją sieci web, możesz to sprawdzić, uruchamiając następujące polecenie:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Następne kroki

- [Migrowanie aplikacji ASP.NET do usługi Azure App Service przy użyciu kontenera systemu Windows (Wersja zapoznawcza)](app-service-web-tutorial-windows-containers-custom-fonts.md).
