---
title: Zestawach skali maszyny wirtualnej platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące zestawy skalowania maszyny wirtualnej.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: negat
ms.custom: na
ms.openlocfilehash: 2b0f463c009d13440f6d3eb2bbbe2315ba7b13f2
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Zestawach skali maszyny wirtualnej platformy Azure — często zadawane pytania

Odpowiedzi na często zadawane pytania dotyczące zestawy skalowania maszyny wirtualnej na platformie Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>TOP — często zadawane pytania dla zestawów skalowania
**PYTANIE** Ile maszyn wirtualnych może się znajdować w zestawie skalowania?

**ODPOWIEDŹ** Zestaw skalowania może zawierać od 0 do 1000 maszyn wirtualnych opartych na obrazach platformy lub od 0 do 300 maszyn wirtualnych opartych na obrazach niestandardowych. 

**PYTANIE** Czy zestawy skalowania obsługują dyski danych?

**ODPOWIEDŹ** Tak. Zestaw skalowania może definiować konfigurację dołączonych dysków danych, która jest stosowana do wszystkich maszyn wirtualnych w zestawie. Aby uzyskać więcej informacji, zobacz [Zestawy skalowania na platformie Azure i dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md). Oto przykłady innych opcji magazynowania danych:

* Pliki platformy Azure (dyski udostępnione za pośrednictwem protokołu SMB)
* Dysk systemu operacyjnego
* Dysk tymczasowy (lokalny, bez kopii zapasowej w usłudze Azure Storage)
* Usługa danych platformy Azure (na przykład tabele platformy Azure, obiekty blob platformy Azure)
* Zewnętrzne usługi danych (na przykład zdalna baza danych)

**PYTANIE** Które regiony świadczenia usługi Azure obsługują zestawy skalowania?

**ODPOWIEDŹ** Wszystkie regiony obsługują zestawy skalowania.

**PYTANIE** Jak utworzyć zestaw skalowania za pomocą obrazu niestandardowego?

**ODPOWIEDŹ** Utwórz dysk zarządzany na podstawie wirtualnego dysku twardego z obrazem niestandardowym i odwołaj się do niego w szablonie zestawu skalowania. [Oto przykład](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os).

**PYTANIE** Jeśli zmniejszę pojemność zestawu skalowania z 20 do 15, które maszyny wirtualne zostaną usunięte?

**ODPOWIEDŹ** W celu zapewnienia maksymalnej dostępności maszyny wirtualne są usuwane z zestawu skalowania równomiernie w domenach błędów i domenach aktualizacji. Najpierw są usuwane maszyny wirtualne o najwyższym identyfikatorze.

**PYTANIE** Co się stanie, jeśli następnie zwiększę pojemność z 15 do 18?

**ODPOWIEDŹ** Zwiększenie pojemności do 18 spowoduje utworzenie 3 nowych maszyn wirtualnych. Za każdym razem następuje zwiększenie identyfikatora wystąpienia maszyny wirtualnej względem poprzedniej najwyższej wartości (na przykład 20, 21, 22). Maszyny wirtualne są równoważone w domenach błędów i domenach aktualizacji.

**PYTANIE** Czy mogę wymusić sekwencję wykonywania w przypadku korzystania z wielu rozszerzeń w zestawie skalowania?

**ODPOWIEDŹ** Nie można tego zrobić bezpośrednio, ale w przypadku rozszerzenia customScript skrypt może poczekać na ukończenie działania innego rozszerzenia. Dodatkowe wytyczne dotyczące sekwencjonowania rozszerzeń można znaleźć we wpisie w blogu: [Extension Sequencing in Azure virtual machine scale sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Sekwencjonowanie rozszerzeń w zestawach skalowania maszyn wirtualnych platformy Azure).

**PYTANIE** Czy zestawy skalowania współdziałają z zestawami dostępności platformy Azure?

**ODPOWIEDŹ** Regionalne (z systemem innym niż zonal) zestawu skalowania maszyny wirtualnej używa *umieszczania grupy*, każdy z nich można skonfigurować do działania jako zestaw z pięciu domen błędów niejawne dostępności i zaktualizować pięć domen. Zestawy skalowania maszyn wirtualnych więcej niż 100 obejmuje wiele grup umieszczania. Aby uzyskać więcej informacji na temat grup umieszczania, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). Zestaw dostępności maszyn wirtualnych może znajdować się w tej samej sieci wirtualnej co zestaw skalowania maszyn wirtualnych. Typowa konfiguracja polega na umieszczeniu maszyn wirtualnych węzła kontrolnego (często wymagających unikatowej konfiguracji) w zestawie dostępności, a węzłów danych w zestawie skalowania.

**PYTANIE** Czy przeskalować zestawów w strefach dostępności Azure?

**ODPOWIEDŹ** Tak! Aby uzyskać więcej informacji, zobacz [doc strefy zestawu skalowania](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatyczne skalowanie

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jakie są najlepsze rozwiązania dotyczące skalowania automatycznego Azure?

Aby uzyskać najlepsze rozwiązania dotyczące skalowania automatycznego, zobacz [najlepsze rozwiązania w przypadku maszyn wirtualnych, skalowanie automatyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Gdzie można znaleźć nazwy metryki Skalowanie automatyczne, który wykorzystuje metryki oparta na hoście

Metryki nazw dla Skalowanie automatyczne, który wykorzystuje metryki oparta na hoście, zobacz [obsługiwane metryki z monitorem Azure](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Czy istnieją przykładami Skalowanie automatyczne w oparciu o długości kolejek i tematów usługi Azure Service Bus?

Tak. Przykłady Skalowanie automatyczne w oparciu o długości kolejek i tematów usługi Azure Service Bus, zobacz [metryki wspólnej Skalowanie automatyczne Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Kolejki usługi Service Bus Użyj następującego formatu JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Kolejki magazynu należy użyć następującego formatu JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Zastąp wartości przykładowe zasobu Uniform Resource Identifier (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Czy należy skalowania automatycznego przy użyciu metryk oparta na hoście lub rozszerzenia diagnostyki?

Można utworzyć ustawienia skalowania automatycznego na maszynie Wirtualnej metryki na poziomie hosta lub metryki na podstawie systemu operacyjnego gościa.

Aby uzyskać listę obsługiwanych metryki, zobacz [metryki wspólnej Skalowanie automatyczne Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics). 

Aby uzyskać pełny przykład dla zestawy skalowania maszyny wirtualnej, zobacz [konfiguracji automatycznego skalowania zaawansowanych przy użyciu szablonów usługi Resource Manager dla zestawy skalowania maszyny wirtualnej](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets). 

Próbki używa Metryka Procesora poziomie hosta i metrykę liczby komunikatów.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak ustawić reguły alertów na zestaw skali maszyny wirtualnej?

Alerty można tworzyć na metryki dla zestawy skalowania maszyny wirtualnej za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [przykłady Szybki Start Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) i [przykłady szybkiego startu interfejsu wiersza polecenia i platform Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

Element TargetResourceId zestawu skali maszyny wirtualnej wygląda następująco: 

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Jako metrykę, aby ustawić alert dla można wybrać żadnych licznika wydajności maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [metryki systemu operacyjnego gościa dla maszyn wirtualnych Menedżera zasobów systemu Windows](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) i [metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) w [Azure Monitor Skalowanie automatyczne wspólnej metryki](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)artykułu.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak skonfigurować automatycznego skalowania w skali maszyny wirtualnej ustawić przy użyciu programu PowerShell?

Aby skonfigurować automatycznego skalowania w skali maszyny wirtualnej ustawić przy użyciu programu PowerShell, zobacz wpis w blogu [sposób dodawania skalowania automatycznego do zestawu skalowania maszyny wirtualnej platformy Azure](https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/).




## <a name="certificates"></a>Certyfikaty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm-how-do-i-provision-a-virtual-machine-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration-the-common-certificate-rotation-operation-would-be-almost-the-same-as-a-configuration-update-operation-do-you-have-an-example-of-how-to-do-this"></a>Jak bezpiecznie wysłać certyfikatów do maszyny Wirtualnej? Jak udostępnić ustawioną bezpiecznego działania witryny sieci Web, gdy protokół SSL dla witryny sieci Web jest dostarczany z konfiguracji certyfikatu skali maszyny wirtualnej? (Typowych operacji obrotu certyfikat będzie niemal taki sam, jak operacja aktualizacji konfiguracji.) Czy jest przykładem to zrobić? 

Aby bezpiecznie wysłać certyfikatów do maszyny Wirtualnej, można zainstalować certyfikatu klienta bezpośrednio do magazynu certyfikatów systemu Windows z magazynu kluczy klienta.

Użyj następującego formatu JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

Kod obsługuje system Windows i Linux.

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizacja zestawu skalowania maszyn wirtualnych](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="example-of-self-signed-certificate"></a>Przykład certyfikatu z podpisem własnym

1.  Tworzenie certyfikatu z podpisem własnym w magazynie kluczy.

    Użyj następujących poleceń programu PowerShell:

    ```powershell
    Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

    Connect-AzureRmAccount

    Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
    ```

    To polecenie umożliwia dane wejściowe dla szablonu usługi Azure Resource Manager.

    Na przykład tworzenie certyfikatu z podpisem własnym w magazynie kluczy, zobacz [scenariusze zabezpieczeń klastra sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

2.  Zmień szablon usługi Resource Manager.

    Dodaj tę właściwość, aby **virtualMachineProfile**, w ramach maszyny wirtualnej zestawu skalowania zasobu:

    ```json 
    "osProfile": {
        "computerNamePrefix": "[variables('namingInfix')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                    {
                        "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                        "certificateStore": "My"
                    }
                ]
            }
        ]
    }
    ```
  

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Czy można określić parę kluczy SSH do uwierzytelniania SSH o skali maszyny wirtualnej systemu Linux ustawiony na podstawie szablonu usługi Resource Manager?  

Tak. Interfejs API REST dla **osProfile** przypomina standardowego interfejsu API REST maszyny Wirtualnej. 

Obejmują **osProfile** w szablonie:

```json 
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```
 
Ten blok JSON jest używany w [szablon szybkiego startu GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).
 
Profil systemu operacyjnego jest również używany w [szablon szybkiego startu GitHub grelayhost.json](https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json).

Aby uzyskać więcej informacji, zobacz [Tworzenie lub aktualizacja zestawu skalowania maszyn wirtualnych](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).
  

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak usunąć przestarzałe certyfikatów? 

Aby usunąć przestarzałe certyfikaty, należy usunąć starego certyfikatu z listy certyfikatów w magazynie. Pozostaw wszystkie certyfikaty, które ma pozostać na komputerze, na liście. Nie powoduje usunięcia certyfikatu z wszystkich maszyn wirtualnych. Ponadto nie dodaje certyfikat do nowych maszyn wirtualnych, które są tworzone w zestawie skalowania maszyn wirtualnych. 

Aby usunąć certyfikat z istniejących maszyn wirtualnych, należy zapisać rozszerzenia niestandardowego skryptu, aby ręcznie usunąć certyfikaty z magazynu certyfikatów.
 
### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning-i-want-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-use-them-in-my-resource-manager-template"></a>Jak iniekcję istniejącego klucza publicznego SSH do warstwy SSH zestaw skali maszyny wirtualnej podczas inicjowania obsługi Chcę przechowywać wartości klucza publicznego SSH w usłudze Azure Key Vault, a następnie używać ich w szablonie usługi Resource Manager.

Maszyny wirtualne są udostępniane tylko za pomocą klucza publicznego SSH, nie trzeba umieścić kluczy publicznych w magazynie kluczy. Klucze publiczne nie są poufne.
 
Klucze publiczne SSH w postaci zwykłego tekstu można podać podczas tworzenia maszyny Wirtualnej systemu Linux:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
```
 
Nazwa elementu linuxConfiguration | Wymagane | Typ | Opis
--- | --- | --- | --- |  ---
SSH | Nie | Collection | Określa konfigurację kluczy SSH w systemie operacyjnym Linux
ścieżka | Yes | Ciąg | Określa ścieżkę pliku Linux, gdzie kluczy SSH lub certyfikat powinien być zlokalizowany
Kontenerem | Yes | Ciąg | Określa klucz publiczny SSH algorytmem Base64

Na przykład zobacz [szablon szybkiego startu GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

 
### <a name="when-i-run-update-azurermvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Po uruchomieniu `Update-AzureRmVmss` po dodaniu więcej niż jeden certyfikat z tym samym magazynie kluczy, pojawia się następujący komunikat:
 
>AzureRmVmss aktualizacji: Klucz tajny listy zawiera powtórzony wystąpienia /subscriptions/ < identyfikator Moje subskrypcji > / resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, który jest niedozwolone.
 
Może to nastąpić, Jeśli spróbujesz ponownie dodać tego samego magazynu, zamiast przy użyciu nowego certyfikatu w magazynie dla istniejącego magazynu źródłowego. `Add-AzureRmVmssSecret` Polecenie nie działa poprawnie, w przypadku dodawania dodatkowych kluczy tajnych.
 
Aby dodać więcej kluczy tajnych z tym samym magazynie kluczy, lista $vmss.properties.osProfile.secrets[0].vaultCertificates aktualizacji.
 
Aby oczekiwanej strukturze wejściowej, zobacz [Tworzenie lub aktualizacja maszyny wirtualnej ustawić](https://msdn.microsoft.com/library/azure/mt589035.aspx).
 
Znajdź klucz tajny obiektu zestawu skali maszyny wirtualnej, który znajduje się w magazynie kluczy. Następnie należy dodać odwołania certyfikatów (adres URL i nazwa magazynu tajne) do listy skojarzonej z magazynem.

> [!NOTE] 
> Obecnie nie można usunąć certyfikaty z maszyn wirtualnych przy użyciu interfejsu API zestawu skali maszyny wirtualnej.
>

Nowe maszyny wirtualne nie będzie miał stary certyfikat. Maszyny wirtualne w tym certyfikatu, które są już wdrożone będzie jednak stary certyfikat.
 
### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Czy można wypychana certyfikaty, na zestaw bez podawania hasła, gdy certyfikat znajduje się w magazynie tajny skalowania maszyny wirtualnej?

Nie należy trwale kodować haseł w skryptach. Można dynamicznie pobrać hasła uprawnień, które są używane do uruchamiania skryptu wdrażania. Jeśli masz skrypt, który przenosi certyfikat z magazynu tajny klucz magazynu, tajne magazynu `get certificate` polecenie wyświetla również hasło pliku pfx.
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak właściwości kluczy tajnych virtualMachineProfile.osProfile skali maszyny wirtualnej ustawić pracy? Dlaczego należy wartość sourceVault, gdy trzeba określić bezwzględny identyfikator URI dla certyfikatu, używając właściwości certificateUrl? 

Zdalne zarządzanie systemem Windows (WinRM) odwołanie certyfikatu musi być obecny we właściwości kluczy tajnych w profilu systemu operacyjnego. 

Źródłowy magazyn wskazujący ma na celu wymuszenia zasad listę kontroli dostępu (ACL) kontroli dostępu, które istnieją w modelu usługi w chmurze Azure użytkownika. Jeśli źródłowy magazyn nie zostanie określona, użytkownicy, którzy nie mają uprawnień do wdrażania lub uzyskać dostępu do kluczy tajnych w magazynie kluczy będą mogli za pośrednictwem obliczeniowe zasobów dostawcy (CRP). Listy ACL istnieje nawet w przypadku zasobów, które nie istnieją.

Jeśli podasz nieprawidłowe źródło identyfikator magazynu, ale adres URL magazynu kluczy prawidłowy błąd jest zgłaszany, gdy sondowania operacji.
 
### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Jeśli klucze tajne I dodać do istniejącej skalowania maszyny wirtualnej ustawić, są dodane do istniejących maszyn wirtualnych lub tylko do nowych kluczy tajnych? 

Certyfikaty są dodawane do wszystkich maszyn wirtualnych, nawet wstępnie istniejące grupy. Jeśli właściwość upgradePolicy Twojego skalowania maszyny wirtualnej ma ustawioną wartość **ręczne**, certyfikat zostanie dodany do maszyny Wirtualnej, podczas wykonywania ręcznej aktualizacji na maszynie Wirtualnej.
 
### <a name="where-do-i-put-certificates-for-linux-vms"></a>Gdzie umieścić certyfikaty dla maszyn wirtualnych systemu Linux?

Aby dowiedzieć się, jak wdrożyć certyfikaty dla maszyn wirtualnych systemu Linux, zobacz [wdrażanie certyfikatów do maszyn wirtualnych z zarządzanego przez klienta magazynu kluczy](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).
  
### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak dodać certyfikat nowego magazynu do nowego obiektu certyfikatu?

Aby dodać certyfikat z magazynu na istniejącym kluczem tajnym, zobacz w poniższym przykładzie programu PowerShell. Użyj tylko jeden obiekt tajny.
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co się dzieje z certyfikatów, jeśli można odtworzyć z obrazu maszyny Wirtualnej?

Czy można odtworzyć z obrazu maszyny Wirtualnej, certyfikaty zostaną usunięte. Usuwa cały system operacyjny dysku ponownej instalacji systemu. 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co się stanie po usunięciu certyfikatu z magazynu kluczy?

Jeśli klucz tajny zostanie usunięta z magazynu kluczy, a następnie uruchom `stop deallocate` dla wszystkich maszyn wirtualnych, a następnie uruchom je ponownie, wystąpi awaria. Błąd występuje, ponieważ CRP musi pobrać kluczy tajnych w magazynie kluczy, ale nie jest. W tym scenariuszu należy usunąć certyfikaty z modelu zestawu skali maszyny wirtualnej. 

Składnik CRP nie jest trwały kluczy tajnych klienta. Po uruchomieniu `stop deallocate` dla wszystkich maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, pamięci podręcznej zostanie usunięty. W tym scenariuszu kluczy tajnych są pobierane z magazynu kluczy.

Ten problem nie wystąpić w trakcie skalowania, ponieważ nie istnieje w pamięci podręcznej kopię hasła w sieci szkieletowej usług Azure (w modelu sieci szkieletowej pojedynczej dzierżawy).
 
### <a name="why-do-i-have-to-specify-the-exact-location-for-the-certificate-url-httpsname-of-the-vaultvaultazurenet443secretsexact-location-as-indicated-in-service-fabric-cluster-security-scenarioshttpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>Dlaczego muszę określania dokładnej lokalizacji dla adresu URL certyfikatu (https://<name of the vault>.vault.azure.net:443/secrets/<exact location>), jak określono w [scenariusze zabezpieczeń klastra sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/)?
 
Dokumentacja usługi Azure Key Vault stany, Pobierz klucz tajny interfejsu API REST powinien zwrócić najnowszą wersję klucza tajnego, jeśli nie określono wersji.
 
Metoda | Adres URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

Zastąp {*nazwa klucza tajnego*} o nazwie i Zastąp {*wersja klucza tajnego*} przy użyciu wersji klucza tajnego do pobrania. Może zostać wyłączone wersję klucza tajnego. W takim przypadku jest pobierana bieżącej wersji.
  
### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Dlaczego trzeba określić wersji certyfikatu podczas korzystania z usługi Key Vault?

Określ wersję certyfikatu wymaganie usługi Key Vault ma na celu była Wyczyść, aby użytkownik certyfikat, który jest wdrożony na swoich maszynach wirtualnych.

Jeśli tworzenie maszyny Wirtualnej, a następnie zaktualizuj klucz tajny w magazynie kluczy, nowy certyfikat nie zostanie pobrana do maszyn wirtualnych. Ale maszyny wirtualne znajdują się do niego odwołują, i nowych maszyn wirtualnych Uzyskaj nowy klucz tajny. Aby tego uniknąć, należy odwoływać się wersję klucza tajnego.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mojego zespołu współpracuje z kilka certyfikatów, które są dystrybuowane do nas jako .cer kluczy publicznych. Co to jest ustawiony zalecane podejście do wdrożenia tych certyfikatów do skalowania maszyny wirtualnej?

Do wdrożenia .cer ustawić klucze publiczne do skalowania maszyny wirtualnej, można wygenerować pliku PFX, który zawiera tylko pliki cer. Aby to zrobić, użyj `X509ContentType = Pfx`. Na przykład załadować plik .cer jako obiekt x509Certificate2 w języku C# lub programu PowerShell, a następnie wywołaj metodę. 

Aby uzyskać więcej informacji, zobacz [metody X509Certificate.Export (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-most-other-resource-providers-have-this-option"></a>Nie ma opcji użytkownikom na przekazywanie certyfikatów jako ciąg base64. Większość dostawców zasobów ma tę opcję.

Aby emulować przekazywanie certyfikatu jako ciąg w formacie base64, można wyodrębnić najnowszej wersji adresu URL w szablonie usługi Resource Manager. Dołącz do szablonu usługi Resource Manager następującej właściwości JSON:

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Czy mają do zakodowania w obiektów JSON w magazynów kluczy certyfikatów?

Zestawy skalowania maszyn wirtualnych i maszyn wirtualnych certyfikaty muszą być ujęte w obiektów JSON. 

Obsługujemy również typu zawartości application/x-pkcs12. Aby uzyskać instrukcje na temat używania application/x-pkcs12, zobacz [certyfikatów PFX w usłudze Azure Key Vault](http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/).
 
Obecnie nie obsługujemy .cer plików. Aby używać plików .cer, eksportowanie ich do kontenerów pfx.



## <a name="compliance-and-security"></a>Zgodności i zabezpieczeń

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Czy na pewno zgodnych zestawach skali maszyn wirtualnych?

Zestawy skalowania maszyn wirtualnych to cienka warstwa interfejsu API na rozwiązaniu CRP. Obydwa składniki są częścią platformy obliczeniowej w drzewie usług platformy Azure.

Z punktu widzenia zgodności zestawy skalowania maszyn wirtualnych są integralną częścią platformy obliczeniowej Azure. Współużytkują one zespół, narzędzia, procesy, metodologię wdrażania, kontrolę zabezpieczeń kompilację dokładnie na czas (JIT), monitorowanie, alerty itd. z rozwiązaniem CRP. Zestawy skalowania maszyn wirtualnych są zgodne ze standardem PCI (Payment Card Industry), ponieważ rozwiązanie CRP jest częścią bieżącego atestu PCI Data Security Standard (DSS).

Aby uzyskać więcej informacji, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-azure-managed-service-identityhttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Jest [tożsamości usługi zarządzane Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) współpracują zestawy skalowania maszyny wirtualnej?

Tak. Niektóre szablony MSI przykładzie w szablonach Szybki Start Azure jest widoczny. Linux: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-linux). System Windows: [ https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-msi-windows).


## <a name="extensions"></a>Rozszerzenia

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak usunąć rozszerzenia zestawu skali maszyny wirtualnej?

Aby usunąć rozszerzenia zestawu skali maszyny wirtualnej, skorzystaj z następującego przykładu środowiska PowerShell:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
Możesz znaleźć wartość extensionName `$vmss`.
   
### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-log-analytics"></a>Jest przykład szablonu, która integruje się z analizy dzienników zestawu skalowania maszyny wirtualnej?

Na przykład szablon, która integruje się z analizy dzienników zestawu skalowania maszyn wirtualnych, zobacz drugi przykład w [wdrażanie klastra usługi sieć szkieletowa usług Azure i Włącz monitorowanie przy użyciu analizy dzienników](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).
   
### <a name="extensions-seem-to-run-in-parallel-on-virtual-machine-scale-sets-this-causes-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this"></a>Rozszerzenia wydają się równolegle na zestawy skalowania maszyny wirtualnej. Powoduje to, że moje rozszerzenie skryptu niestandardowego się niepowodzeniem. Co można zrobić, aby rozwiązać ten problem?

Aby dowiedzieć się więcej na temat sekwencjonowania rozszerzenia w zestawy skalowania maszyny wirtualnej, zobacz [sekwencjonowania rozszerzenia w zestawy skalowania maszyny wirtualnej platformy Azure](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).
 
 
### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Sposób resetowania hasła dla maszyn wirtualnych w zestawu skali maszyny wirtualnej

Istnieją dwa sposoby zmiany hasła dla maszyn wirtualnych w zestawach skali.

- Zmiana modelu zestawu skali maszyny wirtualnej bezpośrednio. Dostępne z obliczeniowe interfejsu API 2017-12-01 lub nowszy.

    Zaktualizuj poświadczenia administratora bezpośrednio w modelu zestawu skali (na przykład za pomocą Eksploratora zasobów Azure, programu PowerShell lub interfejsu wiersza polecenia). Po zaktualizowane, wszystkie nowe zestawu skalowania maszyn wirtualnych ma nowe poświadczenia. Istniejące maszyny wirtualne mieć tylko nowe poświadczenia, jeśli ich są odtworzyć z obrazu. 

- Resetowanie hasła przy użyciu rozszerzenia dostępu do maszyny Wirtualnej.

    Użyj poniższego przykładu z programu PowerShell:
    
    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}
     
    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```


### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak dodać rozszerzenie do wszystkich maszyn wirtualnych w zestawu skali maszyny wirtualnej

Jeśli ustawiono zasad aktualizacji **automatyczne**, ponowne wdrożenie szablonu z nowymi właściwościami rozszerzenia aktualizacji wszystkich maszyn wirtualnych.

Jeśli ustawiono zasad aktualizacji **ręczne**, najpierw zaktualizuj rozszerzenie, a następnie ręcznie zaktualizuj wszystkie wystąpienia w maszyn wirtualnych.

  
### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected-that-is-will-the-vms-not-match-the-virtual-machine-scale-set-model-or-are-they-ignored-when-an-existing-machine-is-service-healed-or-reimaged-are-the-scripts-that-are-currently-configured-on-the-virtual-machine-scale-set-executed-or-are-the-scripts-that-were-configured-when-the-vm-was-first-created-used"></a>Jeśli rozszerzenia skojarzone z istniejącego zestawu skalowania maszyny wirtualnej zostaną zaktualizowane, czy istniejące maszyny wirtualne, których dotyczy problem? (To znaczy będzie maszyn wirtualnych *nie* zgodny z modelem zestaw skali maszyny wirtualnej?) Czy są one zignorowane? Gdy już istniejącej maszynie jest usługi zabliźnione lub odtworzyć z obrazu, to skrypty, które są obecnie skonfigurowane w zestawie skalowania maszyn wirtualnych, które są wykonywane, lub używany jest skrypty, które zostały skonfigurowane, gdy maszyna wirtualna została pierwotnie utworzona?

Jeśli ustawiona definicji rozszerzenia w skali maszyny wirtualnej zaktualizować modelu i ma ustawioną właściwość upgradePolicy **automatyczne**, aktualizuje maszyn wirtualnych. Jeśli ustawiono właściwość upgradePolicy **ręczne**, rozszerzenia są oznaczone jako niezgodny modelu. 

W przypadku istniejącej maszyny Wirtualnej zabliźnione usługi, jest widoczny jako ponowne uruchomienie komputera, a rozszerzenia nie są ponownie. Jeśli go zostanie odtworzone z obrazu, przypomina zamianę dysku systemu operacyjnego obrazu źródłowego. Wszelkie specjalizacji z najnowszego modelu, takie jak rozszerzenia, są uruchamiane.
 
### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak dołączyć skalowania maszyny wirtualnej, ustaw do domeny usługi Active Directory?

Aby przyłączyć skalowania maszyny wirtualnej, ustaw do domeny usługi Active Directory (AD), można zdefiniować rozszerzenia. 

Aby zdefiniować rozszerzenie, należy użyć właściwości JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```
 
### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-example-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>Moje rozszerzenia zestawu skali maszyny wirtualnej próbuje zainstalować coś, co wymaga ponownego uruchomienia. Na przykład "commandToExecute": "powershell.exe - ExecutionPolicy Unrestricted Install-WindowsFeature — nazwa FS-Resource-Manager-IncludeManagementTools"

Jeśli rozszerzenia zestawu skali maszyny wirtualnej próbuje zainstalować coś, co wymaga ponownego uruchomienia, można użyć rozszerzenia Azure Automation konfiguracji żądanego stanu (DSC automatyzacji). Jeśli system operacyjny Windows Server 2012 R2, Azure w przypadku instalacji systemu Windows Management Framework (WMF) 5.0, jest uruchamiany ponownie, a następnie jest kontynuowana z konfiguracją. 
 
### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak włączyć ochrony przed złośliwym oprogramowaniem w zestawu skali maszyny wirtualnej

Aby włączyć funkcję ochrony przed złośliwym kodem na zestaw skali maszyny wirtualnej, skorzystaj z następującego przykładu środowiska PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-thats-hosted-in-a-private-storage-account-the-script-runs-successfully-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signature-sas-it-fails-this-message-is-displayed-missing-mandatory-parameters-for-valid-shared-access-signature-linksas-works-fine-from-my-local-browser"></a>Musisz wykonać niestandardowego skryptu, który znajduje się na koncie magazynu prywatnych. Skrypt zostanie uruchomiony pomyślnie, jeśli magazyn jest publiczny, ale podjęto próbę użycia udostępnionych podpis dostępu (SAS), działanie nie powiodło się. Ten komunikat jest wyświetlany: "Brak parametrów obowiązkowych współużytkowanego podpisu dostępu". Łącze + SAS działa prawidłowo z przeglądarki sieci lokalnej.

Do wykonania niestandardowego skryptu, który znajduje się na koncie magazynu prywatne, skonfigurować ustawienia chronionego, klucz konta magazynu oraz nazwę. Aby uzyskać więcej informacji, zobacz [niestandardowe rozszerzenie skryptu systemu Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).


## <a name="networking"></a>Networking
 
### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Czy jest możliwe do przypisania grupy zabezpieczeń sieci (NSG) do zestawu skalowania, że jest ona stosowana do wszystkich kart maszyny Wirtualnej w zestawie?

Tak. Grupa zabezpieczeń sieci można zastosować bezpośrednio do skali ustawione przez odwołuje się do sekcji Networkinterfaceconfiguration profilu sieciowego. Przykład:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                 }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak wykonać wymiany wirtualnych adresów IP dla zestawów skalowania maszyn wirtualnych w tej samej subskrypcji i tego samego regionu?

Jeśli użytkownik ma dwa zestawy skalowania maszyny wirtualnej z przodu końców modułu równoważenia obciążenia Azure i znajdują się w tej samej subskrypcji i regionu, można cofnięcie przydziału publicznych adresów IP z każdej z nich i przypisz do drugiego. Zobacz [wymiany wirtualnych adresów IP: zielony niebieski wdrożenia usługi Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) np. To oznacza opóźnienie poziomu jednak zasoby są alokację/przydzielane w sieci. Opcja szybsza jest brama aplikacji w usłudze Azure za pomocą dwóch pul zaplecza i reguły routingu. Alternatywnie można udostępniać aplikacji z [usługi aplikacji Azure](https://azure.microsoft.com/services/app-service/) który zapewnia obsługę szybkie przełączanie między miejscami tymczasową i produkcyjną.
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Sposób określania zakresu prywatnych adresów IP do użycia na potrzeby statycznego prywatnego przydzielanie adresów IP?

Adresy IP są wybierane w podsieci, który określisz. 

Metoda alokacji adresów IP zestawu skali maszyny wirtualnej jest zawsze "dynamiczny", ale nie oznacza to, że można zmienić tych adresów IP. W takim przypadku "dynamiczny" tylko oznacza, że nie określaj adresu IP w żądaniu PUT. Określanie statycznego skonfigurowane za pomocą podsieci. 
    
### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Jak wdrożyć skalowania maszyny wirtualnej, ustaw do istniejącej sieci wirtualnej platformy Azure? 

Aby wdrożyć skalowania maszyny wirtualnej, ustaw do istniejącej sieci wirtualnej platformy Azure, zobacz [Wdróż zestawu skalowania maszyn wirtualnych do istniejącej sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet). 

### <a name="how-do-i-add-the-ip-address-of-the-first-vm-in-a-virtual-machine-scale-set-to-the-output-of-a-template"></a>Jak dodać adres IP pierwsza maszyna wirtualna w skali maszyny wirtualnej ustawić dane wyjściowe szablonu?

Aby dodać adres IP pierwsza maszyna wirtualna w skali maszyny wirtualnej ustawić dane wyjściowe szablonu, zobacz [usługi Azure Resource Manager: prywatnych adresów IP zestawach skali maszyn wirtualnych Get](http://stackoverflow.com/questions/42790392/arm-get-vmsss-private-ips).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Przyspieszony sieciowych można używać zestawów skalowania?

Tak. Aby skorzystać z przyspieszonego sieci, ustaw enableAcceleratedNetworking na true w skali sieci ustawienia Networkinterfaceconfiguration zestawu. Na przykład:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
        "name": "niconfig1",
        "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
                ]
            }
            }
        ]
        }
    }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak można skonfigurować serwery DNS używane przez zestaw skali

Aby utworzyć ustawiony za pomocą niestandardowej konfiguracji DNS skali maszyny wirtualnej, należy dodać pakiet JSON dnsSettings do sekcji Networkinterfaceconfiguration zestawu skalowania. Przykład:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak można skonfigurować skali wartość do przypisania do publicznego adresu IP do każdej maszyny Wirtualnej?

Aby utworzyć zestaw skali maszyny wirtualnej, która przypisuje publicznego adresu IP do każdej maszyny Wirtualnej, upewnij się, że wersja interfejsu API zasobów Microsoft.Compute/virtualMachineScaleSets jest 2017-03-30, a następnie dodaj _publicipaddressconfiguration_ JSON pakiet do skali ustawić sekcji elementów Ipconfiguration. Przykład:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Można skonfigurować skali ustawiony do pracy z wielu bram aplikacji?

Tak. Można dodać zasobu identyfikator przez wiele pul adresów zaplecza bramy aplikacji do _applicationGatewayBackendAddressPools_ na liście _Ipconfiguration_ części na skalę ustawić sieć profil.

## <a name="scale"></a>Skalowanie

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>W przypadku jakich może utworzyć skali maszyny wirtualnej ustawić z mniej niż dwóch maszyn wirtualnych?

Jednym z powodów tworzenia skali maszyny wirtualnej ustawić z mniej niż dwóch maszyn wirtualnych jest używany elastycznej właściwości zestawu skali maszyny wirtualnej. Na przykład można wdrożyć skali maszyny wirtualnej ustawić zero maszyn wirtualnych do definiowania infrastruktury bez płatności koszty maszyny Wirtualnej. Następnie gdy wszystko będzie gotowe do wdrożenia maszyn wirtualnych, zwiększyć "pojemność" skali maszyny wirtualnej ustawić liczby wystąpień w środowisku produkcyjnym.

Kolejny powód, które można tworzyć skali maszyny wirtualnej ustawić z mniej niż dwóch maszyn wirtualnych jest, jeśli jesteś danego mniej dostępności niż przy użyciu zestawu z maszynami wirtualnymi odrębny dostępności. Zestawy skalowania maszyny wirtualnej nadaj sposób pracy z jednostkami niesortowalne obliczeniowe, które są zamienne. Ta jednolitość jest główną różnicą dla zestawy skalowania maszyny wirtualnej i zestawów dostępności. Wiele obciążeń bezstanowych nie Śledź pojedyncze jednostki. Spadnie obciążenie, można skalować w dół do obliczeń jednej jednostki i następnie skalowanie w górę do wielu podczas zwiększa obciążenie.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Jak zmienić liczbę maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Aby zmienić liczbę maszyn wirtualnych w skali maszyny wirtualnej w portalu Azure, w skali maszyny wirtualnej ustawić sekcję właściwości, kliknij bloku "Skalowanie" i za pomocą suwaka. Aby uzyskać inny sposób zmienić liczbę wystąpień, zobacz [Zmień liczbę wystąpień zestawu skali maszyny wirtualnej](https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/).

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak określić niestandardowe alerty po osiągnięciu progów niektórych?

Masz pewną swobodę określania w sposób obsługi alertów dla określonej wartości progowe. Na przykład można zdefiniować niestandardowych elementów webhook. W poniższym przykładzie elementu webhook jest z szablonem usługi Resource Manager:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ],
```

W tym przykładzie alert przechodzi do Pagerduty.com po osiągnięciu progu.



## <a name="patching-and-operations"></a>Operacje i stosowanie poprawek

### <a name="how-do-i-create-a-scale-set-in-an-existing-resource-group"></a>Jak utworzyć skali w istniejącej grupie zasobów?

Tworzenie zestawów skali w istniejący zasób grupy nie jest jeszcze możliwe z portalu Azure, ale można określić istniejącą grupę zasobów, podczas wdrażania skali ustawiony na podstawie szablonu usługi Azure Resource Manager. Można również określić istniejącą grupę zasobów podczas tworzenia skali ustawić za pomocą programu Azure PowerShell lub interfejsu wiersza polecenia.

### <a name="can-we-move-a-scale-set-to-another-resource-group"></a>Możemy przenieść skali ustawioną w innej grupie zasobów?

Tak, można przenieść zestawu skalowania zasobów do nowej subskrypcji lub grupy zasobów.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak można aktualizować moje skalowania maszyny wirtualnej ustawić nowy obraz? Jak zarządzać stosowanie poprawek

Aktualizacja Twojego skalowania maszyny wirtualnej ustawić nowy obraz i zarządzanie stosowanie poprawek, zobacz [Uaktualnij zestaw skali maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Aby zresetować maszyny Wirtualnej bez zmiany obrazu można używać operacja odtworzenia z obrazu? (Czyli chcę zresetować maszyny Wirtualnej do ustawień fabrycznych, a nie na nowy obraz.)

Za pomocą operacji odtworzenia z obrazu można tak, zresetuj maszyny Wirtualnej bez modyfikowania obrazu. Jednak jeśli zestawu skalowania maszyn wirtualnych, sieci odwołuje się do obrazu platformy z `version = latest`, maszyny Wirtualnej można zaktualizować do nowszej obrazu systemu operacyjnego podczas wywoływania `reimage`.

Aby uzyskać więcej informacji, zobacz [Zarządzanie wszystkich maszyn wirtualnych w zestawie skalowania maszyn wirtualnych](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set).

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-log-analytics"></a>Czy jest możliwe zestawy skalowania jest integrowana z Azure Log Analytics?

Tak, można zainstalować rozszerzenia analizy dzienników na skali ustawić maszyn wirtualnych. Oto przykład wiersza polecenia platformy Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Wymagane workspaceId i workspaceKey można znaleźć w portalu OMS. Na stronie przeglądu kliknij Kafelek ustawienia. Kliknij kartę połączonych źródeł u góry.

Uwaga: Jeśli ustawione na skalę _upgradePolicy_ jest ustawiony na ręczny, należy zastosować rozszerzenia na wszystkich maszynach wirtualnych w zestawie przez wywołanie metody uaktualniania na nich. W przypadku interfejsu wiersza polecenia to _vmss az update wystąpienia_.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak włączyć diagnostyki rozruchu

Aby włączyć funkcję diagnostyki rozruchu, należy najpierw utworzyć konto magazynu. Następnie, umieść ten blok JSON do zestawu skalowania maszyny wirtualnej **virtualMachineProfile**i zaktualizuj zestaw skali maszyny wirtualnej:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Po utworzeniu nowej maszyny Wirtualnej, właściwość InstanceView maszyny wirtualnej zawiera szczegóły zrzut ekranu i tak dalej. Oto przykład:
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```


## <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak uzyskać informacje dotyczące właściwości dla każdej maszyny Wirtualnej bez wykonywania wywołań wielu? Na przykład jak może uzyskać domeny błędów dla każdego 100 maszyn wirtualnych w zestawu skali maszyny wirtualnej?

Aby uzyskać informacje dotyczące właściwości dla każdej maszyny Wirtualnej bez wykonywania wielu wywołań, można wywołać `ListVMInstanceViews` za pomocą interfejsu API REST `GET` na następującego identyfikatora URI zasobu:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych można przekazać argumenty inne rozszerzenie?

Nie, nie można przekazać argumenty inne rozszerzenie do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Jednak rozszerzenia może działać na unikatowy właściwości maszyny wirtualnej jest uruchomiona w taki sposób jak nazwa komputera. Rozszerzenia także można zbadać metadanych wystąpienia na http://169.254.169.254 Aby uzyskać więcej informacji o maszynie Wirtualnej.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Dlaczego są luki pomiędzy Moje nazw maszyn maszyny Wirtualnej zestawu skali maszyny wirtualnej i identyfikatory maszyny Wirtualnej Na przykład: 0, 1, 3...

Brak przerw między nazw maszyn maszyny Wirtualnej zestawu skali maszyny wirtualnej i identyfikatory maszyny Wirtualnej ponieważ zestawu skalowania maszyn wirtualnych, sieci **nadmiernej aprowizacji** właściwość ma ustawioną wartość domyślną **true**. Jeśli ustawiono nadmiarowe Inicjowanie obsługi administracyjnej **true**, VMs więcej niż żądana są tworzone. Dodatkowe maszyny wirtualne są usuwane. W takim przypadku uzyskania wdrożenia większą niezawodność, ale kosztem ciągły nazywania i ciągłe translatora adresów sieciowych (NAT) reguły. 

Tę właściwość można ustawić, **false**. Dla zestawów skalowania małych maszyny wirtualnej to nie znacząco wpłynąć na niezawodność wdrożenia.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaka jest różnica między usuwanie maszyny Wirtualnej w zestawie skalowania maszyn wirtualnych i cofanie przydziału maszyny Wirtualnej? Kiedy należy używać warunkujące?

Podstawowa różnica między usuwanie maszyny Wirtualnej w zestawie skalowania maszyn wirtualnych i cofanie przydziału maszyny Wirtualnej jest to, że `deallocate` nie powoduje usunięcia wirtualnych dysków twardych (VHD). Są skojarzone z uruchomioną kosztów magazynowania `stop deallocate`. Można użyć jednej lub drugiej dla jednego z następujących powodów:

- Chcesz zatrzymać płatności kosztów obliczeń, ale aby zachować stan dysku maszyn wirtualnych.
- Chcesz uruchomić zestaw maszyn wirtualnych szybciej, niż można skalować w poziomie zestaw skali maszyny wirtualnej.
  - Dotyczących tego scenariusza zostały utworzone własnego aparatu skalowania automatycznego i chcesz szybsze skali end-to-end.
- Masz zestaw skali maszyny wirtualnej, która nierównomiernie jest dystrybuowana do domen błędów lub domen aktualizacji. Może to być, ponieważ selektywnie usunąć maszyn wirtualnych lub maszyn wirtualnych zostały usunięte po nadmiarowe Inicjowanie obsługi administracyjnej. Uruchomiona `stop deallocate` następuje `start` na maszynie wirtualnej równomiernie zestaw skalowania rozdziela maszyn wirtualnych w domenach awarii lub domen aktualizacji.

