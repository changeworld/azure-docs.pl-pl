---
title: 'Szybki start: korzystanie z maszyn wirtualnych vmware na platformie Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Dowiedz się, jak skonfigurować i używać maszyn wirtualnych VMware z witryny Azure portal przy użyciu rozwiązania Azure VMware przez CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019557"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Szybki start — korzystanie z maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w witrynie Azure portal, użyj szablonów maszyn wirtualnych, które administrator CloudSimple włączył dla twojej subskrypcji. Szablony maszyn wirtualnych znajdują się w infrastrukturze VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Tworzenie maszyny wirtualnej CloudSimple na platformie Azure wymaga szablonu maszyny Wirtualnej

Utwórz maszynę wirtualną w chmurze prywatnej z interfejsu użytkownika vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami zawartymi w aplikacji [Klonowanie maszyny wirtualnej z szablonem w kliencie sieci Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Przechowuj szablon maszyny Wirtualnej w centrum vCenter private cloud.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w witrynie Azure Portal

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **maszyny wirtualne CloudSimple**.

3. Kliknij przycisk **Dodaj**.

    ![Tworzenie maszyny wirtualnej CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź podstawowe informacje i kliknij przycisk **Dalej:Rozmiar**.

    ![Tworzenie maszyny wirtualnej CloudSimple — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | Subskrypcja | Subskrypcja platformy Azure skojarzona z chmurą prywatną.  |
    | Grupa zasobów | Grupa zasobów, do której zostanie przypisana maszyna wirtualna. Można wybrać istniejącą grupę lub utworzyć nową. |
    | Nazwa | Nazwa do identyfikowania maszyny Wirtualnej.  |
    | Lokalizacja | Region platformy Azure, w którym jest hostowana ta maszyna wirtualna.  |
    | Chmura prywatna | CloudSimple Private Cloud, w którym chcesz utworzyć maszynę wirtualną. |
    | Pula zasobów | Zamapowana pula zasobów dla maszyny Wirtualnej. Wybierz spośród dostępnych pul zasobów. |
    | Szablon vSphere | szablon vSphere dla maszyny Wirtualnej.  |
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny Wirtualnej (dla szablonów systemu Windows).|
    | Hasło |  Hasło administratora maszyny Wirtualnej (dla szablonów systemu Windows). |
    | Potwierdź hasło | Potwierdź hasło. |

5. Wybierz liczbę rdzeni i pojemność pamięci maszyny Wirtualnej i kliknij przycisk **Dalej:Konfiguracje**. Zaznacz to pole wyboru, jeśli chcesz udostępnić pełną wirtualizację procesora CPU na system operacyjny gościa. Aplikacje wymagające wirtualizacji sprzętu mogą działać na maszynach wirtualnych bez tłumaczenia binarnego lub parawirtualizacji. Aby uzyskać więcej informacji, zobacz artykuł VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expose VMware Hardware Assisted Virtualization</a>.

    ![Tworzenie maszyny wirtualnej CloudSimple — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy i dyski sieciowe zgodnie z opisem w poniższych tabelach i kliknij przycisk **Przejrzyj + utwórz**.

    ![Tworzenie maszyny wirtualnej CloudSimple - konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    W przypadku interfejsów sieciowych kliknij pozycję **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.

    | Kontrola | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę, aby zidentyfikować interfejs.  |
    | Network (Sieć) | Wybierz z listy skonfigurowanej rozproszonej grupy portów w prywatnej chmurze vSphere.  |
    | Adapter | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł bazy wiedzy VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Wybieranie karty sieciowej dla maszyny wirtualnej</a>. |
    | Włączanie przy rozruchu | Wybierz, czy sprzęt karty sieciowej ma być włączany podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **Włącz**. |

    W przypadku dysków kliknij pozycję **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę, aby zidentyfikować dysk.  |
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
    | Kontroler SCSI | Wybierz kontroler SCSI dla dysku.  |
    | Tryb | Określa, jak dysk uczestniczy w migawek. Wybierz jedną z następujących opcji: <br> - Niezależne trwałe: Wszystkie dane zapisane na dysku są zapisywane na stałe.<br> - Niezależne nietrwałe: Zmiany zapisane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej.  Niezależny tryb nietrwałe umożliwia zawsze ponowne uruchomienie maszyny Wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentację VMware</a>.

7. Po zakończeniu sprawdzania poprawności przejrzyj ustawienia i kliknij przycisk **Utwórz**. Aby wprowadzić zmiany, kliknij karty u góry lub kliknij.

    ![Tworzenie maszyny wirtualnej CloudSimple — recenzja](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Następne kroki

* [Wyświetl listę maszyn wirtualnych CloudSimple](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Zarządzanie maszyną wirtualną CloudSimple z platformy Azure](azure-manage-vm.md)
