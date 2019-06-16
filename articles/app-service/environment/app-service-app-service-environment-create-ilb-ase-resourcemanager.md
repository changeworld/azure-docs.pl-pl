---
title: Tworzenie wewnętrznego modułu równoważenia obciążenia środowiska ASE przy użyciu szablonów platformy Azure Resource Manager — App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć środowisko ASE wewnętrznego obciążenia wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager.
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
ms.custom: seodec18
ms.openlocfilehash: 35e0dc5dabaf1602b87ec6a8be86ed609f3ea12f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130759"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Jak tworzyć środowisko ASE wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager

> [!NOTE] 
> Ten artykuł dotyczy środowiska App Service Environment w wersji 1. Istnieje nowsza wersja usługi App Service Environment jest łatwiejszy w obsłudze, która działa na bardziej zaawansowanych infrastruktury. Aby dowiedzieć się więcej na temat nowej wersji rozpoczynać [wprowadzenie do usługi App Service Environment](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Środowiska usługi App Service można tworzyć przy użyciu wewnętrznego adresu sieci wirtualnej zamiast publicznych adresów VIP.  Ten adres wewnętrzny jest zapewniana przez składnik platformy Azure o nazwie wewnętrznego modułu równoważenia obciążenia (ILB).  Środowisko ASE z wewnętrznym modułem równoważenia obciążenia mogą być tworzone przy użyciu witryny Azure portal.  Można także tworzyć, używając usługi automation za pomocą szablonów usługi Azure Resource Manager.  W tym artykule opisano kolejne kroki oraz składni konieczna utworzyć środowisko ASE z wewnętrznym modułem równoważenia obciążenia przy użyciu szablonów usługi Azure Resource Manager.

Istnieją trzy kroki związane z automatyzacji tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

1. Najpierw podstawowego środowiska ASE jest tworzony w sieci wirtualnej przy użyciu adresu modułu równoważenia obciążenia wewnętrznego, zamiast publicznych adresów VIP.  W ramach tego kroku nazwa domeny głównej jest przypisany do środowiska ASE wewnętrznego modułu równoważenia obciążenia.
2. Po utworzeniu środowiska ASE wewnętrznego modułu równoważenia obciążenia jest przekazywany certyfikat SSL.  
3. Przekazanego certyfikatu SSL jest jawnie przypisane do środowiska ASE wewnętrznego modułu równoważenia obciążenia jako jego certyfikatu SSL "default".  Ten certyfikat SSL będzie służyć dla ruchu protokołu SSL do aplikacji w środowisku ASE wewnętrznego modułu równoważenia obciążenia, gdy aplikacje są adresowane za pomocą wspólnego domeny katalogu głównego, przypisany do środowiska ASE (np. https://someapp.mycustomrootcomain.com)

## <a name="creating-the-base-ilb-ase"></a>Tworzenie podstawowej środowiska ASE z wewnętrznym modułem równoważenia obciążenia
Przykładowy szablon usługi Azure Resource Manager i jego pliku skojarzone z nimi parametry są dostępne w serwisie GitHub [tutaj][quickstartilbasecreate].

Większość parametrów w *azuredeploy.parameters.json* pliku są wspólne dla tworzenia zarówno środowiska ASE z wewnętrznym modułem równoważenia obciążenia, a także za pomocą środowisk ASE powiązany z publicznych adresów VIP.  Poniżej wywołania parametrów ważne out lub które są unikatowe, podczas tworzenia środowiska ASE z wewnętrznym modułem równoważenia obciążenia:

* *internalLoadBalancingMode*:  W większości przypadków zestawie to 3, co oznacza, że ruch HTTP/HTTPS na portach 80/443 i kanału kontroli/danych, które porty wysłuchaliśmy przez usługę FTP w środowisku ASE, będą powiązane wewnętrznego przydzielony adres wewnętrznej sieci wirtualnej.  Jeśli ta właściwość zamiast tego jest równa 2, następnie usługę FTP powiązanych portów (kanały zarówno kontroli, jak i dane), będą powiązane adresu wewnętrznego modułu równoważenia obciążenia, podczas gdy ruch HTTP/HTTPS pozostanie na publicznych adresów VIP.
* *dnsSuffix*:  Ten parametr określa domyślne domeny katalogu głównego, który zostanie przypisany do środowiska ASE.  W publicznej wersji usługi Azure App Service, domyślnej domeny katalogu głównego dla wszystkich aplikacji sieci web jest *azurewebsites.net*.  Jednak ponieważ środowisko ASE z wewnętrznym modułem równoważenia obciążenia jest wewnętrzny do sieci wirtualnej klienta, nie ma sensu do korzystania z domeny katalogu głównego domyślnego usług publicznych.  Zamiast tego środowisko ASE z wewnętrznym modułem równoważenia obciążenia powinien mieć domyślnej domeny katalogu głównego, pasującą do użycia w wewnętrznej sieci wirtualnej firmy.  Na przykład użyć domyślnej domeny katalogu głównego, z hipotetyczny Contoso Corporation *wewnętrznego contoso.com* dla aplikacji, które mają być tylko do rozpoznania i jest dostępny w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*:  Ten parametr jest automatycznie ustawiana domyślnie na wartość 0 w *azuredeploy.json* pliku, ponieważ środowiska ASE z wewnętrznym modułem równoważenia obciążenia ma tylko jeden adres wewnętrznego modułu równoważenia obciążenia.  Nie ma żadnych jawnych adresów IP SSL za środowisko ASE wewnętrznego modułu równoważenia obciążenia i dlatego puli adresów IP protokołu SSL dla środowiska ASE z wewnętrznym modułem równoważenia obciążenia musi być równa zero, w przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Gdy *azuredeploy.parameters.json* pliku zostało wypełnione za środowisko ASE wewnętrznego modułu równoważenia obciążenia, następnie można utworzyć środowisko ASE wewnętrznego modułu równoważenia obciążenia za pomocą poniższej wstawki kodu programu Powershell.  Zmienianie pliku ścieżki do dopasowania, gdzie są pliki szablonu usługi Azure Resource Manager znajdują się na swojej maszynie.  Pamiętaj również podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po usługi Azure Resource Manager szablon jest złożone z potrwa kilka godzin ASE wewnętrznego modułu równoważenia obciążenia, który ma zostać utworzony.  Po zakończeniu tworzenia środowiska ASE wewnętrznego modułu równoważenia obciążenia zostaną wyświetlone w portalu środowiska użytkownika na liście środowisk usługi App Service dla subskrypcji, która wyzwoliła wdrożenia.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Przekazywanie i konfigurowanie certyfikatu SSL "Default"
Po utworzeniu środowiska ASE wewnętrznego modułu równoważenia obciążenia, certyfikat SSL powinna być skojarzona z ASE jak "domyślna" certyfikat SSL używa do nawiązywania połączeń SSL do aplikacji.  Kontynuując hipotetycznego przykładu Contoso Corporation, jeśli środowisko ASE domyślne DNS sufiks jest *wewnętrznego contoso.com*, następnie połączenie *https://some-random-app.internal-contoso.com* wymaga certyfikatu SSL, który jest prawidłowe dla * *.internal contoso.com*. 

Istnieją różne sposoby, aby uzyskać prawidłowy certyfikat SSL tym wewnętrzne urzędy certyfikacji, zakup certyfikat od wystawcy zewnętrznego i przy użyciu certyfikatu z podpisem własnym.  Niezależnie od źródła certyfikatu SSL należy poprawnie skonfigurować następujące atrybuty certyfikatu:

* *Podmiot*:  Ten atrybut musi być równa **lokalizacji głównego domain-here.com*
* *Alternatywna nazwa podmiotu*:  Ten atrybut musi zawierać zarówno **lokalizacji głównego domain-here.com*, i * *.SCM.domena-głównego-domeny — here.com*.  Przyczyną drugi wpis jest, że będzie nawiązywane połączenia SSL z witryną SCM/Kudu skojarzonych z poszczególnymi aplikacjami przy użyciu adresu w postaci *your-app-name.scm.your-root-domain-here.com*.

Za pomocą ważnego certyfikatu SSL w kasie potrzebne są dwa dodatkowe kroki przygotowawcze.  Certyfikat SSL musi być konwertowany/zapisany jako plik pfx.  Należy pamiętać, plik PFX musi zawierać wszystkie pośrednie i certyfikaty główne i musi zostać zabezpieczony za pomocą hasła.

Plik PFX wynikowy musi ma zostać przekonwertowany na ciąg base64, ponieważ certyfikat SSL zostanie załadowany przy użyciu szablonu usługi Azure Resource Manager.  Ponieważ szablony usługi Azure Resource Manager są plikami tekstowymi, plik PFX musi można przekonwertować na ciąg w formacie base64, dzięki czemu może być uwzględniany jako parametr szablonu.

Poniższy fragment kodu programu Powershell przedstawiono przykład generowania certyfikatu z podpisem własnym, eksportowanie certyfikatu jako plik PFX, przekonwertować plik pfx w formacie base64 ciąg zakodowany w formacie, a następnie zapisując base64 ciąg zakodowany w formacie do osobnego pliku.  Kod programu Powershell dla kodowania base64 został napisany [Blog skrypty programu Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Gdy certyfikat SSL został pomyślnie wygenerowany i przekonwertowane na zakodowany w formacie base64 string, przykładowy szablon usługi Azure Resource Manager w witrynie GitHub [Konfigurowanie domyślnego certyfikatu SSL] [ configuringDefaultSSLCertificate] mogą być używane.

Parametry w *azuredeploy.parameters.json* pliku są wymienione poniżej:

* *appServiceEnvironmentName*:  Nazwa środowiska ASE wewnętrznego modułu równoważenia obciążenia jest skonfigurowany.
* *existingAseLocation*:  Ciąg tekstowy zawierający regionu platformy Azure, w której wdrożono środowisko ASE wewnętrznego modułu równoważenia obciążenia.  Na przykład:  "Południowo-środkowe stany USA".
* *pfxBlobString*:  Based64 zakodowany ciąg reprezentujący plik pfx.  Przy użyciu fragmentu kodu pokazano wcześniej, czy kopiowania ciąg znajdujący się w "exportedcert.pfx.b64" i wklej go jako wartość *pfxBlobString* atrybutu.
* *hasło*:  Hasło używane do zabezpieczania plików PFX.
* *certificateThumbprint*:  Odcisk palca certyfikatu.  Jeśli pobierania tej wartości ze środowiska Powershell (np. *$certificate. Odcisk palca* z wcześniejszych fragment kodu), możesz użyć wartości jako-to.  Jednak jeśli kopiujesz wartości w oknie dialogowym certyfikat Windows, pamiętaj, aby usunąć nadmiarowe miejsca do magazynowania.  *CertificateThumbprint* powinien wyglądać mniej więcej tak:  AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*:  Identyfikator ciągu przyjazna swoje własne, wybierając używane do identyfikowania certyfikatu.  Nazwa jest używana jako część Unikatowy identyfikator usługi Azure Resource Manager *Microsoft.Web/certificates* jednostka reprezentująca certyfikatów SSL.  Nazwa **musi** kończy się następującym sufiksem: \_yourASENameHere_InternalLoadBalancingASE.  Ten sufiks jest używany przez portal jako wskazówka, że certyfikat jest używany do zabezpieczania środowiska ASE z obsługą wewnętrznego modułu równoważenia obciążenia.

Przykład skróconej *azuredeploy.parameters.json* znajdują się poniżej:

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

Gdy *azuredeploy.parameters.json* pliku została wypełniona, domyślny certyfikat SSL można skonfigurować za pomocą poniższej wstawki kodu programu Powershell.  Zmienianie pliku ścieżki do dopasowania, gdzie są pliki szablonu usługi Azure Resource Manager znajdują się na swojej maszynie.  Pamiętaj również podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po usługi Azure Resource Manager szablon jest złożone z potrwa około 40 minut na ASE frontonu, aby zastosować zmiany.  Na przykład za pomocą środowiska ASE domyślny rozmiar przy użyciu dwóch połączeń frontonach szablon będzie zająć około jednej godziny i dwadzieścia minut.  Uruchomionej szablonu środowiska ASE nie będzie można skalować.  

Po wykonaniu szablonu aplikacji w środowisku ASE wewnętrznego modułu równoważenia obciążenia jest możliwy za pośrednictwem protokołu HTTPS i połączeń zostanie zabezpieczone za pomocą domyślnego certyfikatu SSL.  Domyślny certyfikat SSL będzie używany, gdy aplikacje w środowisku ASE wewnętrznego modułu równoważenia obciążenia są adresowane przy użyciu kombinacji nazwy aplikacji, a także domyślną nazwę hosta.  Na przykład *https://mycustomapp.internal-contoso.com* użyje domyślnego certyfikatu SSL dla * *.internal contoso.com*.

Jednak tak samo jak aplikacje działające na publicznych usługą wielodostępną, deweloperzy mogą również konfigurować nazwy niestandardowego hosta dla poszczególnych aplikacji, a następnie skonfiguruj unikatowych powiązań certyfikatów SNI SSL dla poszczególnych aplikacji.  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć pracę przy użyciu środowisk usługi App Service, zobacz [wprowadzenie do środowiska App Service Environment](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

