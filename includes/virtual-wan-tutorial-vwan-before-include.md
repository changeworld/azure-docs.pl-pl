---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74896604"
---
Przed rozpoczęciem konfiguracji sprawdź, czy spełniasz następujące kryteria:

* Jeśli masz już sieć wirtualną, z którą chcesz się połączyć, sprawdź, czy żadna z podsieci sieci lokalnej nie pokrywa się z nią. Sieć wirtualna nie wymaga podsieci bramy i nie może mieć żadnych bram sieci wirtualnej. Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, wykonując kroki opisane w tym artykule.
* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, a zakres adresów określony dla regionu koncentratora nie może pokrywać się z istniejącą siecią wirtualną, z którą się łączysz. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w lokalnej konfiguracji sieci, koordynuj z kimś, kto może podać te szczegóły.
* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.