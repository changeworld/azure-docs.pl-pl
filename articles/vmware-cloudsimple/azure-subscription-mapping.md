---
title: Mapowania pul zasobów na platformie Azure VMware Solution by CloudSimple subskrypcji platformy Azure
description: W tym artykule opisano sposób mapowania puli zasobów na platformie Azure VMware Solution by CloudSimple do subskrypcji platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efda996e03d46a2f97d19558f7c2930b623a639e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333303"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapowanie pul zasobów z chmury prywatnej do subskrypcji platformy Azure

Mapowanie subskrypcji platformy Azure umożliwia mapowanie pul zasobów z serwera vCenter chmury prywatnej do subskrypcji platformy Azure. Można mapować tylko subskrypcji, której usługa CloudSimple została utworzona.  Tworzenie maszyny wirtualnej VMware w witrynie Azure portal służy do wdrażania maszyny wirtualnej w puli mapowanego zasobu.  W portalu CloudSimple można wyświetlać i zarządzać subskrypcji platformy Azure dla usługi chmur prywatnych.

Subskrypcję można mapować na wiele pul zasobów vCenter chmury prywatnej.  Musisz zamapować pul zasobów każdej chmury prywatnej.  Tylko pule mapowanego zasobu będą dostępne do tworzenia maszyny wirtualnej VMware w witrynie Azure portal.

> [!IMPORTANT]
> Mapowanie puli zasobów mapuje również wszystkie pule zasobów podrzędnych. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie pule zasobów podrzędnych jest już zamapowana.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz CloudSimple usługi i chmurę prywatną w subskrypcji.  Aby utworzyć usługę CloudSimple, zobacz [Szybki Start — Tworzenie usługi](quickstart-create-cloudsimple-service.md).  Jeśli musisz utworzyć chmurę prywatną, zobacz [Szybki Start — Konfigurowanie środowiska chmury prywatnej](quickstart-create-private-cloud.md).

Klaster vCenter (pula zasobów katalogu głównego) można mapować do Twojej subskrypcji.  Jeśli chcesz utworzyć nową pulę zasobów, zobacz [utworzyć pulę zasobów](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) artykuł w witrynie dokumentacji firmy VMware.

## <a name="default-resource-group"></a>Domyślna grupa zasobów

Tworzenie nowej maszyny wirtualnej CloudSimple z witryny Azure portal umożliwia wybranie grupy zasobów.  Maszyna wirtualna utworzona na vCenter chmury prywatnej w puli mapowanego zasobu będą widoczne w witrynie Azure portal.  Odnalezione maszyny wirtualnej zostaną umieszczone w domyślnej grupie zasobów platformy Azure.  Można zmienić nazwę domyślną grupę zasobów.

## <a name="map-azure-subscription"></a>Mapowanie subskrypcji platformy Azure

1. Dostęp do [CloudSimple portal](access-cloudsimple-portal.md).

2. Otwórz **zasobów** strony i wybierz chmurę prywatną, którą chcesz zamapować.

3. Wybierz **subskrypcji platformy Azure, mapowanie**.

4. Kliknij przycisk **Mapowanie subskrypcji Azure Edytuj**.

5. Do mapowania pul dostępnych zasobów, zaznacz je po lewej stronie, a następnie kliknij strzałkę skierowaną w prawo.

6. Aby usunąć mapowania, wybierać po prawej stronie, a następnie kliknij strzałkę skierowaną w lewo.

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

7. Kliknij przycisk **OK**.

## <a name="change-default-resource-group-name"></a>Nazwa grupy zasobów domyślna zmiana

1. Dostęp do [CloudSimple portal](access-cloudsimple-portal.md).

2. Otwórz **zasobów** strony i wybierz chmurę prywatną, którą chcesz zamapować.

3. Wybierz **subskrypcji platformy Azure, mapowanie**.

4. Kliknij przycisk **Edytuj** pod nazwą grupy zasobów platformy Azure.

    ![Edytować nazwę grupy zasobów](media/resources-edit-resource-group-name.png)

5. Wprowadź nową nazwę grupy zasobów, a następnie kliknij przycisk **przesyłania**.

    ![Wprowadź nazwę nowej grupy zasobów](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Kolejne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [CloudSimple maszyn wirtualnych](cloudsimple-virtual-machines.md)