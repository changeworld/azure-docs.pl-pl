---
title: Omówienie identyfikatorów wystąpień dla maszyn wirtualnych zestawu skalowania maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Świadomość, że maszyn wirtualnych w zestawie identyfikatorów wystąpień dla skalowania maszyn wirtualnych platformy Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 6aeba722a0661979664f8d61efdb9b2bf47ad801
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618505"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Świadomość, że maszyn wirtualnych w zestawie identyfikatorów wystąpień dla skalowania maszyn wirtualnych platformy Azure
W tym artykule opisano identyfikatory wystąpień zestawów skalowania i różne sposoby ich powierzchni.

## <a name="scale-set-instance-ids"></a>Identyfikatory wystąpień zestawu skalowania

Każda maszyna wirtualna w zestawie skalowania otrzymuje identyfikator wystąpienia, która jednoznacznie identyfikuje go. To wystąpienie, którego identyfikator jest używany w zestawie skalowania interfejsów API, aby wykonywać operacje na określonym zestawie maszyn wirtualnych w skali. Na przykład można określić identyfikator określonego wystąpienia, aby odtworzyć z obrazu, korzystając z interfejsu API odtworzenia z obrazu:

Interfejs API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Program PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Aby uzyskać więcej informacji, zobacz [dokumentacji programu Powershell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

Interfejs wiersza polecenia: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Lista identyfikatorów wystąpień można uzyskać, wyświetlając listę wszystkich wystąpień w zestawie skalowania:

Interfejs API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Program PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentacji programu Powershell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

Interfejs wiersza polecenia: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)).

Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawami SDK Azure](https://azure.microsoft.com/downloads/) do listy maszyn wirtualnych w zestawie skalowania.

Dokładny opis danych wyjściowych zależy od opcji dostarczania do polecenia, ale poniżej przedstawiono niektóre przykładowe dane wyjściowe z interfejsu wiersza polecenia:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Jak widać, właściwość "instanceId" jest liczba dziesiętna. Wystąpienia identyfikatorów może zostać ponownie użyte dla nowych wystąpień, usunięcie starych wystąpień.

>[!NOTE]
> Brak **żadnej gwarancji** wystąpieniu sposób identyfikatory są przypisywane do maszyn wirtualnych w zestawie skalowania. One może się wydawać sekwencyjnie zwiększenie w czasie, ale nie zawsze jest to wymagane. Nie mają zależności w określony sposób, w którym identyfikatorów wystąpień są przypisane do maszyn wirtualnych.

## <a name="scale-set-vm-names"></a>Nazwy maszyn wirtualnych zestawu skalowania

W przykładowych danych wyjściowych powyżej jest również "name" dla maszyny Wirtualnej. Ta nazwa ma postać "{skali — Nazwa zestawu} _ {identyfikator wystąpienia}". Ta nazwa jest wyświetlana w witrynie Azure portal po wyświetleniu listy wystąpień w zestawie skalowania:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Identyfikator wystąpienia} część nazwy jest ten sam numer dziesiętnej jako właściwość "instanceId" omówionych wcześniej.

## <a name="instance-metadata-vm-name"></a>Nazwa metadanych maszyny Wirtualnej wystąpienia

Po wykonaniu zapytania [wystąpienie metadanych](../virtual-machines/windows/instance-metadata-service.md) z w zestawie skalowania maszyn wirtualnych, zobaczysz "name" w danych wyjściowych:

```
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ta nazwa jest taka sama jak nazwa omówionych wcześniej.

## <a name="scale-set-vm-computer-name"></a>Nazwa komputera maszyny Wirtualnej zestawu skalowania

Każda maszyna wirtualna w zestawie również skalowania otrzymuje nazwę komputera do niej przypisany. Ta nazwa komputera jest nazwą hosta maszyny Wirtualnej w [dostarczone do usługi Azure DNS nazwy rozwiązania w ramach sieci wirtualnej](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Ta nazwa komputera ma postać "{computer-name-prefix}{base-36-instance-id}".

{Base-36-wystąpienie id} jest [podstawowa 36](https://en.wikipedia.org/wiki/Base36) i jest zawsze sześć cyfr długości. Jeśli podstawowy 36 reprezentację liczby w postaci zajmuje mniej niż sześć cyfr, {base-36-wystąpienie id} jest dopełniana zerami ułatwiają sześć cyfr długości. Na przykład wystąpienie o {— prefiks nazwy komputera-} "nsgvmss" i wystąpienia Identyfikatora 85 będą mieć nazwy komputera "nsgvmss00002D".

>[!NOTE]
> Prefiks nazwy komputera jest właściwością modelu zestawu skalowania, które można ustawić, dzięki czemu mogą być inne niż sama nazwa zestawu skalowania.
