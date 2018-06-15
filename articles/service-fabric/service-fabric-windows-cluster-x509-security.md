---
title: Zabezpieczanie klastra sieci szkieletowej usług Azure, w systemie Windows przy użyciu certyfikatów | Dokumentacja firmy Microsoft
description: Zabezpieczenia komunikacji w ramach klastra usługi sieć szkieletowa usług Azure autonomicznej lub lokalnie, a także między klientami i klastra.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: fe0ed74c-9af5-44e9-8d62-faf1849af68c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 62d821894521c5dea8e7577b75d9590adc829263
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212419"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpieczanie klastra autonomicznego w systemie Windows przy użyciu certyfikatów X.509
W tym artykule opisano, jak zabezpieczyć komunikację między różnych węzłów w klastrze Windows autonomicznych. On również opis do uwierzytelniania klientów nawiązujących połączenie z tym klastrem za pomocą certyfikatów X.509. Uwierzytelniania gwarantuje, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożone aplikacje i wykonywanie zadań zarządzania. Certyfikat zabezpieczeń powinien być włączony w klastrze, podczas tworzenia klastra.  

Aby uzyskać więcej informacji na temat zabezpieczeń klastra, takich jak zabezpieczeń węzła do węzła, węzeł klient zabezpieczeń i kontroli dostępu opartej na rolach, zobacz [klastra scenariusze zabezpieczeń](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Certyfikaty, których potrzebujesz?
Do uruchomienia z [pobierania pakietu sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednego z węzłów w klastrze. W pobranego pakietu można znaleźć pliku ClusterConfig.X509.MultiMachine.json. Otwórz plik i zapoznaj się z sekcją zabezpieczeń w sekcji właściwości:

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
                "CertificateIssuerThumbprint": "[Thumbprint]",
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

W tej sekcji opisano certyfikaty, które musi zapewniać bezpieczeństwo autonomiczny klastra systemu Windows. Jeśli określisz certyfikatu klastra, ustaw wartość ClusterCredentialType do _X509_. Jeśli określisz certyfikat serwera dla połączeń zewnętrznych, ustawioną ServerCredentialType _X509_. Chociaż nie jest to konieczne, firma Microsoft zaleca mieć obu tych certyfikatów zabezpieczenie klastra. Jeśli te wartości są ustawione na *X509*, należy również określić odpowiednie certyfikaty lub sieci szkieletowej usług zgłasza wyjątek. W niektórych scenariuszach, można określić tylko _ClientCertificateThumbprints_ lub _ReverseProxyCertificate_. W tych scenariuszach nie trzeba ustawić _ClusterCredentialType_ lub _ServerCredentialType_ do _X509_.


> [!NOTE]
> A [odcisk palca](https://en.wikipedia.org/wiki/Public_key_fingerprint) jest tożsamość podstawowego certyfikatu. Aby uzyskać odcisk palca certyfikatów, które możesz utworzyć, zobacz [pobrać odcisk palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

W poniższej tabeli wymieniono certyfikaty, które należy na konfigurację klastra:

| **Ustawienie CertificateInformation** | **Opis** |
| --- | --- |
| ClusterCertificate |Zalecana dla środowiska testowego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Dwa różne certyfikaty, podstawowego i pomocniczego, można użyć do uaktualnienia. Odcisk palca certyfikatu podstawowego zestawu w sekcji odcisk palca i że w zmiennych ThumbprintSecondary pomocniczej. |
| ClusterCertificateCommonNames |Zalecana dla środowiska produkcyjnego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć jednego lub dwóch klastra wspólnej nazwy certyfikatów. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy certyfikatu. Użycie więcej niż jeden certyfikat o takiej samej nazwie wspólnej można określić wielu wystawców odciski palców.|
| ClusterCertificateIssuerStores |Zalecana dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawca certyfikatu klastra. Nazwa pospolita i odpowiadającą jej nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ClusterCertificateCommonNames, zapewniają wystawcy.  Ułatwia to przerzucania klastra wystawców certyfikatów. Można określić wielu wystawców klastrowania więcej niż jeden certyfikat jest używany. Pusty whitelists IssuerCommonName wszystkie certyfikaty w magazynach odpowiedniego określone w obszarze X509StoreNames.|
| ServerCertificate |Zalecana dla środowiska testowego. Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. Dla wygody można użyć tego samego certyfikatu dla ClusterCertificate i ServerCertificate. Dwa certyfikaty inny serwer, podstawowego i pomocniczego, można użyć do uaktualnienia. Odcisk palca certyfikatu podstawowego zestawu w sekcji odcisk palca i że w zmiennych ThumbprintSecondary pomocniczej. |
| ServerCertificateCommonNames |Zalecana dla środowiska produkcyjnego. Ten certyfikat jest przesyłany do klienta, gdy próbuje połączyć się z tym klastrem. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy certyfikatu. Użycie więcej niż jeden certyfikat o takiej samej nazwie wspólnej można określić wielu wystawców odciski palców. Dla wygody można użyć tego samego certyfikatu dla ClusterCertificateCommonNames i ServerCertificateCommonNames. Można użyć jednego lub dwóch certyfikatu wspólnej nazwy serwerów. |
| ServerCertificateIssuerStores |Zalecana dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu serwera. Nazwa pospolita i odpowiadającą jej nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ServerCertificateCommonNames, zapewniają wystawcy.  Ułatwia to przerzucania wystawców certyfikatów. Wielu wystawców może być określona, jeśli jest używany więcej niż jeden certyfikat. Pusty whitelists IssuerCommonName wszystkie certyfikaty w magazynach odpowiedniego określone w obszarze X509StoreNames.|
| ClientCertificateThumbprints |Zainstaluj ten zestaw certyfikatów na klientach uwierzytelniony. Może mieć wiele certyfikatów innego klienta zainstalowanych na maszynach, które chcesz zezwolić na dostęp do klastra. Ustaw odcisk palca certyfikatu, każdy w zmiennej CertificateThumbprint. Jeśli ustawisz IsAdmin *true*, klient z tym certyfikatem na nim zainstalowany zrobić administrator działania dotyczące zarządzania w klastrze. Jeśli jest IsAdmin *false*, klient z tym certyfikatem można wykonać akcje dozwolone tylko w przypadku praw dostępu użytkownika, zwykle tylko do odczytu. Aby uzyskać więcej informacji dotyczących ról, zobacz [kontroli dostępu opartej na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Ustaw nazwę pospolitą pierwszy certyfikat klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca wystawcy certyfikatu. Aby dowiedzieć się więcej na temat wspólnej nazwy i wystawcy, zobacz [pracy z certyfikatami](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Zalecana dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcę certyfikatu klienta (role zarówno administratora, jak i bez uprawnień administratora). Nazwa pospolita i odpowiadającą jej nazwą magazynu w tej sekcji zamiast określania odcisk palca wystawcy w obszarze ClientCertificateCommonNames, zapewniają wystawcy.  Ułatwia to przerzucania klienta wystawców certyfikatów. Wielu wystawców może być określona, jeśli jest używany więcej niż jeden certyfikat klienta. Pusty whitelists IssuerCommonName wszystkie certyfikaty w magazynach odpowiedniego określone w obszarze X509StoreNames.|
| ReverseProxyCertificate |Zalecana dla środowiska testowego. Ten opcjonalny certyfikat może być określony, jeśli chcesz zabezpieczyć Twoje [odwrotny serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że ten reverseProxyEndpointPort jest ustawiony w elementów NodeType, jeśli użycie tego certyfikatu. |
| ReverseProxyCertificateCommonNames |Zalecana dla środowiska produkcyjnego. Ten opcjonalny certyfikat może być określony, jeśli chcesz zabezpieczyć Twoje [odwrotny serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że ten reverseProxyEndpointPort jest ustawiony w elementów NodeType, jeśli użycie tego certyfikatu. |

Poniżej przedstawiono przykładową konfigurację klastra gdzie dostarczono klastra serwera, a certyfikaty klienta. W przypadku certyfikatów klastra/server/reverseProxy odcisk palca i nazwa pospolita nie można skonfigurować razem dla tego samego typu certyfikatu.

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
        }
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
Jeśli nazwa pospolita certyfikatu można użyć zamiast odcisk palca, przerzucania certyfikatu nie wymaga uaktualnienia konfiguracji klastra. Uaktualnień odcisk palca wystawcy upewnij się, że nowa lista odcisk palca przecina z stara lista. Najpierw trzeba uaktualniania programu config z nowego odcisków palca wystawcy, a następnie zainstaluj nowe certyfikaty (certyfikat serwera/klastra i wystawcy certyfikatów) w magazynie. Zachowaj starego wystawcy certyfikatu w magazynie certyfikatów na co najmniej dwie godziny po zainstalowaniu nowego certyfikatu wystawcy.
Jeśli używasz magazynów wystawcy, uaktualnienie nie konfiguracji wystarczy wykonać wystawcy certyfikatu przerzucania. Zainstaluj nowy certyfikat wystawcy z datą wygaśnięcia ostatnie w magazynie certyfikatów odpowiednich i Usuń stare certyfikaty wystawcy po kilku godzinach.

## <a name="acquire-the-x509-certificates"></a>Uzyskanie certyfikatów X.509
Do zabezpieczania komunikacji wewnątrz klastra, należy najpierw uzyskać certyfikaty X.509 dla węzłów klastra. Ponadto aby ograniczyć połączenia do tego klastra do autoryzowanych komputerów/użytkowników, należy uzyskać i zainstalować certyfikaty dla komputerów klienckich.

W przypadku klastrów z systemem obciążeń produkcyjnych należy użyć [certyfikatu urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority)-podpisanego certyfikatu X.509 do zabezpieczenia klastra. Aby uzyskać więcej informacji na temat sposobu uzyskania tych certyfikatów, zobacz [uzyskiwania certyfikatu](http://msdn.microsoft.com/library/aa702761.aspx).

W przypadku klastrów korzystających do celów testowych można użyć certyfikatu z podpisem własnym.

## <a name="optional-create-a-self-signed-certificate"></a>Opcjonalnie: Utwórz certyfikat z podpisem własnym
Jest jednym ze sposobów tworzenia certyfikatu z podpisem własnym, które mogą być chronione poprawnie używać skryptu CertSetup.ps1 z folderu zestawu SDK usług sieci szkieletowej w katalogu C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edytuj ten plik, aby zmienić domyślną nazwę certyfikatu. (Wyszukaj wartość CN = ServiceFabricDevClusterCert.) Uruchom ten skrypt jako `.\CertSetup.ps1 -Install`.

Teraz można wyeksportować certyfikat do pliku .pfx chroniony hasłem. Najpierw pobierz odcisk palca certyfikatu. 
1. Z **Start** menu, uruchom **zarządzanie certyfikatami komputera**. 

2. Przejdź do **komputera lokalnego** folder i Znajdź certyfikat został utworzony. 

3. Kliknij dwukrotnie certyfikat, aby go otworzyć, wybierz **szczegóły** karcie, a następnie przewiń w dół do **odcisk palca** pola. 

4. Usuń spacje, a następnie skopiuj wartość odcisku palca do następującego polecenia programu PowerShell. 

5. Zmień `String` wartość do odpowiedniego bezpieczne hasło do ochrony i uruchom następujące polecenie w środowiska PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Aby wyświetlić szczegóły certyfikatu, który został zainstalowany na komputerze, uruchom następujące polecenie programu PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternatywnie, jeśli masz subskrypcję platformy Azure, postępuj zgodnie z instrukcjami [tworzenia klastra usługi sieć szkieletowa usług za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>Instalowanie certyfikatów
Po mają certyfikaty, można je zainstalować w węzłach klastra. Węzły muszą mieć najnowsze środowiska Windows PowerShell 3.x na nich zainstalowany. Powtórz te czynności na każdym węźle klastra i certyfikaty serwera oraz wszystkie pomocnicze certyfikaty.

1. Skopiuj plik pfx lub pliki do węzła.

2. Otwórz okno programu PowerShell jako administrator, a następnie wprowadź następujące polecenia. Zastąp *$pswd* przy użyciu hasła, który został użyty do utworzenia tego certyfikatu. Zastąp *$PfxFilePath* z pełną ścieżką PFX skopiowane do tego węzła.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teraz należy ustawić kontrolę dostępu dla tego certyfikatu, tak, aby proces sieci szkieletowej usług, w którym jest uruchomiona na koncie Usługa sieciowa, może być używany przez uruchomienie następującego skryptu. Podaj odcisk palca certyfikatu i **usługi SIECIOWEJ** dla konta usługi. Można sprawdzić, czy listy kontroli dostępu na certyfikacie są poprawne, otwierając certyfikatu w **Start** > **zarządzanie certyfikatami komputera** i patrzeć **wszystkie zadania**  >  **Zarządzaj kluczami prywatnymi**.
   
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
4. Powtórz poprzednie kroki dla każdego certyfikatu serwera. Również służy następujące kroki, aby zainstalować certyfikaty klientów na komputerach, które chcesz zezwolić na dostęp do klastra.

## <a name="create-the-secure-cluster"></a>Tworzenie bezpiecznej klastra
Po skonfigurowaniu zabezpieczeń części pliku ClusterConfig.X509.MultiMachine.json, możesz przejść do [tworzenia klastra](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) sekcji, aby skonfigurować węzły i utworzyć klaster autonomicznej. Pamiętaj, aby użyć pliku ClusterConfig.X509.MultiMachine.json podczas tworzenia klastra. Na przykład polecenia może wyglądać następująco:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po umieszczeniu Windows bezpiecznego autonomiczny klastra, w którym pomyślnie działa i skonfigurowano uwierzytelnionych klientów w celu nawiązania połączenia, wykonaj czynności opisane w sekcji [Połącz z klastrem przy użyciu programu PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) się z nią połączyć. Na przykład:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Następnie możesz uruchomić inne polecenia programu PowerShell do pracy z tym klastrem. Na przykład można uruchomić [Get ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode.md?view=azureservicefabricps) można wyświetlić listy węzłów w tym klastrze bezpieczne.


Aby usunąć klaster, łączenie z węzłem w klastrze, w której pobrano pakiet sieci szkieletowej usług, otwórz wiersz polecenia i przejdź do folderu pakietu. Teraz uruchom następujące polecenie:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nieprawidłowy certyfikat konfiguracji może uniemożliwić powtarzający się podczas wdrażania klastra. Własnym diagnozowanie problemów z zabezpieczeniami, można znaleźć w zdarzeniu grupy podglądu **Dzienniki aplikacji i usług** > **sieci szkieletowej usług Microsoft**.
> 
> 

