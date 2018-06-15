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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743371"
---
## <a name="terminology"></a>Terminologia

Obrazu z witryny Marketplace na platformie Azure ma następujące atrybuty:

* **Wydawca** -organizacji, który utworzył obraz. Przykłady: Canonical, MicrosoftWindowsServer
* **Oferują** — Nazwa grupy powiązanych obrazy utworzone przez wydawcę. Przykłady: Ubuntu Server, Windows Server
* **Jednostka SKU** — wystąpienie oferty, takie jak wydaniem dystrybucji. Przykłady: 16.04-LTS, 2016 Datacenter
* **Wersja** — numer wersji jednostki SKU obrazu. 

Aby zidentyfikować obrazu z witryny Marketplace, podczas wdrażania maszyny Wirtualnej programowo, podać te wartości indywidualnie jako parametry lub niektóre narzędzia Zaakceptuj obrazu *URN*. Nazwy URN łączy tych wartości, rozdzielone znakiem dwukropka (:): *wydawcy*:*oferują*:*Sku*:*wersji*. W URN można zastąpić numer wersji z "najnowszej", który wybiera najnowszą wersję obrazu. 

Wydawcy obrazu udostępnia dodatkowe licencji i warunków zakupu, należy zaakceptować te postanowienia, włączyć wdrożenia programowe. Należy również podać *zakupu planu* parametrów podczas wdrażania maszyny Wirtualnej programowo. Zobacz [wdrażanie obrazu z witryny Marketplace warunkami](#deploy-an-image-with-marketplace-terms).