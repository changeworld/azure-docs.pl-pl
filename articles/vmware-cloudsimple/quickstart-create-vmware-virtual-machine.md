---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple Przewodnik Szybki Start — używanie maszyn wirtualnych VMware na platformie Azure
description: W tym przewodniku Szybki Start dowiesz się, jak konfigurować i korzystać z maszyn wirtualnych VMware w witrynie Azure portal przy użyciu usługi Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393493"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Szybki start: Używanie maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w witrynie Azure portal, można użyć szablonów maszyn wirtualnych dostępnych na vCenter chmury prywatnej. Jako administrator vCenter można utworzyć dodatkowe szablony w chmurze prywatnej.

## <a name="create-a-vm-template"></a>Utwórz szablon maszyny Wirtualnej

Najpierw utwórz maszynę wirtualną w chmurze prywatnej przy użyciu interfejsu użytkownika programu vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami w artykule VMware [sklonować maszynę wirtualną do szablonu w kliencie internetowym vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store szablonu maszyny Wirtualnej na chmurę prywatną serwera vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Utwórz maszynę wirtualną w witrynie Azure portal

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **maszyn wirtualnych CloudSimple**.

3. Wybierz pozycję **Dodaj**.

    ![Wybieranie opcji dodawania](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź następujące informacje o maszynie wirtualnej, a następnie wybierz pozycję **dalej: Rozmiar**.

    ![Tworzenie maszyny wirtualnej CloudSimple — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | **Subskrypcja** | Jeśli subskrypcja platformy Azure jest skojarzony z chmury prywatnej.  |
    | **Grupa zasobów** | Grupa zasobów, do którego zostanie przypisany maszyny Wirtualnej. Można wybrać istniejącą grupę lub Utwórz nową. |
    | **Nazwa** | Nazwa do identyfikowania maszyny Wirtualnej.  |
    | **Lokalizacja** | Region platformy Azure, w którym znajduje się maszyna wirtualna.  |
    | **Chmura prywatna** | Chmura prywatna CloudSimple w którym chcesz utworzyć maszynę Wirtualną. |
    | **ResourcePool** | Pula mapowanego zasobu dla maszyny Wirtualnej. Wybierz pule dostępnych zasobów. |
    | **vSphere szablonu** | Szablon vSphere dla maszyny Wirtualnej.  |
    | **Nazwa użytkownika** | Nazwa użytkownika administratora maszyny Wirtualnej (w przypadku szablonów Windows).|
    | **Hasło** |  Hasło administratora maszyny Wirtualnej (w przypadku szablonów Windows). |
    | **Potwierdź hasło** | Podane hasło w polu poprzedniego. |

5. Wybierz liczbę rdzeni oraz pojemność pamięci dla maszyny Wirtualnej. Wybierz **udostępnienia systemu operacyjnego gościa** Jeśli chcesz udostępnić pełną wirtualizacji Procesora systemu operacyjnego gościa. Aplikacje, które wymagają wirtualizację sprzętu można uruchamiać na maszynach wirtualnych bez tłumaczenie binarne lub parawirtualizacji. Aby uzyskać więcej informacji, zobacz artykuł VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">ujawnić wirtualizacji sprzętowej sprzętu VMware</a>. Gdy wszystko będzie gotowe, wybierz pozycję **dalej: Konfiguracje**.

    ![Tworzenie maszyny wirtualnej CloudSimple — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy sieciowe i dyski, zgodnie z opisem w poniższych tabelach, a następnie wybierz pozycję **przeglądu + Utwórz**.

    ![Tworzenie maszyny wirtualnej CloudSimple - konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    Dla interfejsów sieciowych wybierz **Dodaj interfejs sieciowy** a następnie skonfiguruj następujące ustawienia:
    
    | Ustawienie | Opis |
    | ------------ | ------------- |
    | **Nazwa** | Wprowadź nazwę identyfikującą interfejsu.  |
    | **Sieci** | Wybierz z listy grup skonfigurowanego portu rozproszonych w swojej vSphere chmurze prywatnej.  |
    | **Karta** | Wybierz kartę vSphere z listy dostępnych typów skonfigurowane dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">wybierania karty sieciowej dla maszyny wirtualnej</a>. |
    | **Włącz przy rozruchu** | Wybierz, czy włączyć sprzętowej karcie interfejsu Sieciowego podczas rozruchu maszyny Wirtualnej. Wartość domyślna to **Włącz**. |

    W przypadku dysków wybierz **Dodaj dysk** a następnie skonfiguruj następujące ustawienia:

    | Ustawienie | Opis |
    | ------------ | ------------- |
    | **Nazwa** | Wprowadź nazwę identyfikującą dysku.  |
    | **Rozmiar** | Wybierz jeden z dostępnych rozmiarów.  |
    | **SCSI Controller** | Wybierz kontroler SCSI dysku.  |
    | **Tryb** | Tryb Określa, jak dysk uczestniczy w migawki. Wybierz jedną z następujących opcji: <br> **Niezależnie od trwałego**: Wszystkie zmiany zapisywane na dysku są zapisywane jako trwałe.<br> **Niezależnie od nietrwałe**: Zmiany zapisywane na dysku są odrzucane, wyłączyć lub Resetuj maszynę wirtualną. Niezależnie od trybu nietrwałe można zawsze ponownie maszynę Wirtualną, w tym samym stanie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentacją VMware</a>.

7. Po zakończeniu sprawdzania poprawności, przejrzyj ustawienia, a następnie wybierz **Utwórz**. Aby wprowadzić zmiany, wybierz opcję karty u góry lub wybierz **wstecz: Konfiguracje**.

    ![Tworzenie maszyny wirtualnej CloudSimple — przeglądanie + tworzenie](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Kolejne kroki

* [Widok listy maszyn wirtualnych CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Zarządzanie maszynami wirtualnymi CloudSimple z platformy Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
