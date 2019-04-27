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
ms.openlocfilehash: 8d421adfae335a976485ed463a69484a74be5b44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729532"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych systemu Linux
W tym artykule opisano często zadawane pytania dotyczące maszyn wirtualnych systemu Linux utworzone na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager. Wersja Windows części tego tematu – zobacz [— często zadawane pytania dotyczące maszyn wirtualnych Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [systemu Linux w Dystrybucjach Azure-Endorsed](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może być do 4 TB (4095 GB). Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych).

Usługa Azure Managed Disks są zalecane oferta magazynu na dysku do użycia z usługą Azure Virtual Machines do trwałego magazynowania danych. W każdej maszynie wirtualnej możesz używać wielu funkcji Dyski zarządzane. Zarządzane dyski oferty dwa typy opcji magazynu trwałego: Dyski zarządzane — wersja Premium i standardowa. Aby uzyskać informacje o cenach, zobacz [cennika usługi Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

Konta usługi Azure storage oferuje również magazynu dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak można uzyskać dostęp Moja maszyna wirtualna?
Nawiąż połączenie zdalne logowanie się do maszyny wirtualnej przy użyciu protokołu Secure Shell (SSH). Zobacz instrukcje dotyczące łączenia [z Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub [z systemami Linux i Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli występują problemy, zapoznaj się z [Rozwiązywanie problemów z Secure Shell (SSH) połączenia](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Czy można używać dysku tymczasowego (/ dev/sdb1), do przechowywania danych?
Nie należy używać dysku tymczasowego (/ dev/sdb1) do przechowywania danych. Jest tylko określony jako tymczasowego magazynu. Istnieje ryzyko utraty danych, których nie można go odzyskać.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy mogę skopiować lub klonowanie istniejących maszyn wirtualnych platformy Azure?
Tak. Aby uzyskać instrukcje, zobacz [sposób tworzenia kopii maszyny wirtualnej systemu Linux w modelu wdrażania usługi Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę Kanada Środkowa i Kanada Wschodnia za pośrednictwem usługi Azure Resource Manager?
Dwa nowe regiony, Kanada Środkowa i Kanada Wschodnia, nie są automatycznie zarejestrowani do tworzenia maszyny wirtualnej dla istniejących subskrypcji platformy Azure. Rejestracja odbywa się automatycznie po wdrożeniu maszyny wirtualnej w witrynie Azure portal do dowolnego innego regionu za pomocą usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej do innego regionu platformy Azure, nowe regiony powinna być dostępna dla kolejnych maszyn wirtualnych.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Można dodać kartę Sieciową z maszyną Wirtualną po jej utworzeniu?
Tak, teraz jest to możliwe. Maszyna wirtualna najpierw musi zostać zatrzymane przydział zostanie cofnięty. Następnie można dodać lub usunąć karty Sieciowej (chyba że jest to ostatnia karty Sieciowej na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją jakiekolwiek wymagania nazwa komputera?
Tak. Nazwa komputera może zawierać maksymalnie 64 znaków. Zobacz [ograniczenia i reguły konwencji nazewnictwa](/azure/architecture/best-practices/naming-conventions) Aby uzyskać więcej informacji dotyczących nazewnictwa zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją dowolnego zasobu wymagania dotyczące nazw grupy?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków długości. Zobacz [ograniczenia i reguły konwencji nazewnictwa](/azure/architecture/best-practices/naming-conventions) Aby uzyskać więcej informacji na temat grup zasobów.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników powinien być 1 do 32 znaków.

Następujące nazwy użytkowników nie są dozwolone:

<table>
    <tr>
        <td style="text-align:center">administrator </td><td style="text-align:center"> admin </td><td style="text-align:center"> Użytkownik </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> UŻYTKOWNIK3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">kopia zapasowa </td><td style="text-align:center"> console </td><td style="text-align:center"> David </td><td style="text-align:center"> Gość</td>
    </tr>
    <tr>
        <td style="text-align:center">Jan </td><td style="text-align:center"> właściciel </td><td style="text-align:center"> root </td><td style="text-align:center"> serwer</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> pomoc techniczna </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła, podczas tworzenia maszyny Wirtualnej?
Hasło musi mieć 6 do 72 znaków i spełniać 3 z następujących wymagań dotyczących złożoności 4:

* Niższe znaków
* Górny znaków
* Zawierać cyfrę
* Masz znaków specjalnych (wyrażenie regularne dopasowuje [\W_])

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
