---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174976"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z terminami w portalu Marketplace

Niektóre obrazy maszyn wirtualnych w portalu Azure Marketplace mają dodatkowe warunki licencji i zakupu, które należy zaakceptować, zanim będzie można wdrożyć je programowo.  

Aby wdrożyć maszynę wirtualną z takiego obrazu, należy zarówno zaakceptować warunki obrazu i włączyć wdrażanie programowe. Musisz to zrobić tylko raz na subskrypcję. Następnie za każdym razem, gdy wdrażasz maszynę wirtualną programowo z obrazu, musisz również określić parametry *planu zakupu.*

W poniższych sekcjach pokazano, jak:

* Dowiedz się, czy obraz w marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowo
* Dostarczanie parametrów planu zakupu podczas programowego wdrażania maszyny wirtualnej

