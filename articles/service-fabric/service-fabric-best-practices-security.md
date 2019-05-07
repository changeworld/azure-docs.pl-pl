---
title: Usługa Azure Service Fabric najlepszych rozwiązań dotyczących zabezpieczeń | Dokumentacja firmy Microsoft
description: Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 3349abfb1b7cf85247b1bb5de8eb53fa09299b74
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65136484"
---
# <a name="azure-service-fabric-security"></a>Zabezpieczenia usługi Azure Service Fabric 

Aby uzyskać więcej informacji na temat [najlepsze rozwiązania dotyczące zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/), przejrzyj [najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)

## <a name="key-vault"></a>Usługa Key Vault

[Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) jest zalecane wpisy tajne usługi zarządzania dla usługi Azure Service Fabric, aplikacjami i klastrami.
> [!NOTE]
> Jeśli certyfikatów lub kluczy tajnych z usługi Key Vault są wdrażane w zestawie skalowania maszyn wirtualnych jako klucz tajny zestawu skali maszyny wirtualnej, usługa Key Vault i zestawu skalowania maszyn wirtualnych musi być tej samej lokalizacji.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Utwórz urząd certyfikacji wystawił certyfikat usługi Service Fabric

Certyfikat usługi Azure Key Vault można utworzyć lub zaimportować do usługi Key Vault. Po utworzeniu certyfikatem usługi Key Vault klucz prywatny jest tworzony w usłudze Key Vault i nie są udostępniane do właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w usłudze Key Vault:

- Utwórz certyfikat z podpisem własnym do tworzenia pary kluczy publiczny prywatny i skojarzyć go z certyfikatu. Certyfikat zostanie podpisany przez własnego klucza. 
- Utwórz nowy certyfikat ręcznie, aby utworzyć parę kluczy publiczny prywatny i wygenerować żądanie podpisania certyfikatu X.509. Żądanie podpisania może być podpisany przez urząd rejestrowania lub urzędu certyfikacji. Sparuj x509 podpisanego certyfikatu będzie mogło zostać scalone z kluczem oczekujące do ukończenia KV certyfikatu w usłudze Key Vault. Mimo że ta metoda wymaga większej liczby kroków, jego zapewniają większe bezpieczeństwo, ponieważ klucz prywatny jest tworzone w i ograniczone do usługi Key Vault. Jest to wyjaśnione na poniższym diagramie. 

Przegląd [metod tworzenia certyfikatu magazynu kluczy Azure](https://docs.microsoft.com/azure/key-vault/create-certificate) dodatkowe szczegóły.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Wdrażanie certyfikatów usługi Key Vault dla zestawów skalowania maszyn wirtualnych klastra usługi Service Fabric

Aby wdrożyć certyfikaty z tej samej lokalizacji magazynu kluczy do zestawu skalowania maszyn wirtualnych, użyj zestawu skalowania maszyn wirtualnych [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile). Właściwości szablonu usługi Resource Manager są następujące:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> Magazyn musi być włączona dla wdrożenia szablonu usługi Resource Manager.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Zastosuj listy kontroli dostępu (ACL) do certyfikatu dla klastra usługi Service Fabric

[Rozszerzenia zestawu skali maszyny wirtualnej](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) wydawcy Microsoft.Azure.ServiceFabric służy do konfigurowania zabezpieczeń węzłów.
Aby zastosować listy ACL do certyfikatów dla procesów klaster usługi Service Fabric, należy użyć następujących właściwości szablonu usługi Resource Manager:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpiecz certyfikatu klastra usługi Service Fabric, nazwa pospolita

Aby zabezpieczyć klaster usługi Service Fabric przez certyfikat `Common Name`, należy użyć właściwości szablonu usługi Resource Manager [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), wykonując następujące czynności:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Klastry usługi Service Fabric użyje pierwszego prawidłowy certyfikat, który odnajdzie w magazynie certyfikatów hosta. W Windows będzie to certyfikat przy użyciu najnowszych wygasające datę, która jest zgodna z odciskiem palca nazwa pospolita i wystawcy.

Domen platformy Azure, takich jak *\<YOUR PODDOMENY\>. cloudapp.azure.com lub \<YOUR PODDOMENY\>. trafficmanager.net, są własnością firmy Microsoft. Urzędy certyfikacji nie będzie wystawiać certyfikaty dla domen dla nieautoryzowanych użytkowników. Większość użytkowników będzie trzeba kupować domeny u rejestratora lub mieć uprawnienia administratora domeny autoryzowanych dla urzędu certyfikacji do wystawiania certyfikatu o tej nazwie wspólnej.

Dla dodatkowych szczegółów na temat konfigurowania usługi DNS, aby rozpoznać Twojej domeny z adresem IP firmy Microsoft, Dowiedz się, jak skonfigurować [usługi Azure DNS można hostować swoją domenę](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Po delegowanie swoje serwery nazw strefy DNS platformy Azure na serwerach nazw domen, należy dodać następujące dwa rekordy do strefy DNS:
> - "" Rekordów dla domeny WIERZCHOŁKU, który nie jest `Alias record set` na wszystkie adresy IP rozwiąże domenę niestandardową.
> - Rekord "C" dla Microsoft domen podrzędnych aprowizowanej, które nie są `Alias record set`. Na przykład można użyć usługi Traffic Manager lub nazwa DNS modułu równoważenia obciążenia.

Aby zaktualizować portal tak, aby wyświetlić niestandardową nazwę DNS dla klastra usługi Service Fabric `"managementEndpoint"`, zaktualizuj działaniami właściwości szablonu Menedżera zasobów klastra usługi w sieci szkieletowej:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Szyfrowanie wartościami wpisów tajnych pakietów usługi Service Fabric

Typowe wartości, które są szyfrowane w pakietach sieci szkieletowej usługi obejmują poświadczeń usługi Azure Container Registry (ACR), zmienne środowiskowe, ustawienia i klucze konta magazynu wtyczki Azure woluminu.

Aby [skonfigurować certyfikat szyfrowania i szyfrowanie kluczy tajnych w klastrach Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Wygeneruj certyfikat z podpisem własnym służący do szyfrowania klucz tajny:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Postępuj zgodnie z instrukcjami w [zestawów skalowania certyfikaty wdrażanie usługi Key Vault do maszyny wirtualnej w klastrze usługi Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) do wdrożenia klastra Service Fabric usługi Virtual Machine Scale Sets certyfikaty magazynu kluczy.

Szyfruj klucz tajny przy użyciu następującego polecenia programu PowerShell, a następnie zaktualizować manifest aplikacji usługi Service Fabric przy użyciu zaszyfrowanej wartości:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Aby [skonfigurować certyfikat szyfrowania i szyfrowanie kluczy tajnych w klastrach systemu Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Wygeneruj certyfikat z podpisem własnym służący do szyfrowania, wpisy tajne:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Postępuj zgodnie z instrukcjami w [zestawów skalowania certyfikaty wdrażanie usługi Key Vault do maszyny wirtualnej w klastrze usługi Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) do klastra Service Fabric usługi Virtual Machine Scale Sets.

Szyfruj klucz tajny, używając następujących poleceń, a następnie zaktualizuj swoje Manifest aplikacji programu Service Fabric przy użyciu zaszyfrowanej wartości:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po szyfrowanie chronionego wartości, [Określ zaszyfrowane klucze tajne w aplikacji usługi Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application), i [odszyfrować zaszyfrowane klucze tajne z kodu usługi](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Uwierzytelnianie aplikacji usługi Service Fabric do zasobów platformy Azure przy użyciu tożsamości usługi zarządzanej (MSI)

Aby uzyskać informacje dotyczące zarządzanych tożsamości dla zasobów platformy Azure, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-it-work).
Klastry usługi Service Fabric platformy Azure znajdują się w zestawach skalowania maszyn wirtualnych, które obsługują [tożsamości usługi zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Aby uzyskać listę usług tego pliku MSI może służyć do uwierzytelniania, zobacz [usług platformy Azure, która obsługuje uwierzytelnianie usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Aby włączyć system przypisane tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyn wirtualnych, należy zadeklarować następujące `"Microsoft.Compute/virtualMachinesScaleSets"` właściwości:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity) Aby uzyskać więcej informacji.

Jeśli utworzono [przypisanych przez użytkownika z tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), Zadeklaruj następujący zasób do szablonu, aby przypisać je do zestawu skalowania maszyn wirtualnych. Zastąp `\<USERASSIGNEDIDENTITYNAME\>` o nazwie użytkownik przypisany zarządzanych tożsamości został utworzony:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Przed usługi Service Fabric aplikacji ułatwia korzystanie z tożsamości zarządzanej, muszą mieć uprawnienia do zasobów platformy Azure, potrzebny do uwierzytelniania za pomocą.
Następujące polecenia udzielić dostępu do zasobu platformy Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

W kodzie aplikacji usługi Service Fabric [uzyskania tokenu dostępu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) dla usługi Azure Resource Manager, wprowadzając REST podobne do następujących:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Aplikację usługi Service Fabric można następnie użyć tokenu dostępu do uwierzytelniania w zasobach platformy Azure, który obsługuje usługi Active Directory.
Poniższy przykład pokazuje, jak to zrobić dla zasobu usługi Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```

## <a name="windows-defender"></a>Windows Defender 

Domyślnie programu antywirusowego Windows Defender jest zainstalowany w systemie Windows Server 2016. Aby uzyskać więcej informacji, zobacz [programu antywirusowego Windows Defender w systemie Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Interfejs użytkownika jest domyślnie instalowany w przypadku niektórych jednostek SKU, ale nie jest to wymagane. Aby zmniejszyć wydajność wpływu i zasobów zużycia obciążenie poniesione przez usługę Windows Defender i zasady zabezpieczeń umożliwiają wykluczenia procesów i ścieżek dla oprogramowania typu open-source zadeklarować następujący zasób maszyny wirtualnej skalowania Ustaw rozszerzenia Menedżer właściwości szablonu do wykluczenia z klastra usługi Service Fabric ze skanowania w poszukiwaniu:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Można znaleźć w dokumentacji ochrony przed złośliwym oprogramowaniem dla reguły konfiguracji, jeśli nie używasz programu Windows Defender. Usługa Windows Defender nie jest obsługiwana w systemie Linux.

## <a name="next-steps"></a>Kolejne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcje pomocy technicznej usługi Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
