---
title: Rozwiązanie VMware firmy Azure według CloudSimple — szybki start — korzystanie z maszyn wirtualnych VMware na platformie Azure
description: W tym przewodniku szybki start dowiesz się, jak skonfigurować i korzystać z maszyn wirtualnych VMware z Azure Portal przy użyciu rozwiązania Azure VMware przez CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dd4faf6df54d478654c59ffc18bf8c5873d576b9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816658"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>Szybki start: Używanie maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w Azure Portal, można użyć szablonów maszyn wirtualnych dostępnych w ramach programu vCenter w chmurze prywatnej. Administrator vCenter może utworzyć dodatkowe szablony w chmurze prywatnej.

## <a name="create-a-vm-template"></a>Tworzenie szablonu maszyny wirtualnej

Najpierw utwórz maszynę wirtualną w chmurze prywatnej przy użyciu interfejsu użytkownika programu vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami w artykule VMware [klonowanie maszyny wirtualnej do szablonu w kliencie sieci Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Zapisz szablon maszyny wirtualnej w chmurze prywatnej vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w Azure Portal

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **CloudSimple Virtual Machines**.

3. Wybierz pozycję **Dodaj**.

    ![Wybierz pozycję Dodaj](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź następujące informacje dotyczące maszyny wirtualnej, a następnie wybierz pozycję **dalej: Rozmiar**.

    ![Tworzenie maszyny wirtualnej CloudSimple — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | **Subskrypcja** | Subskrypcja platformy Azure skojarzona z chmurą prywatną.  |
    | **Grupa zasobów** | Grupa zasobów, do której zostanie przypisana maszyna wirtualna. Możesz wybrać istniejącą grupę lub utworzyć nową. |
    | **Nazwa** | Nazwa identyfikująca maszynę wirtualną.  |
    | **Location** | Region świadczenia usługi Azure, w którym jest hostowana maszyna wirtualna.  |
    | **Chmura prywatna** | Chmura prywatna CloudSimple, w której chcesz utworzyć maszynę wirtualną. |
    | **ResourcePool** | Mapowana Pula zasobów dla maszyny wirtualnej. Wybierz z dostępnych pul zasobów. |
    | **Szablon vSphere** | Szablon vSphere dla maszyny wirtualnej.  |
    | **Nazwa użytkownika** | Nazwa użytkownika administratora maszyny wirtualnej (dla szablonów systemu Windows).|
    | **Hasło** |  Hasło administratora maszyny wirtualnej (dla szablonów systemu Windows). |
    | **Potwierdź hasło** | Hasło podane w poprzednim polu. |

5. Wybierz liczbę rdzeni i pojemność pamięci dla maszyny wirtualnej. Wybierz opcję **Udostępnij dla systemu operacyjnego gościa** , jeśli chcesz uwidocznić pełną WIRTUALIZACJĘ procesora CPU w systemie operacyjnym gościa. Aplikacje wymagające wirtualizacji sprzętu można uruchamiać na maszynach wirtualnych bez tłumaczenia binarnego lub właściwościami parawirtualizacji. Aby uzyskać więcej informacji, zapoznaj się z artykułem VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">udostępnianie sprzętowej wirtualizacji</a>przez oprogramowanie VMware. Gdy skończysz, wybierz pozycję **dalej: Konfiguracje**.

    ![Tworzenie maszyny wirtualnej CloudSimple — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy sieciowe i dyski zgodnie z opisem w poniższych tabelach, a następnie wybierz pozycję **Przegląd + Utwórz**.

    ![Tworzenie maszyny wirtualnej CloudSimple — konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    W obszarze interfejsy sieciowe wybierz pozycję **Dodaj interfejs sieciowy** , a następnie skonfiguruj następujące ustawienia:
    
    | Ustawienie | Opis |
    | ------------ | ------------- |
    | **Nazwa** | Wprowadź nazwę identyfikującą interfejs.  |
    | **Sieci** | Wybierz z listy skonfigurowanych rozproszonych grup portów w chmurze prywatnej vSphere.  |
    | **Kartę** | Wybierz kartę vSphere z listy dostępnych typów skonfigurowanych dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł VMware z <a href="https://kb.vmware.com/s/article/1001805" target="_blank">wybieraniem karty sieciowej dla maszyny wirtualnej</a>. |
    | **Włącz przy rozruchu** | Zdecyduj, czy włączyć sprzęt kart sieciowych podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **enable**. |

    W obszarze dyski wybierz pozycję **Dodaj dysk** , a następnie skonfiguruj następujące ustawienia:

    | Ustawienie | Opis |
    | ------------ | ------------- |
    | **Nazwa** | Wprowadź nazwę identyfikującą dysk.  |
    | **Rozmiar** | Wybierz jeden z dostępnych rozmiarów.  |
    | **Kontroler SCSI** | Wybierz kontroler SCSI dla dysku.  |
    | **Wyst** | Tryb określa sposób, w jaki dysk uczestniczy w migawce. Wybierz jedną z następujących opcji: <br> **Niezależne trwałe**: Wszystkie zmiany zapisywane na dysku są zapisywane na stałe.<br> Niezależny nietrwały: Zmiany wprowadzone na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej. Niezależny tryb nietrwały umożliwia zawsze ponowne uruchomienie maszyny wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentację programu VMware</a>.

7. Po zakończeniu walidacji Sprawdź ustawienia i wybierz pozycję **Utwórz**. Aby wprowadzić zmiany, wybierz karty u góry lub wybierz pozycję **Poprzednia: Konfiguracje**.

    ![Tworzenie maszyny wirtualnej CloudSimple — przegląd + tworzenie](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetlanie listy maszyn wirtualnych CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Zarządzanie maszynami wirtualnymi CloudSimple z poziomu platformy Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
