---
title: Zarządzanie certyfikatami w klastrze sieci szkieletowej usług Azure
description: W tym artykule opisano sposób dodawania nowych certyfikatów, certyfikatu rolowania i usuwania certyfikatu do lub z klastra sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458354"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Add or remove certificates for a Service Fabric cluster in Azure (Dodawanie lub usuwanie certyfikatów dla klastra usługi Service Fabric na platformie Azure)
Zaleca się zapoznanie się z tym, w jaki sposób sieci szkieletowej usług używa certyfikatów X.509 i zapoznanie się ze [scenariuszami zabezpieczeń klastra](service-fabric-cluster-security.md). Przed kontynuowaniem należy zrozumieć, czym jest certyfikat klastra i do czego służy.

Domyślne zachowanie ładowania zestawów SDK sieci szkieletowych usług Azure jest wdrożenie i użycie zdefiniowanego certyfikatu z datą wygaśnięcia najdalej w przyszłości; niezależnie od ich podstawowej lub pomocniczej definicji konfiguracji. Powrót do klasycznego zachowania jest niezalecane zaawansowane działania i wymaga ustawienia "UseSecondaryIfNewer" wartość parametru false w konfiguracji. `Fabric.Code`

Sieci szkieletowej usług umożliwia określenie dwóch certyfikatów klastra, podstawowego i pomocniczego, podczas konfigurowania zabezpieczeń certyfikatów podczas tworzenia klastra, oprócz certyfikatów klientów. Szczegółowe informacje na temat [konfigurowania klastra](service-fabric-cluster-creation-via-portal.md) w usłudze Azure za pośrednictwem portalu można znaleźć w usłudze Azure Resource Manager za pośrednictwem portalu azure za [pośrednictwem portalu](service-fabric-cluster-creation-via-arm.md) azure. Jeśli określisz tylko jeden certyfikat klastra w czasie tworzenia, to jest używany jako certyfikat podstawowy. Po utworzeniu klastra można dodać nowy certyfikat jako pomocniczy.

> [!NOTE]
> W przypadku bezpiecznego klastra zawsze potrzebny będzie co najmniej jeden prawidłowy (nie odwołany i nie wygasły) wdrożony certyfikat klastra (podstawowy lub pomocniczy) (jeśli nie, klaster przestanie działać). 90 dni przed wygaśnięciem wszystkich prawidłowych certyfikatów system generuje śledzenie ostrzeżenia, a także zdarzenie kondycji ostrzeżenia w węźle. Obecnie nie ma wiadomości e-mail ani żadnych innych powiadomień wysyłana przez sieci szkieletową usług w tym artykule. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Dodawanie pomocniczego certyfikatu klastra za pomocą portalu
Nie można dodać pomocniczego certyfikatu klastra za pośrednictwem witryny Azure portal, użyj programu Azure powershell. Proces jest opisany w dalszej części tego dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Usuwanie certyfikatu klastra za pomocą portalu
W przypadku bezpiecznego klastra zawsze potrzebny będzie co najmniej jeden ważny (nie odwołany i nie wygasły) certyfikat. Certyfikat wdrożony z najdalej w przyszłej daty wygaśnięcia będzie używany, a usunięcie go spowoduje, że klaster przestanie działać; upewnij się, że tylko usunąć certyfikat, który wygasł, lub nieużywane certyfikat, który wygasa najszybciej.

Aby usunąć nieużyny certyfikat zabezpieczeń klastra, przejdź do sekcji Zabezpieczenia i wybierz opcję "Usuń" z menu kontekstowego nieużywanego certyfikatu.

Jeśli celem jest usunięcie certyfikatu, który jest oznaczony jako podstawowy, należy wdrożyć certyfikat pomocniczy z datą wygaśnięcia dalej w przyszłości niż certyfikat podstawowy, włączając zachowanie automatycznego przerzucania; usunąć certyfikat podstawowy po zakończeniu automatycznego narzutynia.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Dodawanie certyfikatu pomocniczego przy użyciu programu Powershell Menedżera zasobów
> [!TIP]
> Teraz istnieje lepszy i łatwiejszy sposób dodawania certyfikatu pomocniczego przy użyciu polecenia cmdlet [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) Nie musisz wykonać pozostałych kroków w tej sekcji.  Ponadto nie potrzebujesz szablonu pierwotnie używanego do tworzenia i wdrażania klastra podczas korzystania z polecenia cmdlet [Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate)

W tych krokach założono, że użytkownik jest zaznajomiony z tym, jak działa Menedżer zasobów i wdrożył co najmniej jeden klaster sieci szkieletowej usług przy użyciu szablonu Menedżera zasobów i mieć pod ręką szablon używany do konfigurowania klastra. Zakłada się również, że korzystanie z JSON jest wygodne.

> [!NOTE]
> Jeśli szukasz przykładowego szablonu i parametrów, których możesz użyć do naśladowania lub jako punkt wyjścia, pobierz go z tego [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edytowanie szablonu Menedżera zasobów

Aby ułatwić obserwowanie, przykładowy 5-VM-1-NodeTypes-Secure_Step2.JSON zawiera wszystkie zmiany, które będziemy nawiązywania. próbka jest dostępna w [git-repo.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)

**Upewnij się, że postępuj zgodnie ze wszystkimi krokami**

1. Otwórz szablon Menedżera zasobów użyty do wdrożenia klastra. (Jeśli pobrano próbkę z poprzedniego repozytorium, użyj 5-VM-1-NodeTypes-Secure_Step1.JSON, aby wdrożyć bezpieczny klaster, a następnie otwórz ten szablon).

2. Dodaj **dwa nowe parametry** "secCertificateThumbprint" i "secCertificateUrlValue" typu "string" do sekcji parametrów szablonu. Można skopiować następujący fragment kodu i dodać go do szablonu. W zależności od źródła szablonu, może już mieć te zdefiniowane, jeśli tak, przejdź do następnego kroku. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Wprowadzanie zmian w zasobie **Microsoft.ServiceFabric/clusters** — znajdź definicję zasobu "Microsoft.ServiceFabric/clusters" w szablonie. W obszarze właściwości tej definicji znajdziesz tag JSON "Certyfikat", który powinien wyglądać mniej więcej jak następujący fragment kodu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Dodaj nowy tag "thumbprintSecondary" i nadaj mu wartość "[parameters('secCertificateThumbprint')]".  

    Więc teraz definicja zasobu powinna wyglądać następująco (w zależności od źródła szablonu może nie być dokładnie taka, jak fragment kodu poniżej). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Jeśli chcesz **przerzucić certyfikat,** należy określić nowy certyfikat jako podstawowy i przenieść bieżącą podstawową jako pomocniczą. Powoduje to przerzucie bieżącego certyfikatu podstawowego na nowy certyfikat w jednym kroku wdrażania.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Wprowadzanie zmian **we wszystkich** definicjach zasobów **Microsoft.Compute/virtualMachineScaleSets** — zlokalizuj definicję zasobów Microsoft.Compute/virtualMachineScaleSets. Przewiń do "wydawcy": "Microsoft.Azure.ServiceFabric", w obszarze "virtualMachineProfile".

    W ustawieniach wydawcy sieci szkieletowej usług powinien być widoczny coś takiego.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Dodawanie do niego nowych wpisów certyfikatu
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Właściwości powinny teraz wyglądać tak
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Jeśli chcesz **przerzucić certyfikat,** należy określić nowy certyfikat jako podstawowy i przenieść bieżącą podstawową jako pomocniczą. Powoduje to przerzucie bieżącego certyfikatu na nowy certyfikat w jednym kroku wdrażania.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    Właściwości powinny teraz wyglądać tak    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Wprowadzanie zmian **we wszystkich** definicjach zasobów **Microsoft.Compute/virtualMachineScaleSets** — zlokalizuj definicję zasobów Microsoft.Compute/virtualMachineScaleSets. Przewiń do "vaultCertificates": , w obszarze "OSProfile". powinno to wyglądać mniej więcej tak.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Dodaj do niego secCertificateUrlValue. użyj następującego fragmentu kodu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Teraz powstały Json powinien wyglądać mniej więcej tak.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Upewnij się, że powtarzane kroki 4 i 5 dla wszystkich nodetypes/Microsoft.Compute/virtualMachineScaleSets definicje zasobów w szablonie. Jeśli przegapisz jeden z nich, certyfikat nie zostanie zainstalowany na tym zestawie skalowania maszyny wirtualnej i będziesz miał nieprzewidywalne wyniki w klastrze, w tym klaster będzie w dół (jeśli skończysz bez prawidłowych certyfikatów, które klastra mogą używać dla zabezpieczeń. Więc dokładnie sprawdzić, przed kontynuowaniem dalej.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edytowanie pliku szablonu w celu odzwierciedlenia nowych parametrów dodanych powyżej
Jeśli używasz próbki z [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) do naśladowania, możesz rozpocząć wprowadzanie zmian w przykładzie 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Edytuj parametr Szablon Menedżera zasobów Plik, dodaj dwa nowe parametry dla secCertificateThumbprint i secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

- Teraz możesz przystąpić do wdrażania szablonu na platformie Azure. Otwórz wiersz polecenia usługi Azure PS w wersji 1+.
- Zaloguj się na swoje konto platformy Azure i wybierz określoną subskrypcję platformy Azure. Jest to ważny krok dla osób, które mają dostęp do więcej niż jednej subskrypcji platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Przetestuj szablon przed jego wdrożeniem. Użyj tej samej grupy zasobów, w której jest obecnie wdrożony klaster.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Wdrażanie szablonu w grupie zasobów. Użyj tej samej grupy zasobów, w której jest obecnie wdrożony klaster. Uruchom polecenie New-AzResourceGroupDeployment. Nie trzeba określać trybu, ponieważ wartość domyślna jest **przyrostowa**.

> [!NOTE]
> Jeśli ustawisz tryb na Ukończony, możesz przypadkowo usunąć zasoby, których nie ma w szablonie. Więc nie należy go używać w tym scenariuszu.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Oto wypełniony przykład tego samego powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po zakończeniu wdrażania połącz się z klastrem przy użyciu nowego certyfikatu i wykonaj niektóre kwerendy. Jeśli jesteś w stanie to zrobić. Następnie można usunąć stary certyfikat. 

Jeśli używasz certyfikatu z podpisem własnym, nie zapomnij zaimportować go do lokalnego magazynu certyfikatów TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Dla szybkiego odniesienia tutaj jest polecenie, aby połączyć się z bezpiecznym klastrem 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Aby uzyskać szybki punkt odniesienia, oto polecenie, aby uzyskać kondycję klastra

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Wdrażanie certyfikatów klientów w klastrze.

Można użyć tych samych kroków, jak opisano w poprzednich krokach 5, aby certyfikaty zostały wdrożone z keyvault do węzłów. Wystarczy zdefiniować i używać różnych parametrów.


## <a name="adding-or-removing-client-certificates"></a>Dodawanie lub usuwanie certyfikatów klienta

Oprócz certyfikatów klastra można dodać certyfikaty klientów w celu wykonywania operacji zarządzania w klastrze sieci szkieletowej usług.

Można dodać dwa rodzaje certyfikatów klienta — admin lub tylko do odczytu. Następnie mogą one służyć do kontrolowania dostępu do operacji administratora i operacji kwerendy w klastrze. Domyślnie certyfikaty klastra są dodawane do listy dozwolonych certyfikatów administratora.

można określić dowolną liczbę certyfikatów klienta. Każde dodanie/usunięcie powoduje aktualizację konfiguracji do klastra sieci szkieletowej usług


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Dodawanie certyfikatów klientów — administrator lub tylko do odczytu za pośrednictwem portalu

1. Przejdź do sekcji Zabezpieczenia i wybierz przycisk "+ Uwierzytelnianie" u góry sekcji zabezpieczeń.
2. W sekcji "Dodaj uwierzytelnianie" wybierz opcję "Typ uwierzytelniania" - "Klient tylko do odczytu" lub "Klient administratora"
3. Teraz wybierz Autoryzację, metodę. Oznacza to sieci szkieletowej usług, czy należy wyszukać ten certyfikat przy użyciu nazwy podmiotu lub odcisk palca. Ogólnie rzecz biorąc, nie jest dobrą praktyką bezpieczeństwa, aby użyć metody autoryzacji nazwy podmiotu. 

![Dodawanie certyfikatu klienta][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Usuwanie certyfikatów klienta — administrator lub tylko do odczytu za pomocą portalu

Aby usunąć certyfikat pomocniczy z użycia w zabezpieczeniach klastra, przejdź do sekcji Zabezpieczenia i wybierz opcję "Usuń" z menu kontekstowego określonego certyfikatu.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Dodawanie certyfikatów aplikacji do zestawu skalowania maszyny wirtualnej

Aby wdrożyć certyfikat używany dla aplikacji w klastrze, zobacz [ten przykładowy skrypt programu Powershell](scripts/service-fabric-powershell-add-application-certificate.md).

## <a name="next-steps"></a>Następne kroki
Przeczytaj te artykuły, aby uzyskać więcej informacji na temat zarządzania klastrami:

* [Proces uaktualniania klastra sieci szkieletowej usług i oczekiwania od Ciebie](service-fabric-cluster-upgrade.md)
* [Konfigurowanie dostępu opartego na rolach dla klientów](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


