---
title: Rozwiązanie programu VMware na platformie Azure przez CloudSimple Przewodnik Szybki Start — używanie maszyn wirtualnych VMware na platformie Azure
description: Dowiedz się, jak skonfigurować i używanie maszyn wirtualnych VMware z poziomu portalu Azure za pomocą usługi Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0bb8d7a5a1ea30b704b44c9337cd28043597ff7
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209531"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Przewodnik Szybki Start — używanie maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w witrynie Azure portal, należy używać szablonów maszyn wirtualnych, które CloudSimple administrator włączył dla Twojej subskrypcji. Te szablony maszyn wirtualnych znajdują się w infrastrukturze VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Tworzenie maszyny Wirtualnej CloudSimple na platformie Azure wymaga szablonu maszyny Wirtualnej

Utwórz maszynę wirtualną w chmurze prywatnej z interfejsu użytkownika programu vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami [sklonować maszynę wirtualną do szablonu w kliencie internetowym vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store szablonu maszyny Wirtualnej na chmurę prywatną serwera vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Utwórz maszynę wirtualną w witrynie Azure portal

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **maszyn wirtualnych CloudSimple**.

3. Kliknij pozycję **Add** (Dodaj).

    ![Tworzenie maszyny wirtualnej CloudSimple](media/create-cloudsimple-virtual-machine.png)

4. Wprowadzanie podstawowych informacji, kliknij **dalej: rozmiar**.

    ![Tworzenie maszyny wirtualnej CloudSimple — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | Subskrypcja | Subskrypcja platformy Azure skojarzone z chmury prywatnej.  |
    | Grupa zasobów | Grupa zasobów, do którego zostanie przypisany maszyny Wirtualnej. Można wybrać istniejącą grupę lub Utwórz nową. |
    | Name (Nazwa) | Nazwa identyfikująca maszyny Wirtualnej.  |
    | Lokalizacja | Region platformy Azure, w którym znajduje się ta maszyna wirtualna.  |
    | Chmura prywatna | Chmura prywatna CloudSimple, w którym chcesz utworzyć maszynę wirtualną. |
    | Pula zasobów | Mapowany puli zasobów dla maszyny Wirtualnej. Wybierz pule dostępnych zasobów. |
    | vSphere szablonu | Szablon vSphere dla maszyny Wirtualnej.  |
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny Wirtualnej (w przypadku szablonów Windows)|
    | Hasło |  Hasło administratora maszyny Wirtualnej (w przypadku szablonów Windows). |
    | Potwierdź hasło | Potwierdź hasło |

5. Wybierz liczbę rdzeni oraz pojemność pamięci maszyny Wirtualnej, a następnie kliknij przycisk **dalej: konfiguracje**. Zaznacz pole wyboru, jeśli chcesz udostępnić pełną wirtualizacji Procesora systemu operacyjnego gościa. Aplikacje, które wymagają wirtualizację sprzętu można uruchamiać na maszynach wirtualnych bez tłumaczenie binarne lub parawirtualizacji. Aby uzyskać więcej informacji, zobacz artykuł VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">ujawnić wirtualizacji sprzętowej sprzętu VMware</a>.

    ![Tworzenie maszyny wirtualnej CloudSimple — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy sieciowe i dyski, zgodnie z opisem w poniższych tabelach, a następnie kliknij przycisk **przeglądu + Utwórz**.

    ![Tworzenie maszyny wirtualnej CloudSimple - konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    Dla interfejsów sieciowych, kliknij przycisk **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.
    
    | Kontrola | Opis |
    | ------------ | ------------- |
    | Name (Nazwa) | Wprowadź nazwę identyfikującą interfejsu.  |
    | Sieć | Wybierz z listy skonfigurowanego portu rozproszonej grupy w Twojej vSphere chmurze prywatnej.  |
    | Karta | Wybierz łącznik vSphere z listy dostępnych typów skonfigurowane dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł bazy wiedzy knowledge base VMware <a href="https://kb.vmware.com/s/article/1001805" target="_blank">wybierania karty sieciowej dla maszyny wirtualnej</a>. |
    | Włącz przy rozruchu | Wybierz, czy włączyć sprzętowej karcie interfejsu Sieciowego podczas rozruchu maszyny Wirtualnej. Wartość domyślna to **Włącz**. |

    W przypadku dysków, kliknij przycisk **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis | 
    | ------------ | ------------- | 
    | Name (Nazwa) | Wprowadź nazwę identyfikującą dysku.  | 
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  | 
    | Kontroler SCSI | Wybierz kontroler SCSI dysku.  |
    | Tryb | Określa, jak dysk uczestniczy w migawki. Wybierz jedną z następujących opcji: <br> — Niezależnie od stałe: Wszystkie dane zapisane na dysku są zapisywane jako trwałe.<br> — Niezależnie od nietrwałe: Zmiany zapisywane na dysku są odrzucane, wyłączyć lub Resetuj maszynę wirtualną.  Niezależnie od trybu nietrwałe można zawsze ponownie maszynę Wirtualną, w tym samym stanie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentacją VMware</a>.

7. Po zakończeniu weryfikacji Przejrzyj ustawienia i kliknij **Utwórz**. Aby wprowadzić zmiany, kliknij odpowiednią kartę w górnej lub kliknij przycisk.

    ![Tworzenie maszyny wirtualnej CloudSimple — Przegląd](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Kolejne kroki

* [Widok listy maszyn wirtualnych CloudSimple](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Zarządzanie maszyną wirtualną CloudSimple z platformy Azure](https://docs.azure.cloudsimple.com/azure-manage-vm/)
