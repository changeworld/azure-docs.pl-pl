---
title: Uaktualnianie węzłów klastra w celu używania dysków zarządzanych platformy Azure
description: Poniżej opisano, jak uaktualnić istniejący klaster sieci szkieletowej usług, aby używać dysków zarządzanych platformy Azure z niewielkim lub żadnym przestojem klastra.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991215"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Uaktualnianie węzłów klastra w celu używania dysków zarządzanych platformy Azure

[Dyski zarządzane platformy Azure](../virtual-machines/windows/managed-disks-overview.md) są zalecaną ofertą magazynu dysków do użytku z maszynami wirtualnymi platformy Azure do trwałego przechowywania danych. Można zwiększyć odporność obciążeń sieci szkieletowej usług, uaktualniając zestawy skalowania maszyny wirtualnej, które są podstawą typów węzłów do używania dysków zarządzanych. Poniżej opisano, jak uaktualnić istniejący klaster sieci szkieletowej usług, aby używać dysków zarządzanych platformy Azure z niewielkim lub żadnym przestojem klastra.

Ogólna strategia uaktualniania węzła klastra sieci szkieletowej usług w celu użycia dysków zarządzanych jest:

1. Wdrażanie w przeciwnym razie zduplikowany zestaw skalowania maszyny wirtualnej tego typu, ale z [managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) obiektu dodane do `osDisk` sekcji szablonu wdrożenia zestawu skalowania maszyny wirtualnej. Nowy zestaw skalowania powinien być powiązany z tym samym modułem równoważenia obciążenia /IP co oryginał, aby klienci nie doświadczali awarii usługi podczas migracji.

2. Gdy zarówno oryginalne, jak i uaktualnione zestawy skalowania są uruchomione obok siebie, wyłącz oryginalne wystąpienia węzłów po jednym naraz, tak aby usługi systemowe (lub repliki usług stanowych) były migrowane do nowego zestawu skalowania.

3. Sprawdź, czy klaster i nowe węzły są w dobrej kondycji, a następnie usuń oryginalny zestaw skalowania i stan węzła dla usuniętych węzłów.

W tym artykule przejdziesz przez kroki uaktualniania typu węzła podstawowego przykładowego klastra w celu użycia dysków zarządzanych, unikając przestojów klastra (patrz uwaga poniżej). Stan początkowy przykładowego klastra testowego składa się z jednego typu węzła [Silver trwałość](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster), poparte jednym zestawem skali z pięciu węzłów.

> [!CAUTION]
> Wystąpi awaria w tej procedurze tylko wtedy, gdy masz zależności od klastra DNS (na przykład podczas uzyskiwania dostępu do [Eksploratora sieci szkieletowej usług).](service-fabric-visualizing-your-cluster.md) [Najlepszym rozwiązaniem architektury dla usług front-end](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) jest mieć pewnego rodzaju moduł [równoważenia obciążenia](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) przed typami węzłów, aby umożliwić wymianę węzłów bez awarii.

Oto [szablony i polecenia cmdlet](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) dla usługi Azure Resource Manager, które użyliśmy do ukończenia scenariusza uaktualnienia. Zmiany szablonu zostaną wyjaśnione w [Deploy uaktualniony zestaw skalowania dla typu węzła podstawowego](#deploy-an-upgraded-scale-set-for-the-primary-node-type) poniżej.

## <a name="set-up-the-test-cluster"></a>Konfigurowanie klastra testowego

Skonfigurujmy początkowy klaster testowy sieci szkieletowej usług. Najpierw [pobierz](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) przykładowe szablony Menedżera zasobów platformy Azure, których użyjemy do ukończenia tego scenariusza.

Następnie zaloguj się do swojego konta platformy Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Poniższe polecenia poprowadzą Cię przez generowanie nowego certyfikatu z podpisem własnym i wdrażanie klastra testowego. Jeśli masz już certyfikat, którego chcesz użyć, przejdź do [użyj istniejącego certyfikatu, aby wdrożyć klaster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generowanie certyfikatu z podpisem własnym i wdrażanie klastra

Najpierw przypisz zmienne potrzebne do wdrożenia klastra sieci szkieletowej usług. Dostosuj wartości `resourceGroupName`dla `certSubjectName` `parameterFilePath`, `templateFilePath` , i dla określonego konta i środowiska:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Przed uruchomieniem `certOutputFolder` polecenia w celu wdrożenia nowego klastra sieci szkieletowej usług upewnij się, że lokalizacja istnieje na komputerze lokalnym.

Następnie otwórz plik [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) i `clusterName` dostosuj `dnsName` wartości i odpowiada wartościom dynamicznym ustawionym w programie PowerShell i zapisz zmiany.

Następnie wdrożyć klaster testowy sieci szkieletowej usług:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Po zakończeniu wdrażania zlokalizuj plik`$certPfx` *.pfx* ( ) na komputerze lokalnym i zaimportuj go do magazynu certyfikatów:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Operacja zwróci odcisk palca certyfikatu, który będzie używany do [łączenia się z nowym klastrem](#connect-to-the-new-cluster-and-check-health-status) i sprawdzania jego stanu kondycji. (Pomiń następującą sekcję, która jest alternatywnym podejściem do wdrażania klastra).

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Wdrażanie klastra za pomocą istniejącego certyfikatu

Można również użyć istniejącego certyfikatu usługi Azure Key Vault, aby wdrożyć klaster testowy. Aby to zrobić, musisz [uzyskać odwołania do usługi Key Vault](#obtain-your-key-vault-references) i odcisk palca certyfikatu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Otwórz plik [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) i zmień `clusterName` `dnsName` wartości dla i na coś wyjątkowego.

Na koniec należy wyznaczyć nazwę grupy zasobów `templateFilePath` dla `parameterFilePath` klastra i ustawić lokalizacje plików *Initial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> Wyznaczona grupa zasobów musi już istnieć i znajdować się w tym samym regionie co Magazyn kluczy.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Na koniec uruchom następujące polecenie, aby wdrożyć początkowy klaster testowy:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Łączenie się z nowym klastrem i sprawdzanie stanu kondycji

Połącz się z klastrem i upewnij się, że `clusterName` `thumb` wszystkie pięć jego węzłów jest w dobrej kondycji (zastępując zmienne i zmienne dla klastra):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Dzięki takiejowi procedura uaktualniania jest gotowa.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Wdrażanie uaktualnionego zestawu skalowania dla typu węzła podstawowego

Aby uaktualnić lub *skalować pionowo*typ węzła, musimy wdrożyć kopię zestawu skalowania maszyny wirtualnej tego typu węzła, który w `nodeTypeRef`przeciwnym `subnet`razie `loadBalancerBackendAddressPools`jest identyczny z oryginalnym zestawem skalowania (w tym odwołaniem do tego samego , i ), z tą różnicą, że zawiera żądane uaktualnienie/zmiany i własną oddzielną podsieci i przychodzącą pulę adresów NAT. Ponieważ uaktualniamy typ węzła podstawowego, nowy zestaw`isPrimary: true`skalowania zostanie oznaczony jako podstawowy ( ), podobnie jak oryginalny zestaw skalowania. (W przypadku uaktualnień typu węzła innych niż podstawowe po prostu pomiń to.)

Dla wygody wymagane zmiany zostały już wprowadzone w pliku [szablonu](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks.* [parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

W poniższych sekcjach szczegółowo opisano zmiany szablonu. Jeśli wolisz, możesz pominąć wyjaśnienie i przejść do [następnego kroku procedury uaktualniania](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizowanie szablonu klastra za pomocą uaktualnionego zestawu skalowania

Poniżej przedstawiono modyfikacje sekcji po sekcji oryginalnego szablonu wdrożenia klastra w celu dodania uaktualnionego zestawu skalowania dla typu węzła podstawowego.

#### <a name="parameters"></a>Parametry

Dodaj parametry nazwy wystąpienia, liczby i rozmiaru nowego zestawu skalowania. Należy `vmNodeType1Name` zauważyć, że jest unikatowy dla nowego zestawu skalowania, podczas gdy wartości liczby i rozmiaru są identyczne z oryginalnym zestawem skalowania.

**Plik szablonu**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Plik parametrów**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Zmienne

W sekcji `variables` szablon wdrożenia dodaj wpis dla puli adresów przychodzącego translatora adresów nowego zestawu skalowania.

**Plik szablonu**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Zasoby

W sekcji *zasoby* szablonu wdrożenia dodaj nowy zestaw skalowania maszyny wirtualnej, mając na uwadze następujące rzeczy:

* Nowy zestaw skalowania odwołuje się do tego samego typu węzła co oryginał:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Nowy zestaw skalowania odwołuje się do tego samego adresu wewnętrznej bazy danych modułu równoważenia obciążenia i podsieci (ale używa innej puli przychodzącej usługi NAT modułu równoważenia obciążenia):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Podobnie jak oryginalny zestaw skalowania, nowy zestaw skalowania jest oznaczony jako typ węzła podstawowego. (Podczas uaktualniania typów węzłów innych niż podstawowe pomiń tę zmianę).

    ```json
    "isPrimary": true,
    ```

* W przeciwieństwie do oryginalnego zestawu skalowania nowy zestaw skalowania jest uaktualniany do używania dysków zarządzanych.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Po zaimplementowanie wszystkich zmian w plikach szablonu i parametrów przejdź do następnej sekcji, aby uzyskać odwołania do usługi Key Vault i wdrożyć aktualizacje w klastrze.

### <a name="obtain-your-key-vault-references"></a>Uzyskiwanie odwołań do magazynu kluczy

Aby wdrożyć zaktualizowaną konfigurację, najpierw uzyskasz kilka odwołań do certyfikatu klastra przechowywanych w magazynie kluczy. Najprostszym sposobem znalezienia tych wartości jest za pośrednictwem witryny Azure portal. Będą potrzebne:

* **Adres URL magazynu kluczy certyfikatu klastra.** Z magazynu kluczy w witrynie Azure portal wybierz **certyfikaty** > Żądany**identyfikator tajny***certyfikatu:* > 

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Odcisk palca certyfikatu klastra.** (Prawdopodobnie masz już to, jeśli [masz połączenie z początkowym klastrem,](#connect-to-the-new-cluster-and-check-health-status) aby sprawdzić jego stan kondycji). Z tego samego bloku certyfikatu **(Certyfikaty** > *Żądany certyfikat)* w witrynie Azure portal, skopiuj **X.509 SHA-1 Odcisk palca (w szesnasty)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Identyfikator zasobu magazynu kluczy.** W witrynie Magazynu kluczy w witrynie Azure portal wybierz**identyfikator zasobu** **Właściwości:** > 

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Wdrażanie zaktualizowanego szablonu

Dostosuj `parameterFilePath` i `templateFilePath` w razie potrzeby, a następnie uruchom następujące polecenie:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Po zakończeniu wdrażania sprawdź kondycję klastra ponownie i upewnij się, że wszystkie dziesięć węzłów (pięć w oryginalnym i pięć w nowym zestawie skalowania) są w dobrej kondycji.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrowanie węzłów źródłowych do nowego zestawu skalowania

Teraz jesteśmy gotowi, aby rozpocząć wyłączanie węzłów oryginalnego zestawu skalowania. Gdy te węzły stają się wyłączone, usługi systemowe i węzły źródłowe migrują do maszyn wirtualnych nowego zestawu skalowania, ponieważ jest on również oznaczony jako typ węzła podstawowego.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Eksplorator sieci szkieletowej usług służy do monitorowania migracji węzłów źródłowych do nowego zestawu skalowania i postępu węzłów w oryginalnym zestawie skalowania ze *stanu Wyłączanie* na *Wyłączone.*

![Eksplorator sieci szkieletowej usług przedstawiający stan wyłączonych węzłów](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Może upłynąć trochę czasu, aby zakończyć operację wyłączania we wszystkich węzłach oryginalnego zestawu skalowania. Aby zagwarantować spójność danych, tylko jeden węzeł źródłowy może ulec zmianie w danym momencie. Każda zmiana węzła źródłowego wymaga aktualizacji klastra; w ten sposób zastąpienie węzła źródłowego wymaga dwóch uaktualnień klastra (po jednym dla dodawania i usuwania węzła). Uaktualnienie pięciu węzłów źródłowych w tym przykładowym scenariuszu spowoduje dziesięć uaktualnień klastra.

## <a name="remove-the-original-scale-set"></a>Usuwanie oryginalnego zestawu skalowania

Po zakończeniu operacji wyłączania usuń zestaw skalowania.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

W Eksploratorze sieci szkieletowej usług usunięte węzły (a tym samym *stan kondycji klastra)* będą teraz wyświetlane w stanie *Błąd.*

![Eksplorator sieci szkieletowej usług przedstawiający wyłączone węzły w stanie błędu](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Usuń przestarzałe węzły z klastra sieci szkieletowej usług, aby przywrócić stan kondycji klastra do *ok*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Eksplorator sieci szkieletowej usług z usuniętymi węzłami w dół w stanie błędu](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Następne kroki

W tym instruktażu przedstawiono sposób uaktualniania zestawów skalowania maszyny wirtualnej klastra sieci szkieletowej usług w celu używania dysków zarządzanych przy jednoczesnym unikaniu przerw w działaniu usługi podczas procesu. Aby uzyskać więcej informacji na tematy pokrewne, zapoznaj się z następującymi zasobami.

Instrukcje:

* [Skalowanie w górę węzła klastra usługi Service Fabric podstawowego typu](service-fabric-scale-up-node-type.md)

* [Konwertowanie szablonu zestawu skalowania na dyski zarządzane](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Usuwanie typu węzła sieci szkieletowej usług](service-fabric-how-to-remove-node-type.md)

Zobacz też:

* [Przykład: uaktualnianie węzłów klastra do używania dysków zarządzanych platformy Azure](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Zagadnienia dotyczące skalowania w pionie](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)