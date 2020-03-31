---
title: Rozwiązanie Azure VMware według cloudsimple — zarządzanie maszynami wirtualnymi w chmurze prywatnej na platformie Azure
description: W tym artykule opisano sposób zarządzania maszynami wirtualnymi cloudsimple private cloud w portalu Azure, w tym dodawanie dysków, zmienianie pojemności maszyn wirtualnych i dodawanie interfejsów sieciowych
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015001"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Zarządzanie maszynami wirtualnymi cloudsimple private cloud na platformie Azure

Aby zarządzać maszynami wirtualnymi [utworzonymi dla chmury CloudSimple Private Cloud,](azure-create-vm.md)zaloguj się do [portalu Azure](https://portal.azure.com). Wyszukaj i wybierz wirtualny (szukaj w menu **wszystkie usługi** lub **maszyny wirtualne** w menu bocznym).

## <a name="control-virtual-machine-operation"></a>Sterowanie działaniem maszyny wirtualnej

Następujące kontrolki są dostępne na stronie **Przegląd** dla wybranej maszyny wirtualnej.

| Kontrola | Opis |
| ------------ | ------------- |
| Połącz | Połącz się z określoną maszyną wirtualną.  |
| Rozpoczęcie | Uruchom określoną maszynę wirtualną.  |
| Ponowne uruchamianie | Zamknij, a następnie włącz określoną maszynę wirtualną.  |
| Stop | Zamknij określoną maszynę wirtualną.  |
| Przechwytywanie | Przechwyć obraz określonej maszyny Wirtualnej, dzięki czemu może służyć jako obraz do tworzenia innych maszyn wirtualnych. Zobacz [Tworzenie zarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Move | Przejdź do określonej maszyny Wirtualnej.  |
| Usuń | Usuń określoną maszynę wirtualną.  |
| Odświeżanie | Odśwież dane na wyświetlaczu.  |

### <a name="view-performance-information"></a>Wyświetlanie informacji o wydajności

Wykresy w dolnym obszarze strony **Przegląd** przedstawiają dane dotyczące wydajności dla wybranego interwału (ostatnia godzina do 30 dni; domyślnie jest to ostatnia godzina). W ramach każdego wykresu można wyświetlać wartości liczbowe w dowolnym momencie w danym przedziale czasu, przesuwając kursor w tę i z powrotem nad wykresem.

Zostaną wyświetlone następujące wykresy.

| Element | Opis |
| ------------ | ------------- |
| Procesor (średnia) | Średnie wykorzystanie procesora CPU w procentach w wybranym przedziale.   |
| Network (Sieć) | Ruch do sieci i poza siecią (MB) w wybranym przedziale czasu.  |
| Bajty dysku | Całkowita liczba danych odczytanych z dysku i zapisanych na dysku (MB) w wybranym przedziale czasu.  |
| Operacje na dysku | Średnia szybkość operacji na dysku (operacje/sekundę) w wybranym przedziale czasu. |

## <a name="manage-vm-disks"></a>Zarządzanie dyskami maszyn wirtualnych

Aby dodać dysk maszyny Wirtualnej, otwórz stronę **Dyski** dla wybranej maszyny Wirtualnej. Aby dodać dysk, kliknij pozycję **Dodaj dysk**. Skonfiguruj każde z poniższych ustawień, wprowadzając lub wybierając opcję wbudowaną. Kliknij przycisk **Zapisz**.

   | Element | Opis |
   | ------------ | ------------- |
   | Nazwa | Wprowadź nazwę, aby zidentyfikować dysk.  |
   | Rozmiar | Wybierz jeden z dostępnych rozmiarów.  |
   | Kontroler SCSI | Wybierz kontroler SCSI. Dostępne kontrolery różnią się w zależności od obsługiwanych systemów operacyjnych.  |
   | Tryb | Określa, jak dysk uczestniczy w migawek. Wybierz jedną z następujących opcji: <br> - Niezależne trwałe: Wszystkie dane zapisane na dysku są zapisywane na stałe.<br> - Niezależne, nietrwałe: Zmiany zapisane na dysku są odrzucane po wyłączeniu lub zresetowaniu maszyny wirtualnej.  Ten tryb umożliwia zawsze ponowne uruchomienie maszyny Wirtualnej w tym samym stanie. Aby uzyskać więcej informacji, zobacz [dokumentację VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Aby usunąć dysk, zaznacz go i kliknij przycisk **Usuń**.

## <a name="change-the-capacity-of-the-vm"></a>Zmienianie pojemności maszyny Wirtualnej

Aby zmienić pojemność maszyny Wirtualnej, otwórz stronę **Rozmiar** dla wybranej maszyny Wirtualnej. Określ dowolną z poniższych opcji, a następnie kliknij przycisk **Zapisz**.

| Element | Opis |
| ------------ | ------------- |
| Liczba rdzeni | Liczba rdzeni przypisanych do maszyny Wirtualnej.  |
| Wirtualizacja sprzętu | Zaznacz to pole wyboru, aby udostępnić wirtualizację sprzętu na system operacyjny gościa. Zobacz artykuł VMware [Uwidacznianie wirtualizacji wspomaganej sprzętem VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Rozmiar pamięci | Wybierz ilość pamięci do przydzielenia do maszyny Wirtualnej.  

## <a name="manage-network-interfaces"></a>Zarządzanie interfejsami sieciowymi

Aby dodać interfejs, kliknij pozycję **Dodaj interfejs sieciowy**. Skonfiguruj każde z poniższych ustawień, wprowadzając lub wybierając opcję wbudowaną. Kliknij przycisk **Zapisz**.

   | Kontrola | Opis |
   | ------------ | ------------- |
   | Nazwa | Wprowadź nazwę, aby zidentyfikować interfejs.  |
   | Network (Sieć) | Wybierz z listy skonfigurowanych sieci w private cloud vSphere.  |
   | Adapter | Wybierz adapter vSphere z listy dostępnych typów skonfigurowanych dla maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz artykuł bazy wiedzy VMware [Wybieranie karty sieciowej dla maszyny wirtualnej](https://kb.vmware.com/s/article/1001805). |
   | Włączanie przy rozruchu | Wybierz, czy sprzęt karty sieciowej ma być włączany podczas uruchamiania maszyny wirtualnej. Wartość domyślna to **Włącz**. |

Aby usunąć interfejs sieciowy, zaznacz go i kliknij przycisk **Usuń**.
