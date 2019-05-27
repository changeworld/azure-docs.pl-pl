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
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150836"
---
Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Jeśli masz już sieć wirtualną, z którą chcesz nawiązać połączenie, sprawdź, czy żadna z podsieci Twojej sieci lokalnej nie pokrywa się z sieciami wirtualnymi, z którymi chcesz nawiązać połączenie. Sieć wirtualna nie wymaga podsieci bramy i nie może mieć żadnych bram sieci wirtualnej. Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, wykonując czynności opisane w tym artykule.
* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, a zakres adresów określony dla regionu koncentratora nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi chcesz nawiązać połączenie. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).