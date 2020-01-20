---
title: Często zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych platformy Azure
description: Uzyskaj odpowiedzi na najczęściej zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych na platformie Azure.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: manayar
ms.openlocfilehash: 222f26febb7b14c627307295a8cdd68a17694d03
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275900"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Często zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych platformy Azure

Uzyskaj odpowiedzi na często zadawane pytania dotyczące zestawów skalowania maszyn wirtualnych na platformie Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>TOP — często zadawane pytania dotyczące zestawów skalowania

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Ile maszyn wirtualnych może się znajdować w zestawie skalowania?

Zestaw skalowania może zawierać od 0 do 1000 maszyn wirtualnych opartych na obrazach platformy lub 0-600 maszyn wirtualnych opartych na obrazach niestandardowych.

### <a name="are-data-disks-supported-within-scale-sets"></a>Czy zestawy skalowania obsługują dyski danych?

Tak. Zestaw skalowania może definiować konfigurację dołączonych dysków danych, która jest stosowana do wszystkich maszyn wirtualnych w zestawie. Aby uzyskać więcej informacji, zobacz [Zestawy skalowania na platformie Azure i dołączone dyski danych](virtual-machine-scale-sets-attached-disks.md). Oto przykłady innych opcji magazynowania danych:

* Pliki platformy Azure (dyski udostępnione za pośrednictwem protokołu SMB)
* Dysk systemu operacyjnego
* Dysk tymczasowy (lokalny, bez kopii zapasowej w usłudze Azure Storage)
* Usługa danych platformy Azure (na przykład tabele platformy Azure, obiekty blob platformy Azure)
* Zewnętrzne usługi danych (na przykład zdalna baza danych)

### <a name="which-azure-regions-support-scale-sets"></a>Które regiony świadczenia usługi Azure obsługują zestawy skalowania?

Wszystkie regiony obsługują zestawy skalowania.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Jak utworzyć zestaw skalowania za pomocą obrazu niestandardowego?

Tworzenie i przechwytywanie obrazu maszyny Wirtualnej, a następnie używać go jako źródło dla zestawu skalowania. Samouczek dotyczący sposobu tworzenia i używania niestandardowego obrazu maszyny Wirtualnej, można użyć [wiersza polecenia platformy Azure](tutorial-use-custom-image-cli.md) lub [programu Azure PowerShell](tutorial-use-custom-image-powershell.md)

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Jeśli zmniejszę pojemność zestawu skalowania z 20 do 15, które maszyny wirtualne zostaną usunięte?

W celu zapewnienia maksymalnej dostępności maszyny wirtualne są usuwane z zestawu skalowania równomiernie w domenach błędów i domenach aktualizacji. Najpierw są usuwane maszyny wirtualne o najwyższym identyfikatorze.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Co się stanie, jeśli następnie zwiększę pojemność z 15 do 18?

Zwiększenie pojemności do 18 spowoduje utworzenie 3 nowych maszyn wirtualnych. Za każdym razem następuje zwiększenie identyfikatora wystąpienia maszyny wirtualnej względem poprzedniej najwyższej wartości (na przykład 20, 21, 22). Maszyny wirtualne są równoważone w domenach błędów i domenach aktualizacji.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Czy mogę wymusić sekwencję wykonywania w przypadku korzystania z wielu rozszerzeń w zestawie skalowania?

Tak, można użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md)zestawu skalowania.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Czy zestawy skalowania współdziałają z zestawami dostępności platformy Azure?

Używa zestawu regionalnych skalowania (innych niż strefowej) *grup umieszczania*, który pełnić rolę zbiór niejawne dostępności z pięcioma domenami błędów i pięcioma domenami aktualizacji. Zestawy skalowania z ponad 100 maszyn wirtualnych obejmują wiele grup umieszczania. Aby uzyskać więcej informacji na temat grup umieszczania, zobacz [Praca z dużymi zestawami skalowania maszyn wirtualnych](virtual-machine-scale-sets-placement-groups.md). Zestaw dostępności maszyn wirtualnych może znajdować się w tej samej sieci wirtualnej co zestaw skalowania maszyn wirtualnych. Typowa konfiguracja polega na umieszczeniu maszyn wirtualnych węzła kontrolnego (często wymagających unikatowej konfiguracji) w zestawie dostępności, a węzłów danych w zestawie skalowania.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Czy zestawy skalowania współdziałają ze strefami dostępności platformy Azure?

Tak. Aby uzyskać więcej informacji, zobacz [strefy dokumentacji zestawu skalowania](./virtual-machine-scale-sets-use-availability-zones.md).


## <a name="autoscale"></a>Automatyczne skalowanie

### <a name="what-are-best-practices-for-azure-autoscale"></a>Jakie są najlepsze rozwiązania dotyczące skalowania automatycznego platformy Azure?

Najlepsze rozwiązania dotyczące automatycznego skalowania, zobacz [najlepsze rozwiązania dotyczące maszyn wirtualnych skalowania automatycznego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices).

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Gdzie znaleźć nazwy metryki automatycznego skalowania, który wykorzystuje oparte na hoście metryki?

Metryki nazw na potrzeby skalowania automatycznego, która wykorzystuje oparte na hoście metryki, zobacz [metryki obsługiwane z usługą Azure Monitor](https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/).

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Czy istnieją przykładami skalowania automatycznego na podstawie długości tematów i kolejek usługi Azure Service Bus?

Tak. Skalowanie automatyczne w oparciu o długości tematów i kolejek usługi Azure Service Bus, zobacz [typowe metryki automatycznego skalowania usługi Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/).

Kolejki usługi Service Bus można użyć w następujące dane JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Kolejki magazynu należy użyć następujące dane JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Zastąp przykładowe wartości zasobu Uniform Resource Identifier (URI).


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Czy należy skalowania automatycznego za pomocą metryk opartych na hoście lub rozszerzenie diagnostyki?

Ustawienia automatycznego skalowania można tworzyć na Maszynę wirtualną do używania metryki na poziomie hosta lub metryki na podstawie systemu operacyjnego gościa.

Aby uzyskać listę obsługiwanych metryk, zobacz [typowe metryki automatycznego skalowania usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics).

Aby uzyskać pełny przykład dla zestawów skalowania maszyn wirtualnych, zobacz [konfiguracji Zaawansowane automatyczne skalowanie przy użyciu szablonów usługi Resource Manager dla zestawów skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets).

W przykładzie użyto pomiar procesora CPU na poziomie hosta i metryki liczby komunikatów.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Jak ustawić reguły alertów w zestawie skalowania maszyn wirtualnych?

Można tworzyć alerty dotyczące metryk dla zestawów skalowania maszyn wirtualnych za pomocą programu PowerShell lub wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [przykłady szybkiego startu usługi Azure Monitor PowerShell](https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules) i [przykładowych Szybki Start interfejsu wiersza polecenia dla wielu platform Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts).

Element TargetResourceId zestawu skalowania maszyn wirtualnych wygląda następująco:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

Wszelkie licznika wydajności maszyny Wirtualnej można wybrać jako metrykę, aby ustawić alert w przypadku. Aby uzyskać więcej informacji, zobacz [metryk systemu operacyjnego gościa dla maszyn wirtualnych z Windows opartych na usłudze Resource Manager](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-resource-manager-based-windows-vms) i [metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Linux](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#guest-os-metrics-linux-vms) w [typowe metryki automatycznego skalowania usługi Azure Monitor](https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/)artykułu.

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Jak skonfigurować automatyczne skalowanie w skali maszyny wirtualnej ustawić przy użyciu programu PowerShell?

Aby skonfigurować automatyczne skalowanie w skali maszyny wirtualnej ustawić przy użyciu programu PowerShell, zobacz [automatyczne skalowanie zestawu skalowania maszyn wirtualnych](tutorial-autoscale-powershell.md). Można również skonfigurować automatyczne skalowanie przy użyciu [wiersza polecenia platformy Azure](tutorial-autoscale-cli.md) i [szablonów platformy Azure](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Jeśli została zatrzymana (przydział zostanie cofnięty) maszyny Wirtualnej, jest tej maszyny Wirtualnej uruchomiony jako część operacji skalowania automatycznego?

Nie. Jeśli reguł skalowania automatycznego wymaga dodatkowych wystąpień maszyn wirtualnych w ramach zestawu skalowania, tworzone jest nowe wystąpienie maszyny Wirtualnej. Wystąpienia maszyn wirtualnych, które zostały zatrzymane (cofnięty przydział) nie są uruchamiane w ramach zdarzenia skalowania automatycznego. Jednak te zatrzymano (cofnięto przydział) maszyny wirtualne mogą zostać usunięte w ramach zdarzenia skalowania automatycznego, która skaluje się w liczbie wystąpień, identyfikator taki sam sposób, że dowolne wystąpienie maszyny Wirtualnej mogą zostać usunięte, na podstawie kolejności wystąpienia maszyny Wirtualnej.



## <a name="certificates"></a>Certyfikaty

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Jak bezpiecznie wysłać certyfikatu do maszyny Wirtualnej?

Bezpieczne dostarczanie certyfikatu do maszyny Wirtualnej, można zainstalować certyfikat klienta bezpośrednio do magazynu certyfikatów Windows z usługi key vault klienta.

Użyj następujące dane JSON:

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

Kod obsługuje Windows i Linux.

Aby uzyskać więcej informacji, zobacz [tworzenia lub aktualizacji zestawu skalowania maszyn wirtualnych](https://msdn.microsoft.com/library/mt589035.aspx).


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Jak mogę używać certyfikatów z podpisem własnym dla klastrów usługi Azure Service Fabric?
Aby zapoznać się z najnowszym przykładem, użyj następującej instrukcji interfejsu wiersza polecenia platformy Azure w usłudze Azure Shell. Przeczytaj przykładową dokumentację modułu interfejsu wiersza polecenia usługi Service Fabric, która zostanie wydrukowany w stdout:

```bash
az sf cluster create -h
```

Certyfikatów z podpisem własnym nie można używać w przypadku dystrybuowanej relacji zaufania udostępnianej przez urząd certyfikacji i nie należy jej używać w żadnym klastrze Service Fabric przeznaczonym do hostowania rozwiązań produkcyjnych przedsiębiorstwa. Aby uzyskać dodatkowe Service Fabric wskazówki dotyczące zabezpieczeń, zapoznaj się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) dotyczącymi zabezpieczeń Azure Service Fabric i [Service Fabric scenariuszami zabezpieczeń klastra](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/).

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Można określić pary kluczy SSH do uwierzytelniania SSH przy użyciu systemu Linux maszyny wirtualnej zestawu skalowania na podstawie szablonu usługi Resource Manager?

Tak. Interfejs API REST **osProfile** jest podobny do standardowego interfejsu API REST maszyny Wirtualnej.

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

Ten blok JSON jest używany w [tym szablonie szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

Aby uzyskać więcej informacji, zobacz [tworzenia lub aktualizacji zestawu skalowania maszyn wirtualnych](https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration).

### <a name="how-do-i-remove-deprecated-certificates"></a>Jak usunąć certyfikaty przestarzałe?

Aby usunąć przestarzałe certyfikaty, należy usunąć starego certyfikatu z listy certyfikatów w magazynie. Pozostaw wszystkie certyfikaty, które chcesz zachować na komputerze, na liście. To nie powoduje usunięcia certyfikatu na wszystkich maszynach wirtualnych. Ponadto nie dodaje certyfikat do nowych maszyn wirtualnych, które są tworzone w zestawie skalowania maszyn wirtualnych.

Aby usunąć certyfikat z istniejących maszyn wirtualnych, Użyj niestandardowego rozszerzenia skryptu, aby ręcznie usunąć certyfikaty z magazynu certyfikatów.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Jak wstawić istniejącego publicznego klucza SSH do warstwy SSH zestaw skali maszyny wirtualnej podczas inicjowania obsługi

Maszyny wirtualne są udostępniane tylko w przypadku publiczny klucz SSH, nie trzeba umieścić kluczy publicznych w usłudze Key Vault. Klucze prywatne nie są poufne.

Można podać publicznymi kluczami SSH w postaci zwykłego tekstu, podczas tworzenia maszyny Wirtualnej z systemem Linux:

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
}
```

Nazwa elementu linuxConfiguration | Wymagane | Typ | Opis
--- | --- | --- | ---
SSH | Nie | Collection | Określa konfigurację kluczy SSH w systemie operacyjnym Linux
Ścieżka | Tak | Ciąg | Określa ścieżkę pliku systemu Linux, gdzie klucze SSH lub certyfikatu powinien być zlokalizowany
Kontenerem | Tak | Ciąg | Określa klucz publiczny SSH algorytmem Base64

Aby uzyskać przykład, zobacz [szablon szybkiego startu usługi GitHub 101-vm-sshkey](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json).

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Po uruchomieniu `Update-AzVmss` po dodaniu więcej niż jeden certyfikat z magazynu kluczy, w tym samym, I zostanie wyświetlony następujący komunikat:

>Polecenie Update-AzVmss: list Secret zawiera powtórzone wystąpienia/subscriptions/\<my-Subscription-ID >/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev, która jest niedozwolona.

Może to nastąpić, jeśli zostanie podjęta próba ponownego dodania w tym samym magazynie, a nie przy użyciu nowego certyfikatu w magazynie dla istniejącego magazynu źródłowego. `Add-AzVmssSecret` Polecenie nie działa poprawnie, w przypadku dodawania dodatkowych wpisów tajnych.

Aby dodać więcej wpisów tajnych z tego samego magazynu kluczy, należy zaktualizować listę $vmss.properties.osProfile.secrets[0].vaultCertificates.

Do oczekiwanej struktury danych wejściowych, zobacz [tworzenia lub aktualizacji zestawu maszyn wirtualnych](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Znajdź klucz tajny w obiekcie zestaw skali maszyny wirtualnej, który znajduje się w magazynie kluczy. Następnie należy dodać odwołania certyfikatów (adres URL i nazwa magazynu wpisów tajnych) do listy skojarzonej z magazynem.

> [!NOTE]
> Obecnie nie można usunąć certyfikatów z maszyn wirtualnych za pomocą interfejsu API zestawu skalowania maszyn wirtualnych.
>

Nowe maszyny wirtualne nie zostaną starego certyfikatu. Maszyny wirtualne w tym certyfikatu, które są już wdrożone będzie jednak starego certyfikatu.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Czy można wypchnąć certyfikatów, do zestawu bez podawania hasła, gdy certyfikat znajduje się w magazynie wpisu tajnego skalowania maszyn wirtualnych?

Nie musisz kodować sprzętowo hasła w skryptach. Możesz dynamicznie pobrać hasła, za pomocą uprawnień, których można używać do uruchamiania skryptu wdrażania. Jeśli masz skrypt, który przenosi certyfikatu z kluczem magazynu wpisów tajnych magazyn, magazynu wpisów tajnych `get certificate` polecenie również generuje hasło pliku pfx.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Jak właściwość wpisów tajnych virtualMachineProfile.osProfile skalowania maszyny wirtualnej ustawić pracy? Dlaczego należy wartość sourceVault, gdy trzeba określić bezwzględny identyfikator URI dla certyfikatu, używając właściwości certificateUrl?

Odwołanie certyfikatu Windows Remote Management (WinRM) musi być obecny we właściwości wpisy tajne profilu systemu operacyjnego.

Źródłowy magazyn wskazujący ma na celu wymuszenia zasad listę kontroli dostępu (ACL) kontroli dostępu, które istnieją w modelu usługi w chmurze Azure użytkownika. Jeśli źródłowy magazyn nie jest określona, użytkownicy, którzy nie mają uprawnień do wdrażania lub dostęp do danych poufnych do magazynu kluczy będzie można za pomocą obliczeń zasobów dostawcy (CRP). Listy ACL istnieje nawet w przypadku zasobów, które nie istnieją.

Jeśli podasz identyfikator magazynu nieprawidłowe źródło, ale adres URL prawidłowy magazyn kluczy, błąd jest zgłaszany, gdy sondowanie operacji.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Jeśli klucze tajne I dodać do istniejącej skalowania maszyn wirtualnych z zestawu, wpisy tajne, które są wstrzykiwane do istniejących maszyn wirtualnych lub tylko do nowych?

Certyfikaty są dodawane do wszystkich maszyn wirtualnych, nawet wstępnie istniejące. Jeśli właściwość upgradePolicy w zestawie skalowania maszyn wirtualnych jest ustawiony na **ręczne**, certyfikat zostanie dodany do maszyny Wirtualnej, gdy wykonujesz ręcznej aktualizacji na maszynie Wirtualnej.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Gdzie umieścić certyfikaty dla maszyn wirtualnych z systemem Linux?

Aby dowiedzieć się, jak wdrożyć certyfikaty dla maszyn wirtualnych systemu Linux, zobacz [wdrażanie certyfikatów na maszynach wirtualnych z magazynu kluczy zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/).

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Jak dodać nowy certyfikat z magazynu do nowego obiektu certyfikatu?

Aby dodać certyfikat z magazynu do istniejącego wpisu tajnego, zobacz poniższy przykład programu PowerShell. Użyj tylko jeden obiekt wpisu tajnego.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Co stanie się certyfikatów, jeśli można odtworzyć z obrazu maszyny Wirtualnej?

Jeśli możesz odtworzyć z obrazu maszyny Wirtualnej certyfikaty zostaną usunięte. Odtwarzanie z obrazu dysku usuwa całego systemu operacyjnego.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Co się stanie po usunięciu certyfikatu z usługi key vault?

Jeśli klucz tajny został usunięty z usługi key vault, a następnie uruchom `stop deallocate` dla wszystkich maszyn wirtualnych i uruchom je ponownie, wystąpi awaria. Błąd występuje, ponieważ rozwiązanie CRP musi pobrać Wpisy tajne z magazynu kluczy, ale nie jest. W tym scenariuszu należy usunąć certyfikaty z modelu zestawu skalowania maszyn wirtualnych.

Składnik CRP nie utrwala wpisów tajnych klienta. Jeśli uruchamiasz `stop deallocate` dla wszystkich maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, pamięć podręczna zostanie usunięta. W tym scenariuszu wpisów tajnych są pobierane z usługi key vault.

Nie wystąpi ten problem, skalowania w poziomie, ponieważ nie istnieje w pamięci podręcznej kopię klucza tajnego w usłudze Azure Service Fabric (w modelu sieci szkieletowej pojedynczej dzierżawy).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Dlaczego trzeba określić wersję certyfikatu podczas korzystania z usługi Key Vault?

Wymaganie usługi Key Vault, aby określić wersję certyfikatu ma na celu ułatwiają wyczyść użytkownikowi jakie certyfikat jest wdrażany na swoich maszynach wirtualnych.

Jeśli tworzenie maszyny Wirtualnej, a następnie zaktualizuj klucz tajny w usłudze key vault, nowy certyfikat nie zostanie pobrana do maszyn wirtualnych. Ale maszyny wirtualne wydaje się odwoływać się do niego i nowych maszyn wirtualnych Uzyskaj nowy klucz tajny. Aby tego uniknąć, należy odwołać się wersja wpisu tajnego.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mój zespół w programach kilka certyfikatów, które są dystrybuowane do nas jako klucze publiczne cer. Co to jest ustawiony Zalecanym podejściem do wdrażania tych certyfikatów skalowania maszyn wirtualnych?

Aby wdrożyć cer zestawu kluczy publicznych do skalowania maszyn wirtualnych, możesz wygenerować plik PFX, który zawiera tylko pliki cer. Aby to zrobić, należy użyć `X509ContentType = Pfx`. Na przykład Załaduj plik cer jako obiekt x509Certificate2 w języku C# lub programu PowerShell, a następnie wywołaj metodę.

Aby uzyskać więcej informacji, zobacz [metoda X509Certificate.Export (X509ContentType, String)](https://msdn.microsoft.com/library/24ww6yzk(v=vs.110.aspx)).

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Jak mogę przekazać certyfikaty jako ciągi Base64?

Aby emulować przekazywanie w certyfikacie jako ciąg base64, można wyodrębnić najnowsze adres URL o określonej w szablonie usługi Resource Manager. Dołącz następującą właściwość JSON w szablonie usługi Resource Manager:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Czy muszę opakować certyfikaty w obiekty JSON w magazynach kluczy?

W zestawach skalowania maszyn wirtualnych i maszyn wirtualnych certyfikaty muszą być ujęte w obiektów JSON.

Obsługiwane jest również typem zawartości application/x-pkcs12.

Obecnie nie obsługujemy pliki cer. Aby użyć pliki cer, należy wyeksportować je do kontenerów pfx.



## <a name="compliance-and-security"></a>Zgodności i zabezpieczeń

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Czy na zgodne ze standardem PCI zestawów skalowania maszyn wirtualnych?

Zestawy skalowania maszyn wirtualnych to cienka warstwa interfejsu API na rozwiązaniu CRP. Obydwa składniki są częścią platformy obliczeniowej w drzewie usług platformy Azure.

Z punktu widzenia zgodności zestawy skalowania maszyn wirtualnych są integralną częścią platformy obliczeniowej Azure. Współużytkują one zespół, narzędzia, procesy, metodologię wdrażania, kontrolę zabezpieczeń kompilację dokładnie na czas (JIT), monitorowanie, alerty itd. z rozwiązaniem CRP. Zestawy skalowania maszyn wirtualnych są zgodne ze standardem PCI (Payment Card Industry), ponieważ rozwiązanie CRP jest częścią bieżącego atestu PCI Data Security Standard (DSS).

Aby uzyskać więcej informacji, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/TrustCenter/Compliance/PCI).

### <a name="does-managed-identities-for-azure-resourceshttpsdocsmicrosoftcomazureactive-directorymsi-overview-work-with-virtual-machine-scale-sets"></a>Jest [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/msi-overview) pracy z zestawami skalowania maszyn wirtualnych?

Tak. Możesz zobaczyć przykładowe szablony MSI w szablonach szybkiego startu platformy Azure dla [systemów](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) i Windows.

## <a name="deleting"></a>Usuwanie 

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Czy po usunięciu wystąpień będą przestrzegane blokady ustawione w miejscu dla wystąpień zestawu skalowania maszyn wirtualnych?

W witrynie Azure Portal można usunąć pojedyncze wystąpienie lub usuwanie zbiorcze, zaznaczając wiele wystąpień. Jeśli podjęto próbę usunięcia pojedynczego wystąpienia, które ma zablokowaną blokadę, blokada jest przestrzegana i nie będzie można usunąć wystąpienia. Jeśli jednak zbiorczo wybierzesz wiele wystąpień, a dowolne z tych wystąpień ma blokadę, blokady nie będą przestrzegane, a wszystkie wybrane wystąpienia zostaną usunięte. 
 
W interfejsie wiersza polecenia platformy Azure można tylko usunąć pojedyncze wystąpienie. Jeśli podjęto próbę usunięcia pojedynczego wystąpienia, które ma zablokowaną blokadę, blokada jest przestrzegana i nie będzie można usunąć tego wystąpienia. 

## <a name="extensions"></a>Rozszerzenia

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Jak usunąć rozszerzenie zestawu skalowania maszyn wirtualnych?

Aby usunąć rozszerzenie zestawu skalowania maszyn wirtualnych, skorzystaj z następującego przykładu programu PowerShell:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

Można znaleźć wartości extensionName w `$vmss`.

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Czy istnieje przykład szablonu zestawu skalowania maszyn wirtualnych, który integruje się z dziennikami Azure Monitor?

Przykład szablonu zestawu skalowania maszyn wirtualnych, który integruje się z dziennikami Azure Monitor, znajduje się w drugim przykładzie w temacie [wdrażanie klastra Service Fabric platformy Azure i włączanie monitorowania przy użyciu dzienników Azure monitor](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Jak dodać rozszerzenie do wszystkich maszyn wirtualnych w zestawie skalowania mojej maszyny wirtualnej

Jeśli ustawiono zasady aktualizacji **automatyczne**, ponownego wdrażania szablonu z nowymi właściwościami rozszerzenia aktualizacji wszystkich maszyn wirtualnych.

Jeśli ustawiono zasady aktualizacji **ręczne**, najpierw zaktualizuj rozszerzenie, a następnie ręcznie zaktualizować wszystkie wystąpienia w maszynach wirtualnych.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Jeśli rozszerzenia skojarzone z istniejącego zestawu skalowania maszyn wirtualnych zostaną zaktualizowane, czy istniejące maszyny wirtualne, których dotyczy problem?

Jeśli definicja rozszerzenia w skali maszyny wirtualnej ustawić modelu zostanie zaktualizowany, i właściwością upgradePolicy **automatyczne**, aktualizuje maszyny wirtualne. Jeśli ustawiono właściwość upgradePolicy **ręczne**, rozszerzenia są oznaczane jako niezgodne modelu.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Czy rozszerzenia są uruchamiane ponownie, gdy istniejąca maszyna jest zaleczona lub odtworzona z obrazu?

Jeśli istniejąca maszyna wirtualna jest zaleczona, zostanie wyświetlona jako ponowny rozruch, a rozszerzenia nie zostaną uruchomione ponownie. Jeśli maszyna wirtualna zostanie odtworzony z obrazu, proces przypomina zastąpienie dysku systemu operacyjnego obrazem źródłowym. Wszystkie specjalizacje z najnowszego modelu, takie jak rozszerzenia, są uruchamiane ponownie.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Jak dołączyć skalowania maszyn wirtualnych z zestawu do domeny usługi Active Directory?

Aby przyłączyć skalowania maszyn wirtualnych z zestawu do domeny usługi Active Directory (AD), można zdefiniować rozszerzenie.

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

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>Moje rozszerzenie zestawu skalowania maszyn wirtualnych próbuje zainstalować coś, co wymaga ponownego uruchomienia.

Jeśli Twoje rozszerzenie zestawu skalowania maszyn wirtualnych próbuje zainstalować coś, co wymaga ponownego uruchomienia, można użyć rozszerzenia Desired State Configuration automatyzacji platformy Azure (Automation DSC). W przypadku systemu operacyjnego Windows Server 2012 R2 Azure ściąga w Instalatorze Windows Management Framework (WMF) 5.0 jest uruchamiany ponownie, a następnie kontynuuje tworzenie konfiguracji.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Jak włączyć ochrony przed złośliwym oprogramowaniem w mojego zestawu skalowania maszyn wirtualnych

Aby włączyć funkcję ochrony przed złośliwym kodem w zestawie skalowania maszyn wirtualnych, skorzystaj z poniższego przykładu programu PowerShell:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Jak mogę wykonać skrypt niestandardowy, który jest hostowany na prywatnym koncie magazynu?

Do wykonywania niestandardowego skryptu, który znajduje się na koncie magazynu prywatnego, skonfigurować ustawienia chronionego, za pomocą klucza konta magazynu i nazwa. Aby uzyskać więcej informacji, zobacz [rozszerzenie niestandardowego skryptu](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings).

## <a name="passwords"></a>Hasła

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Jak zresetować hasło dla maszyn wirtualnych w zestawie skalowania mojej maszyny wirtualnej

Istnieją dwa główne sposoby, aby zmienić hasło dla maszyn wirtualnych w zestawach skalowania.

- Zmiana modelu zestawu skalowania maszyn wirtualnych bezpośrednio. Dostępne w przypadku interfejsu API 2017-12-01 i nowszych.

    Zaktualizuj poświadczenia administratora bezpośrednio w modelu zestawu skalowania (na przykład przy użyciu Eksploratora zasobów Azure, programu PowerShell lub interfejsu wiersza polecenia). Gdy zestaw skalowania jest zaktualizowane, wszystkie nowe maszyny wirtualne mają nowe poświadczenia. Istniejące maszyny wirtualne tylko mieć nowe poświadczenia, jeśli jest odtwarzany z obrazu.

- Zresetuj hasło przy użyciu rozszerzeń dostępu do maszyny Wirtualnej.

    Użyj poniższego przykładu z programu PowerShell:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Networking

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Jest możliwe do przypisania grupy zabezpieczeń sieci (NSG) do zestawu skalowania, tak aby dotyczyła wszystkich maszyn wirtualnych kart interfejsu sieciowego w zestawie?

Tak. Bezpośrednio do odwoływania się do niego w sekcji Dnssettings profil sieciowy zestawu skalowania można zastosować sieciową grupę zabezpieczeń. Przykład:

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
                            },
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

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Jak? wymiany wirtualnych adresów IP dla zestawów skalowania maszyn wirtualnych w tym samym regionie i tej samej subskrypcji

Jeśli masz dwa zestawy skalowania maszyn wirtualnych za pomocą usługi Azure Load Balancer Frontony i znajdują się w tej samej subskrypcji i regionu, może deallocate publiczne adresy IP z każdej z nich i przypisać do innego. Zobacz [wymiany wirtualnych adresów IP: wdrożenie niebieski zielony w usłudze Azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) na przykład. Pociąga to opóźnienia na poziomie jednak zasoby są z cofniętą alokacją/przydzielany w sieci. Opcja szybsza jest używać usługi Azure Application Gateway z dwóch pul zaplecza i reguły routingu. Alternatywnie można hostować swoją aplikację, za pomocą [usługi Azure App service](https://azure.microsoft.com/services/app-service/) który zapewnia obsługę szybkie przełączanie się między środowiskiem przejściowym i produkcyjnym miejscami.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Jak określić zakres prywatnych adresów IP do używania statycznych alokacji prywatnego adresu IP?

Adresy IP są wybierane w podsieci, który określisz.

Metoda alokacji adresy IP zestawu skalowania maszyn wirtualnych jest zawsze "dynamiczne", ale nie oznacza to, że te adresy IP można zmienić. W tym przypadku "dynamiczne" oznacza jedynie, czy nie określisz adresu IP w żądaniu PUT. Określ statyczne ustawić przy użyciu tej podsieci.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Jak wdrożyć maszynę wirtualną zestawie skalowania do istniejącej sieci wirtualnej platformy Azure?

Aby wdrożyć maszynę wirtualną zestawie skalowania do istniejącej sieci wirtualnej platformy Azure, zobacz [wdrażanie zestawu skalowania maszyn wirtualnych do istniejącej sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Dzięki przyspieszonej sieci można używać zestawów skalowania?

Tak. Aby korzystać z przyspieszonej łączności sieciowej, właściwości enableAcceleratedNetworking wartość true w zestawie skalowania ustawieniach networkInterfaceConfigurations zestawu. Na przykład:
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
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Jak można skonfigurować serwery DNS używane przez zestaw skalowania?

Aby utworzyć maszyny wirtualnej zestawu skalowania z niestandardową konfiguracją DNS, należy dodać pakiet dnsSettings JSON do sekcji networkInterfaceConfigurations zestawu skalowania. Przykład:
```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Jak można skonfigurować zestaw skalowania, aby przypisać publiczny adres IP do każdej maszyny Wirtualnej?

Aby utworzyć zestaw skalowania maszyn wirtualnych, który przypisuje publicznego adresu IP do każdej maszyny Wirtualnej, upewnij się, że wersja interfejsu API zasobu Microsoft.Compute/virtualMachineScaleSets to 2017-03-30, a następnie dodaj _publicipaddressconfiguration_ JSON pakiet do skalowania sekcji ipConfigurations zestawu. Przykład:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Można skonfigurować zestaw skalowania, aby pracować z wielu bram aplikacji?

Tak. Identyfikatory zasobów dla wielu pul adresów zaplecza Application Gateway można dodać do listy _applicationGatewayBackendAddressPools_ w obszarze elementy _ipconfiguration_ profilu sieciowego zestawu skalowania.

## <a name="scale"></a>Skalowanie

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>W przypadku, jakie może utworzyć maszyn wirtualnych zestawu skalowania z mniej niż dwie maszyny wirtualne?

Jednym z powodów do utworzenia maszyny wirtualnej zestawu skalowania z mniej niż dwie maszyny wirtualne są elastyczne właściwości zestawu skalowania maszyn wirtualnych. Na przykład można wdrożyć maszyny wirtualnej zestawu skalowania z bez maszyn wirtualnych w celu zdefiniowania infrastruktury bez ponoszenia koszty działania maszyny Wirtualnej. Następnie gdy wszystko jest gotowe do wdrożenia maszyn wirtualnych, należy zwiększyć wartość "pojemność" skalowania maszyn wirtualnych równa liczby wystąpień produkcyjnych.

Kolejnym powodem, czego można utworzyć maszyny wirtualnej zestawu skalowania z mniej niż dwie maszyny wirtualne jest, jeśli istnieje podejrzenie mniej o dostępności niż przy użyciu zestaw dostępności z dyskretnych maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych zapewniają sposób pracy z takimi samymi jednostkami obliczeniowymi, które są zamienne. Ta zgodność jest główną różnicą między zestawami dla zestawów skalowania maszyn wirtualnych i zestawów dostępności. Wielu bezstanowych obciążeń "nie Śledź" pojedyncze jednostki. Zmniejszenia obciążenia, można skalować w dół do jednej jednostki obliczeniowej, a następnie skalować w górę do wielu przypadku zwiększenia obciążenia.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Jak zmienić liczbę maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Aby zmienić liczbę maszyn wirtualnych w maszyny wirtualnej zestawu skalowania w witrynie Azure portal, od skalowania maszyn wirtualnych zestawu sekcji właściwości, kliknij w bloku "Skalowanie" i za pomocą suwaka.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Jak zdefiniować niestandardowe alerty dla po osiągnięciu określonych wartości progowych?

Masz pewną swobodę określania w sposób obsługi alertów dla określonej wartości progowe. Na przykład można zdefiniować niestandardowe elementy webhook. W poniższym przykładzie element webhook jest szablon usługi Resource Manager:

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
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
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
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Stosowanie poprawek i operacje

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Czy można utworzyć zestaw skalowania w istniejącej grupie zasobów?

Tak, można utworzyć zestaw skalowania w istniejącej grupie zasobów.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Czy mogę przenieść zestaw skalowania do innej grupy zasobów?

Tak, można przenieść zasobów zestawu skalowania na nową subskrypcję lub grupę zasobów.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Jak można zaktualizować mój zestaw skalowania maszyn wirtualnych do nowego obrazu? Jak zarządzać poprawkami

Aby zaktualizować swoje zestawu skalowania maszyn wirtualnych do nowego obrazu i Zarządzaj stosowaniem poprawek, zobacz [uaktualniania zestawu skalowania maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Czy można użyć operacji odtworzenia z obrazu, aby przywrócić Maszynę wirtualną bez wprowadzania zmian w obrazie? (Czyli chcę Resetuj Maszynę wirtualną do ustawień fabrycznych, a nie do nowego obrazu.)

Tak, można użyć operacji odtworzenia z obrazu, aby przywrócić Maszynę wirtualną bez wprowadzania zmian w obrazie. Jednakże, jeśli w zestawie skalowania maszyny wirtualnej odwołuje się obrazie platformy z `version = latest`, maszyny Wirtualnej można zaktualizować do nowszej obrazu systemu operacyjnego, podczas wywoływania `reimage`.

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Czy możliwe jest zintegrowanie zestawów skalowania z dziennikami Azure Monitor?

Tak, możesz zainstalować rozszerzenie Azure Monitor na maszynach wirtualnych zestawu skalowania. Oto przykład wiersza polecenia platformy Azure:
```
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```
Wymagany identyfikator workspaceId i klucz workspaceKey można znaleźć w obszarze roboczym usługi Log Analytics w witrynie Azure portal. Na stronie Przegląd kliknij Kafelek ustawienia. Kliknij kartę połączone źródła u góry.

> [!NOTE]
> Jeśli zestaw skalowania _upgradePolicy_ jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia to _az vmss update-instances_.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Jak włączyć diagnostykę rozruchu

Aby włączyć diagnostykę rozruchu, najpierw utwórz konto magazynu. Następnie umieść ten blok JSON w zestawie skalowania maszyn wirtualnych **virtualMachineProfile**i zaktualizuj zestaw skalowania maszyn wirtualnych:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Po utworzeniu nowej maszyny Wirtualnej InstanceView własności maszyny Wirtualnej zawiera szczegóły dotyczące zrzutów ekranu i tak dalej. Oto przykład:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Właściwości maszyny wirtualnej

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Jak uzyskać informacje o właściwości dla każdej maszyny Wirtualnej bez wprowadzania wielu wywołań? Na przykład jak będzie uzyskać domenę błędów dla każdej 100 maszyn wirtualnych w mojego zestawu skalowania maszyn wirtualnych?

Aby uzyskać informacje o właściwości dla każdej maszyny Wirtualnej bez wprowadzania wielu wywołań, można wywołać `ListVMInstanceViews` , wykonując interfejsu API REST `GET` na następujący identyfikator URI zasobu:

/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Argumenty inne rozszerzenie można przekazywać do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych?

Nie, nie można przekazać argumenty inne rozszerzenie do różnych maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Jednak rozszerzenia może działać w oparciu o unikatowe właściwości maszyny wirtualnej są uruchomione na takie jak nazwa maszyny. Rozszerzenia także można zbadać metadanych wystąpienia na http://169.254.169.254 Aby uzyskać więcej informacji o maszynie Wirtualnej.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Dlaczego są luki pomiędzy mojej nazwy maszyn maszyn wirtualnych zestawu skalowania maszyn wirtualnych i identyfikatory maszyn wirtualnych Na przykład: 0, 1, 3...

Istnieją luki między nazwy maszyn maszyn wirtualnych zestawu skalowania maszyn wirtualnych i identyfikatory maszyny Wirtualnej, ponieważ w zestawie skalowania maszyn wirtualnych **overprovision** właściwość jest ustawiona na wartość domyślną **true**. Jeśli ustawiono celi **true**, więcej maszyn wirtualnych, niż żądana są tworzone. Dodatkowe maszyny wirtualne są usuwane. W tym przypadku uzyskiwanie wdrożenia większą niezawodność, ale kosztem nazewnictwa ciągłej i ciągłego translacji adresów sieciowych (NAT) reguły.

Można ustawić tę właściwość na **false**. Dla małych zestawów skalowania to nie znacznie wpłynąć na niezawodność wdrażania.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Jaka jest różnica między usunięciem maszyny Wirtualnej w zestawie skalowania maszyn wirtualnych i cofanie przydziału maszyny Wirtualnej? Kiedy należy jednej lub drugiej wybrać?

Główna różnica między usunięciem maszyny Wirtualnej w zestawie skalowania maszyn wirtualnych i cofanie przydziału maszyny Wirtualnej jest to, że `deallocate` nie powoduje usunięcia wirtualne dyski twarde (VHD). Istnieją koszty magazynowania związane z uruchomiona `stop deallocate`. Można użyć jednej z nich do jednej z następujących powodów:

- Aby zatrzymać, zwracając koszty operacji obliczeniowych, ale chcesz zachować stan dysku maszyn wirtualnych.
- Chcesz uruchomić zestaw maszyn wirtualnych szybciej, niż można skalować zestaw skalowania maszyn wirtualnych.
  - Dotyczące tego scenariusza, być może utworzono własny aparat skalowania automatycznego i chcesz szybciej skalowania end-to-end.
- Masz zestaw skalowania maszyn wirtualnych, które są nierównomiernie rozłożone między domeny błędów i domenach aktualizacji. Może to być, ponieważ selektywnie usunąć maszyny wirtualne lub maszyny wirtualne zostały usunięte po udostępniania. Uruchamianie `stop deallocate` następuje `start` na maszynie wirtualnej zestawu skalowania równomiernie rozdziela maszyny wirtualne między domeny błędów i domenach aktualizacji.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Jak mogę utworzyć migawki wystąpienia zestawu skalowania maszyn wirtualnych?
Utwórz migawkę z wystąpienia zestawu skalowania maszyn wirtualnych.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Utwórz dysk zarządzany na podstawie migawki.

```azurepowershell-interactive
$snapshotName = "myShapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
