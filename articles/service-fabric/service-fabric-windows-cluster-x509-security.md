---
title: Zabezpieczanie klastra usługi Azure Service Fabric na Windows przy użyciu certyfikatów | Dokumentacja firmy Microsoft
description: Bezpieczna komunikacja w ramach klastra usługi Azure Service Fabric autonomicznego lub w środowisku lokalnym, a także między klientami i klastra.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: ee2ce03fccc3e6556f9d261687edb050c8cfa1cc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628153"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpieczanie klastra autonomicznego w Windows przy użyciu certyfikatów X.509
W tym artykule opisano sposób zabezpieczania komunikacji między różnymi węzłami klastra autonomicznego Windows. Opisuje ona również, jak do uwierzytelniania klientów łączących się z tym klastrem przy użyciu certyfikatów X.509. Uwierzytelnianie zapewnia, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożone aplikacje i wykonywać zadania zarządzania. Certyfikat zabezpieczeń powinna być włączona w klastrze podczas tworzenia klastra.  

Aby uzyskać więcej informacji na temat zabezpieczeń klastra, takie jak zabezpieczenia węzła do węzła, węzeł klienta zabezpieczeń i kontroli dostępu opartej na rolach, zobacz [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>O certyfikatach, które jest potrzebne?
Na początek z [Pobierz pakiet usługi Service Fabric dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednego z węzłów w klastrze. Pobrany pakiet służy do znalezienia pliku ClusterConfig.X509.MultiMachine.json. Otwórz plik, a następnie zapoznaj się z sekcją zabezpieczeń w sekcji właściwości:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

W tej sekcji opisano certyfikaty, które trzeba Zabezpieczanie klastra autonomicznego Windows. Jeśli określisz certyfikatu klastra, ustaw wartość ClusterCredentialType do _X509_. Jeśli określisz certyfikat serwera dla połączeń zewnętrznych, ustaw ServerCredentialType _X509_. Chociaż nie jest to konieczne, zaleca się mieć oba te certyfikaty do odpowiednio zabezpieczonego klastra. Jeśli te wartości są ustawione na *X509*, musisz również podać odpowiednie certyfikaty lub zgłasza wyjątek, Usługa Service Fabric. W niektórych scenariuszach możesz chcieć określić tylko _ClientCertificateThumbprints_ lub _ReverseProxyCertificate_. W tych scenariuszach, nie trzeba ustawić _ClusterCredentialType_ lub _ServerCredentialType_ do _X509_.


> [!NOTE]
> A [odcisk palca](https://en.wikipedia.org/wiki/Public_key_fingerprint) jest podstawową tożsamość certyfikatu. Aby dowiedzieć się, odcisk palca certyfikatów, które tworzysz, zobacz [pobrać odcisk palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

Poniższa tabela zawiera listę certyfikatów, które należy na konfigurację klastra:

| **Ustawienie CertificateInformation** | **Opis** |
| --- | --- |
| ClusterCertificate |Zalecane w przypadku środowiska testowego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Dwóch różnych certyfikatów, podstawowy i pomocniczy, można użyć do uaktualnienia. Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisku palca, które pomocniczej w zmiennych ThumbprintSecondary. |
| ClusterCertificateCommonNames |Zalecane w środowisku produkcyjnym. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć jednej lub dwóch certyfikatów wspólnej nazwy klastrów. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy certyfikatu. Jeśli jest używany więcej niż jeden certyfikat o takiej samej nazwie wspólnej, można określić wiele odcisków palca wystawcy.|
| ClusterCertificateIssuerStores |Zalecane w środowisku produkcyjnym. Ten certyfikat odpowiada wystawcy certyfikatu klastra. Nazwa pospolita i odpowiednią nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ClusterCertificateCommonNames, możesz podać wystawcy.  Ułatwia to Przerzucanie certyfikatów wystawcy klastra. Można określić wielu wystawców klastrowania więcej niż jeden certyfikat jest używany. Pusty umieszczenie IssuerCommonName wszystkie certyfikaty w odpowiednich sklepach określone w obszarze X509StoreNames.|
| ServerCertificate |Zalecane w przypadku środowiska testowego. Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Dla wygody możesz używać tego samego certyfikatu dla ClusterCertificate i ServerCertificate. Dwa certyfikaty na innym serwerze, podstawowy i pomocniczy, można użyć do uaktualnienia. Ustaw odcisk palca certyfikatu podstawowego w sekcji odcisku palca, które pomocniczej w zmiennych ThumbprintSecondary. |
| ServerCertificateCommonNames |Zalecane w środowisku produkcyjnym. Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy certyfikatu. Jeśli jest używany więcej niż jeden certyfikat o takiej samej nazwie wspólnej, można określić wiele odcisków palca wystawcy. Dla wygody możesz używać tego samego certyfikatu dla ClusterCertificateCommonNames i ServerCertificateCommonNames. Można użyć co najmniej dwie nazwy certyfikatów serwera wspólnej. |
| ServerCertificateIssuerStores |Zalecane w środowisku produkcyjnym. Ten certyfikat odpowiada wystawcy certyfikatu serwera. Nazwa pospolita i odpowiednią nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ServerCertificateCommonNames, możesz podać wystawcy.  Ułatwia to Przerzucanie serwera wystawcy certyfikatów. Wielu wystawców może być określona, jeśli więcej niż jeden certyfikat jest używany. Pusty umieszczenie IssuerCommonName wszystkie certyfikaty w odpowiednich sklepach określone w obszarze X509StoreNames.|
| ClientCertificateThumbprints |Zainstaluj ten zestaw certyfikatów na uwierzytelnionych klientów. Może mieć wiele certyfikatów innego klienta do zainstalowania na maszynach, które chcesz zezwolić na dostęp do klastra. Ustaw odcisk palca każdy z certyfikatów w zmiennej CertificateThumbprint. Jeśli ustawisz IsAdmin *true*, klienta przy użyciu tego certyfikatu na nim zainstalowany zrobić administrator działań związanych z zarządzaniem w klastrze. Jeśli jest IsAdmin *false*, klienta przy użyciu tego certyfikatu można wykonać akcje dozwolone tylko w przypadku praw dostępu, zwykle tylko do odczytu. Aby uzyskać więcej informacji o rolach, zobacz [kontroli dostępu opartej na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Nazwa pospolita pierwszy certyfikat klienta należy ustawić dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu. Aby dowiedzieć się więcej na temat typowych nazw i wystawca, zobacz [Praca z certyfikatami](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Zalecane w środowisku produkcyjnym. Ten certyfikat odpowiada wystawcy certyfikatu klienta (Role administratora i bez uprawnień administratora). Nazwa pospolita i odpowiednią nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ClientCertificateCommonNames, możesz podać wystawcy.  Ułatwia to certyfikaty wystawców klienta przerzucania. Wielu wystawców może być określona, jeśli więcej niż jeden certyfikat klienta jest używany. Pusty umieszczenie IssuerCommonName wszystkie certyfikaty w odpowiednich sklepach określone w obszarze X509StoreNames.|
| ReverseProxyCertificate |Zalecane w przypadku środowiska testowego. Ten opcjonalny certyfikat może być określona, jeśli chcesz zabezpieczyć swoje [zwrotny serwer proxy](service-fabric-reverseproxy.md). Należy upewnić się, że reverseProxyEndpointPort znajduje się w elementy NodeType, jeśli użycie tego certyfikatu. |
| ReverseProxyCertificateCommonNames |Zalecane w środowisku produkcyjnym. Ten opcjonalny certyfikat może być określona, jeśli chcesz zabezpieczyć swoje [zwrotny serwer proxy](service-fabric-reverseproxy.md). Należy upewnić się, że reverseProxyEndpointPort znajduje się w elementy NodeType, jeśli użycie tego certyfikatu. |

Poniżej przedstawiono przykładową konfigurację klastra gdzie dostarczono klastra, serwera i certyfikatów klientów. W przypadku certyfikatów klastra/serwera/zwrotny serwer proxy odcisk palca i nazwa pospolita nie można skonfigurować jednocześnie tego samego typu certyfikatu.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Przerzucanie certyfikatów
Gdy używasz nazwy pospolitej certyfikatu zamiast odcisku palca Przerzucanie certyfikatów, nie wymaga uaktualniania konfiguracji klastra. Uaktualnień odcisk palca wystawcy upewnij się, że nowa lista odcisk palca przecina przy użyciu starego listy. Najpierw musisz wykonać uaktualnienie konfiguracji, przy użyciu nowych odcisków palca wystawcy, a następnie zainstaluj nowe certyfikaty (certyfikat serwera/klastra i certyfikaty wystawców) w magazynie. Zachowaj starego certyfikatu wystawcy w magazynie certyfikatów dla co najmniej dwóch godzin po zainstalowaniu nowego certyfikatu wystawcy.
Jeśli używasz magazynów wystawcy, uaktualnienie nie konfiguracji musi zostać wykonana Przerzucanie certyfikatów wystawcy. Zainstaluj nowy certyfikat wystawcy z datą jego wygaśnięcia w magazynie certyfikatów odpowiednich i usunąć starego certyfikatu wystawcy po kilku godzinach.

## <a name="acquire-the-x509-certificates"></a>Uzyskiwanie certyfikatów X.509
Do zabezpieczenia komunikacji w klastrze, należy najpierw uzyskać certyfikaty X.509 dla węzłów klastra. Ponadto aby ograniczyć liczbę połączeń do tego klastra do autoryzowanych komputerów/użytkowników, należy uzyskać i zainstalować certyfikaty dla komputerów klienckich.

W przypadku klastrów uruchomionych obciążeń produkcyjnych należy użyć [certyfikatu urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority)-podpisany certyfikat X.509 do zabezpieczenia klastra. Aby uzyskać więcej informacji na temat sposobu uzyskania tych certyfikatów, zobacz [sposobie uzyskiwania certyfikatu](https://msdn.microsoft.com/library/aa702761.aspx).

W przypadku klastrów, których używasz do celów testowych można użyć certyfikatu z podpisem własnym.

## <a name="optional-create-a-self-signed-certificate"></a>Opcjonalnie: Tworzenie certyfikatu z podpisem własnym
Jest jednym ze sposobów tworzenia certyfikatu z podpisem własnym, które mogą być chronione prawidłowo używać skryptu CertSetup.ps1 z folderu zestawu SDK usługi Service Fabric w katalogu C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edytuj ten plik, aby zmienić domyślną nazwę certyfikatu. (Wyszukaj wartość CN = ServiceFabricDevClusterCert.) Uruchom ten skrypt jako `.\CertSetup.ps1 -Install`.

Teraz należy wyeksportować certyfikat do chronionego hasłem pliku pfx. Najpierw pobierz odcisk palca certyfikatu. 
1. Z **Start** menu, uruchom **zarządzania certyfikatami komputera**. 

2. Przejdź do **komputera lokalnego** folder i Znajdź certyfikat został utworzony. 

3. Kliknij dwukrotnie certyfikat aby go otworzyć, wybierz **szczegóły** , a następnie przewiń w dół do **odcisk palca** pola. 

4. Usuń spacje, a następnie skopiuj wartość odcisku palca do następującego polecenia programu PowerShell. 

5. Zmiana `String` wartość do odpowiedniego bezpieczne hasło do ich ochrony i uruchom następujące polecenie w programie PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Aby wyświetlić szczegóły certyfikatu zainstalowanego na komputerze, uruchom następujące polecenie programu PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternatywnie, jeśli masz subskrypcję platformy Azure, postępuj zgodnie z instrukcjami w [tworzenia klastra usługi Service Fabric za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Zainstaluj certyfikaty
Po utworzeniu certyfikaty, można zainstalować je na węzłach klastra. Węzły, które muszą mieć najnowszą wersję programu Windows PowerShell 3.x na nich zainstalowany. Powtórz te kroki dla każdego węzła klastra i certyfikaty serwera i wszystkie pomocnicze certyfikaty.

1. Skopiuj plik pfx lub plików do węzła.

2. Otwórz okno programu PowerShell jako administrator, a następnie wprowadź następujące polecenia. Zastąp *$pswd* przy użyciu hasła, który został użyty do utworzenia tego certyfikatu. Zastąp *$PfxFilePath* z pełną ścieżką plik PFX skopiowane do tego węzła.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teraz można ustawić kontroli dostępu dla tego certyfikatu, aby proces usługi Service Fabric, który jest uruchamiany na koncie usługi sieciowej, można go używać, uruchamiając poniższy skrypt. Podaj odcisk palca certyfikatu i **Usługa sieciowa** dla konta usługi. Możesz sprawdzić, czy listy ACL na certyfikat są poprawne, otwierając certyfikatu w **Start** > **zarządzania certyfikatami komputera** i przeglądając **wszystkie zadania**  >  **Zarządzaj kluczami prywatnymi**.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Powtórz poprzednie kroki dla każdego certyfikatu serwera. Możesz również użyć tych kroków instalowanie certyfikatów klienta na komputerach, które chcesz zezwolić na dostęp do klastra.

## <a name="create-the-secure-cluster"></a>Tworzenie bezpiecznego klastra
Po skonfigurowaniu zabezpieczeń części pliku ClusterConfig.X509.MultiMachine.json, możesz przejść do [utworzenie klastra było możliwe](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) sekcji, aby skonfigurować węzły i Tworzenie autonomicznego klastra. Pamiętaj, aby użyć pliku ClusterConfig.X509.MultiMachine.json, podczas tworzenia klastra. Na przykład Twoje polecenie może wyglądać następująco:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po utworzeniu Windows bezpiecznego autonomicznego klastra pomyślnie uruchomiono zadanie i skonfigurowaniu uwierzytelnieni klienci się z nim łączyć, postępuj zgodnie z instrukcjami w sekcji [Połącz z klastrem przy użyciu programu PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) się z nim łączyć. Na przykład:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Następnie można uruchomić innych poleceń programu PowerShell do pracy z tym klastrem. Na przykład, można uruchomić [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) Aby wyświetlić listę węzłów w tym klastrze bezpieczne.


Aby usunąć klaster, połącz się z węzłem w klastrze, w której pobrano pakiet w usłudze Service Fabric, otwórz wiersz polecenia i przejdź do folderu pakietu. Teraz uruchom następujące polecenie:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nieprawidłowy certyfikat konfiguracji może uniemożliwić pojawi się podczas wdrażania klastra. Samodzielnie diagnozować problemy z zabezpieczeniami, Szukaj w zdarzeniu grupy podglądu **Dzienniki aplikacji i usług** > **Microsoft Service Fabric**.
> 
> 

