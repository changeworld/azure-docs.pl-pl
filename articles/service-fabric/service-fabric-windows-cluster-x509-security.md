---
title: Zabezpieczanie klastra w systemie Windows przy użyciu certyfikatów
description: Bezpieczna komunikacja w ramach autonomicznego lub lokalnego klastra usługi Azure Service Fabric, a także między klientami a klastrem.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613928"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Zabezpieczanie autonomicznego klastra w systemie Windows przy użyciu certyfikatów X.509
W tym artykule opisano sposób zabezpieczania komunikacji między różnymi węzłami autonomicznego klastra systemu Windows. Opisano w nim również sposób uwierzytelniania klientów łączących się z tym klastrem przy użyciu certyfikatów X.509. Uwierzytelnianie zapewnia, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do klastra i wdrożonych aplikacji oraz wykonywać zadania zarządzania. Podczas tworzenia klastra należy włączyć zabezpieczenia certyfikatów w klastrze.  

Aby uzyskać więcej informacji na temat zabezpieczeń klastra, takich jak zabezpieczenia między węzłami, zabezpieczenia między klientami i kontrola dostępu opartego na [rolach, zobacz Scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md).

## <a name="which-certificates-do-you-need"></a>Jakich certyfikatów potrzebujesz?
Na początek [pobierz pakiet sieci szkieletowej usług dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) do jednego z węzłów w klastrze. W pobranym pakiecie znajdziesz plik ClusterConfig.X509.MultiMachine.json. Otwórz plik i przejrzyj sekcję pod kątem zabezpieczeń w sekcji właściwości:

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

W tej sekcji opisano certyfikaty potrzebne do zabezpieczenia autonomicznego klastra systemu Windows. Jeśli określisz certyfikat klastra, ustaw wartość ClusterCredentialType na _X509_. Jeśli określisz certyfikat serwera dla połączeń zewnętrznych, ustaw ServerCredentialType na _X509_. Chociaż nie jest to obowiązkowe, zaleca się, aby mieć oba te certyfikaty dla prawidłowo zabezpieczonego klastra. Jeśli ustawisz te wartości na *X509,* należy również określić odpowiednie certyfikaty lub sieci szkieletowej usług zgłasza wyjątek. W niektórych scenariuszach można określić tylko _ClientCertificateThumbprints_ lub _ReverseProxyCertificate_. W tych scenariuszach nie trzeba ustawiać _clustercredentialtype_ lub _ServerCredentialType_ na _X509_.


> [!NOTE]
> [Odcisk palca](https://en.wikipedia.org/wiki/Public_key_fingerprint) jest podstawową tożsamością certyfikatu. Aby znaleźć odcisk palca utworzonych certyfikatów, zobacz [Pobieranie odcisku palca certyfikatu](https://msdn.microsoft.com/library/ms734695.aspx).
> 
> 

W poniższej tabeli wymieniono certyfikaty potrzebne w konfiguracji klastra:

| **Ustawienie Informacje o certyfikacie** | **Opis** |
| --- | --- |
| Certyfikat ClusterCertificate |Zalecane dla środowiska testowego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Do uaktualnienia można użyć dwóch różnych certyfikatów, podstawowego i pomocniczego. Ustaw odcisk palca certyfikatu podstawowego w sekcji Odcisk palca i pomocniczy w Zmiennych ThumbprintSecondary. |
| ClusterCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten certyfikat jest wymagany do zabezpieczenia komunikacji między węzłami w klastrze. Można użyć jednej lub dwóch nazw wspólnych certyfikatów klastra. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy tego certyfikatu. Jeśli używany jest więcej niż jeden certyfikat o tej samej nazwie pospolitej, można określić wiele odcisków palców wystawcy.|
| ClusterCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu klastra. W tej sekcji można podać nazwę pospolitą wystawcy i odpowiednią nazwę magazynu, zamiast określać odcisk palca wystawcy w obszarze ClusterCertificateCommonNames.  Ułatwia to przerzucanie certyfikatów wystawcy klastra. Można określić wielu wystawców, jeśli używany jest więcej niż jeden certyfikat klastra. Pusty IssuerCommonName umieszcza na białej liście wszystkie certyfikaty w odpowiednich sklepach określonych w obszarze X509StoreNames.|
| ServerCertificate |Zalecane dla środowiska testowego. Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. Dla wygody można użyć tego samego certyfikatu dla clustercertificate i ServerCertificate. Do uaktualnienia można użyć dwóch różnych certyfikatów serwera, podstawowego i pomocniczego. Ustaw odcisk palca certyfikatu podstawowego w sekcji Odcisk palca i pomocniczy w Zmiennych ThumbprintSecondary. |
| ServerCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten certyfikat jest prezentowany klientowi podczas próby nawiązania połączenia z tym klastrem. CertificateIssuerThumbprint odpowiada odcisk palca wystawcy tego certyfikatu. Jeśli używany jest więcej niż jeden certyfikat o tej samej nazwie pospolitej, można określić wiele odcisków palców wystawcy. Dla wygody można użyć tego samego certyfikatu dla ClusterCertificateCommonNames i ServerCertificateCommonNames. Można użyć jednej lub dwóch nazw wspólnych certyfikatów serwera. |
| ServerCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu serwera. W tej sekcji można podać nazwę pospolitą wystawcy i odpowiednią nazwę sklepu, zamiast określać odcisk palca wystawcy w obszarze ServerCertificateCommonNames.  Ułatwia to przerzucanie certyfikatów wystawców serwera. Można określić wielu wystawców, jeśli używany jest więcej niż jeden certyfikat serwera. Pusty IssuerCommonName umieszcza na białej liście wszystkie certyfikaty w odpowiednich sklepach określonych w obszarze X509StoreNames.|
| ClientCertificateThumbprints |Zainstaluj ten zestaw certyfikatów na uwierzytelnionych klientach. Na komputerach, które mają umożliwić dostęp do klastra, może być zainstalowanych wiele różnych certyfikatów klienta. Ustaw odcisk palca każdego certyfikatu w zmiennej CertificateThumbprint. Jeśli ustawisz isadmin na *true*, klient z tym certyfikatem zainstalowany na nim może wykonać działania administratora zarządzania w klastrze. Jeśli IsAdmin jest *false*, klient z tym certyfikatem można wykonywać akcje dozwolone tylko dla praw dostępu użytkownika, zazwyczaj tylko do odczytu. Aby uzyskać więcej informacji na temat ról, zobacz [Kontrola dostępu oparta na rolach (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac). |
| ClientCertificateCommonNames |Ustaw nazwę pospolitą pierwszego certyfikatu klienta dla CertificateCommonName. CertificateIssuerThumbprint jest odcisk palca dla wystawcy tego certyfikatu. Aby dowiedzieć się więcej o nazwach pospolitych i wystawcy, zobacz [Praca z certyfikatami](https://msdn.microsoft.com/library/ms731899.aspx). |
| ClientCertificateIssuerStores |Zalecane dla środowiska produkcyjnego. Ten certyfikat odpowiada wystawcy certyfikatu klienta (zarówno ról administratora, jak i nie-administratora). W tej sekcji można podać nazwę pospolitą wystawcy i odpowiednią nazwę magazynu, zamiast określać odcisk palca wystawcy w obszarze ClientCertificateCommonNames.  Ułatwia to przerzucanie certyfikatów wystawcy klienta. Można określić wiele wystawców, jeśli używany jest więcej niż jeden certyfikat klienta. Pusty IssuerCommonName umieszcza na białej liście wszystkie certyfikaty w odpowiednich sklepach określonych w obszarze X509StoreNames.|
| ReverseProxyCertificate |Zalecane dla środowiska testowego. Ten opcjonalny certyfikat można określić, jeśli chcesz zabezpieczyć [odwrócony serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że reverseProxyEndpointPort jest ustawiona w nodeTypes, jeśli używasz tego certyfikatu. |
| OdwróconyProxyCertificateCommonNames |Zalecane dla środowiska produkcyjnego. Ten opcjonalny certyfikat można określić, jeśli chcesz zabezpieczyć [odwrócony serwer proxy](service-fabric-reverseproxy.md). Upewnij się, że reverseProxyEndpointPort jest ustawiona w nodeTypes, jeśli używasz tego certyfikatu. |

Oto przykładowa konfiguracja klastra, w której dostarczono certyfikaty klastra, serwera i klienta. W przypadku certyfikatów cluster/server/reverseProxy odcisk palca i nazwa pospolita nie mogą być skonfigurowane razem dla tego samego typu certyfikatu.

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

## <a name="certificate-rollover"></a>Przerzucie certyfikatu
W przypadku używania nazwy pospolitej certyfikatu zamiast odcisku palca najazd certyfikatu nie wymaga uaktualnienia konfiguracji klastra. W przypadku uaktualnień odcisków palców wystawcy upewnij się, że nowa lista odcisków palców przecina się ze starą listą. Najpierw musisz wykonać uaktualnienie konfiguracji za pomocą nowych odcisków palców wystawcy, a następnie zainstalować nowe certyfikaty (zarówno certyfikaty klastra/serwera, jak i certyfikaty wystawcy) w magazynie. Stary certyfikat wystawcy należy przechowywać w magazynie certyfikatów przez co najmniej dwie godziny po zainstalowaniu nowego certyfikatu wystawcy.
Jeśli używasz magazynów wystawców, nie trzeba przeprowadzać uaktualniania konfiguracji w przypadku rolowania certyfikatu wystawcy. Zainstaluj nowy certyfikat wystawcy z drugą datą wygaśnięcia w odpowiednim magazynie certyfikatów i usuń stary certyfikat wystawcy po kilku godzinach.

## <a name="acquire-the-x509-certificates"></a>Zdobądź certyfikaty X.509
Aby zabezpieczyć komunikację w klastrze, należy najpierw uzyskać certyfikaty X.509 dla węzłów klastra. Ponadto, aby ograniczyć połączenie z tym klastrem do autoryzowanych maszyn/użytkowników, należy uzyskać i zainstalować certyfikaty dla komputerów klienckich.

W przypadku klastrów z uruchomionymi obciążeniami produkcyjnymi należy użyć certyfikatu X.509 podpisanego [przez urząd certyfikacji(CA)](https://en.wikipedia.org/wiki/Certificate_authority)w celu zabezpieczenia klastra. Aby uzyskać więcej informacji na temat uzyskiwania tych certyfikatów, zobacz [Jak uzyskać certyfikat](https://msdn.microsoft.com/library/aa702761.aspx).

W przypadku klastrów używanych do celów testowych można użyć certyfikatu z podpisem własnym.

## <a name="optional-create-a-self-signed-certificate"></a>Opcjonalnie: tworzenie certyfikatu z podpisem własnym
Jednym ze sposobów utworzenia certyfikatu z podpisem własnym, który można poprawnie zabezpieczyć, jest użycie skryptu CertSetup.ps1 w folderze SDK sieci szkieletowej usług w katalogu C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Edytuj ten plik, aby zmienić domyślną nazwę certyfikatu. (Poszukaj wartości CN=ServiceFabricDevClusterCert).) Uruchom ten `.\CertSetup.ps1 -Install`skrypt jako .

Teraz wyeksportuj certyfikat do pliku .pfx z chronionym hasłem. Najpierw pobierz odcisk palca certyfikatu. 
1. Z menu **Start** uruchom polecenie **Zarządzaj certyfikatami komputera**. 

2. Przejdź do folderu **Komputer lokalny\Osobisty** i znajdź utworzony certyfikat. 

3. Kliknij dwukrotnie certyfikat, aby go otworzyć, wybierz kartę **Szczegóły** i przewiń w dół do pola **Odcisk palca.** 

4. Usuń spacje i skopiuj wartość odcisku palca do następującego polecenia programu PowerShell. 

5. Zmień `String` wartość na odpowiednie bezpieczne hasło, aby ją chronić, i uruchom następujące elementy w programie PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Aby wyświetlić szczegóły certyfikatu zainstalowanego na komputerze, uruchom następujące polecenie programu PowerShell:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Alternatywnie, jeśli masz subskrypcję platformy Azure, wykonaj kroki opisane w [sekcji Tworzenie klastra sieci szkieletowej usług przy użyciu usługi Azure Resource Manager.](service-fabric-cluster-creation-via-arm.md)

## <a name="install-the-certificates"></a>Instalowanie certyfikatów
Po certyfikatach można je zainstalować w węzłach klastra. Węzły muszą mieć zainstalowane najnowsze programy Windows PowerShell 3.x. Powtórz te kroki w każdym węźle dla certyfikatów klastra i serwera oraz wszelkich certyfikatów pomocniczych.

1. Skopiuj plik pfx lub pliki do węzła.

2. Otwórz okno programu PowerShell jako administrator i wprowadź następujące polecenia. Zastąp *$pswd* hasłem użytym do utworzenia tego certyfikatu. Zastąp *$PfxFilePath* pełną ścieżką .pfx skopiowaną do tego węzła.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Teraz ustaw kontrolę dostępu na tym certyfikacie, aby proces sieci szkieletowej usług, który działa w ramach konta usługi sieciowej, mógł go używać, uruchamiając następujący skrypt. Podaj odcisk palca certyfikatu i **usługi sieciowej** dla konta usługi. Można sprawdzić, czy listy ACL na certyfikacie są poprawne, otwierając certyfikat w **trybie Start** > **Manage certyfikatów komputera** i zajrzyj do wszystkich **zadań** > **Zarządzanie kluczami prywatnymi**.
   
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
4. Powtórz poprzednie kroki dla każdego certyfikatu serwera. Można również użyć tych kroków, aby zainstalować certyfikaty klienta na komputerach, które mają umożliwić dostęp do klastra.

## <a name="create-the-secure-cluster"></a>Tworzenie bezpiecznego klastra
Po skonfigurowaniu sekcji zabezpieczeń pliku ClusterConfig.X509.MultiMachine.json można przejść do sekcji [Utwórz klaster,](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) aby skonfigurować węzły i utworzyć klaster autonomiczny. Pamiętaj, aby podczas tworzenia klastra używać pliku ClusterConfig.X509.MultiMachine.json. Na przykład polecenie może wyglądać następująco:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Po pomyślnym uruchomieniu bezpiecznego autonomicznego klastra systemu Windows i skonfigurowaniu uwierzytelnionych klientów do łączenia się z nim wykonaj kroki opisane w sekcji [Łączenie się z klastrem przy użyciu programu PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) w celu nawiązania z nim połączenia. Przykład:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

Następnie można uruchomić inne polecenia programu PowerShell do pracy z tym klastrem. Na przykład można uruchomić [Get-ServiceFabricNode,](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) aby wyświetlić listę węzłów w tym bezpiecznym klastrze.


Aby usunąć klaster, połącz się z węzłem klastra, w którym pobrano pakiet sieci szkieletowej usług, otwórz wiersz polecenia i przejdź do folderu pakietów. Teraz uruchom następujące polecenie:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Nieprawidłowa konfiguracja certyfikatu może uniemożliwić klastrowi utworzenie podczas wdrażania. Aby samodzielnie zdiagnozować problemy z zabezpieczeniami, poszukaj w grupie Podgląd zdarzeń **Dzienniki sieci szkieletowej** > **usługi Microsoft.**
> 
> 

