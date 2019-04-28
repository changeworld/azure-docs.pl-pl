---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9158e6bfe07fc5d06b0685d77eff26644b594a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485278"
---
Rozszerzenia maszyn wirtualnych umożliwiają:

* Modyfikowanie funkcji zabezpieczeń i tożsamości, takich jak resetowanie wartości konta i korzystanie z oprogramowania chroniącego przed złośliwym kodem
* Uruchamianie, zatrzymywanie lub konfigurowanie monitorowania i diagnostyki
* Resetowanie lub instalowanie funkcji łączności, takich jak protokoły RDP i SSH
* Diagnozowanie i monitorowanie maszyn wirtualnych oraz zarządzanie nimi

Istnieje również wiele innych funkcji. Nowe funkcje rozszerzenia maszyny wirtualnej są regularnie wydawane. W tym artykule opisano agentów maszyn wirtualnych platformy Azure dla systemu Windows i Linux oraz sposób obsługi przez nich funkcji rozszerzenia maszyny wirtualnej. Aby uzyskać listę rozszerzeń maszyn wirtualnych według kategorii funkcji, zobacz [Azure VM Extensions and Features](../articles/virtual-machines/extensions/features-windows.md) (Rozszerzenia i funkcje maszyn wirtualnych platformy Azure).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agenci maszyn wirtualnych platformy Azure dla systemu Windows i Linux
Agent usługi Azure Virtual Machines (agent maszyny wirtualnej) to bezpieczny i lekki proces służący do instalowania, konfigurowania i usuwania rozszerzeń maszyn wirtualnych w wystąpieniach usługi Azure Virtual Machines. Agent maszyny wirtualnej działa jako bezpieczna lokalna usługa sterowania na potrzeby maszyny wirtualnej platformy Azure. Rozszerzenia ładowane przez agenta zapewniają określone funkcje służące do zwiększenia produktywności przy użyciu wystąpienia.

Istnieją dwaj agenci maszyn wirtualnych platformy Azure — jeden dla maszyn wirtualnych z systemem Windows i jeden dla maszyn wirtualnych z systemem Linux.

Aby wystąpienie maszyny wirtualnej używało co najmniej jednego rozszerzenia maszyny wirtualnej, w ramach wystąpienia musi być zainstalowany agent maszyny wirtualnej. Obraz maszyny wirtualnej utworzony za pomocą witryny Azure Portal i obraz z portalu **Marketplace** automatycznie instalują agenta maszyny wirtualnej podczas procesu tworzenia. Jeśli wystąpienie maszyny wirtualnej nie ma agenta maszyny wirtualnej, można zainstalować go po utworzeniu wystąpienia maszyny wirtualnej. Innym rozwiązaniem jest zainstalowanie agenta w ramach niestandardowego obrazu maszyny wirtualnej, który następnie zostanie przekazany.

> [!IMPORTANT]
> Agenci maszyn wirtualnych to bardzo lekkie usługi umożliwiające bezpieczne administrowanie wystąpieniami maszyn wirtualnych. Możliwe są przypadki, w których użycie agenta maszyny wirtualnej nie jest potrzebne. W takiej sytuacji należy utworzyć maszyny wirtualne bez zainstalowanego agenta maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Mimo że agent maszyny wirtualnej może zostać usunięty fizycznie, zachowanie rozszerzeń maszyn wirtualnych w ramach wystąpienia jest niezdefiniowane. W związku z tym usunięcie zainstalowanego agenta maszyny wirtualnej nie jest obsługiwane.
>

Agent maszyny wirtualnej jest włączony w następujących sytuacjach:

* Po utworzeniu wystąpienia maszyny wirtualnej za pomocą witryny Azure Portal i wybraniu obrazu z portalu **Marketplace**.
* Po utworzeniu wystąpienia maszyny wirtualnej za pomocą polecenia cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) lub [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Maszynę wirtualną można utworzyć bez agenta maszyny wirtualnej, dodając parametr **–DisableGuestAgent** do polecenia cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

* Po ręcznym pobraniu i zainstalowaniu agenta maszyny wirtualnej w ramach istniejącego wystąpienia maszyny wirtualnej i ustawienia parametru **ProvisionGuestAgent** na wartość **true**. Tej metody można użyć w przypadku agentów systemu Windows i Linux za pomocą polecenia programu PowerShell lub wywołania REST. Jeśli wartość parametru **ProvisionGuestAgent** nie zostanie ustawiona po ręcznej instalacji agenta maszyny wirtualnej, dodanie agenta maszyny wirtualnej nie zostanie poprawnie wykryte. Poniższy przykład kodu przedstawia sposób wykonania tej czynności przy użyciu programu PowerShell, gdzie argumenty `$svc` i `$name` zostały już określone:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Po utworzeniu obrazu maszyny wirtualnej zawierającego zainstalowanego agenta maszyny wirtualnej. Gdy obraz z agentem maszyny wirtualnej już istnieje, możesz przekazać go na platformę Azure. W przypadku maszyny wirtualnej z systemem Windows pobierz [plik MSI agenta maszyny wirtualnej z systemem](https://go.microsoft.com/fwlink/?LinkID=394789), a następnie zainstaluj agenta maszyny wirtualnej. Dla maszyny Wirtualnej systemu Linux, zainstaluj agenta maszyny Wirtualnej z repozytorium GitHub, znajdujący się w <https://github.com/Azure/WALinuxAgent>. Aby uzyskać informacje na temat instalowania agenta maszyny wirtualnej w systemie Linux, zobacz [Azure Linux VM Agent User Guide](../articles/virtual-machines/extensions/agent-linux.md) (Podręcznik użytkownika agenta maszyny wirtualnej z systemem Linux na platformie Azure).

> [!NOTE]
> W usługach PaaS agent maszyny wirtualnej jest nazywany **WindowsAzureGuestAgent** i jest zawsze dostępny w sieci Web i w ramach maszyn wirtualnych roli procesu roboczego. Aby uzyskać więcej informacji, zobacz [Azure Role Architecture](https://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) (Architektura ról platformy Azure). Agent maszyny wirtualnej dla maszyn wirtualnych roli może teraz dodawać rozszerzenia do maszyn wirtualnych usługi w chmurze w taki sam sposób, jak w przypadku trwałych maszyn wirtualnych. Największa różnica między rozszerzeniami maszyn wirtualnych w przypadku maszyn wirtualnych roli i trwałych maszyn wirtualnych ma miejsce podczas dodawania rozszerzeń maszyn wirtualnych. W przypadku maszyn wirtualnych roli rozszerzenia są najpierw dodawane do usługi w chmurze, a następnie do wdrożeń w ramach tej usługi w chmurze.
>
> Użyj polecenia cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx), aby wyświetlić listę wszystkich dostępnych rozszerzeń maszyn wirtualnych roli.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Znajdowanie, dodawanie, aktualizowanie i usuwanie rozszerzeń maszyn wirtualnych
Aby uzyskać szczegółowe informacje na temat tych zadań, zobacz [Add, Find, Update, and Remove Azure VM Extensions](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Dodawanie, znajdowanie, aktualizowanie i usuwanie rozszerzeń maszyn wirtualnych platformy Azure).
