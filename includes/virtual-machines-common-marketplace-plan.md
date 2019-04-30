---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880561"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z warunkami witryny Marketplace

Niektóre obrazy maszyn wirtualnych w witrynie Azure Marketplace mają dodatkową licencję i zakupu warunki, które muszą zaakceptować przed ich wdrożeniem programowo.  

Aby wdrożyć Maszynę wirtualną z takiego obrazu, należy zaakceptować postanowienia obrazu i włączyć wdrożenia programowe. Tylko należy zrobić to raz na subskrypcję. Następnie przy każdym wdrożysz maszynę Wirtualną programowo z obrazu należy także określić *zakupić plan* parametrów.

Następujące sekcje show jak:

* Dowiedz się, czy obraz z witryny Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowe
* Podaj parametry planu zakupu, gdy programowe wdrażanie maszyny Wirtualnej

<!-- Update_Description: update meta properties, wording update -->