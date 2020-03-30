---
title: Tworzenie pul zasobów za pomocą mapowania subskrypcji platformy Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: W tym artykule opisano sposób tworzenia pul zasobów dla chmury prywatnej za pomocą mapowania subskrypcji platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014967"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Tworzenie pul zasobów dla chmury prywatnej za pomocą mapowania subskrypcji platformy Azure
Mapowanie subskrypcji platformy Azure umożliwia tworzenie pul zasobów dla chmury prywatnej z puli dostępnych zasobów vSphere. W portalu CloudSimple można wyświetlać subskrypcję platformy Azure dla chmur prywatnych i zarządzać nią.

> [!NOTE]
> Mapowanie puli zasobów mapuje również wszystkie pule zasobów podrzędnych. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie pule zasobów podrzędnych są już mapowane.

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md).
2. Otwórz stronę **Zasoby** i wybierz **mapowanie subskrypcji platformy Azure**.  
3. Kliknij **pozycję Edytuj mapowanie subskrypcji platformy Azure**.  
4. Aby zamapować dostępne pule zasobów, zaznacz je po lewej stronie i kliknij strzałkę skierowaną w prawo. 
5. Aby usunąć mapowania, zaznacz je po prawej stronie i kliknij strzałkę skierowaną w lewo. 

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

6. Kliknij przycisk **OK**.
