---
title: Tworzenie ILB ASE v1
description: Utwórz środowisko App Service przy użyciu wewnętrznego modułu równoważenia obciążenia (ILB ASE). Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9cbd8b178bfd2edcf99e3bba9b0d967aebcb5cc2
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688771"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Jak tworzyć środowisko ASE wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager

> [!NOTE] 
> Ten artykuł dotyczy App Service Environment v1. Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd
Środowiska App Service można utworzyć przy użyciu wewnętrznego adresu sieci wirtualnej zamiast publicznego adresu VIP.  Ten adres wewnętrzny jest dostarczany przez składnik platformy Azure o nazwie wewnętrzny moduł równoważenia obciążenia (ILB).  ILB ASE można utworzyć przy użyciu Azure Portal.  Można go również utworzyć za pomocą usługi Automation w postaci szablonów Azure Resource Manager.  W tym artykule przedstawiono kroki i składni wymagane do utworzenia środowiska ILB ASE z szablonami Azure Resource Manager.

W celu automatyzacji tworzenia środowiska ILB ASE należy wykonać trzy czynności:

1. Najpierw podstawowy ASE jest tworzony w sieci wirtualnej przy użyciu adresu wewnętrznego modułu równoważenia obciążenia, a nie do publicznego adresu VIP.  W ramach tego kroku nazwa domeny głównej jest przypisana do ILB ASE.
2. Po utworzeniu ILB ASE zostanie przekazany certyfikat SSL.  
3. Przekazany certyfikat SSL jest jawnie przypisany do ILB ASE jako "domyślny" certyfikat protokołu SSL.  Ten certyfikat SSL będzie używany przez ruch SSL do aplikacji w ILB ASE, gdy aplikacje są rozkierowane przy użyciu wspólnej domeny głównej przypisanej do środowiska ASE (np. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Tworzenie podstawowego ILB środowiska ASE
Przykładowy szablon Azure Resource Manager i skojarzony z nim plik parametrów są dostępne w witrynie GitHub [tutaj][quickstartilbasecreate].

Większość parametrów w pliku *azuredeploy. Parameters. JSON* jest często używanych do tworzenia zarówno ILB środowisk ASE, jak i środowisk ASE powiązanych z publicznym adresem VIP.  Poniższa lista wywołuje Parametry specjalne uwagi lub są unikatowe, podczas tworzenia ILB ASE:

* *internalLoadBalancingMode*: w większości przypadków to 3, co oznacza, że zarówno ruch http/https na portach 80/443, jak i porty kontroli/kanału danych nasłuchune przez usługę FTP w środowisku ASE, będą powiązane z adresem wewnętrznym sieci wirtualnej z przydzielonym ILB.  Jeśli ta właściwość jest ustawiona na wartość 2, tylko porty powiązane z usługą FTP (zarówno kanały kontroli i danych) będą powiązane z adresem ILB, podczas gdy ruch HTTP/HTTPS pozostanie w publicznym wirtualnym adresie IP.
* *dnsSuffix*: ten parametr definiuje domyślną domenę główną, która zostanie przypisana do środowiska ASE.  W publicznej odmianie Azure App Service domyślną domeną główną dla wszystkich aplikacji sieci Web jest *azurewebsites.NET*.  Jednak ponieważ środowisko ILB ASE jest wewnętrzne dla sieci wirtualnej klienta, nie ma sensu używania domyślnej domeny głównej usługi publicznej.  Zamiast tego, ILB ASE powinien mieć domyślną domenę główną, która ma Sense w przypadku użycia w wewnętrznej sieci wirtualnej firmy.  Na przykład hipotetyczna firma Contoso Corporation może używać domyślnej domeny głównej *Internal-contoso.com* dla aplikacji, które mają być rozpoznawalne i dostępne w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: ten parametr jest automatycznie domyślną wartością 0 w pliku *azuredeploy. JSON* , ponieważ środowisk ASE ILB ma tylko jeden adres ILB.  Brak jawnych adresów IP-SSL dla środowiska ILB ASE, a w związku z tym Pula adresów IP-SSL dla ILB ASE musi mieć wartość zero, w przeciwnym razie wystąpi błąd aprowizacji. 

Po wypełnieniu pliku *azuredeploy. Parameters. JSON* dla środowiska ILB ASE, ILB ASE można utworzyć przy użyciu następującego fragmentu kodu programu PowerShell.  Zmień ścieżki plików tak, aby pasowały do lokalizacji plików szablonów Azure Resource Manager znajdujących się na komputerze.  Należy również pamiętać, aby podać własne wartości nazwy wdrożenia Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po przesłaniu szablonu Azure Resource Manager środowisko ASE zostanie utworzone dopiero po kilku godzinach.  Po zakończeniu tworzenia środowisko ILB ASE zostanie wyświetlone w obszarze pracy portalu na liście środowisk App Service dla subskrypcji, która wyzwoliła wdrożenie.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Przekazywanie i Konfigurowanie certyfikatu protokołu SSL "default"
Po utworzeniu ILB ASE należy skojarzyć certyfikat SSL ze środowiskiem ASE jako "domyślny" certyfikat SSL używany do ustanawiania połączeń SSL z aplikacjami.  W przypadku korzystania z przykładu hipotetycznej firmy Contoso Corporation, jeśli domyślny sufiks DNS środowiska ASE to *Internal-contoso.com*, połączenie z *https://some-random-app.internal-contoso.com* wymaga certyfikatu SSL, który jest prawidłowy dla * *. Internal-contoso.com*. 

Istnieje wiele sposobów uzyskania ważnego certyfikatu SSL, w tym wewnętrzne urzędy certyfikacji, zakup certyfikatu od zewnętrznego wystawcy i użycie certyfikatu z podpisem własnym.  Niezależnie od źródła certyfikatu SSL należy poprawnie skonfigurować następujące atrybuty certyfikatu:

* *Podmiot*: ten atrybut musi być ustawiony na * *. your-root-Domain-here.com*
* *Alternatywna nazwa podmiotu*: ten atrybut musi zawierać zarówno * *. your-root-Domain-here.com*, jak i * *. SCM.your-root-Domain-here.com*.  Przyczyna drugiego wpisu polega na tym, że połączenia SSL z witryną SCM/kudu skojarzoną z każdą aplikacją zostaną wykonane przy użyciu adresu formularza *Your-App-Name.SCM.your-root-Domain-here.com*.

Mając na ręką prawidłowy certyfikat SSL, wymagane są dwa dodatkowe kroki przygotowawcze.  Certyfikat SSL musi zostać przekonwertowany/zapisany jako plik PFX.  Należy pamiętać, że plik PFX musi zawierać wszystkie certyfikaty pośrednie i główne, a także musi być zabezpieczony hasłem.

Następnie wynikowy plik PFX musi zostać przekonwertowany na ciąg Base64, ponieważ certyfikat SSL zostanie przekazany przy użyciu szablonu Azure Resource Manager.  Ponieważ Azure Resource Manager szablony są plikami tekstowymi, plik PFX musi być konwertowany do ciągu Base64, aby można go było uwzględnić jako parametr szablonu.

Poniższy fragment kodu programu PowerShell zawiera przykład generowania certyfikatu z podpisem własnym, Eksportowanie certyfikatu jako pliku PFX, Konwertowanie pliku PFX na ciąg zakodowany w formacie Base64, a następnie zapisanie ciągu zakodowanego w formacie base64 do osobnego pliku.  Kod programu PowerShell dla kodowania base64 został dostosowany z [blogu skrypty programu PowerShell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Po pomyślnym wygenerowaniu i przekonwertowaniu certyfikatu SSL na ciąg zakodowany w formacie base64 można użyć przykładowego szablonu Azure Resource Manager w usłudze GitHub w celu [skonfigurowania domyślnego certyfikatu protokołu SSL][configuringDefaultSSLCertificate] .

Poniżej wymieniono parametry w pliku *azuredeploy. Parameters. JSON* :

* *appServiceEnvironmentName*: Nazwa KONFIGUROWANEgo ILB środowiska ASE.
* *existingAseLocation*: ciąg tekstowy zawierający region platformy Azure, w którym WDROŻONO ILB ASE.  Na przykład: "Południowo-środkowe stany USA".
* *pfxBlobString*: based64 zakodowany ciąg reprezentacji pliku PFX.  Korzystając z podanego wcześniej fragmentu kodu, skopiuj ciąg zawarty w pliku "exportedcert. pfx. B64" i wklej go jako wartość atrybutu *pfxBlobString* .
* *hasło*: hasło użyte do zabezpieczenia pliku PFX.
* *certificateThumbprint*: odcisk palca certyfikatu.  Jeśli ta wartość zostanie pobrana z programu PowerShell (np. *$Certificate. Odcisk palca* ze starszego fragmentu kodu), można użyć wartości jako-is.  Jednakże w przypadku skopiowania wartości z okna dialogowego certyfikat systemu Windows należy pamiętać, aby rozdzielić spacje.  *CertificateThumbprint* powinien wyglądać następująco: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: przyjazny identyfikator ciągu używany do wybrania tożsamości certyfikatu.  Nazwa jest używana jako część unikatowego identyfikatora Azure Resource Manager jednostki *Microsoft. Web/Certificates* reprezentującej certyfikat SSL.  Nazwa **musi** kończyć się następującym sufiksem: \_yourASENameHere_InternalLoadBalancingASE.  Ten sufiks jest używany przez portal jako wskaźnik, którego certyfikat jest używany do zabezpieczania środowiska ASE z włączoną obsługą ILB.

Poniżej przedstawiono skrócony przykład pliku *azuredeploy. Parameters. JSON* :

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

Po wypełnieniu pliku *azuredeploy. Parameters. JSON* można skonfigurować domyślny certyfikat SSL przy użyciu następującego fragmentu kodu programu PowerShell.  Zmień ścieżki plików tak, aby pasowały do lokalizacji plików szablonów Azure Resource Manager znajdujących się na komputerze.  Należy również pamiętać, aby podać własne wartości nazwy wdrożenia Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po przesłaniu szablonu Azure Resource Manager zajmiemy się około 40 minut na fronton środowiska ASE w celu zastosowania zmiany.  Na przykład w przypadku domyślnego rozmiaru ASE przy użyciu dwóch frontonów szablon zajmie około godziny i dwadzieścia minut.  Gdy szablon jest uruchomiony, środowisko ASE nie będzie w stanie skalować.  

Po zakończeniu szablonu aplikacje w ILB ASE są dostępne za pośrednictwem protokołu HTTPS, a połączenia będą zabezpieczone przy użyciu domyślnego certyfikatu protokołu SSL.  Domyślny certyfikat SSL będzie używany, gdy aplikacje na ILB ASE są rozkierowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta.  Na przykład *https://mycustomapp.internal-contoso.com* będzie używać domyślnego certyfikatu SSL dla * *. Internal-contoso.com*.

Jednak podobnie jak aplikacje działające w publicznej usłudze wielu dzierżawców, deweloperzy mogą również konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji, a następnie konfigurować unikatowe SNI SSL powiązania certyfikatów dla poszczególnych aplikacji.  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę z App Service środowiskami, zobacz [wprowadzenie do App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

