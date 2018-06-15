---
title: Interfejsu wiersza polecenia 2.0 umożliwia tworzenie aplikacji usługi Azure AD i skonfigurować go, aby uzyskać dostępu do interfejsu API usługi Azure Media Services | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak używać do tworzenia aplikacji usługi Azure AD i skonfigurować go, aby uzyskać dostępu do interfejsu API usługi Azure Media Services 2.0 interfejsu wiersza polecenia.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 7685be97213f0b298499d474c0a6a772ca608fb2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788179"
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Użyj 2.0 interfejsu wiersza polecenia, aby utworzyć aplikację AAD i skonfigurować go, aby uzyskać dostępu do interfejsu API usługi Azure Media Services

W tym temacie przedstawiono sposób użycia 2.0 interfejsu wiersza polecenia, aby utworzyć aplikację usługi Azure Active Directory (Azure AD) i nazwy głównej usługi dostępu do zasobów usługi Azure Media Services. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Konto usługi Media Services. Aby uzyskać więcej informacji, zobacz [utworzyć konto usługi Azure Media Services przy użyciu portalu Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Użyj powłoki chmury Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Uruchom powłokę chmury w górnym okienku nawigacji portalu.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud powłoki](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Utwórz aplikację usługi Azure AD i konfigurowanie dostępu do konta usługi media 2.0 interfejsu wiersza polecenia
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Na przykład:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

W tym przykładzie **zakres** jest kontem usług ścieżka pełna zasobu multimediów. Jednak **zakres** może być dowolnym poziomie.

Może to być na przykład jeden z następujących poziomów:
 
* **Subskrypcji** poziom.
* **Grupy zasobów** poziom.
* **Zasobów** poziom (na przykład konto nośnika).

Aby uzyskać więcej informacji, zobacz [Tworzenie nazwy głównej usługi platformy Azure z 2.0 interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Zobacz też [Manage Role-Based kontroli dostępu przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Kolejne kroki

Rozpoczynanie pracy z [przekazywanie plików do konta](media-services-portal-upload-files.md).
