---
title: Często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux na platformie Azure
description: Zawiera odpowiedzi na niektóre z typowych pytań dotyczących maszyn wirtualnych systemu Linux utworzonych za pomocą modelu Menedżera zasobów.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 3c6a5e011a536cc9c34565d4f72a9bee6c6a5254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945165"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych systemu Linux
Ten artykuł dotyczy niektórych typowych pytań dotyczących maszyn wirtualnych systemu Linux utworzonych na platformie Azure przy użyciu modelu wdrażania usługi Resource Manager. Aby zapoznać się z wersją systemu Windows w tym temacie, zobacz [Często zadawane pytania dotyczące maszyn wirtualnych systemu Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [System Linux w dystrybucjach zatwierdzonych przez platformę Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może mieć maksymalnie 32 767 GiB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Rozmiary maszyn wirtualnych).

Dyski zarządzane platformy Azure to zalecane oferty magazynu dysków do użytku z maszynami wirtualnymi platformy Azure do trwałego przechowywania danych. Z każdej maszyną wirtualną można używać wielu dysków zarządzanych. Dyski zarządzane oferują dwa typy opcji trwałego przechowywania: dyski zarządzane w wersji Premium i standardowe. Aby uzyskać informacje o cenach, zobacz [Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks).

Konta magazynu platformy Azure mogą również zapewnić magazyn dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak uzyskać dostęp do mojej maszyny wirtualnej?
Ustanawianie połączenia zdalnego w celu zalogowania się na maszynie wirtualnej przy użyciu bezpiecznej powłoki (SSH). Zobacz instrukcje dotyczące łączenia się [z systemem Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lub z systemów Linux i [Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli masz problemy, zapoznaj się [z rozwiązywaniem problemów z połączeniami Secure Shell (SSH).](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Czy mogę używać dysku tymczasowego (/dev/sdb1) do przechowywania danych?
Nie używaj dysku tymczasowego (/dev/sdb1) do przechowywania danych. Jest tam tylko do tymczasowego przechowywania. Istnieje ryzyko utraty danych, których nie można odzyskać.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy można skopiować lub sklonować istniejącą maszynę wirtualną platformy Azure?
Tak. Aby uzyskać instrukcje, zobacz [Jak utworzyć kopię maszyny wirtualnej systemu Linux w modelu wdrażania Menedżera zasobów](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę regionów Kanada Środkowa i Kanada Wschodnia za pośrednictwem usługi Azure Resource Manager?
Dwa nowe regiony Kanada Central i Kanada Wschód nie są automatycznie rejestrowane do tworzenia maszyn wirtualnych dla istniejących subskrypcji platformy Azure. Ta rejestracja jest wykonywana automatycznie, gdy maszyna wirtualna jest wdrażana za pośrednictwem witryny Azure portal do dowolnego innego regionu przy użyciu usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej w dowolnym innym regionie platformy Azure nowe regiony powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Czy mogę dodać kartę sieciową do maszyny Wirtualnej po jej utworzeniu?
Tak, jest to teraz możliwe. Maszyna wirtualna najpierw musi zostać zatrzymana cofnięto alokację. Następnie można dodać lub usunąć kartę sieciową (chyba że jest to ostatnia karta sieciowa na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy są jakieś wymagania dotyczące nazwy komputera?
Tak. Nazwa komputera może mieć maksymalnie 64 znaki. Zobacz [Reguły i ograniczenia konwencji nazewnictwa, aby](/azure/architecture/best-practices/resource-naming) uzyskać więcej informacji na temat nazywania zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy są jakieś wymagania dotyczące nazwy grupy zasobów?
Tak. Nazwa grupy zasobów może mieć maksymalnie 90 znaków. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Reguły i ograniczenia konwencji nazewnictwa.](/azure/architecture/best-practices/resource-naming)

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników powinny mieć długość od 1 do 32 znaków.

Następujące nazwy użytkowników nie są dozwolone:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny Wirtualnej?

Istnieją różne wymagania dotyczące długości hasła, w zależności od używanego narzędzia:
 - Portal - od 12 do 72 znaków
 - PowerShell - od 8 do 123 znaków
 - CLI - między 12 - 123
 

Hasła muszą również spełniać 3 z następujących 4 wymagań dotyczących złożoności:

* Musi zawierać małe litery
* Musi zawierać wielkie litery
* Musi zawierać cyfrę
* Musi zawierać znak specjalny (zgodność wyrażenia regularnego [\W_])

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
        <td style="text-align:center">Hasło1</td>
        <td style="text-align:center">Hasło22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>