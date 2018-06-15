---
title: Jak utworzyć ASE ILB przy użyciu szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć ASE usługi równoważenia obciążenia wewnętrznego przy użyciu szablonów usługi Azure Resource Manager.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.openlocfilehash: ea9407208f1bf555cf1a6d166825896dec89ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836854"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Jak tworzyć środowisko ASE wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager

> [!NOTE] 
> Ten artykuł dotyczy v1 środowiska usługi aplikacji. Istnieje nowsza wersja środowiska usługi aplikacji jest łatwiejsza w użyciu, który jest uruchamiany na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej o nowy początek wersji z [wprowadzenie do środowiska usługi aplikacji](intro.md).
>

## <a name="overview"></a>Omówienie
Środowiska usługi aplikacji mogą być tworzone za pomocą adresu wewnętrznej sieci wirtualnej zamiast publicznego adresu VIP.  Ten adres wewnętrzny są udostępniane przez składnik Azure o nazwie wewnętrznego modułu równoważenia obciążenia (ILB).  ASE ILB mogą być tworzone przy użyciu portalu Azure.  Można również tworzyć, przy użyciu automatyzacji i szablony usługi Azure Resource Manager.  W tym artykule przedstawiono kroki i składni potrzebne do utworzenia ASE ILB przy użyciu szablonów usługi Azure Resource Manager.

Istnieją trzy kroki związane z automatyzacji tworzenia ASE ILB:

1. Najpierw ASE podstawowy jest tworzony w sieć wirtualną przy użyciu adres usługi równoważenia obciążenia wewnętrznego, zamiast publicznego adresu VIP.  W ramach tego kroku nazwę domeny katalogu głównego jest przypisany do ILB ASE.
2. Po utworzeniu ILB ASE przekazaniu certyfikatu SSL.  
3. Przekazany certyfikat SSL jest jawnie przypisany do ILB ASE jako swojego certyfikatu SSL "domyślny".  Ten certyfikat protokołu SSL będzie można używać dla ruchu protokołu SSL do aplikacji w ILB ASE, jeśli aplikacje zostały rozwiązane, za pomocą wspólnego domeny głównej przypisane do ASE (np. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Tworzenie podstawowej ILB ASE
Przykład szablonu usługi Azure Resource Manager i jego pliku skojarzone z nimi parametry są dostępne w serwisie GitHub [tutaj][quickstartilbasecreate].

Większość parametrów w *azuredeploy.parameters.json* plików są często używane do utworzenia zarówno ILB ASEs, jak i ASEs powiązany do publicznego adresu IP.  Poniżej wywołania parametry specjalne Uwaga out lub które są unikatowe, tworząc ASE ILB:

* *interalLoadBalancingMode*: W większości przypadków będzie powiązany zestaw to 3, co oznacza ruchu HTTP/HTTPS na porty 80/443 i kontroli/danych portów kanału nasłuch przez usługę FTP na ASE, ILB przydzielony adres wewnętrzny sieci wirtualnej.  Jeśli ta właściwość zamiast tego jest równa 2, następnie usługę FTP związanych z portów (kanały zarówno kontroli, jak i dane) zostanie powiązany adres ILB, podczas gdy pozostanie ruchu HTTP/HTTPS na publiczny adres VIP.
* *dnsSuffix*: ten parametr określa zostanie przypisana do ASE domyślnej domeny katalogu głównego.  W publicznej wersji usługi Azure App Service, domyślnej domeny głównej dla wszystkich aplikacji w sieci web jest *azurewebsites.net*.  Jednak ponieważ ASE ILB jest wewnętrzny do klienta sieci wirtualnej, go nie ma sensu do korzystania z usługi publicznego domyślnego głównym domeny.  Zamiast tego ASE ILB powinien mieć domyślnej domeny katalogu głównego, pasującą do użytku w wewnętrznej sieci wirtualnej firmy.  Na przykład użyć hipotetyczny Contoso Corporation domyślną domeną katalogu głównego *wewnętrzny contoso.com* dla aplikacji, które mają być rozpoznawalna i jest dostępny w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: ten parametr jest automatycznie ustawiana domyślnie na wartość 0 w *azuredeploy.json* pliku, ponieważ ILB ASEs mieć tylko jeden adres ILB.  Nie ma żadnych jawnych adresów IP protokołu SSL dla ASE ILB i dlatego puli adresów IP protokołu SSL dla ASE ILB musi mieć wartość zero, w przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Raz *azuredeploy.parameters.json* pliku wprowadzony dla ASE ILB, następnie można utworzyć ILB ASE przy użyciu poniższy fragment kodu programu Powershell.  Zmienianie pliku ścieżki do dopasowania, gdy pliki szablonów usługi Azure Resource Manager znajdują się na tym komputerze.  Również Pamiętaj, aby podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager, a nazwa grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po Menedżera zasobów Azure szablonu jest złożone potrwa kilka godzin ASE ILB, który ma zostać utworzony.  Po zakończeniu tworzenia ILB ASE będą widoczne w portalu środowiska użytkownika na liście subskrypcji, która wyzwoliła wdrożenia środowiska usługi App Service.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Przekazywanie i konfigurowanie certyfikatu SSL "Domyślne"
Po utworzeniu ILB ASE, certyfikat SSL powinna być skojarzona z ASE, jak "domyślne" certyfikat SSL używa do nawiązywania połączeń SSL do aplikacji.  Sufiks kontynuowaniem hipotetyczny przykład Contoso Corporation, jeśli ASE domyślne DNS jest *wewnętrzny contoso.com*, następnie połączenie *https://some-random-app.internal-contoso.com* wymaga certyfikatu SSL, który jest prawidłowy dla **.internal contoso.com*. 

Istnieje wiele sposobów, aby uzyskać prawidłowy certyfikat SSL tym wewnętrzne urzędy certyfikacji, zakup certyfikatu od zewnętrznego wystawcy i przy użyciu certyfikatu z podpisem własnym.  Niezależnie od źródła certyfikatu SSL, należy poprawnie skonfigurowane następujące atrybuty certyfikatu:

* *Temat*: ten atrybut musi być równa **.your głównego domeny here.com*
* *Alternatywna nazwa podmiotu*: ten atrybut musi zawierać zarówno **.your głównego domeny here.com*, i **.scm.your-głównego-domeny-here.com*.  Przyczyna drugi wpis to, że połączeń SSL do witryny SCM/Kudu skojarzone z każdej aplikacji zostaną wprowadzone przy użyciu adresu w postaci *your-app-name.scm.your-root-domain-here.com*.

Przy użyciu prawidłowego certyfikatu SSL w ręcznie potrzebne są dwa dodatkowe czynności przygotowawczych.  Certyfikat SSL musi być konwertowane/zapisany jako plik pfx.  Należy pamiętać, musi zawierać wszystkie pośrednie i certyfikaty głównego pliku PFX, a również musi być zabezpieczone za pomocą hasła.

Następnie plik PFX wynikowy musi można przekonwertować na ciąg w formacie base64, ponieważ certyfikat SSL zostaną przekazane za pomocą szablonu usługi Azure Resource Manager.  Ponieważ szablonów usługi Azure Resource Manager są plikami tekstowymi, plik PFX potrzebuje do przekonwertowania na ciąg w formacie base64, więc można dołączyć jako parametr szablonu.

Poniższy fragment kodu programu Powershell pokazano przykład generowania certyfikatu z podpisem własnym, eksportowanie certyfikatu jako plik PFX konwertowania pliku .pfx na base64 ciąg kodowany w formacie, a następnie zapisując base64 ciąg kodowany w formacie do pliku.  Kod programu Powershell dla kodowania base64 pochodzi z [Blog skryptów Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Gdy certyfikat SSL został pomyślnie wygenerowany i przekonwertować algorytmem base64 ciągu, przykładowy szablon usługi Azure Resource Manager w witrynie GitHub dla [konfigurowania domyślnego certyfikatu SSL] [ configuringDefaultSSLCertificate] mogą być używane.

Parametry w *azuredeploy.parameters.json* plików wymienionych poniżej:

* *appServiceEnvironmentName*: Nazwa ASE ILB skonfigurowany.
* *existingAseLocation*: ciąg tekstowy zawierający wdrożonym ILB ASE region platformy Azure.  Na przykład: "Południowo-środkowe Stany".
* *pfxBlobString*: based64 zakodowane reprezentację ciągu pliku .pfx.  Za pomocą fragmentu kodu pokazano wcześniej, czy kopiowania ciąg "exportedcert.pfx.b64" i wklej go jako wartość *pfxBlobString* atrybutu.
* *hasło*: hasło używane do zabezpieczania pliku .pfx.
* *certificateThumbprint*: Odcisk palca certyfikatu.  Jeśli pobierania tej wartości ze środowiska Powershell (np. *$certificate. Odcisk palca* z wcześniejszych fragment kodu), można użyć wartości jako — jest.  Jednak skopiowanie wartości w oknie dialogowym certyfikat systemu Windows, pamiętaj, aby usunąć nadmiarowe spacji.  *CertificateThumbprint* powinien wyglądać mniej więcej tak: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: identyfikator ciągu przyjazną wybranej przez użytkownika używane do identyfikowania certyfikatu.  Nazwa jest używana jako część Unikatowy identyfikator usługi Azure Resource Manager *Microsoft.Web/certificates* jednostki reprezentujący certyfikatu SSL.  Nazwa **musi** kończyć się sufiksem następujących: \_yourASENameHere_InternalLoadBalancingASE.  Ten sufiks jest używane przez portal jako certyfikat służy do zabezpieczania ASE włączone ILB wskaźnika.

Przykład skróconej *azuredeploy.parameters.json* przedstawiono poniżej:

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

Raz *azuredeploy.parameters.json* wypełnione pliku, domyślny certyfikat protokołu SSL można skonfigurować przy użyciu poniższy fragment kodu programu Powershell.  Zmienianie pliku ścieżki do dopasowania, gdy pliki szablonów usługi Azure Resource Manager znajdują się na tym komputerze.  Również Pamiętaj, aby podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager, a nazwa grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po przesłaniu szablonu usługi Azure Resource Manager, że potrwa około minuty 40 minutach na ASE frontonu do zastosowania zmiany.  Na przykład z ASE domyślny rozmiar, przy użyciu dwa końce przodu, szablon potrwa około jednej godziny i 20 minut do ukończenia.  Szablon jest uruchomiona ASE nie będzie mógł skalowania.  

Po zakończeniu szablonu aplikacji w ILB ASE jest możliwy za pośrednictwem protokołu HTTPS i połączenia będzie można zabezpieczyć przy użyciu domyślnego certyfikatu SSL.  Domyślny certyfikat SSL będzie używany, gdy aplikacje na ILB ASE są opisane przy użyciu kombinacji nazwy aplikacji oraz nazwa hosta domyślnego.  Na przykład *https://mycustomapp.internal-contoso.com* użyje domyślnego certyfikatu SSL dla **.internal contoso.com*.

Jednak podobnie jak aplikacje działające na publiczny usługę wielodostępną, deweloperzy mogą również skonfigurować nazwy hosta niestandardowego dla poszczególnych aplikacji, a następnie skonfiguruj unikatowy powiązania certyfikatu SNI SSL dla poszczególnych aplikacji.  

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie do środowiska usługi App Service, zobacz [wprowadzenie do środowiska usługi aplikacji](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

