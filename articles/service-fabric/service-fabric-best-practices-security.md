---
title: Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Service Fabric
description: Najważniejsze wskazówki dotyczące projektowania i zagadnienia dotyczące projektowania dla zapewnienia bezpieczeństwa klastrów i aplikacji usługi Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258930"
---
# <a name="azure-service-fabric-security"></a>Zabezpieczenia usługi Azure Service Fabric 

Aby uzyskać więcej informacji na temat [najlepszych rozwiązań dotyczących zabezpieczeń platformy Azure,](https://docs.microsoft.com/azure/security/)zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń usługi Azure Service Fabric](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Usługa Key Vault

[Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) to zalecana usługa zarządzania wpisami tajnymi dla aplikacji i klastrów sieci szkieletowej usług Azure.
> [!NOTE]
> Jeśli certyfikaty/wpisy tajne z magazynu kluczy są wdrażane w zestawie skalowania maszyny wirtualnej jako klucz tajny zestawu skalowania maszyny wirtualnej, należy zlokalizować współlokowany zestaw skalowania magazynu kluczy i maszyny wirtualnej.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Tworzenie certyfikatu sieci szkieletowej wystawionej przez urząd certyfikacji certyfikatów

Certyfikat usługi Azure Key Vault można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu usługi Key Vault klucz prywatny jest tworzony wewnątrz magazynu kluczy i nigdy nie jest narażony na działanie właściciela certyfikatu. Oto sposoby tworzenia certyfikatu w magazynie kluczy:

- Utwórz certyfikat z podpisem własnym, aby utworzyć parę kluczy publiczno-prywatnych i skojarzyć go z certyfikatem. Certyfikat zostanie podpisany własnym kluczem. 
- Utwórz nowy certyfikat ręcznie, aby utworzyć parę kluczy publiczno-prywatnych i wygenerować żądanie podpisywania certyfikatów X.509. Żądanie podpisywania może być podpisane przez urząd rejestracji lub urząd certyfikacji. Podpisany certyfikat x509 można scalić z oczekującą parą kluczy, aby ukończyć certyfikat KV w Magazynie kluczy. Mimo że ta metoda wymaga więcej kroków, zapewnia większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony i ograniczony do usługi Key Vault. Jest to wyjaśnione na poniższym diagramie. 

Przejrzyj [metody tworzenia certyfikatów usługi Azure Keyvault, aby](https://docs.microsoft.com/azure/key-vault/create-certificate) uzyskać dodatkowe informacje.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Wdrażanie certyfikatów magazynu kluczy w zestawach skalowania maszyny wirtualnej klastra sieci szkieletowej usług

Aby wdrożyć certyfikaty z wielomiejscowego keyvaultu w zestawie skalowania maszyny wirtualnej, należy użyć programu [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)zestawu skalowania maszyny wirtualnej . Poniżej znajdują się właściwości szablonu Menedżera zasobów:

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
> Przechowalnia musi być włączona dla wdrażania szablonów Menedżera zasobów.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Stosowanie listy kontroli dostępu (ACL) do certyfikatu klastra sieci szkieletowej usług

[Wydawca rozszerzeń zestawu skalowania maszyny wirtualnej](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) Microsoft.Azure.ServiceFabric służy do konfigurowania zabezpieczeń węzłów.
Aby zastosować acl do certyfikatów dla procesów klastra sieci szkieletowej usług, należy użyć następujących właściwości szablonu Menedżera zasobów:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Zabezpieczanie certyfikatu klastra sieci szkieletowej usług według nazwy pospolitej

Aby zabezpieczyć klaster sieci `Common Name`szkieletowej usług według certyfikatu, użyj certyfikatu właściwości menedżera [zasobówCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), w następujący sposób:

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
> Klastry sieci szkieletowej usług użyją pierwszego prawidłowego certyfikatu, który znajdzie w magazynie certyfikatów hosta. W systemie Windows będzie to certyfikat z najpóźniejszą datą wygaśnięcia, która pasuje do nazwy pospolitej i odcisk palca wystawcy.

Domeny platformy Azure,\<takie jak\>* TWOJA PODDOMENA .cloudapp.azure.com lub \<TWOJA SUBDOMAIN\>.trafficmanager.net, są własnością firmy Microsoft. Urzędy certyfikacji nie będą wystawiać certyfikatów dla domen nieautoryzowanym użytkownikom. Większość użytkowników będzie musiała zakupić domenę od rejestratora lub być autoryzowanym administratorem domeny, aby urząd certyfikacji wystawił ci certyfikat o tej nazwie pospolitej.

Aby uzyskać dodatkowe informacje na temat konfigurowania usługi DNS w celu rozpoznania domeny na adres IP firmy Microsoft, zapoznaj się z instrukcjami konfigurowania [usługi Azure DNS do hostowania domeny.](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

> [!NOTE]
> Po oddaniu serwerów nazw domen do serwerów nazw strefy DNS platformy Azure dodaj do strefy DNS następujące dwa rekordy:
> - Rekord "A" dla domeny APEX, `Alias record set` który nie jest dla wszystkich adresów IP domeny niestandardowej zostanie rozpoznany.
> - Rekord "C" dla subdomen firmy Microsoft, `Alias record set`które zostały zainicjowane, które NIE są domeną . Można na przykład użyć nazwy DNS Menedżera ruchu lub modułu równoważenia obciążenia.

Aby zaktualizować portal w celu wyświetlenia niestandardowej `"managementEndpoint"`nazwy DNS klastra sieci szkieletowej usług, zaktualizuj właściwości szablonu Menedżera zasobów klastra sieci szkieletowej usług:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Szyfrowanie wartości tajnych pakietu sieci szkieletowej usług

Typowe wartości, które są szyfrowane w pakietach sieci szkieletowej usług obejmują poświadczenia usługi Azure Container Registry (ACR), zmienne środowiskowe, ustawienia i klucze kont magazynu wtyczek usługi Azure Volume.

Aby [skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Windows:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)

Generowanie certyfikatu z podpisem własnym w celu szyfrowania klucza tajnego:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Użyj instrukcji zawartych w zestawach [skalowania klastra usług Magazyn kluczy do](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) wdrażania certyfikatów magazynu kluczy w zestawach skalowania maszyn wirtualnych klastra sieci szkieletowej usługi.

Zaszyfruj klucz tajny przy użyciu następującego polecenia programu PowerShell, a następnie zaktualizuj manifest aplikacji sieci szkieletowej usług przy użyciu zaszyfrowanej wartości:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Aby [skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Linux:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)

Generowanie certyfikatu z podpisem własnym w celu szyfrowania wpisów tajnych:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Użyj instrukcji w [wdrażaniu certyfikatów magazynu kluczy do zestawów skalowania maszyny wirtualnej klastra sieci](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) szkieletowej usługi do zestawów skalowania maszyny wirtualnej klastra sieci szkieletowej usług.

Zaszyfruj klucz tajny przy użyciu następujących poleceń, a następnie zaktualizuj manifest aplikacji sieci szkieletowej usług za pomocą zaszyfrowanej wartości:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Po zaszyfrowaniu chronionych wartości [określ zaszyfrowane wpisy tajne w aplikacji sieci szkieletowej usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)i [odszyfruj zaszyfrowane wpisy tajne z kodu usługi.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code)

## <a name="include-certificate-in-service-fabric-applications"></a>Dołącz certyfikat w aplikacjach sieci szkieletowej usług

Aby nadać aplikacji dostęp do wpisów tajnych, dołącz certyfikat, dodając **SecretsCertificate** element do manifestu aplikacji.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Uwierzytelnij aplikacje sieci szkieletowej usług w zasobach platformy Azure przy użyciu tożsamości usługi zarządzanej (MSI)

Aby dowiedzieć się więcej o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
Klastry sieci szkieletowej usług Azure są hostowane w zestawach skalowania maszyn wirtualnych, które obsługują [tożsamość usługi zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)
Aby uzyskać listę usług, do których można uwierzytelnić się msi, zobacz [Usługi platformy Azure obsługujące uwierzytelnianie usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Aby włączyć system przypisany tożsamości zarządzanej podczas tworzenia zestawu skalowania maszyn wirtualnych `"Microsoft.Compute/virtualMachinesScaleSets"` lub istniejącego zestawu skalowania maszyn wirtualnych, zadeklaruj następującą właściwość:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)

Jeśli [utworzono tożsamość zarządzaną przypisaną przez użytkownika,](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)zadeklaruj następujący zasób w szablonie, aby przypisać go do zestawu skalowania maszyny wirtualnej. Zastąp `\<USERASSIGNEDIDENTITYNAME\>` nazwą utworzonej tożsamości zarządzanej przypisanej przez użytkownika:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Aby aplikacja sieci szkieletowej usług mogła korzystać z tożsamości zarządzanej, uprawnienia muszą być przyznane zasobom platformy Azure, które muszą być uwierzytelniane.
Następujące polecenia udzielają dostępu do zasobu platformy Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

W kodzie aplikacji sieci [szkieletowej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) usług uzyskaj token dostępu dla usługi Azure Resource Manager, tworząc REST wszystkie podobne do następujących czynności:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Aplikacja sieci szkieletowej usług może następnie użyć tokenu dostępu do uwierzytelniania w zasobach platformy Azure, które obsługują usługę Active Directory.
W poniższym przykładzie pokazano, jak to zrobić dla zasobu usługi Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Plany bazowe zabezpieczeń systemu Windows
[Zaleca się wdrożenie konfiguracji standardu branżowego, która jest powszechnie znana i dobrze przetestowana, na przykład linie bazowe zabezpieczeń firmy Microsoft, w przeciwieństwie do samodzielnego tworzenia linii bazowej;](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) opcja inicjowania obsługi administracyjnej tych w zestawach skalowania maszyny wirtualnej jest użycie programu obsługi rozszerzeń konfiguracji żądanego stanu platformy Azure (DSC), aby skonfigurować maszyny wirtualne, gdy są one w trybie online, więc są one uruchomione oprogramowanie produkcyjne.

## <a name="azure-firewall"></a>Azure Firewall
[Zapora azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa zapora jako usługa o wbudowanej wysokiej dostępności i nieograniczonej skalowalności chmury.](https://docs.microsoft.com/azure/firewall/overview) umożliwia to ograniczenie wychodzącego ruchu HTTP/S do określonej listy w pełni kwalifikowanych nazw domen (FQDN), w tym symboli wieloznacznych. Ta funkcja nie wymaga kończenia żądań protokołu SSL. Zaleca się, aby korzystać z [tagów FQDN zapory platformy Azure](https://docs.microsoft.com/azure/firewall/fqdn-tags) dla aktualizacji systemu Windows i włączyć ruch sieciowy do punktów końcowych usługi Microsoft Windows Update może przepływać przez zaporę. [Wdrażanie Zapory platformy Azure przy użyciu szablonu](https://docs.microsoft.com/azure/firewall/deploy-template) zawiera przykład dla definicji szablonu zasobów Microsoft.Network/azureFirewalls. Reguły zapory typowe dla aplikacji sieci szkieletowej usług zezwalają na następujące zasady dla sieci wirtualnej klastrów:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Te reguły zapory uzupełniają dozwolone wychodzące grupy zabezpieczeń sieci, które obejmują ServiceFabric i Storage, zgodnie z dozwolonymi miejscami docelowymi z sieci wirtualnej.

## <a name="tls-12"></a>TLS 1.2
[Tsg](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Domyślnie program antywirusowy Windows Defender jest zainstalowany w systemie Windows Server 2016. Aby uzyskać szczegółowe informacje, zobacz [Program antywirusowy usługi Windows Defender w systemie Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). Interfejs użytkownika jest domyślnie instalowany w przypadku niektórych jednostek SKU, ale nie jest to wymagane. Aby zmniejszyć wpływ na wydajność i obciążenie związane z zużyciem zasobów poniesione przez usługę Windows Defender, a jeśli zasady zabezpieczeń umożliwiają wykluczanie procesów i ścieżek dla oprogramowania typu open source, zadeklaruj następujący zasób rozszerzenia zestawu skalowania maszyny wirtualnej Właściwości szablonu menedżera, aby wykluczyć klaster sieci szkieletowej usług ze skanowania:


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
> Jeśli nie korzystasz z usługi Windows Defender, zapoznaj się z dokumentacją ochrony przed złośliwym oprogramowaniem. Usługa Windows Defender nie jest obsługiwana w systemie Linux.

## <a name="platform-isolation"></a>Izolacja platformy
Domyślnie aplikacje sieci szkieletowej usług są przyznawane dostęp do środowiska uruchomieniowego sieci szkieletowej usług, który manifestuje się w różnych formach: [zmienne środowiskowe](service-fabric-environment-variables-reference.md) wskazujące ścieżki plików na hoście odpowiadające plikom aplikacji i sieci szkieletowej, punkt końcowy komunikacji między procesami, który akceptuje żądania specyficzne dla aplikacji, oraz certyfikat klienta, który sieć szkieletowa oczekuje, że aplikacja będzie używać do uwierzytelniania. W przypadku, gdy usługa obsługuje się niezaufany kod, wskazane jest, aby wyłączyć ten dostęp do środowiska wykonawczego SF — chyba że jawnie potrzebne. Dostęp do środowiska wykonawczego jest usuwany przy użyciu następującej deklaracji w sekcji Zasady manifestu aplikacji: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Tworzenie klastra sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra linuksa](service-fabric-cluster-creation-via-portal.md).
* Dowiedz się więcej o [opcjach pomocy technicznej sieci szkieletowej](service-fabric-support.md)usług .

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
