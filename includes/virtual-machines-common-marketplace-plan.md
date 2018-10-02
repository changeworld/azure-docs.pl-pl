---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f9974aae1e5996ffeaa6cde690a5e10ccba4cc32
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019016"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z warunkami witryny Marketplace

Określonych obrazów maszyn wirtualnych w witrynie Azure Marketplace mają dodatkową licencję i zakupu warunki, które muszą zaakceptować przed ich wdrożeniem programowo.  

Aby wdrożyć Maszynę wirtualną z takiego obrazu, należy zaakceptować postanowienia obrazu i włączyć wdrożenia programowe. Wystarczy zrobić to raz w ramach subskrypcji. Następnie każdorazowo wdrożysz maszynę Wirtualną programowo z obrazu, należy również określić *zakupić plan* parametrów.

Następujące sekcje show jak:

* Dowiedz się, czy obraz z witryny Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowe
* Podaj parametry planu zakupu, gdy programowe wdrażanie maszyny Wirtualnej

