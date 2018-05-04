---
title: Zarządzanie certyfikatami w klastrze usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób dodawać nowe certyfikaty, certyfikat przerzucania i Usuń certyfikat do lub z klastra sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: chackdan
ms.openlocfilehash: 70de9ed6175a5fe7a8e3eed9c0a921cf8184d50d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Dodaj lub Usuń certyfikaty dla klastra usługi sieć szkieletowa na platformie Azure
Zalecane jest, zapoznaj się z używaniu certyfikatów X.509 w sieci szkieletowej usług, a następnie należy zapoznać się z [klastra scenariusze zabezpieczeń](service-fabric-cluster-security.md). Trzeba zrozumieć, jakie certyfikat klastra jest i do czego służy, przed przejściem dalej.

Sieć szkieletowa usług pozwala określić dwa certyfikaty klastra, podstawowego i pomocniczego, podczas konfigurowania certyfikatu zabezpieczeń podczas tworzenia klastra, oprócz certyfikatów klienta. Zapoznaj się [tworzenia klastra platformy azure za pośrednictwem portalu](service-fabric-cluster-creation-via-portal.md) lub [tworzenia klastra platformy azure za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) dla informacji o konfiguracji na czas utworzenia. Jeśli określisz tylko jeden certyfikat klastra na czas utworzenia, następnie używany jako podstawowego certyfikatu. Po utworzeniu klastra można dodać nowego certyfikatu jako dodatkowej.

> [!NOTE]
> Bezpieczne klastra zawsze należy co najmniej jeden prawidłowy klastra (nie odwołane i niewygasły) certyfikatu (podstawowe lub pomocnicze) wdrożony (Jeśli nie, zatrzymuje klastra działa). 90 dni, zanim wszystkie prawidłowe certyfikaty osiągną wygaśnięcia, system generuje ostrzeżenie śledzenia, a także zdarzenie kondycji ostrzeżenia w węźle. Jest obecnie Brak poczty e-mail ani innych powiadomień, który wysyła sieci szkieletowej usług w tym artykule. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Dodawanie certyfikatu dodatkowej klastra przy użyciu portalu
Nie można dodać certyfikatu dodatkowej klastra za pośrednictwem portalu Azure, użyj programu Azure powershell. Proces jest opisane w dalszej części tego dokumentu.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>Wymiana certyfikatów klastra przy użyciu portalu
Po pomyślnie wdrożeniu certyfikatu klastra pomocniczego, jeśli chcesz zamienić podstawowe i pomocnicze, następnie przejdź do sekcji zabezpieczeń i wybierz opcję "Zamiany z podstawowej" z menu kontekstowego, można zamienić dodatkowej certyfikatu z serwera podstawowego certyfikat.

![Wymiany certyfikatów][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Usuń certyfikat klastra za pomocą portalu
Bezpieczne klastra należy zawsze co najmniej jeden prawidłowy (nie odwołane i niewygasły) certyfikat (podstawowe lub pomocnicze) wdrożone w przeciwnym razie klaster przestanie działać.

Aby usunąć dodatkowego certyfikatu z używany do zabezpieczenia klastra, przejdź do sekcji Zabezpieczenia i zaznacz opcję "Delete" z menu kontekstowego na dodatkowego certyfikatu.

Jeśli Twoje zamierzeniu można usunąć certyfikatu, który jest oznaczony jako podstawowy, następnie należy najpierw wymiany z lokacji dodatkowej, a następnie usuń pomocniczą po zakończeniu uaktualniania.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Dodania dodatkowego certyfikatu przy użyciu programu Powershell Menedżera zasobów
> [!TIP]
> Teraz jest lepsze i łatwiejsze w sposób dodać dodatkowego certyfikatu za pomocą [AzureRmServiceFabricClusterCertificate Dodaj](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) polecenia cmdlet. Nie trzeba wykonaj pozostałe kroki opisane w tej sekcji.  Ponadto nie trzeba pierwotnie używana do tworzenia i wdrażania klastra przy użyciu szablonu [AzureRmServiceFabricClusterCertificate Dodaj](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) polecenia cmdlet.

Tych krokach przyjęto założenie, zapoznali się z działania Menedżera zasobów i zostaną wdrożone co najmniej jeden klaster sieci szkieletowej usług za pomocą szablonu usługi Resource Manager i szablon, który został użyty do skonfigurowania klastra pod ręką. Zakłada się również, że masz doświadczenia, za pomocą formatu JSON.

> [!NOTE]
> Jeśli szukasz przykładowy szablon i parametrów, które można wykonać wzdłuż lub jako punkt początkowy, następnie pobierz go z tego [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edytuj szablon Menedżera zasobów

W celu ułatwienia następujące wzdłuż próbki 5-VM-1-elementów NodeType-Secure_Step2.JSON zawiera wszystkie wprowadzamy zmiany. przykład jest dostępny w [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Upewnij się, że wszystkie kroki**

1. Otwórz szablon usługi Resource Manager można używać do wdrażania należy klastra. (Jeśli pobrano próbki z poprzednim repozytorium, następnie użyj 5-VM-1-elementów NodeType-Secure_Step1.JSON, aby wdrożyć klaster bezpiecznego i następnie otwarcia tego szablonu).

2. Dodaj **dwa nowe parametry** "secCertificateThumbprint" i "secCertificateUrlValue" z typu "string" w sekcji parametrów szablonu. Można skopiować poniższy fragment kodu i dodaj go do szablonu. W zależności od źródła do szablonu może już istnieć te zdefiniowane, jeśli więc Przenieś do następnego kroku. 
 
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

3. Wprowadzanie zmian do **Microsoft.ServiceFabric/clusters** zasobu, zlokalizować definicji zasobu "Microsoft.ServiceFabric/clusters" w szablonie. We właściwościach tej definicji, można znaleźć "Certyfikatu" JSON tagu, który powinien być podobny do następującego fragmentu kodu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Dodaj nowy znacznik "thumbprintSecondary" i nadaj mu wartość "[parameters('secCertificateThumbprint')]".  

    Tak, teraz definicji zasobu powinna wyglądać następująco (w zależności od źródła szablonu, może nie być dokładnie takie same jak poniższy fragment). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Jeśli chcesz **przerzucane cert**, określ nowego certyfikatu jako podstawowego i przenoszenie bieżącej podstawowej pomocniczym. W efekcie Przerzucanie bieżącego certyfikatu podstawowego do nowego świadectwa w jednym kroku wdrożenia.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Wprowadzanie zmian do **wszystkie** **Microsoft.Compute/virtualMachineScaleSets** definicji zasobów — Znajdź Microsoft.Compute/virtualMachineScaleSets definicji zasobu. Przewiń do "publisher": "Microsoft.Azure.ServiceFabric", w obszarze "virtualMachineProfile".

    W ustawieniach wydawcy sieci szkieletowej usług powinny zostać wyświetlone informacje podobne.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Dodaj nowe wpisy certyfikatu do niej
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Właściwości powinna wyglądać tak jak to
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Jeśli chcesz **przerzucane cert**, określ nowego certyfikatu jako podstawowego i przenoszenie bieżącej podstawowej pomocniczym. W efekcie Przerzucanie bieżącego certyfikatu do nowego świadectwa w jednym kroku wdrożenia.     

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

    Właściwości powinna wyglądać tak jak to    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Wprowadzanie zmian do **wszystkie** **Microsoft.Compute/virtualMachineScaleSets** definicji zasobów — Znajdź Microsoft.Compute/virtualMachineScaleSets definicji zasobu. Przewiń do "vaultCertificates":, w obszarze "OSProfile". powinien on wyglądać następująco.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Dodaj secCertificateUrlValue do niej. Użyj następującego fragmentu kodu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Teraz wynikowy Json powinien wyglądać następująco.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Upewnij się, że zostało wpisane kroki 4 i 5, wszystkie definicje zasobów Nodetypes/Microsoft.Compute/virtualMachineScaleSets szablonu. Jeśli pominiesz jeden z nich, certyfikat zostanie zainstalować na czy zestawu skalowania maszyn wirtualnych i może spowodować nieprzewidywalne skutki w klastrze, w tym klastrze przechodzi w dół (Jeśli na końcu nie prawidłowe certyfikaty, które klaster może używać zabezpieczeń. Dlatego dwukrotnie wyboru przed kontynuacją.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edytowanie pliku szablonu, aby uwzględnić nowe parametry dodanych powyżej
Jeśli używasz próbki z [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) aby z niego skorzystać, można uruchomić wprowadzić zmiany w próbki 5-VM-1-elementów NodeType — Secure.paramters_Step2.JSON 

Przeprowadź edycję pliku parametru szablonu usługi Resource Manager, dodaj dwa nowe parametry secCertificateThumbprint i secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

- Teraz można przystąpić do wdrażania szablonu Azure. Otwórz wiersz polecenia w wersji 1 + Azure PS.
- Zaloguj się do konta platformy Azure i wybierz określoną subskrypcję platformy azure. Jest to ważny krok do pracowników, którzy mają dostęp do więcej niż jedną subskrypcją platformy azure.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Przetestuj szablon przed jego wdrożeniem. Użyj tej samej grupie zasobów, który obecnie jest wdrożony klastra.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Wdrażanie szablonu w danej grupie zasobów. Użyj tej samej grupie zasobów, który obecnie jest wdrożony klastra. Uruchom polecenie New-AzureRmResourceGroupDeployment. Nie trzeba określać trybu, ponieważ wartość domyślna to **przyrostowe**.

> [!NOTE]
> Jeśli tryb jest zakończone, możesz usunąć przypadkowo zasobów, które nie znajdują się w szablonie. Dlatego nie jest używana w tym scenariuszu.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Oto wypełnionego przykład tego samego środowiska powershell.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Po zakończeniu wdrażania należy połączyć się z klastrem przy użyciu nowego certyfikatu i wykonywania niektórych zapytań. Jeśli można zrobić. Następnie można usunąć starego certyfikatu. 

Jeśli używasz certyfikatu z podpisem własnym, pamiętaj je zaimportować do magazynu certyfikatów TrustedPeople lokalnego.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Dla wygody Oto polecenia do nawiązania bezpiecznego klastra 

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
Dla wygody Oto polecenie, aby pobrać stan klastra

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>Wdrażanie certyfikatów aplikacji do klastra.

Te same kroki można użyć w sposób opisany w poprzedzających kroki 5 mają certyfikaty wdrożone z keyvault do węzłów. Możesz po prostu należy zdefiniować i użyć innych parametrów.


## <a name="adding-or-removing-client-certificates"></a>Dodanie lub usunięcie certyfikatów klienta

Poza certyfikatami z klastra można dodawać certyfikatów klienta do wykonywania operacji zarządzania w klastrze usługi sieć szkieletowa usług.

Możesz dodać dwa rodzaje certyfikatów klienta — administratora lub w trybie tylko do odczytu. Następnie mogą one być używane do kontrolowania dostępu do operacji zapytania w klastrze i operacje administracyjne. Domyślnie certyfikaty klastra zostaną dodane do listy dozwolonych certyfikatów administratora.

można określić dowolną liczbę certyfikatów klienta. Każdy dodanie/usunięcie powoduje aktualizacji konfiguracji klastra sieci szkieletowej usług


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Dodawanie certyfikatów klienta — administratora lub w trybie tylko do odczytu za pomocą portalu

1. Przejdź do sekcji zabezpieczeń, a następnie wybierz pozycję "+ uwierzytelniania" przycisk u góry sekcji zabezpieczeń.
2. W sekcji "Dodaj uwierzytelniania" Wybierz "Typ uwierzytelniania" - "Tylko do odczytu" lub "Admin klienta"
3. Teraz wybierz metodę autoryzacji. Wskazuje sieci szkieletowej usług czy powinna wyszukiwać ten certyfikat za pomocą nazwy podmiotu lub odcisku palca. Ogólnie rzecz biorąc nie jest dobrą praktyką przy użyciu metody autoryzacji nazwy podmiotu. 

![Dodawanie certyfikatu klienta][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Usunięcie certyfikatów klienta — administratora lub tylko do odczytu za pomocą portalu

Aby usunąć dodatkowego certyfikatu z używany do zabezpieczenia klastra, przejdź do sekcji Zabezpieczenia i zaznacz opcję "Delete" z menu kontekstowego na określonego certyfikatu.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły, aby uzyskać więcej informacji na temat zarządzania klastrem:

* [Proces uaktualniania klastra sieci szkieletowej usług oraz oczekiwań od użytkownika](service-fabric-cluster-upgrade.md)
* [Ustawienia dostępu opartego na rolach dla klientów](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


