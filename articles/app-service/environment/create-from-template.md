---
title: Utwórz środowisko usługi Azure App Service przy użyciu szablonu usługi Resource Manager
description: Wyjaśniono, jak utworzyć środowisko zewnętrznego lub ILB usłudze Azure App Service przy użyciu szablonu usługi Resource Manager
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 69ead9e6dae400ce16cb2442c7b1c13e348d1572
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Utwórz ASE za pomocą szablonu usługi Azure Resource Manager

## <a name="overview"></a>Przegląd
Środowiska usługi aplikacji Azure (ASEs) mogą być tworzone z Internetu dostępnym punkcie końcowym lub punkt końcowy wewnętrznego adresu w sieci wirtualnej platformy Azure (VNet). Podczas tworzenia z wewnętrzny punkt końcowy tego punktu końcowego jest udostępniane przez platformę Azure składnika o nazwie wewnętrznego modułu równoważenia obciążenia (ILB). ASE na wewnętrzny adres IP jest nazywany ASE ILB. ASE z publiczny punkt końcowy jest nazywany ASE zewnętrznych. 

ASE mogą być tworzone przy użyciu portalu Azure lub szablonu usługi Azure Resource Manager. W tym artykule przedstawiono kroki i składni konieczne utworzenie ASE zewnętrznych lub ILB ASE z szablonami usługi Resource Manager. Aby dowiedzieć się, jak utworzyć ASE w portalu Azure, zobacz [wprowadzić zewnętrznego ASE] [ MakeExternalASE] lub [upewnij ASE ILB][MakeILBASE].

Po utworzeniu ASE w portalu Azure, możesz utworzyć sieci wirtualnej w tym samym czasie, lub wybrać istniejących sieci wirtualnej do wdrożenia w. Po utworzeniu ASE z szablonu musi rozpoczynać się od: 

* Menedżer zasobów sieci wirtualnej.
* Podsieci w tej sieci wirtualnej. Firma Microsoft zaleca rozmiar podsieci ASE `/25` 128 adresy umożliwiających rozwój w przyszłości. Po utworzeniu ASE, nie można zmienić rozmiar.
* Identyfikator zasobu z sieci wirtualnej. Te informacje można uzyskać z portalu Azure w obszarze właściwości sieci wirtualnej.
* Subskrypcję, którą chcesz wdrożyć w.
* Lokalizacja, którą chcesz wdrożyć w.

Aby zautomatyzować tworzenie użytkownika ASE:

1. Utwórz ASE na podstawie szablonu. Jeśli utworzysz zewnętrznych ASE zakończeniu po wykonaniu tego kroku. Jeśli tworzysz ASE ILB, istnieje kilka czynności wykonywane.

2. Po utworzeniu sieci ASE ILB przekazaniu pasujący domenę ILB ASE certyfikat SSL.

3. Przekazany certyfikat SSL jest przypisany do ILB ASE jako swojego certyfikatu SSL "domyślny".  Ten certyfikat jest używany dla ruchu protokołu SSL do aplikacji w ILB ASE, jeśli korzystają z typowych domeny głównej, przypisane do ASE (na przykład https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Utwórz ASE
Szablon usługi Resource Manager, który pozwala ASE i jego pliku skojarzone z nimi parametry jest dostępna [w przykładzie] [ quickstartasev2create] w witrynie GitHub.

Jeśli chcesz ASE ILB, użyj tych szablonu usługi Resource Manager [przykłady][quickstartilbasecreate]. One automatycznie dostosowują się do których przypadek użycia. Większość parametrów w *azuredeploy.parameters.json* plików są często używane do tworzenia ILB ASEs i ASEs zewnętrznych. Poniższa lista uwidacznia parametry specjalne uwagi lub są unikatowe, podczas tworzenia ASE ILB:

* *internalLoadBalancingMode*: W większości przypadków będzie powiązany zestaw to 3, co oznacza ruchu HTTP/HTTPS na porty 80/443 i kontroli/danych portów kanału nasłuch przez usługę FTP na ASE przydzielone ILB sieci wirtualnej wewnętrzny adres. Jeśli ta właściwość jest równa 2, tylko FTP związane z usługą portów (kanały zarówno kontroli, jak i dane) są powiązane z adresem ILB. Pozostaje ruchu HTTP/HTTPS na publiczny adres VIP.
* *dnsSuffix*: ten parametr określa domyślnej domeny katalogu głównego jest przypisany do ASE. W publicznej wersji usługi Azure App Service, domyślnej domeny głównej dla wszystkich aplikacji w sieci web jest *azurewebsites.net*. Ponieważ ASE ILB jest wewnętrzna sieć wirtualna klienta, go nie ma sensu do korzystania z usługi publicznego domyślnego głównym domeny. Zamiast tego ASE ILB powinien mieć domyślnej domeny katalogu głównego, pasującą do użytku w wewnętrznej sieci wirtualnej firmy. Na przykład firma Contoso może użyć domyślną domeną katalogu głównego *wewnętrzny contoso.com* dla aplikacji, które mają być rozpoznawalna i dostępny tylko w ramach sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: Domyślnie ustawienie tego parametru na wartość 0 w *azuredeploy.json* pliku, ponieważ ILB ASEs mieć tylko jeden adres ILB. Nie ma żadnych jawnych adresów IP protokołu SSL dla ASE ILB. W związku z tym puli adresów IP protokołu SSL dla ASE ILB musi mieć ustawioną wartość zero. W przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Po *azuredeploy.parameters.json* plik zostanie wypełnione, Utwórz ASE przy użyciu programu PowerShell fragmentu kodu. Zmienianie ścieżki pliku do dopasowania lokalizacji pliku szablonu usługi Resource Manager na tym komputerze. Pamiętaj, aby podać własne wartości dla nazwy wdrożenia usługi Resource Manager i nazwę grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trwa około godziny ASE ma zostać utworzony. Następnie ASE zostaną wyświetlone w portalu na liście ASEs subskrypcji, która wyzwoliła wdrożenia.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Przekazać i skonfigurować certyfikat SSL "domyślny"
Certyfikat SSL musi być skojarzony z ASE jako certyfikat SSL "domyślne", który jest używany do ustanawiania połączeń SSL do aplikacji. Jeśli ASE domyślny sufiks DNS jest *wewnętrzny contoso.com*, połączenie https://some-random-app.internal-contoso.com wymaga certyfikatu SSL, który jest prawidłowy dla **.internal contoso.com*. 

Uzyskaj prawidłowy certyfikat SSL przy użyciu certyfikatu wewnętrznego urzędów, zakup certyfikatu od zewnętrznego wystawcy lub przy użyciu certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu protokołu SSL następujące atrybuty certyfikatu musi być prawidłowo skonfigurowane:

* **Temat**: ten atrybut musi być równa **.your głównego domeny here.com*.
* **Alternatywna nazwa podmiotu**: ten atrybut musi zawierać zarówno **.your głównego domeny here.com* i **.scm.your-głównego-domeny-here.com*. Połączenia SSL do witryny SCM/Kudu skojarzone z każdej aplikacji używać adresu w postaci *your-app-name.scm.your-root-domain-here.com*.

Przy użyciu prawidłowego certyfikatu SSL w ręcznie potrzebne są dwa dodatkowe czynności przygotowawczych. Przekonwertuj lub zapisz certyfikat SSL jako plik pfx. Pamiętać, że plik PFX musi zawierać wszystkie pośrednie i certyfikaty główne. Zabezpiecz go przy użyciu hasła.

Plik PFX wymaga do przekonwertowania na ciąg w formacie base64, ponieważ certyfikat SSL jest przekazywany za pomocą szablonu usługi Resource Manager. Ponieważ szablonów Resource Manager są plikami tekstowymi, należy przekonwertować plik PFX do ciąg w formacie base64. W ten sposób może zostać dołączony jako parametr szablonu.

Użyj następującego fragmentu kodu programu PowerShell do:

* Wygeneruj certyfikat z podpisem własnym.
* Wyeksportuj certyfikat jako plik pfx.
* Konwertowanie pliku .pfx na ciąg kodowany w formacie base64.
* Zapisz ciąg kodowany w formacie base64 w oddzielnym pliku. 

Ten kod programu PowerShell dla kodowania base64 pochodzi z [blogu skrypty programu PowerShell][examplebase64encoding]:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Po certyfikat SSL jest pomyślnie wygenerowany i konwertowana na ciąg kodowany w formacie base64, użyj szablonu usługi Resource Manager przykład [skonfigurować certyfikat SSL domyślne] [ quickstartconfiguressl] w witrynie GitHub. 

Parametry w *azuredeploy.parameters.json* plików są wyświetlane tutaj:

* *appServiceEnvironmentName*: Nazwa ASE ILB skonfigurowany.
* *existingAseLocation*: ciąg tekstowy zawierający wdrożonym ILB ASE region platformy Azure.  Na przykład: "Południowo-środkowe Stany".
* *pfxBlobString*: ciąg kodowany w formacie based64 reprezentację pliku .pfx. Użyj fragment kodu pokazano wcześniej, a następnie skopiuj ciąg "exportedcert.pfx.b64". Wklej go jako wartość *pfxBlobString* atrybutu.
* *hasło*: hasło używane do zabezpieczania pliku .pfx.
* *certificateThumbprint*: Odcisk palca certyfikatu. Jeśli pobierania tej wartości ze środowiska PowerShell (na przykład *$certificate. Odcisk palca* z wcześniejszych fragment kodu), można użyć wartości, ponieważ jest. Jeśli wartość jest skopiować z okna dialogowego certyfikatu systemu Windows, pamiętaj, aby usuwają usunięciu spacji. *CertificateThumbprint* powinien wyglądać jak AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: identyfikator ciągu przyjazną wybranej przez użytkownika używane do identyfikowania certyfikatu. Nazwa jest używana jako część Unikatowy identyfikator menedżera zasobów *Microsoft.Web/certificates* jednostki, która reprezentuje certyfikatu SSL. Nazwa *musi* kończyć się sufiksem następujących: \_yourASENameHere_InternalLoadBalancingASE. Azure portal używa ten sufiks jako wskaźnik certyfikat służy do zabezpieczania ASE włączone ILB.

Przykład skróconej *azuredeploy.parameters.json* jest następujący:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Po *azuredeploy.parameters.json* plik zostanie wypełnione, skonfigurować domyślnego certyfikatu SSL za pomocą programu PowerShell fragmentu kodu. Zmienianie ścieżki pliku do dopasowania, gdy pliki szablonu usługi Resource Manager znajdują się na tym komputerze. Pamiętaj, aby podać własne wartości dla nazwy wdrożenia usługi Resource Manager i nazwę grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trwa około 40 minut na ASE frontonu do zastosowania zmiany. Na przykład dla ASE o rozmiarze domyślnym wykorzystuje dwa końce frontonu, szablon zajmie około jednej godziny i 20 minut do wykonania. Szablon jest uruchomiona, nie można skalować ASE.  

Po zakończeniu szablonu aplikacji w ILB ASE są dostępne za pośrednictwem protokołu HTTPS. Połączenia są zabezpieczone za pomocą domyślnego certyfikatu SSL. Domyślny certyfikat SSL służy do aplikacji w ILB ASE dotyczą przy użyciu kombinacji nazwy aplikacji oraz nazwa hosta domyślnego. Na przykład https://mycustomapp.internal-contoso.com korzysta z domyślnego certyfikatu SSL dla **.internal contoso.com*.

Jednak podobnie jak aplikacji uruchamianych w publicznej usługi wielodostępnym, deweloperzy mogą konfigurować nazwy hosta niestandardowego dla poszczególnych aplikacji. Może również skonfigurować unikatowe powiązania certyfikatu SNI SSL dla poszczególnych aplikacji.

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##
Środowisko App Service Environment występuje w dwóch wersjach: ASEv1 i ASEv2. Podane wcześniej informacje dotyczyły wersji 2 — ASEv2. W tej sekcji przedstawiono różnice między środowiskami ASEv1 i ASEv2.

ASEv1 zarządzania wszystkie zasoby ręcznie. Obejmuje to frontony, procesy robocze oraz adresy IP używane do obsługi połączeń SSL opartych na protokole IP. Zanim można skalować w poziomie planu usługi aplikacji, musisz skalowanie puli procesów roboczych, który chcesz udostępnić go.

W przypadku środowiska ASEv1 używany jest inny model cenowy niż w przypadku środowiska ASEv2. W przypadku środowiska ASEv1 płacisz za każdy przydzielony procesor vCPU. Zawierającą Vcpu, używane w przypadku interfejsy lub pracowników, którzy nie są hosting dowolnych zadań. W przypadku środowiska ASEv1 domyślny rozmiar w skali maksymalnej środowiska ASE to 55 hostów łącznie. Obejmuje to frontony i procesy robocze. Jedną z zalet środowiska ASEv1 jest to, że można je wdrożyć w klasycznej sieci wirtualnej oraz w sieci wirtualnej usługi Resource Manager. Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [App Service Environment v1 introduction][ASEv1Intro] (Wprowadzenie do środowiska App Service Environment w wersji 1).

Aby utworzyć ASEv1 przy użyciu szablonu usługi Resource Manager, zobacz [utworzyć przy użyciu szablonu usługi Resource Manager v1 ILB ASE][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: http://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
