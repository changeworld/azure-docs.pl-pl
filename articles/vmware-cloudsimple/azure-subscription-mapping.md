---
title: Tworzenie pul zasobów przy użyciu mapowania subskrypcji platformy Azure
description: Opisuje sposób tworzenia pul zasobów dla chmury prywatnej za pomocą mapowania subskrypcji platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ea102ad8377da70b88a0e59834ebe3a09866632
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563222"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Tworzenie pul zasobów dla chmury prywatnej przy użyciu mapowania subskrypcji platformy Azure
Mapowanie subskrypcji platformy Azure umożliwia tworzenie pul zasobów dla chmury prywatnej na podstawie dostępnych pul zasobów vSphere. W portalu CloudSimple można wyświetlać subskrypcję platformy Azure dla chmur prywatnych i zarządzać nią.

> [!NOTE]
> Mapowanie puli zasobów również mapuje wszystkie podrzędne pule zasobów. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie podrzędne pule zasobów zostały już zamapowane.

1. [Dostęp do portalu CloudSimple](access-cloudsimple-portal.md).
2. Otwórz stronę **zasoby** i wybierz pozycję **Mapowanie subskrypcji platformy Azure**.  
3. Kliknij pozycję **Edytuj mapowanie subskrypcji platformy Azure**.  
4. Aby zmapować dostępne pule zasobów, zaznacz je po lewej stronie, a następnie kliknij strzałkę skierowaną w prawo. 
5. Aby usunąć mapowania, zaznacz je po prawej stronie, a następnie kliknij strzałkę skierowaną w lewo. 

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

6. Kliknij przycisk **OK**.
