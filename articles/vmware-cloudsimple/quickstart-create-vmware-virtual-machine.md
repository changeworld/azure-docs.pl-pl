---
title: Azure VMware Solutions (Automatyczna synchronizacja) — szybki start — Tworzenie maszyn wirtualnych VMware na platformie Azure
description: Dowiedz się, jak tworzyć i konfigurować maszyny wirtualne VMware na podstawie Azure Portal przy użyciu rozwiązań VMware platformy Azure (Automatyczna synchronizacja)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019557"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Szybki Start — Tworzenie maszyn wirtualnych VMware na platformie Azure

Aby utworzyć maszynę wirtualną w Azure Portal, użyj szablonów maszyn wirtualnych, które zostały włączone przez administratora automatycznej synchronizacji dla Twojej subskrypcji. Szablony maszyn wirtualnych znajdują się w infrastrukturze programu VMware.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>Tworzenie maszyn wirtualnych na platformie Azure wymaga szablonu maszyny wirtualnej

Utwórz maszynę wirtualną w chmurze prywatnej automatycznej synchronizacji z poziomu interfejsu użytkownika programu vCenter. Aby utworzyć szablon, postępuj zgodnie z instrukcjami w temacie [klonowanie maszyny wirtualnej do szablonu w kliencie sieci Web vSphere](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Zapisz szablon maszyny wirtualnej w chmurze prywatnej chmury programu vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Tworzenie maszyny wirtualnej w Azure Portal

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **Virtual Machines automatyczna synchronizacja**.

3. Kliknij pozycję **Dodaj**.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji](media/create-cloudsimple-virtual-machine.png)

4. Wprowadź podstawowe informacje i kliknij przycisk **Dalej: rozmiar**.

    ![Tworzenie maszyny wirtualnej do automatycznej synchronizacji — podstawy](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Pole | Opis |
    | ------------ | ------------- |
    | Subskrypcja | Subskrypcja platformy Azure skojarzona z chmurą prywatną. |
    | Grupa zasobów | Grupa zasobów, do której zostanie przypisana maszyna wirtualna. Możesz wybrać istniejącą grupę lub utworzyć nową. |
    | Nazwa | Nazwa identyfikująca maszynę wirtualną.  |
    | Lokalizacja | Region świadczenia usługi Azure, w którym jest hostowana ta maszyna wirtualna.  |
    | Automatyczna synchronizacja chmury prywatnej | Automatyczna synchronizacja chmury prywatnej, w której chcesz utworzyć maszynę wirtualną. |
    | Pula zasobów | Mapowana Pula zasobów dla maszyny wirtualnej. Wybierz z dostępnych pul zasobów. |
    | Szablon vSphere | szablon vSphere dla maszyny wirtualnej.  |
    | Nazwa użytkownika | Nazwa użytkownika administratora maszyny wirtualnej (dla szablonów systemu Windows).|
    | Hasło |  Hasło administratora maszyny wirtualnej (dla szablonów systemu Windows). |
    | Potwierdź hasło | Potwierdź hasło. |

5. Wybierz liczbę rdzeni i pojemność pamięci dla maszyny wirtualnej, a następnie kliknij przycisk **Dalej: konfiguracje**. Zaznacz pole wyboru, jeśli chcesz uwidocznić pełną wirtualizację procesora CPU w systemie operacyjnym gościa. Aplikacje wymagające wirtualizacji sprzętu można uruchamiać na maszynach wirtualnych bez tłumaczenia binarnego lub właściwościami parawirtualizacji. Aby uzyskać więcej informacji, zapoznaj się z artykułem VMware <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">udostępnianie sprzętowej wirtualizacji</a>przez oprogramowanie VMware.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — rozmiar](media/create-cloudsimple-virtual-machine-size.png)

6. Skonfiguruj interfejsy sieciowe i dyski zgodnie z opisem w poniższych tabelach i kliknij kolejno pozycje **Przegląd + Utwórz**.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — konfiguracje](media/create-cloudsimple-virtual-machine-configurations.png)

    W obszarze interfejsy sieciowe kliknij pozycję **Dodaj interfejs sieciowy** i skonfiguruj następujące ustawienia.

    | Kontrola | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę identyfikującą interfejs.  |
    | Network (Sieć) | Wybierz z listy skonfigurowanych rozproszonych grup portów w vSphere prywatnej chmury automatycznej. |
    | Kartę | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł z bazy wiedzy VMware z <a href="https://kb.vmware.com/s/article/1001805" target="_blank">wybieraniem karty sieciowej dla maszyny wirtualnej</a>. |
    | Włącz przy rozruchu | Zdecyduj, czy włączyć sprzęt kart sieciowych podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **enable**. |

    W obszarze dyski kliknij pozycję **Dodaj dysk** i skonfiguruj następujące ustawienia.

    | Element | Opis |
    | ------------ | ------------- |
    | Nazwa | Wprowadź nazwę identyfikującą dysk. |
    | Rozmiar | Wybierz jeden z dostępnych rozmiarów. |
    | Kontroler SCSI | Wybierz kontroler SCSI dla dysku. |
    | Tryb | Określa, w jaki sposób dysk uczestniczy w migawce. Wybierz jedną z następujących opcji: <br> -Niezależne trwałe: wszystkie dane zapisywane na dysku są zapisywane trwale.<br> -Niezależne nietrwałe: zmiany zapisywane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej. Niezależny tryb nietrwały umożliwia zawsze ponowne uruchomienie maszyny wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentację programu VMware</a>.

7. Po zakończeniu walidacji Sprawdź ustawienia i kliknij przycisk **Utwórz**. Aby wprowadzić zmiany, kliknij karty u góry lub kliknij pozycję.

    ![Utwórz maszynę wirtualną o automatycznej synchronizacji — przegląd](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Następne kroki

* [Wyświetlanie listy maszyn wirtualnych o automatycznej synchronizacji](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Zarządzanie maszyną wirtualną automatycznej synchronizacji na platformie Azure](azure-manage-vm.md)
