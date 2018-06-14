---
title: Często zadawane pytania dotyczące maszyn wirtualnych systemu Linux na platformie Azure | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące maszyn wirtualnych systemu Linux utworzone za pomocą modelu usługi Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: 8a4d93ff12affac56c12c0eab85168c609400ee2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30242442"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych systemu Linux
W tym artykule opisano często zadawane pytania dotyczące maszyn wirtualnych systemu Linux utworzone na platformie Azure przy użyciu modelu wdrażania Menedżera zasobów. Dla wersji systemu Windows w tym temacie, zobacz [często zadawane pytania dotyczące maszyn wirtualnych systemu Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [systemu Linux na Azure-Endorsed dystrybucji](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może być do 4 TB (4,095 GB). Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych).

Dyskach zarządzanych platformy Azure są zalecane dysku magazynu oferty do użytku z maszyn wirtualnych platformy Azure dla trwałego magazynu danych. W każdej maszynie wirtualnej możesz używać wielu dysków Managed Disks. Usługa Managed Disks oferuje dwa typy opcji magazynu trwałego: dyski Managed Disks w warstwie Premium i Standardowa. Aby uzyskać informacje o cenach, zobacz [zarządzane cennik dysków](https://azure.microsoft.com/pricing/details/managed-disks).

Konta magazynu Azure oferuje również magazynu dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak można uzyskać dostęp Moja maszyna wirtualna?
Ustanowić połączenia zdalnego do logowania się do maszyny wirtualnej przy użyciu protokołu Secure Shell (SSH). Zobacz instrukcje dotyczące nawiązywania [z systemu Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [z systemami Linux i komputerów Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli masz problemy, zapoznaj się [Rozwiązywanie problemów z Secure Shell (SSH) połączenia](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Dysku tymczasowym (/ dev/sdb1) może być używany do przechowywania danych?
Nie należy używać do przechowywania danych tymczasowych dysku (/ dev/sdb1). Jest dostępne tylko do tymczasowego przechowywania. Istnieje ryzyko utraty danych, która nie może zostać odzyskany.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Można skopiować lub sklonować istniejącą maszynę Wirtualną platformy Azure?
Tak. Aby uzyskać instrukcje, zobacz [jak utworzyć kopię maszyny wirtualnej systemu Linux w modelu wdrażania usługi Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę centralnej Zjednoczone i Kanada Wschodnia regionów za pośrednictwem usługi Azure Resource Manager?
Dwóch regionach nowej centralnej Zjednoczone i Kanada Wschodnia nie są automatycznie zarejestrowany dla tworzenia maszyny wirtualnej dla istniejącej subskrypcji platformy Azure. Rejestracja odbywa się automatycznie podczas wdrażania maszyny wirtualnej za pośrednictwem portalu Azure w innym regionie przy użyciu usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej do innego regionu Azure nowych regionów powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Można dodać karty Sieciowej do mojego maszyny Wirtualnej po jej utworzeniu?
Tak, obecnie jest to możliwe. Maszyna wirtualna musi zostać zatrzymana deallocated. Następnie można dodać lub usunąć karty Sieciowej (o ile nie jest ostatni karty Sieciowej na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją wszystkie wymagania dotyczące nazwy komputera?
Tak. Nazwa komputera może zawierać maksymalnie z 64 znaków. Zobacz [nazewnictwa konwencje reguły i ograniczenia](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) uzyskać więcej informacji dotyczących nazw zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją dowolnego zasobu wymagania dotyczące nazw grupy?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków długości. Zobacz [nazewnictwa konwencje reguły i ograniczenia](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Aby uzyskać więcej informacji na temat grup zasobów.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników należy 1-32 znaków.

Następujące nazwy użytkowników nie są dozwolone:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> administrator </td><td style="text-align:center"> użytkownik </td><td style="text-align:center"> Użytkownik1</td>
    </tr>
    <tr>
        <td style="text-align:center">testowanie </td><td style="text-align:center"> UŻYTKOWNIK2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> UŻYTKOWNIK3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">kopia zapasowa </td><td style="text-align:center"> Konsoli </td><td style="text-align:center"> ADAM </td><td style="text-align:center"> Gość</td>
    </tr>
    <tr>
        <td style="text-align:center">Jan </td><td style="text-align:center"> właściciel </td><td style="text-align:center"> główny </td><td style="text-align:center"> serwer</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> pomoc techniczna </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny Wirtualnej?
Hasła muszą być 6-72 znaków i spełnić 3 z następujących wymagań złożoności 4:

* Mieć niższe znaków
* Ma górny znaków
* Zawierać cyfrę
* Ma znak specjalny (wyrażenie regularne zgodne [\W_])

Następujące hasła nie są dozwolone:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Hasło!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
