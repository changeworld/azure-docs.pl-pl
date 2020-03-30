---
title: Tworzenie aplikacji usługi Azure AD za pomocą interfejsu WIERSZA polecenia platformy Azure i konfigurowanie jej w celu uzyskiwania dostępu do interfejsu API usługi Azure Media Services | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia aplikacji usługi Azure AD i konfigurowania jej w celu uzyskania dostępu do interfejsu API usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70035808"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Tworzenie aplikacji usługi Azure AD za pomocą interfejsu wiersza polecenia platformy Azure i konfigurowanie jej w celu uzyskiwania dostępu do interfejsu API usługi Media Services 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Sprawdź najnowszą wersję usługi [Media Services w wersji 3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówki dotyczące migracji z wersji 2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym temacie pokazano, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia aplikacji i jednostki usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do zasobów usługi Azure Media Services. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure Portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Korzystanie z powłoki chmury azure

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Uruchom powłokę chmury z górnego okienka nawigacji portalu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Tworzenie aplikacji usługi Azure AD i konfigurowanie dostępu do konta multimedialnego za pomocą interfejsu wiersza polecenia platformy Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Przykład:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

W tym przykładzie **zakres** jest pełną ścieżką zasobów dla konta usług multimedialnych. Jednak **zakres** może być na dowolnym poziomie.

Na przykład może to być jeden z następujących poziomów:
 
* Poziom **subskrypcji.**
* Poziom **grupy zasobów.**
* Poziom **zasobu** (na przykład konto media).

Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) interfejsu wiersza polecenia platformy Azure

Zobacz [też: Zarządzanie kontrolą dostępu opartą na rolach za pomocą interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Następne kroki

Zacznij od [przesyłania plików na swoje konto](media-services-portal-upload-files.md).
