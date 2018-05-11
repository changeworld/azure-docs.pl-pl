---
title: Często zadawane pytania dotyczące maszyn wirtualnych systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące systemu Windows maszyny wirtualne utworzone za pomocą modelu usługi Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: c60b7df4d766ddf321ce2c807994f3b83046f1ef
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych systemu Windows
W tym artykule opisano często zadawane pytania dotyczące maszyn wirtualnych systemu Windows utworzonych na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów. W tym temacie w wersji systemu Linux [— często zadawane pytania dotyczące maszyn wirtualnych systemu Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Informacje dotyczące zasad pomocy technicznej dla uruchomione oprogramowanie serwera firmy Microsoft na platformie Azure, zobacz [obsługi oprogramowania Microsoft server maszyn wirtualnych platformy Azure](https://support.microsoft.com/kb/2721672)

Niektóre wersje systemu Windows 7, Windows 8.1 i Windows 10 są dostępne dla subskrybentów korzyści MSDN Azure i subskrybentów MSDN: Programowanie i testowanie płatność za rzeczywiste użycie, dla zadań tworzenia i testowania. Aby uzyskać szczegółowe informacje, łącznie z instrukcjami i ograniczeniami, zobacz [Windows Client images for MSDN subscribers](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) (Obrazy klienta systemu Windows dla subskrybentów MSDN). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może być do 4 TB (4,095 GB). Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Rozmiary maszyn wirtualnych).

Dyskach zarządzanych platformy Azure są zalecane dysku magazynu oferty do użytku z maszyn wirtualnych platformy Azure dla trwałego magazynu danych. W każdej maszynie wirtualnej możesz używać wielu dysków Managed Disks. Usługa Managed Disks oferuje dwa typy opcji magazynu trwałego: dyski Managed Disks w warstwie Premium i Standardowa. Aby uzyskać informacje o cenach, zobacz [zarządzane cennik dysków](https://azure.microsoft.com/pricing/details/managed-disks).

Konta magazynu Azure oferuje również magazynu dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak można uzyskać dostęp Moja maszyna wirtualna?
Ustanowić połączenia zdalnego przy użyciu usługi Podłączanie pulpitu zdalnego (RDP) dla maszyny Wirtualnej systemu Windows. Aby uzyskać instrukcje, zobacz [jak połączenia i zaloguj się do maszyny wirtualnej platformy Azure systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Obsługuje maksymalnie dwóch jednoczesnych połączeń, chyba że serwer jest skonfigurowany jako host sesji usług pulpitu zdalnego.  

Jeśli masz problemy z pulpitu zdalnego, zobacz [połączeń Rozwiązywanie problemów z pulpitu zdalnego do systemu Windows Azure maszyny wirtualnej](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Jeśli znasz funkcję Hyper-V, możesz poszukać narzędzia podobnego do VMConnect. Platforma Azure nie oferuje podobnego narzędzia, ponieważ dostęp do konsoli maszyny wirtualnej nie jest obsługiwany.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Dysk tymczasowy (dysk D: domyślnie) może być używany do przechowywania danych?
Nie używaj tymczasowych dysku do przechowywania danych. Jest tylko magazyn tymczasowy, czy istnieje ryzyko utraty danych, która nie może zostać odzyskany. Może wystąpić utrata danych, gdy maszyna wirtualna zostanie przeniesiona do innego hosta. Zmiana rozmiaru maszyny wirtualnej, aktualizowanie hosta lub awaria sprzętu na hoście to kilka przyczyn, które mogą spowodować przeniesienie maszyny wirtualnej.

Jeśli masz aplikację, która powinna być używana D: literę dysku, aby używał dysku tymczasowym, inną niż D: można ponownie przypisać litery dysku. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak mogę zmienić literę dysku tymczasowego?
Można zmienić literę dysku, przenosząc plik stronicowania i ponowne przypisywanie litery dysku, ale należy upewnić się, że wykonaj kroki opisane w określonej kolejności. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Można dodać istniejącej maszyny Wirtualnej do zestawu dostępności?
Nie. Jeśli ma być częścią zestawu dostępności maszyny Wirtualnej, należy utworzyć maszynę Wirtualną w zestawie. Obecnie nie ma sposób można dodać maszyny Wirtualnej do dostępności po jego utworzeniu.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Na platformie Azure można przekazać maszyny wirtualnej?
Tak. Aby uzyskać instrukcje, zobacz [Migrowanie lokalnych maszyn wirtualnych Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Czy można zmienić rozmiar dysku systemu operacyjnego?
Tak. Aby uzyskać instrukcje, zobacz [sposobu rozszerzania dysku systemu operacyjnego maszyny wirtualnej w grupie zasobów Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Można skopiować lub sklonować istniejącą maszynę Wirtualną platformy Azure?
Tak. Przy użyciu zarządzanych obrazów, można utworzyć obraz maszyny wirtualnej, a następnie użyć obrazu do kompilacji z wielu nowych maszyn wirtualnych. Aby uzyskać instrukcje, zobacz [utworzyć niestandardowy obraz maszyny wirtualnej](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę centralnej Zjednoczone i Kanada Wschodnia regionów za pośrednictwem usługi Azure Resource Manager?

Dwóch regionach nowej centralnej Zjednoczone i Kanada Wschodnia nie są automatycznie zarejestrowany dla tworzenia maszyny wirtualnej dla istniejącej subskrypcji platformy Azure. Rejestracja odbywa się automatycznie podczas wdrażania maszyny wirtualnej za pośrednictwem portalu Azure w innym regionie przy użyciu usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej do innego regionu Azure nowych regionów powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="does-azure-support-linux-vms"></a>Azure obsługuje maszyn wirtualnych systemu Linux?
Tak. Aby szybko utworzyć Maszynę wirtualną systemu Linux wypróbowanie, zobacz [Utwórz Maszynę wirtualną systemu Linux na platformie Azure przy użyciu portalu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Można dodać karty Sieciowej do mojego maszyny Wirtualnej po jej utworzeniu?
Tak, obecnie jest to możliwe. Maszyna wirtualna musi zostać zatrzymana deallocated. Następnie można dodać lub usunąć karty Sieciowej (o ile nie jest ostatni karty Sieciowej na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją wszystkie wymagania dotyczące nazwy komputera?
Tak. Nazwa komputera może zawierać maksymalnie 15 znaków. Zobacz [nazewnictwa konwencje reguły i ograniczenia](/azure/architecture/best-practices/naming-conventions#compute) uzyskać więcej informacji dotyczących nazw zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją dowolnego zasobu wymagania dotyczące nazw grupy?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków długości. Zobacz [nazewnictwa konwencje reguły i ograniczenia](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) Aby uzyskać więcej informacji na temat grup zasobów.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników nie może przekraczać 20 znaków długości i nie może kończyć się kropką ("."). 


Następujące nazwy użytkowników nie są dozwolone:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">usługi adm</td><td style="text-align:center">Administrator</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">administrator</td><td style="text-align:center">aspnet</td><td style="text-align:center">kopia zapasowa</td><td style="text-align:center">console</td>
    </tr>
    <tr>
        <td style="text-align:center">ADAM </td><td style="text-align:center">Gość</td><td style="text-align:center">Jan</td><td style="text-align:center">właściciel</td>
    </tr>
    <tr>
        <td style="text-align:center">główny</td><td style="text-align:center">serwer</td><td style="text-align:center">sql</td><td style="text-align:center">pomoc techniczna</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2</td><td style="text-align:center">test3</td><td style="text-align:center">Użytkownik</td><td style="text-align:center">Użytkownik1</td>
    </tr>
    <tr>
        <td style="text-align:center">UŻYTKOWNIK2</td><td style="text-align:center">UŻYTKOWNIK3</td><td style="text-align:center">user4</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny Wirtualnej?
Hasła muszą być 12 123 znaków długości i spełnić 3 z następujących wymagań złożoności 4:

* Mieć niższe znaków
* Ma górny znaków
* Zawierać cyfrę
* Ma znak specjalny (wyrażenie regularne zgodne [\W_])

Następujące hasła nie są dozwolone:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Hasło!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
