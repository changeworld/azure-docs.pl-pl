---
title: Tworzenie pul zasobów przy użyciu mapowania subskrypcji platformy Azure
description: Zawiera opis sposobu tworzenia pul zasobów dla chmury prywatnej automatycznej synchronizacji za pomocą mapowania subskrypcji platformy Azure
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014967"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Tworzenie pul zasobów dla chmury prywatnej automatycznej synchronizacji przy użyciu mapowania subskrypcji platformy Azure
Mapowanie subskrypcji platformy Azure umożliwia tworzenie pul zasobów dla chmury prywatnej automatycznej synchronizacji z dostępnych pul zasobów vSphere. W portalu automatycznej wersji zapoznawczej można wyświetlić subskrypcję platformy Azure dla chmur prywatnych i zarządzać nią.

> [!NOTE]
> Mapowanie puli zasobów również mapuje wszystkie podrzędne pule zasobów. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie podrzędne pule zasobów zostały już zamapowane.

1. [Uzyskaj dostęp do portalu automatycznej synchronizacji](access-cloudsimple-portal.md).
2. Otwórz stronę **zasoby** i wybierz pozycję **Mapowanie subskrypcji platformy Azure**.  
3. Kliknij pozycję **Edytuj mapowanie subskrypcji platformy Azure**.  
4. Aby zmapować dostępne pule zasobów, zaznacz je po lewej stronie, a następnie kliknij strzałkę skierowaną w prawo. 
5. Aby usunąć mapowania, zaznacz je po prawej stronie, a następnie kliknij strzałkę skierowaną w lewo. 

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

6. Kliknij przycisk **OK**.
