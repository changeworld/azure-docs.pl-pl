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
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305312"
---
1. Upewnij się, że zalogowano się do konta platformy Azure i korzystania z subskrypcji, które chcesz dołączyć do tej wersji zapoznawczej. Skorzystaj z następującego przykładu, aby zarejestrować:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Ponownie zarejestrować swoją subskrypcję z *Microsoft.Network* przestrzeń nazw dostawcy.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Użyj następującego polecenia, aby sprawdzić, czy *AllowBastionHost* funkcji jest zarejestrowany w ramach subskrypcji:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Może upłynąć kilka minut, zanim rejestracji w celu ukończenia.
