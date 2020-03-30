---
title: Tworzenie ASE za pomocą ARM
description: Dowiedz się, jak utworzyć zewnętrzne środowisko usługi app service lub równoważenia obciążenia przy użyciu szablonu Usługi Azure Resource Manager.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 1212e77db5e0ec83f8dd966a14872a682b3e0202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295550"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Tworzenie ase przy użyciu szablonu usługi Azure Resource Manager

## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Środowiska usługi Azure App Service (ASE) można tworzyć za pomocą punktu końcowego dostępnego dla Internetu lub punktu końcowego na adres wewnętrzny w sieci wirtualnej platformy Azure (sieć wirtualna). Po utworzeniu z wewnętrznym punktem końcowym ten punkt końcowy jest dostarczany przez składnik platformy Azure o nazwie wewnętrzny moduł równoważenia obciążenia (ILB). ASE na wewnętrznym adresie IP jest nazywany ASE RÓWNOWAżenia obciążenia. ASE z publicznym punktem końcowym jest nazywany zewnętrznego ASE. 

Ase można utworzyć przy użyciu witryny Azure portal lub szablonu usługi Azure Resource Manager. W tym artykule ominiao o krokach i składni potrzebnej do utworzenia zewnętrznego ase ase lub ilb iwymiaru z szablonami Menedżera zasobów. Aby dowiedzieć się, jak utworzyć ase w witrynie Azure portal, zobacz [Tworzenie zewnętrznego ase][MakeExternalASE] lub [Tworzenie ASE ILB.][MakeILBASE]

Podczas tworzenia ase w witrynie Azure portal, można utworzyć sieć wirtualną w tym samym czasie lub wybrać istniejącą sieć wirtualną do wdrożenia w. Podczas tworzenia ase z szablonu, należy zacząć od: 

* Sieci wirtualnej Menedżera zasobów.
* Podsieć w tej sieci wirtualnej. Firma Microsoft zaleca rozmiar podsieci ASE `/24` z 256 adresów, aby zaspokoić przyszłe potrzeby wzrostu i skalowania. Po utworzeniu ase nie można zmienić rozmiaru.
* Identyfikator zasobu z sieci wirtualnej. Te informacje można uzyskać z witryny Azure portal w ramach właściwości sieci wirtualnej.
* Subskrypcja, w której chcesz wdrożyć.
* Lokalizacja, w której chcesz wdrożyć.

Aby zautomatyzować tworzenie ASE:

1. Utwórz ase na podstawie szablonu. Jeśli utworzysz zewnętrznego ASE, po tym kroku zostanie zakończona. Jeśli utworzysz ASE ILB, istnieje kilka innych rzeczy do zrobienia.

2. Po utworzeniu ase ILB, certyfikat SSL, który pasuje do domeny ASE ILB jest przekazyany.

3. Przekazany certyfikat SSL jest przypisany do ASE równoważenia obciążenia sieciowego jako "domyślny" certyfikat SSL.  Ten certyfikat jest używany do ruchu SSL do aplikacji na ASE ILB, gdy używają wspólnej domeny `https://someapp.mycustomrootdomain.com`głównej, która jest przypisana do ASE (na przykład).


## <a name="create-the-ase"></a>Tworzenie ase
Szablon Menedżera zasobów, który tworzy ASE i skojarzony z nim plik parametrów jest dostępny [w przykładzie][quickstartasev2create] w usłudze GitHub.

Jeśli chcesz stworzyć ASE równoważenia obciążenia, użyj tych [przykładów szablonów][quickstartilbasecreate]Menedżera zasobów . Zaspokajają one ten przypadek użycia. Większość parametrów w pliku *azuredeploy.parameters.json* są wspólne dla tworzenia ases i zewnętrznych ases i zewnętrznych. Poniższa lista wywołuje parametry specjalnej notatki lub które są unikatowe podczas tworzenia ASE ILB:

* *internalLoadBalancingMode*: W większości przypadków należy ustawić to na 3, co oznacza, że zarówno ruch HTTP/HTTPS na portach 80/443, jak i porty kanału sterowania/transmisji danych słuchane przez usługę FTP na ase, będą powiązane z adresem wewnętrznym sieci wirtualnej przydzielonej przez przynajście ILB. Jeśli ta właściwość jest ustawiona na 2, tylko porty związane z usługą FTP (zarówno kanały sterowania, jak i kanałów danych) są powiązane z adresem równoważenia obciążenia sieciowego. Ruch HTTP/HTTPS pozostaje w publicznym adresie VIP.
* *dnsSuffix*: Ten parametr definiuje domyślną domenę główną przypisaną do ASE. W publicznej odmianie usługi Azure App Service domyślną domeną główną dla wszystkich aplikacji internetowych jest *azurewebsites.net*. Ponieważ środowisko ASE równoważenia obciążenia sieciowego jest wewnętrzne w sieci wirtualnej klienta, nie ma sensu używać domyślnej domeny głównej usługi publicznej. Zamiast tego środowisko ASE równoważenia obciążenia sieciowego powinno mieć domyślną domenę główną, która ma sens do użycia w wewnętrznej sieci wirtualnej firmy. Na przykład contoso Corporation może używać domyślnej domeny głównej *internal-contoso.com* dla aplikacji, które mają być rozwiązywalne i dostępne tylko w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: Ten parametr automatycznie domyślnie wartość 0 w pliku *azuredeploy.json,* ponieważ ases ILB mają tylko jeden adres równoważenia obciążenia. Nie ma żadnych jawnych adresów IP-SSL dla ASE równoważenia obciążenia. W związku z tym puli adresów IP-SSL dla ase ILB musi być ustawiona na zero. W przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Po *wypełnieniu pliku azuredeploy.parameters.json* utwórz program ASE przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików, aby były zgodne z lokalizacjami plików szablonów Menedżera zasobów na komputerze. Pamiętaj, aby podać własne wartości dla nazwy wdrożenia Menedżera zasobów i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Utworzenie ase zajmuje około godziny. Następnie ASE pojawia się w portalu na liście ASES dla subskrypcji, która wyzwoliła wdrożenia.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Przekazywanie i konfigurowanie "domyślnego" certyfikatu SSL
Certyfikat SSL musi być skojarzony z ASE jako "domyślny" certyfikat SSL używany do ustanawiania połączeń SSL z aplikacjami. Jeśli domyślny sufiks DNS ase jest *internal-contoso.com,* `https://some-random-app.internal-contoso.com` połączenie wymaga certyfikatu SSL, który jest ważny dla **.internal-contoso.com*. 

Uzyskaj ważny certyfikat SSL przy użyciu wewnętrznych urzędów certyfikacji, zakupu certyfikatu od zewnętrznego wystawcy lub przy użyciu certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu SSL następujące atrybuty certyfikatu muszą być poprawnie skonfigurowane:

* **Temat:** Ten atrybut musi być ustawiony na **.your-root-domain-here.com*.
* **Alternatywna nazwa podmiotu:** Ten atrybut musi zawierać zarówno **.your-root-domain-here.com* i **.scm.your-root-domain-here.com*. Połączenia SSL z witryną SCM/Kudu skojarzoną z każdą aplikacją używają adresu formularza *your-app-name.scm.your-root-domain-here.com*.

Z ważnym certyfikatem SSL w ręku, potrzebne są dwa dodatkowe kroki przygotowawcze. Przekonwertuj lub zapisz certyfikat SSL jako plik pfx. Należy pamiętać, że plik .pfx musi zawierać wszystkie certyfikaty pośrednie i główne. Zabezpiecz go przy użyciu hasła.

Plik .pfx musi zostać przekonwertowany na ciąg base64, ponieważ certyfikat SSL jest przekazywał przy użyciu szablonu Menedżera zasobów. Ponieważ szablony Menedżera zasobów są plikami tekstowymi, plik .pfx musi zostać przekonwertowany na ciąg base64. W ten sposób może być dołączony jako parametr szablonu.

Użyj następującego fragmentu kodu programu PowerShell, aby:

* Generowanie certyfikatu z podpisem własnym.
* Wyeksportuj certyfikat jako plik pfx.
* Konwertuj plik pfx na ciąg zakodowany w base64.
* Zapisz ciąg zakodowany base64 w oddzielnym pliku. 

Ten kod programu PowerShell do kodowania base64 został zaadaptowany z [bloga skryptów programu PowerShell:][examplebase64encoding]

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

Po pomyślnym wygenerowaniu certyfikatu SSL i przekonwertowaniu go na ciąg zakodowany w programie base64 użyj przykładowego szablonu Menedżera zasobów [Skonfiguruj domyślny certyfikat SSL][quickstartconfiguressl] w usłudze GitHub. 

Parametry w pliku *azuredeploy.parameters.json* są wymienione tutaj:

* *appServiceEnvironmentName*: Nazwa skonfigurowanego środowiska ASE równoważenia obciążenia równoważącego.
* *existingAseLocation:* Ciąg tekstowy zawierający region platformy Azure, w którym wdrożono ase równoważenia obciążenia.  Na przykład: "Południowo-środkowe stany USA".
* *pfxBlobString*: Na podstawie64-zakodowana reprezentacja ciągu pliku .pfx. Użyj fragmentu kodu pokazanego wcześniej i skopiuj ciąg zawarty w pliku "exportedcert.pfx.b64". Wklej go jako wartość atrybutu *pfxBlobString.*
* *hasło*: Hasło używane do zabezpieczenia pliku .pfx.
* *certificateThumbprint*: odcisk palca certyfikatu. Jeśli pobrać tę wartość z programu PowerShell (na przykład *$certificate. Odcisk palca* z wcześniejszego fragmentu kodu), można użyć wartości, jak jest. Jeśli wartość zostanie skopiowana z okna dialogowego certyfikatu systemu Windows, pamiętaj, aby usunąć obce spacje. *CertyfikatThumbprint* powinien wyglądać jak AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: Przyjazny identyfikator ciągu według własnego wyboru używany do identyfikacji certyfikatu. Nazwa jest używana jako część unikatowego identyfikatora Menedżera zasobów dla jednostki *Microsoft.Web/certificates* reprezentującej certyfikat SSL. Nazwa *musi kończyć się* następującym \_sufiksem: yourASENameHere_InternalLoadBalancingASE. Portal Azure używa tego sufiksu jako wskaźnika, że certyfikat jest używany do zabezpieczania ase z włączoną funkcją równoważenia obciążenia sieciowego.

W tym miejscu pokazano skrócony przykład *pliku azuredeploy.parameters.json:*

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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

Po *wypełnieniu pliku azuredeploy.parameters.json* skonfiguruj domyślny certyfikat SSL przy użyciu fragmentu kodu programu PowerShell. Zmień ścieżki plików, aby dopasować pliki szablonów Menedżera zasobów na komputerze. Pamiętaj, aby podać własne wartości dla nazwy wdrożenia Menedżera zasobów i nazwy grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Zastosowanie zmiany zajmuje około 40 minut na przednim końcu ASE. Na przykład dla domyślnego rozmiaru ASE, który używa dwóch front ends, szablon trwa około godziny i 20 minut, aby zakończyć. Gdy szablon jest uruchomiony, ASE nie można skalować.  

Po zakończeniu szablonu aplikacje na ASE równoważenia obciążenia są dostępne za pośrednictwem protokołu HTTPS. Połączenia są zabezpieczone przy użyciu domyślnego certyfikatu SSL. Domyślny certyfikat SSL jest używany, gdy aplikacje na ase ILB są adresowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta. Na przykład `https://mycustomapp.internal-contoso.com` używa domyślnego certyfikatu SSL dla **.internal-contoso.com*.

Jednak podobnie jak aplikacje, które działają w publicznej usłudze wielodostępnych, deweloperzy mogą konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji. Mogą również konfigurować unikatowe powiązania certyfikatów SSL SNI dla poszczególnych aplikacji.

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##
Środowisko App Service Environment występuje w dwóch wersjach: ASEv1 i ASEv2. Podane wcześniej informacje dotyczyły wersji 2 — ASEv2. W tej sekcji przedstawiono różnice między środowiskami ASEv1 i ASEv2.

W ASEv1 zarządzasz wszystkimi zasobami ręcznie. Obejmuje to frontony, procesy robocze oraz adresy IP używane do obsługi połączeń SSL opartych na protokole IP. Aby można było skalować w poziomie plan usługi app service, należy skalować w poziomie puli procesów roboczych, które chcesz go hostować.

W przypadku środowiska ASEv1 używany jest inny model cenowy niż w przypadku środowiska ASEv2. W przypadku środowiska ASEv1 płacisz za każdy przydzielony procesor vCPU. Obejmuje to procesory wirtualne, które są używane dla front ends lub pracowników, którzy nie obsługują żadnych obciążeń. W przypadku środowiska ASEv1 domyślny rozmiar w skali maksymalnej środowiska ASE to 55 hostów łącznie. Obejmuje to frontony i procesy robocze. Jedną z zalet środowiska ASEv1 jest to, że można je wdrożyć w klasycznej sieci wirtualnej oraz w sieci wirtualnej usługi Resource Manager. Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [App Service Environment v1 introduction][ASEv1Intro] (Wprowadzenie do środowiska App Service Environment w wersji 1).

Aby utworzyć ASEv1 przy użyciu szablonu Menedżera zasobów, zobacz [Tworzenie ase ase ilb z szablonem Menedżera zasobów][ILBASEv1Template].


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
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
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
