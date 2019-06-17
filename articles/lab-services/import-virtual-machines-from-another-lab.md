---
title: Importowanie maszyn wirtualnych z innego laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak importować maszyny wirtualne z innego laboratorium do laboratorium bieżącego.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61322879"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importowanie maszyn wirtualnych z innego laboratorium w usłudze Azure DevTest Labs
Ten artykuł zawiera informacje o tym, jak importować maszyny wirtualne z innego laboratorium do laboratorium.

## <a name="scenarios"></a>Scenariusze
Poniżej przedstawiono kilka scenariuszy, w których należy zaimportować maszyn wirtualnych z jednym laboratorium do laboratorium innego:

- Osoba w zespole jest przenoszona do innej grupy w obrębie przedsiębiorstwa i chce wykonać pulpitu dla deweloperów, aby nowy zespół DevTest Labs.
- Grupa osiągnie [limitu przydziału na poziomie subskrypcji](../azure-subscription-service-limits.md) i chce podzielić zespołów do kilka subskrypcji
- Firma jest przenoszona do Express Route (lub niektórych innych nową topologię sieci), a zespół chce przenieść maszyny wirtualne, aby korzystała z tej nowej infrastruktury

## <a name="solution-and-constraints"></a>Rozwiązania i ograniczenia
Ta funkcja umożliwia importowanie maszyn wirtualnych w jednym laboratorium (źródła) do innego laboratorium (docelowy). Opcjonalnie można nadać nową nazwę dla docelowej maszyny Wirtualnej w procesie. Proces importowania obejmuje wszystkie zależności, takich jak dyski, harmonogramy, ustawienia sieciowe i tak dalej.

Proces zająć sporo czasu i wpływ następujące czynniki:

- Liczba/rozmiar dysków, które są dołączone do maszyny źródłowej (ponieważ jest to operacja kopiowania, a nie operacji przenoszenia)
- Odległość do miejsca docelowego (na przykład region wschodnie stany USA do Azji Południowo-Wschodnia).

Po zakończeniu procesu źródłowa maszyna wirtualna pozostaje zamknięcia, a nowe jednego działania aplikacji w środowisku laboratoryjnym docelowego.

Istnieją dwa ograniczenia klucza pod uwagę podczas planowania do importowania maszyn wirtualnych z jednym laboratorium w do innego laboratorium:

- Importy maszyny wirtualnej między subskrypcjami i regionach są obsługiwane, ale subskrypcji musi być skojarzone z tą samą dzierżawą usługi Azure Active Directory.
- Maszyny wirtualne nie może być w stanie przejęcia laboratorium źródła.
- Jesteś właścicielem maszyny wirtualnej w laboratorium źródła i właściciel laboratorium w laboratorium docelowego.
- Obecnie ta funkcja jest obsługiwana wyłącznie za pośrednictwem programu Powershell i interfejsu API REST.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell
Pobierz plik ImportVirtualMachines.ps1 z [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Skrypt służy do importowania jednej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w laboratorium źródła do docelowego laboratorium.

### <a name="use-powershell-to-import-a-single-vm"></a>Aby zaimportować pojedynczej maszyny Wirtualnej przy użyciu programu PowerShell
Wykonującego ten skrypt programu powershell wymaga zidentyfikowanie źródłowej maszyny Wirtualnej i laboratorium z docelowej i opcjonalnie podanie nowej nazwy do użycia dla docelowej maszyny:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Aby zaimportować wszystkie maszyny wirtualne w laboratorium źródła przy użyciu programu PowerShell
Jeśli źródłowa maszyna wirtualna nie jest określona, skrypt importuje automatycznie wszystkich maszyn wirtualnych w usłudze DevTest Labs.  Na przykład:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Aby zaimportować Maszynę wirtualną za pomocą interfejsu REST protokołu HTTP
Wywołania REST jest proste. Musisz udzielić wystarczających informacji do identyfikowania zasoby źródłowe i docelowe. Należy pamiętać, że operacji odbywa się na zasobie laboratorium docelowego.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [Ustawianie zasad laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
