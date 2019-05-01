---
title: Użyj wiersza polecenia platformy Azure, aby utworzyć aplikację usługi Azure AD i skonfigurować go do dostępu do interfejsu API usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak utworzyć aplikację usługi Azure AD i skonfigurować go do dostępu do interfejsu API usługi Azure Media Services za pomocą wiersza polecenia platformy Azure.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e7ae5f83ff9dbb16733656a3bb4452ace750cf3f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690116"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Użyj wiersza polecenia platformy Azure, aby utworzyć aplikację usługi Azure AD i skonfigurować go do dostępu do interfejsu API usługi Media Services 

> [!NOTE]
> Do usługi Media Services w wersji 2 nie są już dodawane żadne nowe funkcje. <br/>Zapoznaj się z najnowszą wersją, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zobacz też [wskazówek dotyczących migracji od v2 do v3](../latest/migrate-from-v2-to-v3.md)

W tym temacie dowiesz się, jak utworzyć aplikację usługi Azure Active Directory (Azure AD) i jednostki usługi dostęp do zasobów usługi Azure Media Services za pomocą wiersza polecenia platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [Tworzenie konta usługi Azure Media Services przy użyciu witryny Azure portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Użyj usługi Azure Cloud Shell

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Uruchomienie usługi Cloud Shell z okienka nawigacji górnej części portalu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Tworzenie aplikacji usługi Azure AD i konfigurowanie dostępu do konta multimediów za pomocą wiersza polecenia platformy Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Na przykład:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

W tym przykładzie **zakres** to ścieżka zasobu pełny dla nośnika konta usług. Jednak **zakres** może być na dowolnym poziomie.

Może to być na przykład jeden z następujących poziomów:
 
* **Subskrypcji** poziom.
* **Grupy zasobów** poziom.
* **Zasobów** poziom (na przykład konto usługi Media).

Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Zobacz też [Manage Role-Based kontroli dostępu przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z usługą [przekazywania plików na koncie](media-services-portal-upload-files.md).
