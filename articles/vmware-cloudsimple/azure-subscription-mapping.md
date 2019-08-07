---
title: Mapowanie subskrypcji platformy Azure na pule zasobów na platformie Azure VMware według CloudSimple
description: Opisuje sposób mapowania puli zasobów w rozwiązaniu VMware platformy Azure przez CloudSimple na subskrypcję platformy Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bf721f35500d2ff1344996e7750c5e574f40f31
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816266"
---
# <a name="map-resource-pools-from-your-private-cloud-to-your-azure-subscription"></a>Mapowanie pul zasobów z chmury prywatnej do subskrypcji platformy Azure

Mapowanie subskrypcji platformy Azure umożliwia mapowanie pul zasobów z chmury prywatnej programu vCenter do subskrypcji platformy Azure. Można mapować tylko subskrypcję, w której utworzono usługę CloudSimple.  Tworzenie maszyny wirtualnej VMware na podstawie Azure Portal wdraża maszynę wirtualną w mapowanej puli zasobów.  W portalu CloudSimple można wyświetlać subskrypcję platformy Azure dla chmur prywatnych i zarządzać nią.

Subskrypcję można zamapować na wiele pul zasobów programu vCenter w chmurze prywatnej.  Należy zmapować pule zasobów każdej chmury prywatnej.  Tylko mapowane pule zasobów będą dostępne do tworzenia maszyny wirtualnej VMware na podstawie Azure Portal.

> [!IMPORTANT]
> Mapowanie puli zasobów również mapuje wszystkie podrzędne pule zasobów. Nie można zamapować nadrzędnej puli zasobów, jeśli wszystkie podrzędne pule zasobów zostały już zamapowane.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz usługę CloudSimple i chmurę prywatną w ramach subskrypcji.  Aby utworzyć usługę CloudSimple, zobacz [Szybki Start — Tworzenie usługi](quickstart-create-cloudsimple-service.md).  Jeśli musisz utworzyć chmurę prywatną, zobacz [Przewodnik Szybki Start — Konfigurowanie środowiska chmury prywatnej](quickstart-create-private-cloud.md).

Klaster vCenter (główna Pula zasobów) można zmapować na subskrypcję.  Jeśli chcesz utworzyć nową pulę zasobów, zobacz artykuł [Tworzenie puli zasobów](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-0F6C6709-A5DA-4D38-BE08-6CB1002DD13D.html) w witrynie dokumentacji programu VMware.

## <a name="default-resource-group"></a>Domyślna grupa zasobów

Utworzenie nowej maszyny wirtualnej CloudSimple z Azure Portal umożliwia wybranie grupy zasobów.  Maszyna wirtualna utworzona w chmurze prywatnej usługi vCenter w mapowanej puli zasobów będzie widoczna na Azure Portal.  Odnaleziona maszyna wirtualna zostanie umieszczona w domyślnej grupie zasobów platformy Azure.  Można zmienić nazwę domyślnej grupy zasobów.

## <a name="map-azure-subscription"></a>Mapowanie subskrypcji platformy Azure

1. Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** i wybierz chmurę prywatną, którą chcesz zmapować.

3. Wybierz pozycję **Mapowanie subskrypcji platformy Azure**.

4. Kliknij pozycję **Edytuj mapowanie subskrypcji platformy Azure**.

5. Aby zmapować dostępne pule zasobów, zaznacz je po lewej stronie, a następnie kliknij strzałkę skierowaną w prawo.

6. Aby usunąć mapowania, zaznacz je po prawej stronie, a następnie kliknij strzałkę skierowaną w lewo.

    ![Subskrypcje platformy Azure](media/resources-azure-mapping.png)

7. Kliknij przycisk **OK**.

## <a name="change-default-resource-group-name"></a>Zmień nazwę domyślnej grupy zasobów

1. Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Otwórz stronę **zasoby** i wybierz chmurę prywatną, którą chcesz zmapować.

3. Wybierz pozycję **Mapowanie subskrypcji platformy Azure**.

4. Kliknij przycisk **Edytuj** w obszarze Nazwa grupy zasobów platformy Azure.

    ![Edytuj nazwę grupy zasobów](media/resources-edit-resource-group-name.png)

5. Wprowadź nową nazwę grupy zasobów, a następnie kliknij pozycję **Prześlij**.

    ![Wprowadź nazwę nowej grupy zasobów](media/resources-new-resource-group-name.png)

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [maszynach wirtualnych CloudSimple](cloudsimple-virtual-machines.md)