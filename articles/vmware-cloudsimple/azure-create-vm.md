---
title: Rozwiązanie Azure VMware by CloudSimple — tworzenie maszyny wirtualnej na platformie Azure za pomocą szablonów maszyn wirtualnych
description: W tym artykule opisano sposób tworzenia maszyn wirtualnych na platformie Azure przy użyciu szablonów maszyn wirtualnych w infrastrukturze VMware dla chmury CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244695"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Tworzenie maszyny wirtualnej na platformie Azure przy użyciu szablonów maszyn wirtualnych w infrastrukturze VMware

Maszynę wirtualną można utworzyć w witrynie Azure portal przy użyciu szablonów maszyn wirtualnych w infrastrukturze VMware, które administrator CloudSimple włączył dla twojej subskrypcji.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Tworzenie maszyny wirtualnej CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **maszyny wirtualne CloudSimple**.

3. Kliknij przycisk **Dodaj**.

    ![Tworzenie maszyny wirtualnej CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź podstawowe informacje kliknij przycisk **Dalej:Rozmiar**.

    > [!NOTE]
    > CloudSimple tworzenie maszyny wirtualnej na platformie Azure wymaga szablonu maszyny wirtualnej.  Ten szablon maszyny Wirtualnej powinien istnieć w centrum vCenter private cloud.  Utwórz maszynę wirtualną w chmurze prywatnej z interfejsu użytkownika vCenter z żądanym systemem operacyjnym i konfiguracjami.  Za pomocą instrukcji w [klonowanie maszyny wirtualnej do szablonu w vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), utworzyć szablon.

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
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny Wirtualnej (dla szablonów systemu Windows)|
    | Hasło <br>Potwierdź hasło | Hasło administratora maszyny Wirtualnej (dla szablonów systemu Windows).  |

5. Wybierz liczbę rdzeni i pojemność pamięci maszyny Wirtualnej i kliknij przycisk **Dalej:Konfiguracje**. Zaznacz to pole wyboru, jeśli chcesz udostępnić pełną wirtualizację procesora CPU na system operacyjny gościa, aby aplikacje wymagające wirtualizacji sprzętu mogły działać na maszynach wirtualnych bez tłumaczenia binarnego lub parawirtualizacji. Aby uzyskać więcej informacji, zobacz artykuł VMware [Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Tworzenie maszyny wirtualnej CloudSimple — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy i dyski sieciowe zgodnie z opisem w poniższych tabelach i kliknij przycisk **Przejrzyj + utwórz**.

    ![Tworzenie maszyny wirtualnej CloudSimple - konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    W przypadku interfejsów sieciowych kliknij pozycję **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.

    | Kontrola | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę, aby zidentyfikować interfejs.  |
    | Network (Sieć) | Wybierz z listy skonfigurowanej rozproszonej grupy portów w prywatnej chmurze vSphere.  |
    | Adapter | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł bazy wiedzy VMware [Wybieranie karty sieciowej dla maszyny wirtualnej](https://kb.vmware.com/s/article/1001805). |
    | Włączanie przy rozruchu | Wybierz, czy sprzęt karty sieciowej ma być włączany podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **Włącz**. |

    W przypadku dysków kliknij pozycję **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę, aby zidentyfikować dysk.  |
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
    | Kontroler SCSI | Wybierz kontroler SCSI dla dysku.  |
    | Tryb | Określa, jak dysk uczestniczy w migawek. Wybierz jedną z następujących opcji: <br> - Niezależne trwałe: Wszystkie dane zapisane na dysku są zapisywane na stałe.<br> - Niezależne nietrwałe: Zmiany zapisane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej.  Niezależny tryb nietrwałe umożliwia zawsze ponowne uruchomienie maszyny Wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz [dokumentację VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Po zakończeniu sprawdzania poprawności przejrzyj ustawienia i kliknij przycisk **Utwórz**. Aby wprowadzić zmiany, kliknij karty u góry lub kliknij.

    ![Tworzenie maszyny wirtualnej CloudSimple — recenzja](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Wyświetl listę maszyn wirtualnych CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **maszyny wirtualne CloudSimple**.

3. Wybierz, na którym została utworzona chmura prywatna.

    ![Lista maszyn wirtualnych CloudSimple](media/list-cloudsimple-virtual-machines.png)

Lista maszyn wirtualnych CloudSimple zawiera maszyny wirtualne utworzone z witryny Azure portal.  Maszyny wirtualne utworzone w usłudze Private Cloud vCenter w zamapowanym pula zasobów vCenter zostaną wyświetlone na liście.  
