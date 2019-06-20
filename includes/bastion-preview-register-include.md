---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d05d425cc9bfb206207801f15a25e17d60dc0aaf
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191748"
---
1. Upewnij się, że zalogowano się do konta platformy Azure i korzystania z subskrypcji, które chcesz dołączyć do tej wersji zapoznawczej. Skorzystaj z następującego przykładu, aby zarejestrować:

    ```azurepowershell-interactive
    Register-AzureRmProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Ponownie zarejestrować swoją subskrypcję z *Microsoft.Network* przestrzeń nazw dostawcy.

    ```azurepowershell-interactive
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Użyj następującego polecenia, aby sprawdzić, czy *AllowBastionHost* funkcji jest zarejestrowany w ramach subskrypcji:

    ```azurepowershell-interactive
    Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Może upłynąć kilka minut, zanim rejestracji w celu ukończenia.