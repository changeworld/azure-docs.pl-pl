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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577723"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Przewodnik Szybki Start — używanie maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w witrynie Azure portal, należy używać szablonów maszyn wirtualnych, które CloudSimple administrator włączył dla Twojej subskrypcji. Te szablony maszyn wirtualnych znajdują się w infrastrukturze VMware.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Tworzenie maszyny Wirtualnej CloudSimple na platformie Azure wymaga szablonu maszyny Wirtualnej

Utwórz maszynę wirtualną w chmurze prywatnej z interfejsu użytkownika programu vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami [sklonować maszynę wirtualną do szablonu w kliencie internetowym vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Store szablonu maszyny Wirtualnej na chmurę prywatną serwera vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Utwórz maszynę wirtualną w witrynie Azure portal

1. W menu po lewej stronie, kliknij polecenie **+** lub **Utwórz zasób**.

2. W menu po lewej stronie, kliknij polecenie **obliczenia**, a następnie kliknij przycisk **maszyny wirtualnej CloudSimple**.

3. Kliknij przycisk **Potwierdź** Aby sprawdzić, czy chcesz utworzyć nową maszynę Wirtualną.

4. Ustaw konfigurację podstawowe, zgodnie z opisem w poniższej tabeli, a następnie kliknij przycisk **dalej: Rozmiar**.

    | Pole | Opis |
    | ------------ | ------------- |
    | Subskrypcja | Subskrypcja platformy Azure skojarzone z wdrożeniem chmury prywatnej.  |
    | Grupa zasobów | Grupa wdrożenia, do którego zostanie przypisany maszyny Wirtualnej. Można wybrać istniejącą grupę lub Utwórz nową. |
    | Name (Nazwa) | Nazwa identyfikująca maszyny Wirtualnej.  |
    | Lokalizacja | Region platformy Azure, w którym znajduje się ta maszyna wirtualna.  |
    | Pula zasobów | Zasoby fizyczne dla maszyny Wirtualnej. Wybierz pule dostępnych zasobów. |
    | vSphere szablonu | Typ szablonu systemu operacyjnego dla maszyny Wirtualnej.  |
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny Wirtualnej. |
    | Hasło Potwierdź hasło | Hasło administratora maszyny Wirtualnej.  |

5. Wybierz liczbę rdzeni oraz pojemność pamięci dla maszyny Wirtualnej.

6. (Opcjonalnie) Do udostępnienia pełną wirtualizacji Procesora systemu operacyjnego gościa, należy zaznaczyć **udostępnienia systemu operacyjnego gościa** pola wyboru.
Zaznacz to pole wyboru umożliwia aplikacji, które wymagają wirtualizację sprzętu można uruchamiać na maszynach wirtualnych bez tłumaczenie binarne lub parawirtualizacji. Aby uzyskać więcej informacji, zobacz artykuł VMware [ujawnić wirtualizacji sprzętowej sprzętu VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

7. Kliknij pozycję **Next: Konfiguracja**.

8. Skonfiguruj interfejsy sieciowe i dyski, zgodnie z opisem w poniższych tabelach.

    Dla interfejsów sieciowych, kliknij przycisk **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.

    | Kontrola | Opis |
    | ------------ | ------------- |
    | Name (Nazwa) | Wprowadź nazwę identyfikującą interfejsu.  |
    | Sieć | Wybierz z listy skonfigurowanych sieci w swojej vSphere chmurze prywatnej.  |
    | Karta | Wybierz łącznik vSphere z listy dostępnych typów skonfigurowane dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł bazy wiedzy knowledge base VMware [wybierania karty sieciowej dla maszyny wirtualnej](https://kb.vmware.com/s/article/1001805). |
    | Włącz przy rozruchu | Wybierz, czy włączyć sprzętowej karcie interfejsu Sieciowego podczas rozruchu maszyny Wirtualnej. Wartość domyślna to **Włącz**. |

    W przypadku dysków, kliknij przycisk **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis |
    | ------------ | ------------- |
    | Name (Nazwa) | Wprowadź nazwę identyfikującą dysku.  |
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
    | Kontroler SCSI | Wybierz kontroler SCSI. Dostępnych kontrolerów różnią się dla różnych obsługiwanych systemów operacyjnych.  |
    | Tryb | Określa, jak dysk uczestniczy w migawki. Wybierz jedną z następujących opcji: <br> — Niezależnie od stałe: Wszystkie dane zapisane na dysku są zapisywane jako trwałe.<br> — Niezależnie od nietrwałe: Zmiany zapisywane na dysku są odrzucane, wyłączyć lub Resetuj maszynę wirtualną.  Niezależnie od trybu nietrwałe można zawsze ponownie maszynę Wirtualną, w tym samym stanie. Aby uzyskać więcej informacji, zobacz [dokumentacją VMware.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. Przejrzyj ustawienia. Aby wprowadzić zmiany, kliknij odpowiednią kartę u góry.

10. Kliknij przycisk **Utwórz** Aby zapisać ustawienia i utworzyć maszynę Wirtualną.

## <a name="next-steps"></a>Kolejne kroki

* [Widok listy maszyn wirtualnych CloudSimple](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Zarządzanie maszyną wirtualną CloudSimple z platformy Azure](https://docs.azure.cloudsimple.com/azureoverviewpage/)