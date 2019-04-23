---
title: Importowanie maszyn wirtualnych z innego laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak importować maszyny wirtualne z innego laboratorium do bieżącego laboratorium w usłudze Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: cb4a3ec9be82957b4c0366ec232f1147c52d0251
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148776"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importowanie maszyn wirtualnych z innego laboratorium w usłudze Azure DevTest Labs
Usługa Azure DevTest Labs znacznie ułatwia zarządzanie maszyn wirtualnych (VM) do tworzenia i testowania działania. Pozwala na przenoszenie maszyny Wirtualnej z jednym laboratorium do innego zespołu lub zmienią się wymagania dotyczące infrastruktury. Poniżej przedstawiono kilka typowych scenariuszy, w których konieczne może być w tym celu:

- Osoby w zespole przenosi do innej grupy w obrębie przedsiębiorstwa i chce wykonać tworzenia maszyn wirtualnych do nowego zespołu laboratorium.
- Grupa osiągnął przydział poziomu subskrypcji i chce podzielić zespołów do wielu subskrypcji.
- Firma planuje przenieść do Express Route (lub niektórych innych nową topologię sieci), a zespół chce przenieść maszyny wirtualne, aby korzystała z tej nowej infrastruktury.

## <a name="solution-and-constraints"></a>Rozwiązania i ograniczenia
Usługa Azure DevTest Labs umożliwia właściciel laboratorium do importowania maszyn wirtualnych w laboratorium źródła do docelowego laboratorium. Właściciel laboratorium Opcjonalnie można podać nową nazwę dla docelowej maszyny Wirtualnej w procesie. Proces importowania obejmuje wszystkie zależności, takich jak dyski, harmonogramy, ustawienia sieciowe itp. Proces zająć sporo czasu i wpływ numer/rozmiaru dysków dołączonych do maszyny źródłowej i odległość do miejsca docelowego (na przykład region wschodnie stany USA do regionu Azja południowo-wschodnia). Po zakończeniu procesu importowania źródłowa maszyna wirtualna pozostaje zamknięcia, a nowe jednego działania aplikacji w środowisku laboratoryjnym docelowego.

Istnieją dwa ograniczenia klucza pod uwagę podczas planowania do importowania maszyn wirtualnych do innego laboratorium:

- Importowanie maszyn wirtualnych między subskrypcjami i na regiony są obsługiwane, ale subskrypcji musi być skojarzone z tą samą dzierżawą usługi Azure Active Directory.
- Maszyny wirtualne nie może być w stanie przejęcia laboratorium źródła.

Ponadto aby można było zaimportować Maszynę wirtualną z jednym laboratorium, musisz być właścicielem maszyny Wirtualnej w laboratorium źródła i właściciel laboratorium w laboratorium docelowego.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Kroki, aby zaimportować Maszynę wirtualną z innej laboratorium
Obecnie można zaimportować Maszynę wirtualną z jednym laboratorium do innego tylko przy użyciu programu Azure PowerShell i interfejsu API REST.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell
Pobieranie ImportVirtualMachines.ps1 z plik skryptu programu PowerShell [Azure DevTest Lab repozytorium](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) na dysk lokalny.

#### <a name="import-a-single-vm"></a>Importowanie pojedynczej maszyny Wirtualnej
Uruchom skrypt ImportVirtualMachines.ps1 importować pojedynczej maszyny Wirtualnej z laboratorium źródła do docelowego laboratorium. Można określić nową nazwę dla maszyny Wirtualnej, która zostanie skopiowana za pomocą DestinationVirtualMachineName paramer.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importowanie wszystkich maszyn wirtualnych
Po uruchomieniu skryptu ImportVirtualMachines.ps1, jeśli nie określisz maszyny Wirtualnej w laboratorium źródła, skrypt importuje wszystkie maszyny wirtualne w laboratorium źródła do laboratorium docelowego.

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Korzystanie z interfejsu API REST
Wywołania interfejsu API REST względem laboratorium/docelowej i przekaż w laboratorium źródła, subskrypcji i informacji o maszyny Wirtualnej jako parametry, jak pokazano w poniższym przykładzie:

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat zmieniania rozmiaru maszyny Wirtualnej z systemem, zobacz [zmienić rozmiar maszyny Wirtualnej](devtest-lab-resize-vm.md).
- Aby dowiedzieć się, jak ponowne wdrożenie maszyny Wirtualnej, zobacz [ponowne wdrożenie maszyny Wirtualnej](devtest-lab-redeploy-vm.md).
