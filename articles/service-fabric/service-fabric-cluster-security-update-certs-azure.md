---
title: Zarządzanie certyfikatami w klastrze usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak dodawać nowe certyfikaty, certyfikat przerzucania i Usuń certyfikat do / z klastra usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: f1998ec2fe82b9fd52547fbccb208542b22bc949
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306913"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Dodawanie lub usuwanie certyfikatów dla klastra usługi Service Fabric na platformie Azure
Zalecane jest, zapoznaj się z jak Usługa Service Fabric używa certyfikatów X.509, a następnie należy zapoznać się z [scenariusze zabezpieczeń klastra](service-fabric-cluster-security.md). Należy zrozumieć certyfikat klastra i do czego służy, zanim przejdziesz dalej.

Azure sieci szkieletowe zestawu SDK usług domyślne zachowanie obciążenia certyfikatu, to wdrożenie i używanie certyfikatów zdefiniowanych najdalej wygasające datę późniejszą; niezależnie od ich definicji podstawowej lub dodatkowej konfiguracji. Nastąpi powrót do klasycznego zachowanie jest zalecana Akcja zaawansowane i wymaga ustawienia wartości parametru "UseSecondaryIfNewer" ustawienie na wartość false w ramach konfiguracji Fabric.Code.

Usługi Service fabric umożliwia określenie dwóch certyfikatów klastra, podstawowy i pomocniczy, po skonfigurowaniu zabezpieczeń certyfikatu podczas tworzenia klastra, aby oprócz certyfikatów klienta. Zapoznaj się [tworzenia klastra usługi azure za pośrednictwem portalu](service-fabric-cluster-creation-via-portal.md) lub [tworzenia klastra usługi azure za pomocą usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) dla szczegółowe informacje na temat konfigurowania na czas utworzenia. Jeśli określisz tylko jeden certyfikat klastra na czas utworzenia, następnie używany jako podstawowego certyfikatu. Po utworzeniu klastra można dodać nowego certyfikatu jako pomocniczego.

> [!NOTE]
> W przypadku bezpiecznego klastra zawsze należy co najmniej jeden prawidłowy klastra (nie odwołane i musi być ważny) certyfikatu (podstawowe lub pomocnicze) wdrożony (Jeśli nie, zatrzymuje klaster działa). 90 dni, zanim wszystkie prawidłowe certyfikaty osiągną wygaśnięcia, system generuje ostrzeżenie śledzenia, a także zdarzenia kondycji ostrzeżenia w węźle. Obecnie jest brak poczty e-mail lub innych powiadomień, które wysyła usługi Service Fabric w tym artykule. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Dodaj certyfikat pomocniczy klastra przy użyciu portalu
Nie można dodać certyfikat pomocniczy klastra za pośrednictwem witryny Azure portal, użyj programu Azure powershell. Proces jest opisany w dalszej części tego dokumentu.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Usuń certyfikat klastra przy użyciu portalu
W przypadku bezpiecznego klastra należy zawsze co najmniej jeden prawidłowy certyfikat (nie odwołane i musi być ważny). Certyfikat wdrożony za pomocą najdalej w przyszłości wygasające równolegle będą używane i usuwając go wprowadzi swoje działanie stop klastra; Upewnij się, że tylko usunięcie certyfikatu, który wygasł lub nieużywane certyfikat, który się wkrótce wygaśnie.

Aby usunąć certyfikat zabezpieczeń klastra nieużywane, przejdź do sekcji Zabezpieczenia, a następnie wybierz opcję "Usuń" z menu kontekstowego na nieużywany certyfikat.

Jeśli jest zgodne z zamiarami użytkownika można usunąć certyfikatu, który jest oznaczony jako podstawowy, należy wdrożyć certyfikat pomocniczy dalsze wygasające datę późniejszą niż podstawowy certyfikat włączenie automatycznego przerzucania zachowanie; Po zakończeniu automatycznego przerzucania, należy usunąć podstawowego certyfikatu.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Dodawanie certyfikatu pomocniczego przy użyciu programu Powershell usługi Resource Manager
> [!TIP]
> Istnieje teraz sposób lepsze i łatwiejsze do dodania dodatkowego certyfikatu przy użyciu [AzServiceFabricClusterCertificate Dodaj](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) polecenia cmdlet. Nie trzeba wykonać pozostałe kroki w tej sekcji.  Ponadto nie trzeba szablonu pierwotnie używana do tworzenia i wdrażania klastra, korzystając z [AzServiceFabricClusterCertificate Dodaj](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) polecenia cmdlet.

Tej procedury przyjęto są zaznajomieni z działaniem Menedżera zasobów i zostaną wdrożone co najmniej jeden klaster usługi Service Fabric przy użyciu szablonu usługi Resource Manager i szablon, którego użyto do skonfigurowania klastra w wygodny. Zakłada również, czy masz doświadczenia, przy użyciu formatu JSON.

> [!NOTE]
> Jeśli szukasz przykładowy szablon i parametry, które można użyć do wykonania wzdłuż lub jako punktu wyjścia, pobierz go z tego [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Edytuj szablon usługi Resource Manager

W celu ułatwienia następujące wzdłuż przykładowe 5-VM-1 — elementy NodeType — Secure_Step2.JSON zawiera wszystkie zmiany, które wprowadzamy. przykład znajduje się w temacie [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Upewnij się, że wszystkie kroki**

1. Otwórz szablon usługi Resource Manager został użyty do wdrożenia możesz klastra. (Jeśli zostały pobrane próbki z poprzednim repozytorium, następnie wdrażanie zabezpieczonego klastra, korzystając z 5-VM-1-elementy NodeType-Secure_Step1.JSON i następnie otworzyć tego szablonu).

2. Dodaj **dwa nowe parametry** "secCertificateThumbprint" i "secCertificateUrlValue" dla typu "string", do sekcji parametrów szablonu. Można skopiować poniższy fragment kodu i dodaj go do szablonu. W zależności od źródła do szablonu może już istnieć są zdefiniowane, jeśli więc Przenieś do następnego kroku. 
 
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

3. Wprowadzanie zmian w **Microsoft.ServiceFabric/clusters** zasób — Znajdź definicję zasobu "Microsoft.ServiceFabric/clusters" w szablonie. W obszarze właściwości tej definicji, można znaleźć "Certificate" JSON znacznik, który powinien wyglądać podobnie do następującego fragmentu kodu JSON:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Dodaj nowy tag "thumbprintSecondary" i nadaj mu wartość "[parameters('secCertificateThumbprint')]".  

    Teraz w definicji zasobu powinna wyglądać podobnie do następującej (w zależności od źródła szablonu może nie być tak samo jak poniższy fragment kodu). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Jeśli chcesz **przechodzą cert**, a następnie określ nowy certyfikat główny i przeniesienie bieżącego podstawowego pomocniczym. Skutkuje to przerzucania bieżącego certyfikatu podstawowego na nowy certyfikat w jednym kroku wdrażania.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Wprowadzanie zmian w **wszystkich** **Microsoft.Compute/virtualMachineScaleSets** definicji zasobów — Znajdź definicję zasobu Microsoft.Compute/virtualMachineScaleSets. Przewiń do "publisher": "Microsoft.Azure.ServiceFabric", w obszarze "virtualMachineProfile".

    W ustawieniach wydawcy usługi Service Fabric powinien zostać wyświetlony podobny do poniższego.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Dodaj nowe wpisy certyfikatu do niej
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    Właściwości powinny teraz wyglądać następująco
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Jeśli chcesz **przechodzą cert**, a następnie określ nowy certyfikat główny i przeniesienie bieżącego podstawowego pomocniczym. Skutkuje to przerzucania bieżącego certyfikatu na nowy certyfikat w jednym kroku wdrażania.     

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

    Właściwości powinny teraz wyglądać następująco    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Wprowadzanie zmian w **wszystkich** **Microsoft.Compute/virtualMachineScaleSets** definicji zasobów — Znajdź definicję zasobu Microsoft.Compute/virtualMachineScaleSets. Przewiń do "vaultCertificates":, w obszarze "OSProfile". powinno to wyglądać mniej więcej tak.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    SecCertificateUrlValue należy dodać do niego. Użyj następującego fragmentu kodu:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Wynikowy Json powinien wyglądać mniej więcej następująco.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Upewnij się, że zostało wpisane kroki 4 i 5, dla wszystkich definicji zasobu Nodetypes/Microsoft.Compute/virtualMachineScaleSets szablonu. Jeśli pominiesz jeden z nich certyfikat będzie zainstalować na czy zestawu skalowania maszyn wirtualnych i może spowodować nieprzewidywalne skutki w klastrze, w tym klastrze, przechodząc w dół (Jeśli na końcu nie ważne certyfikaty, które klaster może używać zabezpieczeń. Dlatego Sprawdź, przed kontynuowaniem.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Edytuj plik szablonu, aby odzwierciedlić nowe parametry, z którą dodałeś powyżej
Jeśli używasz próbkę z [repozytorium git](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) z nich skorzystać, można uruchomić do wprowadzania zmian w przykładowe 5-VM-1 — elementy NodeType — Secure.parameters_Step2.JSON 

Edycja parametru szablonu usługi Resource Manager pliku i dodaj dwa nowe parametry secCertificateThumbprint i secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>Wdrażanie szablonu na platformie Azure

- Teraz można przystąpić do wdrażania szablonu na platformie Azure. Otwórz wiersz polecenia w wersji 1 + PS platformy Azure.
- Zaloguj się do konta platformy Azure, a następnie wybierz określoną subskrypcję platformy azure. Jest to ważny krok dla osób, które mają dostęp do więcej niż jedną subskrypcję platformy azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Testuj szablon przed jego wdrożeniem. Użyj tej samej grupie zasobów, który aktualnie jest wdrażany klaster.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Wdróż szablon do grupy zasobów. Użyj tej samej grupie zasobów, który aktualnie jest wdrażany klaster. Uruchom polecenie New-AzResourceGroupDeployment. Nie trzeba określić tryb, ponieważ wartość domyślna to **przyrostowe**.

> [!NOTE]
> Jeśli tryb jest zakończone, możesz przypadkowo usunąć zasoby, które nie są w szablonie. Dlatego nie jest używana w tym scenariuszu.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Oto przykład wypełnionego tego samego narzędzia.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Po zakończeniu wdrożenia połączenia z klastrem przy użyciu nowego certyfikatu i wykonywania zapytań. Jeśli jest to możliwe zrobić. Następnie można usunąć starego certyfikatu. 

Jeśli używasz certyfikatu z podpisem własnym, pamiętaj je zaimportować do magazynu lokalnego certyfikatu TrustedPeople.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Jako podręczna karta informacyjna Oto polecenie, aby nawiązać połączenie z zabezpieczonym klastrem 

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
Jako podręczna karta informacyjna Oto polecenie, aby pobrać stan klastra

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Wdrażanie certyfikatów klienta do klastra.

Umożliwia te same czynności co zostało opisane w poprzednim kroki 5 mają certyfikaty wdrożone z magazynu kluczy do węzłów. Możesz po prostu należy zdefiniować i użyć innych parametrów.


## <a name="adding-or-removing-client-certificates"></a>Dodanie lub usunięcie certyfikatów klienta

Oprócz certyfikatów klastra można dodać certyfikatów klienta do wykonywania operacji zarządzania w klastrze usługi Service Fabric.

Możesz dodać dwa rodzaje certyfikatami klienta - Admin lub tylko do odczytu. Są następnie może służyć do kontrolowania dostępu do operacje administracyjne i operacje zapytań w klastrze. Domyślnie certyfikaty klastra są dodawane do listy dozwolonych certyfikatów administratora.

można określić dowolną liczbę certyfikatów klienta. Każdego dodawania/usuwania nie powoduje aktualizacji konfiguracji w klastrze usługi Service Fabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Dodawanie certyfikatów klienta — administratora lub tylko do odczytu za pomocą portalu

1. Przejdź do sekcji Zabezpieczenia, a następnie wybierz pozycję "+ uwierzytelniania" przycisk na górze sekcji Zabezpieczenia.
2. W sekcji "Dodawanie uwierzytelniania" Wybierz "Typ uwierzytelniania" - "Tylko do odczytu" lub "Administrator klienta"
3. Teraz można wybrać metody autoryzacji. Oznacza to, do usługi Service Fabric czy powinna wyszukiwać ten certyfikat za pomocą nazwy podmiotu lub odcisku palca. Ogólnie rzecz biorąc nie jest dobrą praktyką, należy użyć metody autoryzacji nazwy podmiotu. 

![Dodaj certyfikat klienta][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Usuwanie certyfikatów klienta — administrator lub tylko do odczytu przy użyciu portalu

Aby usunąć certyfikat pomocniczy używany do zabezpieczania klastra, przejdź do sekcji Zabezpieczenia, a następnie wybierz opcję "Usuń" z menu kontekstowe dla określonego certyfikatu.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły, aby uzyskać więcej informacji na temat zarządzania klastrem:

* [Proces uaktualniania klastra usługi Service Fabric i oczekiwania ze strony użytkownika](service-fabric-cluster-upgrade.md)
* [Konfigurowanie dostępu opartej na rolach dla klientów](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


