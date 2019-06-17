---
title: Utwórz konto usługi Media Services przy użyciu wiersza polecenia platformy Azure — Azure | Dokumentacja firmy Microsoft
description: Wykonaj kroki tego przewodnika Szybki start, aby utworzyć konto usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/19/2019
ms.author: juliako
ms.openlocfilehash: f2cb2e2ee6393a59125ee879f2058516eb50d6b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956712"
---
# <a name="create-an-azure-media-services-account"></a>Tworzenie konta usługi Azure Media Services

Aby rozpocząć, szyfrowanie, kodowanie, analizowanie, zarządzanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. Konto usługi Media Services musi być skojarzone z co najmniej jednym kontem magazynu.

> [!NOTE]
> Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Zdecydowanie zaleca się używanie kont magazynu znajdujących się w tej samej lokalizacji co konto usługi Media Services, aby uniknąć dodatkowych opóźnień i kosztów danych wychodzących.

W tym artykule opisano kroki tworzenia nowego konta usługi Azure Media Services przy użyciu wiersza polecenia platformy Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Kolejne kroki

* [Interfejsy API w wersji 3 dostępu](access-api-cli-how-to.md)
* [Strumieniowe przesyłanie pliku](stream-files-dotnet-quickstart.md)
* [Dołącz magazyn pomocniczy na konto usługi Media Services](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

