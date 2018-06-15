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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743378"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu z witryny Marketplace warunkami

Niektóre obrazy maszyny Wirtualnej w portalu Azure Marketplace ma dodatkowych licencji i zakupu warunki, że należy zaakceptować przed ich wdrożeniem programowo.  

Aby wdrożyć Maszynę wirtualną z takich obrazu, należy zaakceptować postanowienia obrazu i włączyć wdrożenia programowe. Wystarczy wykonać to jeden raz w ramach subskrypcji. Następnie w każdym wdrożeniu maszyny Wirtualnej programowo z obrazu, również należy określić *zakupu planu* parametrów.

W poniższych sekcjach przedstawiono Pokaż jak:

* Sprawdź, czy obrazu z witryny Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj postanowienia programowo
* Podaj parametry planu zakupów programowo wdrażania maszyny Wirtualnej

