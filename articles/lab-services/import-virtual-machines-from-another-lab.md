---
title: Importowanie maszyn wirtualnych z innego laboratorium w laboratorium devtest labs platformy Azure
description: W tym artykule opisano sposób importowania maszyn wirtualnych z innego laboratorium do bieżącego laboratorium w laboratorium usługi Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759520"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importowanie maszyn wirtualnych z innego laboratorium w laboratorium devtest labs platformy Azure
Ten artykuł zawiera informacje dotyczące sposobu importowania maszyn wirtualnych z innego laboratorium do laboratorium.

## <a name="scenarios"></a>Scenariusze
Oto kilka scenariuszy, w których należy zaimportować maszyny wirtualne z jednego laboratorium do innego laboratorium:

- Osoba w zespole przenosi się do innej grupy w przedsiębiorstwie i chce przenieść pulpit dewelopera do nowego zespołu DevTest Labs.
- Grupa osiągnęła [limit subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md) i chce podzielić zespoły na kilka subskrypcji
- Firma przenosi się do Express Route (lub innej nowej topologii sieci), a zespół chce przenieść maszyny wirtualne, aby korzystać z tej nowej infrastruktury

## <a name="solution-and-constraints"></a>Rozwiązanie i ograniczenia
Ta funkcja umożliwia importowanie maszyn wirtualnych w jednym laboratorium (źródle) do innego laboratorium (miejsca docelowego). Opcjonalnie można nadać nową nazwę docelowej maszyny Wirtualnej w procesie. Proces importowania obejmuje wszystkie zależności, takie jak dyski, harmonogramy, ustawienia sieciowe i tak dalej.

Proces zajmuje trochę czasu i ma wpływ na następujące czynniki:

- Liczba/rozmiar dysków, które są podłączone do komputera źródłowego (ponieważ jest to operacja kopiowania, a nie operacja przenoszenia)
- Odległość do miejsca docelowego (na przykład region Wschodnie stany USA do Azji Południowo-Wschodniej).

Po zakończeniu procesu źródło maszyny wirtualnej pozostaje zamknięciem, a nowa jest uruchomiona w laboratorium docelowym.

Istnieją dwa kluczowe ograniczenia, o których należy pamiętać podczas planowania importowania maszyn wirtualnych z jednego laboratorium do innego laboratorium:

- Obsługiwane są importowanie maszyn wirtualnych między subskrypcjami i między regionami, ale subskrypcje muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory.
- Maszyny wirtualne nie mogą być w stanie roszczenia w laboratorium źródłowym.
- Jesteś właścicielem maszyny Wirtualnej w laboratorium źródłowym i właścicielem laboratorium w laboratorium docelowym.
- Obecnie ta funkcja jest obsługiwana tylko za pośrednictwem interfejsu API programu Powershell i REST.

## <a name="use-powershell"></a>Korzystanie z programu PowerShell
Pobierz plik ImportVirtualMachines.ps1 z [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Skrypt służy do importowania pojedynczej maszyny Wirtualnej lub wszystkich maszyn wirtualnych w laboratorium źródłowym do laboratorium docelowego.

### <a name="use-powershell-to-import-a-single-vm"></a>Importowanie pojedynczej maszyny Wirtualnej za pomocą programu PowerShell
Wykonywanie tego skryptu programu PowerShell wymaga zidentyfikowania źródłowej maszyny Wirtualnej i laboratorium docelowego i opcjonalnie podawanie nowej nazwy używanej dla komputera docelowego:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Importowanie wszystkich maszyn wirtualnych w laboratorium źródłowym za pomocą programu PowerShell
Jeśli źródło maszyny wirtualnej nie jest określony, skrypt automatycznie importuje wszystkie maszyny wirtualne w DevTest Labs.  Przykład:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Importowanie maszyny wirtualnej za pomocą funkcji HTTP REST
Połączenie REST jest proste. Podajesz wystarczająco dużo informacji, aby zidentyfikować zasoby źródłowe i docelowe. Należy pamiętać, że operacja odbywa się w zasób laboratorium docelowego.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Ustawianie zasad dla laboratorium](devtest-lab-get-started-with-lab-policies.md)
- [Często zadawane pytania](devtest-lab-faq.md)
