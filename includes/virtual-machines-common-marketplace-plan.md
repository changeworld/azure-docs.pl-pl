---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117286"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z warunkami witryny Marketplace

Niektóre obrazy maszyn wirtualnych w witrynie Azure Marketplace mają dodatkową licencję i zakupu warunki, które muszą zaakceptować przed ich wdrożeniem programowo.  

Aby wdrożyć Maszynę wirtualną z takiego obrazu, należy zaakceptować postanowienia obrazu i włączyć wdrożenia programowe. Tylko należy zrobić to raz na subskrypcję. Następnie przy każdym wdrożysz maszynę Wirtualną programowo z obrazu należy także określić *zakupić plan* parametrów.

Następujące sekcje show jak:

* Dowiedz się, czy obraz z witryny Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowe
* Podaj parametry planu zakupu, gdy programowe wdrażanie maszyny Wirtualnej

