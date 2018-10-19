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
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437107"
---
## <a name="terminology"></a>Terminologia

Obraz z witryny Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca**: organizacji, który utworzył obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferty**: Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: Ubuntu Server, WindowsServer
* **Jednostka SKU**: wystąpienie oferty, takich jak głównej wersji dystrybucji. Przykłady: 16.04-LTS, 2016-Datacenter
* **Wersja**: numer wersji obrazu jednostki SKU. 

Aby zidentyfikować obrazu z witryny Marketplace, podczas wdrażania maszyny Wirtualnej programowo, należy podać te wartości indywidualnie jako parametry. Niektóre narzędzia zaakceptować obrazu *URN*, który łączy te wartości, który jest oddzielony znakiem dwukropka (:): *wydawcy*:*oferują*:*jednostki Sku*: *Wersji*. W nazwa URN można zastąpić numer wersji za pomocą "latest", co spowoduje wybranie najnowszej wersji obrazu. 

Jeśli wydawca obrazu udostępnia dodatkową licencję i warunki zakupu, należy zaakceptować te warunki i włączyć wdrożenia programowe. Należy także podać *zakupić plan* parametrów podczas wdrażania maszyny Wirtualnej programowo. Zobacz [wdrażanie obrazu z warunkami witryny Marketplace](#deploy-an-image-with-marketplace-terms).