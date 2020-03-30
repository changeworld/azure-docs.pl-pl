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
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174977"
---
## <a name="terminology"></a>Terminologia

Obraz portalu Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca**: Organizacja, która utworzyła obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferta**: Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: UbuntuServer, WindowsServer
* **Jednostka SKU**: Wystąpienie oferty, takie jak główne wydanie dystrybucji. Przykłady: 18.04-LTS, 2019-Datacenter
* **Wersja**: Numer wersji jednostki SKU obrazu. 

Aby zidentyfikować obraz portalu Marketplace podczas programowego wdrażania maszyny Wirtualnej, należy podać te wartości indywidualnie jako parametry. Niektóre narzędzia akceptują obraz *URN*, który łączy te wartości, oddzielone dwukropkiem (:) znak: *Wydawca*:*Oferta*:*Sku*:*Wersja*. W urnie można zastąpić numer wersji "najnowszą", która wybiera najnowszą wersję obrazu. 

Jeśli wydawca obrazu udostępnia dodatkowe warunki licencji i zakupu, należy zaakceptować te warunki i włączyć wdrażanie programowe. Podczas programowego wdrażania maszyny wirtualnej należy również podać parametry *planu zakupu.* Zobacz [Wdrażanie obrazu z terminami w portalu Marketplace](#deploy-an-image-with-marketplace-terms).
