---
title: Włączanie usług domenowych usług domenowych usług usług domenowych usług usług usług cyfrowych platformy Azure przy użyciu szablonu | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować i włączyć usługi domenowe usługi domenowe Active Directory platformy Azure przy użyciu szablonu Usługi Azure Resource Manager
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: 2daadb539bc08df37f15c187866b735e45309288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612792"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Tworzenie domeny zarządzanej usług domenowych usługi active directory platformy Azure przy użyciu szablonu usługi Azure Resource Manager

Usługi domenowe Usługi Active Directory (Azure AD DS) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, uwierzytelnianie LDAP, Kerberos/NTLM, które jest w pełni zgodne z usługą Active Directory systemu Windows Server. Te usługi domeny są używane bez samodzielnego wdrażania, zarządzania i instalowania poprawek kontrolerów domeny. Usługi Azure AD DS integruje się z istniejącą dzierżawą usługi Azure AD. Ta integracja umożliwia użytkownikom logowanie się przy użyciu poświadczeń firmowych i można użyć istniejących grup i kont użytkowników, aby zabezpieczyć dostęp do zasobów.

W tym artykule pokazano, jak włączyć usługi Azure AD DS przy użyciu szablonu usługi Azure Resource Manager. Zasoby pomocnicze są tworzone przy użyciu programu Azure PowerShell.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł, potrzebne są następujące zasoby:

* Zainstaluj i skonfiguruj program Azure PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure PowerShell i połączyć się z subskrypcją platformy Azure.](/powershell/azure/install-az-ps)
    * Upewnij się, że logujesz się do subskrypcji platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instalowanie i konfigurowanie programu Azure AD PowerShell.
    * W razie potrzeby postępuj zgodnie z instrukcjami, aby [zainstalować moduł programu Azure AD PowerShell i połączyć się z usługą Azure AD](/powershell/azure/active-directory/install-adv2).
    * Upewnij się, że logujesz się do dzierżawy usługi Azure AD przy użyciu polecenia cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Aby włączyć usługi Azure AD DS, potrzebujesz uprawnień *administratora globalnego* w dzierżawie usługi Azure AD.
* Aby utworzyć wymagane zasoby usług Azure AD DS, potrzebujesz uprawnień *współautora* w ramach subskrypcji platformy Azure.

## <a name="dns-naming-requirements"></a>Wymagania dotyczące nazewnictwa DNS

Podczas tworzenia wystąpienia usług Azure AD DS należy określić nazwę DNS. Po wybraniu tej nazwy DNS należy wziąć pod uwagę pewne kwestie:

* **Wbudowana nazwa domeny:** Domyślnie używana jest wbudowana nazwa domeny katalogu (sufiks *.onmicrosoft.com).* Jeśli chcesz włączyć bezpieczny dostęp LDAP do domeny zarządzanej przez Internet, nie możesz utworzyć certyfikatu cyfrowego w celu zabezpieczenia połączenia z tą domeną domyślną. Firma Microsoft jest właścicielem domeny *onmicrosoft.com,* więc urząd certyfikacji (CA) nie wystawi certyfikatu.
* **Niestandardowe nazwy domen:** Najczęstszym podejściem jest określenie niestandardowej nazwy domeny, zazwyczaj tej, która jest już właścicielem i jest rutowalna. Korzystając z domeny niestandardowej z rutowalnymi, ruch może poprawnie przepływać zgodnie z potrzebami do obsługi aplikacji.
* **Sufiksy domen nienastępnych do routingu:** Ogólnie zaleca się unikanie sufiksu nazwy domeny nierutowalnej, takiego jak *contoso.local*. Sufiks *.local* nie jest rutowalny i może powodować problemy z rozpoznawaniem DNS.

> [!TIP]
> Jeśli utworzysz niestandardową nazwę domeny, zadbaj o istniejące przestrzenie nazw DNS. Zaleca się używanie nazwy domeny oddzielnie od istniejącego obszaru nazw dns platformy Azure lub lokalnego.
>
> Na przykład, jeśli masz istniejący odstęp nazw DNS *contoso.com,* utwórz domenę zarządzaną usługą Azure AD DS o niestandardowej nazwie domeny *aaddscontoso.com*. Jeśli musisz użyć bezpiecznego protokołu LDAP, musisz zarejestrować i posiadać tę niestandardową nazwę domeny, aby wygenerować wymagane certyfikaty.
>
> Może być konieczne utworzenie dodatkowych rekordów DNS dla innych usług w twoim środowisku lub warunkowych usług przesyłania dalej DNS między istniejącymi spaniami nazw DNS w twoim środowisku. Na przykład po uruchomieniu serwera sieci Web, który obsługuje witrynę przy użyciu głównej nazwy DNS, mogą istnieć konflikty nazw, które wymagają dodatkowych wpisów DNS.
>
> W tych samouczkach i artykułach instruktażowych domena niestandardowa *aaddscontoso.com* jest używana jako krótki przykład. We wszystkich poleceniach określ własną nazwę domeny.

Obowiązują również następujące ograniczenia nazw DNS:

* **Ograniczenia prefiksu domeny:** Nie można utworzyć domeny zarządzanej z prefiksem dłuższym niż 15 znaków. Prefiks określonej nazwy domeny (na przykład *aaddscontoso* w *aaddscontoso.com* nazwa domeny) musi zawierać 15 lub mniej znaków.
* **Konflikty nazw sieciowych:** Nazwa domeny DNS domeny zarządzanej nie powinna już istnieć w sieci wirtualnej. W szczególności sprawdź następujące scenariusze, które mogłyby prowadzić do konfliktu nazw:
    * Jeśli masz już domenę usługi Active Directory o tej samej nazwie domeny DNS w sieci wirtualnej platformy Azure.
    * Jeśli sieć wirtualna, w której planujesz włączyć domenę zarządzaną, ma połączenie sieci VPN z siecią lokalną. W tym scenariuszu upewnij się, że nie masz domeny o tej samej nazwie domeny DNS w sieci lokalnej.
    * Jeśli masz istniejącą usługę w chmurze platformy Azure o tej nazwie w sieci wirtualnej platformy Azure.

## <a name="create-required-azure-ad-resources"></a>Tworzenie wymaganych zasobów usługi Azure AD

Usługa Azure AD DS wymaga jednostki usługi i grupy usługi Azure AD. Te zasoby umożliwiają domenie zarządzanej usług Azure AD DS synchronizowanie danych i definiowanie, którzy użytkownicy mają uprawnienia administracyjne w domenie zarządzanej.

Najpierw zarejestruj dostawcę zasobów usług domenowych usługi Azure AD przy użyciu polecenia cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Utwórz jednostkę usługi Azure AD przy użyciu polecenia cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] dla usług Azure AD DS do komunikowania się i uwierzytelniania. Identyfikator określonej aplikacji jest używany o nazwie *Domain Controller Services* o identyfikatorze *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Nie zmieniaj tego identyfikatora aplikacji.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Teraz utwórz grupę usługi Azure AD o nazwie *Administratorzy kontrolera domeny usługi AAD* przy użyciu polecenia cmdlet [New-AzureADGroup.][New-AzureADGroup] Użytkownicy dodani do tej grupy otrzymują uprawnienia do wykonywania zadań administracyjnych w domenie zarządzanej usług Azure AD DS.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Po utworzeniu grupy *Administratorzy kontrolera domeny usługi AAD* dodaj użytkownika do grupy przy użyciu polecenia cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Najpierw otrzymasz identyfikator obiektu grupy *administratorów kontrolera domeny usługi AAD* przy użyciu polecenia cmdlet [Get-AzureADGroup,][Get-AzureADGroup] a następnie identyfikator obiektu żądanego użytkownika przy użyciu polecenia cmdlet [Get-AzureADUser.][Get-AzureADUser]

W poniższym przykładzie identyfikator obiektu użytkownika dla konta `admin@aaddscontoso.onmicrosoft.com`z omdyną UPN . Zastąp to konto użytkownika numerem UPN użytkownika, którego chcesz dodać do grupy *Administratorzy kontrolera domeny usługi AAD:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Na koniec utwórz grupę zasobów przy użyciu polecenia cmdlet [New-AzResourceGroup.][New-AzResourceGroup] W poniższym przykładzie grupa zasobów nosi nazwę *myResourceGroup* i jest tworzona w regionie *westus.* Użyj własnej nazwy i żądanego regionu:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Jeśli wybierzesz region, który obsługuje strefy dostępności, zasoby usługi Azure AD DS są dystrybuowane między strefami w celu uzyskania dodatkowej redundancji. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach.

Nie ma nic do skonfigurowania dla usługi Azure AD DS, które mają być dystrybuowane w różnych strefach. Platforma Azure automatycznie obsługuje dystrybucji strefy zasobów. Aby uzyskać więcej informacji i wyświetlić dostępność regionu, zobacz [Co to są strefy dostępności na platformie Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definicja zasobu dla usług Azure AD DS

W ramach definicji zasobu Menedżera zasobów wymagane są następujące parametry konfiguracji:

| Parametr               | Wartość |
|-------------------------|---------|
| Nazwa_domeny              | Nazwa domeny DNS domeny zarządzanej, biorąc pod uwagę poprzednie punkty dotyczące nazewnictwa prefiksów i konfliktów. |
| filteredSync            | Usługi Azure AD DS umożliwia synchronizację *wszystkich* użytkowników i grup dostępnych w usłudze Azure AD lub synchronizacji o *określonym zakresie* tylko określonych grup. Jeśli zdecydujesz się zsynchronizować wszystkich użytkowników i grupy, nie można później wybrać tylko wykonać synchronizację o określonym zakresie.<br /> Aby uzyskać więcej informacji na temat synchronizacji o określonym zakresie, zobacz [Synchronizacja zakresu usług domenowych usługi Azure AD][scoped-sync].|
| powiadomieniaUstawienia    | Jeśli istnieją alerty generowane w domenie zarządzanej usługi Azure AD DS, powiadomienia e-mail mogą być wysyłane. <br />*Administratorzy globalni dzierżawy* platformy Azure i członkowie grupy *Administratorzy kontrolera domeny usługi AAD* mogą być *włączeni* dla tych powiadomień.<br /> W razie potrzeby można dodać dodatkowych adresatów powiadomień, gdy istnieją alerty, które wymagają uwagi.|
| typ konfiguracji domeny | Domyślnie domena zarządzana usługą Azure AD DS jest tworzona jako las *użytkownika.* Ten typ lasu synchronizuje wszystkie obiekty z usługi Azure AD, w tym wszystkie konta użytkowników utworzone w lokalnym środowisku usług AD DS. Aby utworzyć las użytkownika, nie trzeba określać wartości *konfiguracji domeny.*<br /> Las *zasobów* synchronizuje tylko użytkowników i grupy utworzone bezpośrednio w usłudze Azure AD. Lasy zasobów są obecnie w wersji zapoznawczej. Ustaw wartość *ResourceTrusting,* aby utworzyć las zasobów.<br />Aby uzyskać więcej informacji na temat lasów *zasobów,* w tym dlaczego można ich używać i jak tworzyć relacje zaufania lasów za pomocą lokalnych domen usług AD DS, zobacz [omówienie lasów zasobów usługi Azure AD DS][resource-forests].|

Poniższa definicja skondensowanych parametrów pokazuje sposób deklarowania tych wartości. Las użytkownika o nazwie *aaddscontoso.com* jest tworzony ze wszystkimi użytkownikami z usługi Azure AD zsynchronizowanymi z domeną zarządzaną usługą Azure AD DS:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Następujący typ zasobu szablonu skróconego Menedżera zasobów jest następnie używany do definiowania i tworzenia domeny zarządzanej usług Azure AD DS. Sieć wirtualna platformy Azure i podsieć muszą już istnieć lub zostać utworzone jako część szablonu Menedżera zasobów. Domena zarządzana usługą Azure AD DS jest połączona z tą podsiecią.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Te parametry i typ zasobu mogą być używane jako część szerszego szablonu Menedżera zasobów do wdrażania domeny zarządzanej, jak pokazano w poniższej sekcji.

## <a name="create-a-managed-domain-using-sample-template"></a>Tworzenie domeny zarządzanej przy użyciu przykładowego szablonu

Poniższy kompletny przykładowy szablon Menedżera zasobów tworzy domenę zarządzaną usługą Azure AD DS oraz obsługujące reguły sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. Reguły sieciowej grupy zabezpieczeń są wymagane do zabezpieczenia domeny zarządzanej i upewnienia się, że ruch może przepływać poprawnie. Tworzony jest las użytkownika o nazwie DNS *aaddscontoso.com,* z synchronizacją wszystkich użytkowników z usługą Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Ten szablon można wdrożyć przy użyciu preferowanej metody wdrażania, takiej jak [witryna Azure portal][portal-deploy], [Azure PowerShell][powershell-deploy]lub potok ciągłej integracji/ciągłego wdrażania. W poniższym przykładzie użyto polecenia cmdlet [New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Określ własną nazwę grupy zasobów i nazwę pliku szablonu:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Utworzenie zasobu i zwrócenie kontroli do monitu programu PowerShell zajmuje kilka minut. Domena zarządzana usługą Azure AD DS nadal jest aprowizowana w tle i może potrwać do godziny, aby zakończyć wdrożenie. W witrynie Azure portal na stronie **Przegląd** domeny zarządzanej usług Azure AD DS jest wyświetlany bieżący stan na tym etapie wdrażania.

Gdy w witrynie Azure Portal zostanie wykazanie, że domena zarządzana usługą Azure AD DS zakończyła inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzana dla dołączania do domeny lub uwierzytelniania.
    * Aby skonfigurować usługę DNS, wybierz domenę zarządzana usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* [Włącz synchronizację haseł z usługami domenowymi usługi Azure AD, aby](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić domenę zarządzaną usługą Azure AD DS w akcji, możesz [dołączyć do maszyny Wirtualnej systemu Windows,][windows-join] [skonfigurować bezpieczną usługę LDAP][tutorial-ldaps]i [skonfigurować synchronizację skrótów haseł][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
