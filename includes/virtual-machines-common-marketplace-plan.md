---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943122"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z warunkami witryny Marketplace

Określonych obrazów maszyn wirtualnych w witrynie Azure Marketplace mają dodatkową licencję i zakupu warunki, które muszą zaakceptować przed ich wdrożeniem programowo.  

Aby wdrożyć Maszynę wirtualną z takiego obrazu, należy zaakceptować postanowienia obrazu i włączyć wdrożenia programowe. Wystarczy zrobić to raz w ramach subskrypcji. Następnie każdorazowo wdrożysz maszynę Wirtualną programowo z obrazu, należy również określić *zakupić plan* parametrów.

Następujące sekcje show jak:

* Dowiedz się, czy obraz z witryny Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowe
* Podaj parametry planu zakupu, gdy programowe wdrażanie maszyny Wirtualnej

