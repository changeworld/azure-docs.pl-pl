---
title: Azure VMware Solutions (Automatyczna synchronizacja) — Zarządzanie maszynami wirtualnymi chmur prywatnych w systemie Azure
description: Opisuje sposób zarządzania maszynami wirtualnymi chmur prywatnych w Azure Portal, takich jak dodawanie dysków, zmiana pojemności maszyny wirtualnej i Dodawanie interfejsów sieciowych
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0cce1dc7ff3935a3174d4e96b553a5485950df73
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015001"
---
# <a name="manage-your-avs-private-cloud-virtual-machines-in-azure"></a>Zarządzanie maszynami wirtualnymi chmury prywatnej w chmurze na platformie Azure

Aby zarządzać maszynami wirtualnymi [utworzonymi dla chmury prywatnej automatycznej synchronizacji](azure-create-vm.md), zaloguj się do [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję Virtual (Wyszukaj w obszarze **wszystkie usługi** lub **Virtual Machines** w menu po stronie).

## <a name="control-virtual-machine-operation"></a>Sterowanie operacją maszyny wirtualnej

Poniższe kontrolki są dostępne na stronie **Przegląd** dla wybranej maszyny wirtualnej.

| Kontrola | Opis |
| ------------ | ------------- |
| Łączenie | Nawiąż połączenie z określoną maszyną wirtualną.  |
| Rozpocznij | Uruchom określoną maszynę wirtualną.  |
| Ponowne uruchamianie | Zamknij i Wyłącz określoną maszynę wirtualną.  |
| Stop | Zamknij konkretną maszynę wirtualną.  |
| Przechwytywanie | Przechwyć obraz określonej maszyny wirtualnej, aby można było go użyć jako obrazu do utworzenia innych maszyn wirtualnych. Zobacz [Tworzenie obrazu zarządzanego uogólnionej maszyny wirtualnej na platformie Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Move | Przejdź do określonej maszyny wirtualnej.  |
| Usuń | Usuń określoną maszynę wirtualną.  |
| Odśwież | Odśwież dane na ekranie.  |

### <a name="view-performance-information"></a>Wyświetlanie informacji o wydajności

Na wykresach w dolnym obszarze strony **Przegląd** znajdują się dane wydajności dla wybranego interwału (Ostatnia godzina do ostatnich 30 dni; wartość domyślna to Ostatnia godzina). W ramach każdego wykresu można wyświetlić wartości liczbowe dla dowolnego czasu w interwale, przesuwając kursor z powrotem i w dół na wykresie.

Wyświetlane są następujące wykresy.

| Element | Opis |
| ------------ | ------------- |
| Procesor CPU (średnia) | Średnie użycie procesora (w procentach) w wybranym interwale.   |
| Network (Sieć) | Ruch do i z sieci (MB) przez wybrany interwał.  |
| Bajty dysku | Łączna ilość danych odczytanych z dysku i zapisywana na dysku (MB) w wybranym interwale.  |
| Operacje dysków | Średnia liczba operacji dyskowych (operacji na sekundę) w wybranym interwale. |

## <a name="manage-vm-disks"></a>Zarządzaj dyskami maszyny Wirtualnej

Aby dodać dysk maszyny wirtualnej, Otwórz stronę **dyski** dla wybranej maszyny wirtualnej. Aby dodać dysk, kliknij przycisk **Dodaj dysk**. Skonfiguruj następujące ustawienia, wprowadzając lub wybierając opcję wbudowaną. Kliknij pozycję **Zapisz**.

   | Element | Opis |
   | ------------ | ------------- |
   | Nazwa | Wprowadź nazwę identyfikującą dysk.  |
   | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
   | Kontroler SCSI | Wybierz kontroler SCSI. Dostępne kontrolery różnią się w zależności od różnych obsługiwanych systemów operacyjnych.  |
   | Tryb | Określa, w jaki sposób dysk uczestniczy w migawce. Wybierz jedną z następujących opcji: <br> -Niezależne trwałe: wszystkie dane zapisywane na dysku są zapisywane trwale.<br> -Niezależne, nietrwałe: zmiany zapisywane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej. Ten tryb umożliwia zawsze ponowne uruchomienie maszyny wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz [dokumentację programu VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Aby usunąć dysk, zaznacz go i kliknij przycisk **Usuń**.

## <a name="change-the-capacity-of-the-vm"></a>Zmiana pojemności maszyny wirtualnej

Aby zmienić pojemność maszyny wirtualnej, Otwórz stronę **rozmiar** wybranej maszyny wirtualnej. Określ dowolne z następujących elementów, a następnie kliknij przycisk **Zapisz**.

| Element | Opis |
| ------------ | ------------- |
| Liczba rdzeni | Liczba rdzeni przypisanych do maszyny wirtualnej.  |
| Wirtualizacja sprzętu | Zaznacz pole wyboru, aby uwidocznić wirtualizację sprzętu w systemie operacyjnym gościa. Zapoznaj się z artykułem VMware [udostępnianie sprzętowej wirtualizacji przez oprogramowanie VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Rozmiar pamięci | Wybierz ilość pamięci, która ma zostać przydzielona do maszyny wirtualnej.  

## <a name="manage-network-interfaces"></a>Zarządzanie interfejsami sieciowymi

Aby dodać interfejs, kliknij przycisk **Dodaj interfejs sieciowy**. Skonfiguruj wszystkie poniższe ustawienia, wprowadzając lub wybierając opcję wbudowaną. Kliknij pozycję **Zapisz**.

   | Kontrola | Opis |
   | ------------ | ------------- |
   | Nazwa | Wprowadź nazwę identyfikującą interfejs.  |
   | Network (Sieć) | Wybierz z listy skonfigurowanych sieci w vSphere prywatnej chmury automatycznej.  |
   | Kartę | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł z bazy wiedzy VMware z [wybieraniem karty sieciowej dla maszyny wirtualnej](https://kb.vmware.com/s/article/1001805). |
   | Włącz przy rozruchu | Zdecyduj, czy włączyć sprzęt kart sieciowych podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **enable**. |

Aby usunąć interfejs sieciowy, zaznacz go i kliknij przycisk **Usuń**.
