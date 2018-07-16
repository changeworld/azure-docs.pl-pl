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
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38945047"
---
## <a name="terminology"></a>Terminologia

Obraz z witryny Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca** -organizacji, który utworzył obraz. Przykłady: Kanonicznej MicrosoftWindowsServer
* **Oferty** — Nazwa grupy powiązanych obrazów utworzonych przez wydawcę. Przykłady: Ubuntu Server, WindowsServer
* **Jednostka SKU** — wystąpienia oferty, takich jak głównej wersji dystrybucji. Przykłady: 16.04-LTS, 2016 Datacenter
* **Wersja** — numer wersji obrazu jednostki SKU. 

Do identyfikowania obrazu z witryny Marketplace, podczas programistycznie wdrożyć Maszynę wirtualną, podać te wartości indywidualnie jako parametry lub niektóre narzędzia Zaakceptuj obraz *URN*. Nazwa URN łączy te wartości, który jest oddzielony znakiem dwukropka (:): *wydawcy*:*oferują*:*jednostki Sku*:*wersji*. W nazwa URN można zastąpić numer wersji za pomocą "latest", co spowoduje wybranie najnowszej wersji obrazu. 

Jeśli wydawca obrazu udostępnia dodatkową licencję i warunki zakupu, należy zaakceptować te warunki i włączyć wdrożenia programowe. Należy również podać *zakupić plan* parametrów podczas wdrażania maszyny Wirtualnej programowo. Zobacz [wdrażanie obrazu z warunkami witryny Marketplace](#deploy-an-image-with-marketplace-terms).