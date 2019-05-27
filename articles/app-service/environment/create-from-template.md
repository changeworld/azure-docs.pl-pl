---
title: Tworzenie środowiska usługi App Service przy użyciu szablonu usługi Resource Manager — Azure
description: Wyjaśnia, jak utworzyć środowisko zewnętrznego lub wewnętrznego modułu równoważenia obciążenia Azure App Service przy użyciu szablonu usługi Resource Manager
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
ms.custom: seodec18
ms.openlocfilehash: bdf722ffa7a7c499ff256392886e0f229f27c7a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66137083"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Tworzenie środowiska ASE przy użyciu szablonu usługi Azure Resource Manager

## <a name="overview"></a>Przegląd

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Środowisk Azure App Service Environment (środowiska ASE) mogą być tworzone przy użyciu punktu końcowego dostępne za pośrednictwem Internetu lub punkt końcowy wewnętrznego adresu w usłudze Azure virtual network (VNet). Po utworzeniu za pomocą wewnętrznego punktu końcowego, punkt końcowy jest dostarczone przez platformę Azure składnika o nazwie wewnętrznego modułu równoważenia obciążenia (ILB). Środowisko ASE wewnętrznego adresu IP nazywa się środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Środowisko ASE z publicznym punktem końcowym nosi nazwę zewnętrznego środowiska ASE. 

Środowisko ASE można utworzyć za pomocą witryny Azure portal lub szablonu usługi Azure Resource Manager. W tym artykule opisano kolejne kroki oraz składni, należy utworzyć zewnętrzne środowisko ASE lub środowisku ASE z wewnętrznym modułem równoważenia obciążenia przy użyciu szablonów usługi Resource Manager. Aby dowiedzieć się, jak utworzyć środowisko ASE w witrynie Azure portal, zobacz [wprowadzić zewnętrznego środowiska ASE] [ MakeExternalASE] lub [utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia][MakeILBASE].

Po utworzeniu środowiska ASE w witrynie Azure portal możesz utworzyć sieć wirtualną, w tym samym czasie, lub wybrać istniejące wcześniej sieci wirtualnej do wdrożenia w. Po utworzeniu środowiska ASE z szablonu, należy rozpocząć od: 

* Sieci wirtualnej usługi Resource Manager.
* Podsieci w tej sieci wirtualnej. Firma Microsoft zaleca rozmiar podsieci środowiska ASE `/24` za pomocą 256 adresów na potrzeby przyszłego rozwoju i skalowanie potrzeb. Po utworzeniu środowiska ASE nie można zmienić rozmiar.
* Identyfikator zasobu z sieci wirtualnej. Te informacje można uzyskać w witrynie Azure portal w obszarze właściwości sieci wirtualnej.
* Subskrypcja, który chcesz wdrożyć w.
* Lokalizacja, w których mają zostać wdrożone w.

Aby zautomatyzować tworzenie Twoje środowisko ASE:

1. Tworzenie środowiska ASE na podstawie szablonu. Jeśli tworzysz zewnętrznego środowiska ASE, skończysz po wykonaniu tego kroku. Jeśli tworzysz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, istnieje kilka więcej zadań do wykonania.

2. Po utworzeniu środowiska ASE wewnętrznego modułu równoważenia obciążenia jest przekazywany certyfikat SSL, który pasuje do domeny środowiska ASE wewnętrznego modułu równoważenia obciążenia.

3. Przekazanego certyfikatu SSL jest przypisany do środowiska ASE wewnętrznego modułu równoważenia obciążenia jako jego certyfikatu SSL "default".  Ten certyfikat jest używany dla ruchu protokołu SSL do aplikacji w środowisku ASE wewnętrznego modułu równoważenia obciążenia, jeśli korzystają z typowych domeny katalogu głównego, który jest przypisany do środowiska ASE (na przykład https://someapp.mycustomrootdomain.com).


## <a name="create-the-ase"></a>Tworzenie środowiska ASE
Szablon usługi Resource Manager, który tworzy środowisko ASE i jego skojarzone parametrów pliku jest dostępna [w przykładzie] [ quickstartasev2create] w witrynie GitHub.

Jeśli chcesz utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia, użyj tych szablonu usługi Resource Manager [przykłady][quickstartilbasecreate]. One adresowanych do których przypadek użycia. Większość parametrów w *azuredeploy.parameters.json* pliku są wspólne dla tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia i zewnętrznego środowiska ASE. Poniższa lista wywołuje parametry ważne lub są unikatowe, po utworzeniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

* *internalLoadBalancingMode*: W większości przypadków będą powiązane zestaw, który to 3, co oznacza, że ruch HTTP/HTTPS na portach 80/443 i kontroli/danych portów kanału posłuchaliśmy przez usługę FTP w środowisku ASE, do wewnętrznego adresu przydzielone wewnętrznego modułu równoważenia obciążenia sieci wirtualnej. Jeśli ta właściwość jest równa 2, tylko związanych z usługą portów FTP (kanały zarówno kontroli, jak i dane) są powiązane z adresu wewnętrznego modułu równoważenia obciążenia. Ruch HTTP/HTTPS pozostaje na publicznych adresów VIP.
* *dnsSuffix*: Ten parametr określa domeny katalogu głównego domyślnego, która jest przypisana do środowiska ASE. W publicznej wersji usługi Azure App Service, domyślnej domeny katalogu głównego dla wszystkich aplikacji sieci web jest *azurewebsites.net*. Ponieważ środowisko ASE z wewnętrznym modułem równoważenia obciążenia jest wewnętrzny do sieci wirtualnej klienta, nie ma sensu do korzystania z domeny katalogu głównego domyślnego usług publicznych. Zamiast tego środowisko ASE z wewnętrznym modułem równoważenia obciążenia powinien mieć domyślnej domeny katalogu głównego, pasującą do użycia w wewnętrznej sieci wirtualnej firmy. Na przykład firma Contoso może użyć domyślnej domeny katalogu głównego, z *wewnętrznego contoso.com* dla aplikacji, które mają być rozpoznawany i jest dostępny tylko w obrębie sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: Ten parametr domyślnie przyjmuje wartość na wartość 0 w *azuredeploy.json* pliku, ponieważ środowiska ASE z wewnętrznym modułem równoważenia obciążenia ma tylko jeden adres wewnętrznego modułu równoważenia obciążenia. Nie ma żadnych jawnych adresów IP SSL za środowisko ASE wewnętrznego modułu równoważenia obciążenia. Dzięki temu puli adresów IP protokołu SSL dla środowiska ASE z wewnętrznym modułem równoważenia obciążenia musi być równa zero. W przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Po *azuredeploy.parameters.json* pliku jest wypełnione, należy utworzyć środowisko ASE przy użyciu fragmentu kodu programu PowerShell. Zmiana ścieżek plików, aby dopasować lokalizacje pliku szablonu usługi Resource Manager na komputerze. Pamiętaj, aby podać własne wartości dla nazwy wdrażania usługi Resource Manager i nazwę grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trwa około godziny na środowisko ASE ma zostać utworzony. Następnie ASE wyświetlana w portalu na liście dla subskrypcji, która wyzwoliła wdrażania środowiska ASE.

## <a name="upload-and-configure-the-default-ssl-certificate"></a>Przekazać i skonfigurować certyfikat SSL "default"
Certyfikat SSL musi być skojarzony z ASE jako certyfikat SSL "domyślna", który jest używany do ustanawiania połączeń SSL do aplikacji. Jeśli środowisko ASE, domyślny sufiks DNS jest *wewnętrznego contoso.com*, połączenie https://some-random-app.internal-contoso.com wymaga certyfikatu SSL, który jest prawidłowy dla **.internal contoso.com*. 

Uzyskaj certyfikat protokołu SSL za pomocą wewnętrznych urzędów certyfikacji, zakup certyfikat od wystawcy zewnętrznego lub przy użyciu certyfikatu z podpisem własnym. Niezależnie od źródła certyfikatu SSL musi być prawidłowo skonfigurowane następujące atrybuty certyfikatu:

* **Podmiot**: Ten atrybut musi być równa **lokalizacji głównego domain-here.com*.
* **Alternatywna nazwa podmiotu**: Ten atrybut musi zawierać zarówno **lokalizacji głównego domain-here.com* i **.SCM.domena-głównego-domeny — here.com*. Połączenia SSL z witryną SCM/Kudu skojarzonych z poszczególnymi aplikacjami, użyj adresu w postaci *your-app-name.scm.your-root-domain-here.com*.

Za pomocą ważnego certyfikatu SSL w kasie potrzebne są dwa dodatkowe kroki przygotowawcze. Przekonwertuj lub zapisz certyfikat SSL jako plik pfx. Należy pamiętać, że plik PFX musi obejmować wszystkie pośrednie i certyfikaty główne. Zabezpiecz go przy użyciu hasła.

Plik PFX musi ma zostać przekonwertowany na ciąg base64, ponieważ certyfikat SSL jest przekazywany przy użyciu szablonu usługi Resource Manager. Ponieważ szablony usługi Resource Manager są plikami tekstowymi, należy przekonwertować plik PFX do ciąg w formacie base64. W ten sposób może zostać dołączony jako parametr szablonu.

Użyj poniższego fragmentu kodu programu PowerShell do:

* Wygeneruj certyfikat z podpisem własnym.
* Wyeksportuj certyfikat jako plik pfx.
* Przekonwertuj plik pfx na ciąg kodowany w formacie base64.
* Zapisz ciąg kodowany w formacie base64 w oddzielnym pliku. 

Tego kodu programu PowerShell na potrzeby kodowania base64 został napisany [skryptów programu PowerShell, blog][examplebase64encoding]:

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

Gdy certyfikat SSL jest pomyślnie i konwertowana na ciąg kodowany w formacie base64, użyj przykładowy szablon usługi Resource Manager [Konfigurowanie domyślnego certyfikatu protokołu SSL] [ quickstartconfiguressl] w witrynie GitHub. 

Parametry w *azuredeploy.parameters.json* pliku na liście:

* *appServiceEnvironmentName*: Nazwa środowiska ASE wewnętrznego modułu równoważenia obciążenia jest skonfigurowany.
* *existingAseLocation*: Ciąg tekstowy zawierający regionu platformy Azure, w której wdrożono środowisko ASE wewnętrznego modułu równoważenia obciążenia.  Na przykład: "Południowo-środkowe stany USA".
* *pfxBlobString*: Ciąg kodowany w formacie based64 reprezentacja pliku .pfx. Użyj fragmentu kodu pokazano wcześniej i skopiuj ciąg znajdujący się w "exportedcert.pfx.b64". Wklej je jako wartość *pfxBlobString* atrybutu.
* *hasło*: Hasło używane do zabezpieczania plików PFX.
* *certificateThumbprint*: Odcisk palca certyfikatu. Jeśli pobierania tej wartości ze środowiska PowerShell (na przykład *$certificate. Odcisk palca* z wcześniejszych fragment kodu), możesz użyć wartości, ponieważ jest. Jeśli kopiujesz wartości w oknie dialogowym certyfikat Windows, pamiętaj, aby usunąć nadmiarowe miejsca do magazynowania. *CertificateThumbprint* powinien wyglądać podobnie AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: Identyfikator ciągu przyjazna swoje własne, wybierając używane do identyfikowania certyfikatu. Nazwa jest używana jako część Unikatowy identyfikator usługi Resource Manager *Microsoft.Web/certificates* jednostki, która reprezentuje certyfikatu SSL. Nazwa *musi* kończy się następującym sufiksem: \_yourASENameHere_InternalLoadBalancingASE. Korzysta z witryny Azure portal ten sufiks jako wskazówka, że certyfikat jest używany do zabezpieczania środowiska ASE z obsługą wewnętrznego modułu równoważenia obciążenia.

Przykład skróconej *azuredeploy.parameters.json* jest następująca:

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

Po *azuredeploy.parameters.json* pliku zostanie wypełnione, Konfigurowanie domyślnego certyfikatu protokołu SSL przy użyciu fragmentu kodu programu PowerShell. Zmiana ścieżek plików, aby dopasować, gdzie są pliki szablonu usługi Resource Manager znajdują się na swojej maszynie. Pamiętaj, aby podać własne wartości dla nazwy wdrażania usługi Resource Manager i nazwę grupy zasobów:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Trwa około 40 minut na środowisko ASE frontonu do zastosowania zmiany. Na przykład ASE o rozmiarze domyślnym wykorzystuje dwa Frontony, szablon trwa około jednej godziny i 20 minut. Gdy szablon jest uruchomiona, nie można skalować środowiska ASE.  

Po zakończeniu działania tego szablonu, dostęp do aplikacji w środowisku ASE wewnętrznego modułu równoważenia obciążenia są dostępne za pośrednictwem protokołu HTTPS. Połączenia są chronione przy użyciu domyślnego certyfikatu SSL. Domyślny certyfikat SSL jest używany, gdy aplikacje w środowisku ASE wewnętrznego modułu równoważenia obciążenia są rozwiązywane przez przy użyciu kombinacji nazwy aplikacji, a także domyślna nazwa hosta. Na przykład https://mycustomapp.internal-contoso.com korzysta z domyślnego certyfikatu SSL dla **.internal contoso.com*.

Jednak podobnie jak aplikacje uruchamiane w usłudze wielodostępnych publicznych, deweloperzy mogą konfigurować nazwy niestandardowego hosta dla poszczególnych aplikacji. Mogą również skonfigurować unikatowych powiązań certyfikatów SNI SSL dla poszczególnych aplikacji.

## <a name="app-service-environment-v1"></a>Środowisko usługi App Service — wersja 1 ##
Środowisko App Service Environment występuje w dwóch wersjach: ASEv1 i ASEv2. Podane wcześniej informacje dotyczyły wersji 2 — ASEv2. W tej sekcji przedstawiono różnice między środowiskami ASEv1 i ASEv2.

W przypadku środowiska ASEv1 możesz zarządzać wszystkimi zasobów ręcznie. Obejmuje to frontony, procesy robocze oraz adresy IP używane do obsługi połączeń SSL opartych na protokole IP. Zanim będzie można skalować w poziomie plan usługi App Service, musisz przeprowadzać skalowanie w poziomie pulę procesów roboczych, którą chcesz udostępnić go.

W przypadku środowiska ASEv1 używany jest inny model cenowy niż w przypadku środowiska ASEv2. W przypadku środowiska ASEv1 płacisz za każdy przydzielony procesor vCPU. Obejmuje to procesorów wirtualnych, które są używane na potrzeby Frontony i procesy robocze, które nie hostują żadnych obciążeń. W przypadku środowiska ASEv1 domyślny rozmiar w skali maksymalnej środowiska ASE to 55 hostów łącznie. Obejmuje to frontony i procesy robocze. Jedną z zalet środowiska ASEv1 jest to, że można je wdrożyć w klasycznej sieci wirtualnej oraz w sieci wirtualnej usługi Resource Manager. Aby dowiedzieć się więcej na temat środowiska ASEv1, zobacz [App Service Environment v1 introduction][ASEv1Intro] (Wprowadzenie do środowiska App Service Environment w wersji 1).

Aby utworzyć środowiska ASEv1 przy użyciu szablonu usługi Resource Manager, zobacz [tworzenie v1 środowiska ASE z wewnętrznym modułem równoważenia obciążenia przy użyciu szablonu usługi Resource Manager][ILBASEv1Template].


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
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service/web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
