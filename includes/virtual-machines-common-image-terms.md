---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880572"
---
## <a name="terminology"></a>Terminologia

Obraz z witryny Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca**: Organizacja, która utworzyła obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferty**: Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: UbuntuServer, WindowsServer
* **SKU**: Wystąpienie oferty, na przykład główna wersja dystrybucji. Przykłady: 18.04-LTS, 2019-Datacenter
* **Wersja**: Numer wersji obrazu jednostki SKU. 

Aby zidentyfikować obrazu z witryny Marketplace, podczas wdrażania maszyny Wirtualnej programowo, należy podać te wartości indywidualnie jako parametry. Niektóre narzędzia zaakceptować obrazu *URN*, który łączy te wartości, który jest oddzielony znakiem dwukropka (:): *Wydawca*:*oferują*:*jednostki Sku*:*wersji*. W nazwa URN można zastąpić numer wersji za pomocą "latest", co spowoduje wybranie najnowszej wersji obrazu. 

Jeśli wydawca obrazu udostępnia dodatkową licencję i warunki zakupu, należy zaakceptować te warunki i włączyć wdrożenia programowe. Należy także podać *zakupić plan* parametrów podczas wdrażania maszyny Wirtualnej programowo. Zobacz [wdrażanie obrazu z warunkami witryny Marketplace](#deploy-an-image-with-marketplace-terms).

<!-- Update_Description: wording update -->