---
title: Zrozumienie identyfikatorów wystąpienia dla maszyn wirtualnych zestawu skalowania maszyny Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumienie zestawu wystąpienia identyfikatory skali maszyny Wirtualnej Azure maszyny wirtualne
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
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
ms.author: negat
ms.openlocfilehash: 3a43dc86f1fb53dfde4bce3938faaa30e18f5a6d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29742776"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Zrozumienie zestawu wystąpienia identyfikatory skali maszyny Wirtualnej Azure maszyny wirtualne
W tym artykule opisano identyfikatory wystąpienia zestawów skali i różne sposoby ich powierzchni.

## <a name="scale-set-instance-ids"></a>Identyfikatory wystąpienia zestawu skalowania

Każda maszyna wirtualna w zestawie skalowania pobiera identyfikator wystąpienia, który unikatowo identyfikuje go. Ten identyfikator jest używany w zestawie skalowania interfejsów API do operacji na określonym wystąpienia zestawu maszyny Wirtualnej w skali. Na przykład można określić Identyfikatora określone wystąpienie do odtworzenia z obrazu przy użyciu odtworzenia z obrazu interfejsu API:

Interfejs API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

Środowiska PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Aby uzyskać więcej informacji, zobacz [dokumentacji programu Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

Interfejs wiersza polecenia: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Lista identyfikatorów wystąpień można uzyskać poprzez wyszczególnienie wszystkich wystąpień w zestawie skali:

Interfejs API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

Środowiska PowerShell: `Get-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentacji programu Powershell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

Interfejs wiersza polecenia: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Aby uzyskać więcej informacji, zobacz [dokumentacji interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instances)).

Można również użyć [resources.azure.com](https://resources.azure.com) lub [zestawów SDK usługi Azure](https://azure.microsoft.com/downloads/) do listy maszyn wirtualnych w zestawie skalowania.

Dokładny opis dane wyjściowe zależy od opcji podane polecenia, ale poniżej przedstawiono niektóre przykładowe dane wyjściowe z poziomu interfejsu wiersza polecenia:

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

Jak widać, właściwość "instanceId" jest liczbę dziesiętną. Wystąpienie identyfikatorów może nastąpić dla nowych wystąpień po usunięciu starego wystąpień.

>[!NOTE]
> Brak **żadnej gwarancji** w wystąpieniu sposób identyfikatory są przypisane do maszyn wirtualnych w zestawie skalowania. One mogą wydawać się po kolei rośnie w czasie, ale nie zawsze jest wielkość liter. Nie mają zależności w określony sposób, w którym identyfikatorów wystąpienia są przypisane do maszyn wirtualnych.

## <a name="scale-set-vm-names"></a>Nazwy maszyny Wirtualnej zestawu skalowania

W przykładowych danych wyjściowych powyżej jest również "name" dla maszyny Wirtualnej. Ta nazwa ma postać "_ {identyfikator wystąpienia} {Skala set-name}". Ta nazwa jest widoczny w portalu Azure, gdy lista wystąpień w zestawie skali:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

{Identyfikator wystąpienia} część nazwy jest ten sam numer dziesiętnej jako właściwość "instanceId" omówionych wcześniej.

## <a name="instance-metadata-vm-name"></a>Nazwa wystąpienia metadanych maszyny Wirtualnej

Po wykonaniu zapytania [metadanych wystąpienia](../virtual-machines/windows/instance-metadata-service.md) od w ramach zestawu skali maszyny Wirtualnej, zobaczysz "name" w danych wyjściowych:

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

Każda maszyna wirtualna w skali, ustaw również otrzymuje nazwę komputera, przypisane do niej. Ta nazwa komputera jest nazwą hosta maszyny Wirtualnej w ramach [rozpoznawanie w sieci wirtualnej nazw DNS platformy Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Ta nazwa komputera jest w formie "{computer-name-prefix}{base-36-instance-id}".

{Base-36-— identyfikator wystąpienia} znajduje się w [podstawowa 36](https://en.wikipedia.org/wiki/Base36) i jest zawsze sześć cyfr długości. Jeśli podstawowy 36 reprezentujący liczbę ma mniej niż sześć cyfr, {base-36-— identyfikator wystąpienia} jest uzupełniana zerami, aby była sześć cyfr o długości z. Na przykład wystąpienie o {komputera name prefiks} "nsgvmss" i wystąpienie 85 identyfikator będą mieć nazwy komputera "nsgvmss00002D".

>[!NOTE]
> Prefiks nazwy komputera jest właściwością modelu zestawu skali, który można ustawić, może być inna niż nazwa zestawu skali samej siebie.