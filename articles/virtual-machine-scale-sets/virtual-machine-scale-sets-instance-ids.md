---
title: Opis identyfikatorów wystąpień dla maszyn wirtualnych z zestawem skalowania maszyn wirtualnych platformy Azure
description: Zrozumienie identyfikatorów wystąpień dla skali maszyny wirtualnej platformy Azure ustawia maszyny wirtualne i różne sposoby, które są one powierzchni.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 99ad4249a4134bcc1b1cf5aba92b8a95a034db33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534426"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Opis identyfikatorów wystąpień dla maszyn wirtualnych z zestawem skalowania maszyn wirtualnych platformy Azure
W tym artykule opisano identyfikatory wystąpień dla zestawów skalowania i różne sposoby ich powierzchni.

## <a name="scale-set-instance-ids"></a>Identyfikatory wystąpień zestawu skalowania

Każda maszyna wirtualna w zestawie skalowania otrzymuje identyfikator wystąpienia, który jednoznacznie ją identyfikuje. Ten identyfikator wystąpienia jest używany w interfejsach API zestawu skalowania do wykonywania operacji na określonej maszynie Wirtualnej w zestawie skalowania. Na przykład można określić identyfikator określonego wystąpienia, aby ponownie zamówić podczas korzystania z interfejsu API reimage:

INTERFEJS API `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` REST: (aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)

Powershell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (aby uzyskać więcej informacji, zobacz [dokumentację programu Powershell)](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

Listę identyfikatorów wystąpień można uzyskać, wymieniając wszystkie wystąpienia w zestawie skalowania:

INTERFEJS API `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` REST: (aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API REST)](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list)

Powershell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (aby uzyskać więcej informacji, zobacz [dokumentację programu Powershell)](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (aby uzyskać więcej informacji, zobacz [dokumentację interfejsu wiersza polecenia).](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)

Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads/) do listy maszyn wirtualnych w zestawie skalowania.

Dokładna prezentacja danych wyjściowych zależy od opcji, które podasz do polecenia, ale oto kilka przykładowych danych wyjściowych z interfejsu wiersza polecenia:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
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

Jak widać, właściwość "instanceId" jest tylko liczbą dziesiętną. Identyfikatory wystąpień mogą być ponownie używane dla nowych wystąpień po usunięciu starych wystąpień.

>[!NOTE]
> Nie **ma żadnej gwarancji,** że identyfikatory wystąpienia sposobu są przypisywane do maszyn wirtualnych w zestawie skalowania. Czasami mogą wydawać się one coraz bardziej niż się zdarzały, ale nie zawsze tak jest. Nie należy przyjmować zależności od określonego sposobu, w którym identyfikatory wystąpień są przypisywane do maszyn wirtualnych.

## <a name="scale-set-vm-names"></a>Nazwy maszyn wirtualnych zestawu skalowania

W danych wyjściowych próbki powyżej istnieje również "nazwa" dla maszyny Wirtualnej. Ta nazwa ma postać "{scale-set-name}_{instance-id}". Ta nazwa jest tym, który jest widoczny w witrynie Azure portal, gdy lista wystąpień w zestawie skalowania:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Część nazwy {instance-id} jest tą samą liczbą dziesiętną, co wcześniej omówiona właściwość "instanceId".

## <a name="instance-metadata-vm-name"></a>Nazwa maszyny Wirtualnej metadanych wystąpienia

Jeśli kwerendy [metadane wystąpienia](../virtual-machines/windows/instance-metadata-service.md) z poziomu zestawu skalowania maszyny Wirtualnej, pojawi się "nazwa" w danych wyjściowych:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Ta nazwa jest taka sama jak nazwa omówiona wcześniej.

## <a name="scale-set-vm-computer-name"></a>Nazwa komputera zestawu skalowania maszyny Wirtualnej

Każda maszyna wirtualna w zestawie skalowania pobiera również przypisaną do niej nazwę komputera. Ta nazwa komputera jest nazwą hosta maszyny Wirtualnej w [rozpoznawaniu nazw DNS dostarczonych przez platformę Azure w sieci wirtualnej](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Ta nazwa komputera ma formę "{computer-name-prefix}{base-36-instance-id}".

{base-36-instance-id} znajduje się w [bazie 36](https://en.wikipedia.org/wiki/Base36) i ma zawsze sześć cyfr długości. Jeśli podstawowa reprezentacja 36 liczby przyjmuje mniej niż sześć cyfr, {base-36-instance-id} jest wyściełana zerami, aby mieć sześć cyfr długości. Na przykład wystąpienie z {computer-name-prefix} "nsgvmss" i identyfikatorem wystąpienia 85 będzie miało nazwę komputera "nsgvmsss00002D".

>[!NOTE]
> Prefiks nazwy komputera jest właściwością modelu zestawu skalowania, który można ustawić, dzięki czemu może się różnić od samej nazwy zestawu skalowania.
