---
title: Tworzenie ILB ASE v1
description: Tworzenie środowiska usługi app service z wewnętrznym modułem równoważenia obciążenia (ILB ASE). Ten doc jest dostępna tylko dla klientów, którzy używają starszej wersji ASE w wersji 1.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 1a0ec9465be3b714e90bfca6a15b60423d6065a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295575"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Jak tworzyć środowisko ASE wewnętrznego modułu równoważenia przy użyciu szablonów usługi Azure Resource Manager

> [!NOTE] 
> Ten artykuł dotyczy środowiska usługi app service w wersji 1. Istnieje nowsza wersja środowiska usługi app service, która jest łatwiejsza w użyciu i działa na bardziej zaawansowanej infrastruktury. Aby dowiedzieć się więcej o nowej wersji, zacznij od [wprowadzenia do środowiska usługi app service](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Środowiska usługi app service można tworzyć przy pomocą adresu wewnętrznego sieci wirtualnej zamiast publicznego adresu VIP.  Ten adres wewnętrzny jest dostarczany przez składnik platformy Azure o nazwie wewnętrzny moduł równoważenia obciążenia (ILB).  Ase równoważenia obciążenia sieciowego można utworzyć za pomocą witryny Azure portal.  Można go również utworzyć przy użyciu automatyzacji za pomocą szablonów usługi Azure Resource Manager.  W tym artykule o wiele więcej niż w tym artykule i w składni potrzebnej do utworzenia ase równoważenia obciążenia przy pomocy szablonów usługi Azure Resource Manager.

Istnieją trzy kroki związane z automatyzacją tworzenia ASE ILB:

1. Najpierw podstawowa grupa ASE jest tworzona w sieci wirtualnej przy użyciu wewnętrznego adresu modułu równoważenia obciążenia zamiast publicznego adresu VIP.  W ramach tego kroku nazwa domeny głównej jest przypisywana do ase równoważenia obciążenia sieciowego.
2. Po utworzeniu ASE równoważenia obciążenia sieciowego jest przekazywał certyfikat SSL.  
3. Przekazany certyfikat SSL jest jawnie przypisany do ASE ILB jako "domyślny" certyfikat SSL.  Ten certyfikat SSL będzie używany do ruchu SSL do aplikacji na ASE ILB, gdy aplikacje są adresowane `https://someapp.mycustomrootcomain.com`przy użyciu wspólnej domeny głównej przypisanej do ASE (np. )

## <a name="creating-the-base-ilb-ase"></a>Tworzenie podstawowego ase równoważenia obciążenia sieciowego
Przykładowy szablon usługi Azure Resource Manager i skojarzony z nim plik parametrów są dostępne w usłudze GitHub [w tym miejscu.][quickstartilbasecreate]

Większość parametrów w pliku *azuredeploy.parameters.json* są wspólne dla tworzenia zarówno ases równoważenia obciążenia, jak również asów powiązanych z publicznym adresem VIP.  Poniższa lista wywołuje parametry specjalnej notatki lub które są unikatowe podczas tworzenia ASE ILB:

* *internalLoadBalancingMode*: W większości przypadków ustawić to na 3, co oznacza, że zarówno ruch HTTP/HTTPS na portach 80/443, jak i porty kanału sterowania/danych słuchane przez usługę FTP na ASE, będą powiązane z adresem wewnętrznym sieci wirtualnej przydzielonej przez przynajemnicy ILB.  Jeśli ta właściwość jest zamiast tego ustawiona na 2, tylko porty związane z usługą FTP (zarówno kanały kontroli, jak i kanałów danych) będą powiązane z adresem ILB, podczas gdy ruch HTTP/HTTPS pozostanie w publicznym programie VIP.
* *dnsSuffix*: Ten parametr definiuje domyślną domenę główną, która zostanie przypisana do ASE.  W publicznej odmianie usługi Azure App Service domyślną domeną główną dla wszystkich aplikacji internetowych jest *azurewebsites.net*.  Jednak ponieważ środowisko ASE równoważenia obciążenia sieciowego jest wewnętrzne w sieci wirtualnej klienta, nie ma sensu używać domyślnej domeny głównej usługi publicznej.  Zamiast tego środowisko ASE równoważenia obciążenia sieciowego powinno mieć domyślną domenę główną, która ma sens do użycia w wewnętrznej sieci wirtualnej firmy.  Na przykład hipotetyczna firma Contoso Corporation może używać domyślnej domeny głównej *internal-contoso.com* dla aplikacji, które mają być dostępne tylko do rozwiązania i dostępne w sieci wirtualnej firmy Contoso. 
* *ipSslAddressCount*: Ten parametr jest automatycznie domyślnie do wartości 0 w pliku *azuredeploy.json,* ponieważ ases ILB mają tylko jeden adres równoważenia obciążenia.  Nie ma żadnych jawnych adresów IP-SSL dla ase ILB, a zatem puli adresów IP-SSL dla ase ILB musi być ustawiona na zero, w przeciwnym razie wystąpi błąd inicjowania obsługi administracyjnej. 

Po *wypełnieniu pliku azuredeploy.parameters.json* dla ase ILB, ILB ASE można następnie utworzyć przy użyciu następującego fragmentu kodu programu Powershell.  Zmień paths pliku, aby dopasować, gdzie znajdują się pliki szablonów usługi Azure Resource Manager na komputerze.  Należy również pamiętać, aby podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po przesłaniu szablonu usługi Azure Resource Manager utworzenie serwera ASE równoważenia obciążenia sieciowego zajmie kilka godzin.  Po zakończeniu tworzenia środowisko ASE równoważenia obciążenia sieciowego pojawi się w środowisku użytkownika portalu na liście środowisk usługi app service dla subskrypcji, która wyzwoliła wdrożenie.

## <a name="uploading-and-configuring-the-default-ssl-certificate"></a>Uploading and Configuring the "Default" SSL Certificate (Przekazywanie i konfigurowanie domyślnego certyfikatu protokołu SSL)
Po utworzeniu ase ILB certyfikat SSL powinien być skojarzony z ASE jako "domyślne" użycie certyfikatu SSL do ustanawiania połączeń SSL z aplikacjami.  Kontynuując hipotetyczny przykład Contoso Corporation, jeśli domyślny sufiks DNS ASE jest *internal-contoso.com,* *https://some-random-app.internal-contoso.com* połączenie wymaga certyfikatu SSL, który jest ważny dla **.internal-contoso.com*. 

Istnieje wiele sposobów uzyskania prawidłowego certyfikatu SSL, w tym wewnętrznych urzędów certyfikacji, zakupu certyfikatu od zewnętrznego wystawcy i użycia certyfikatu z podpisem własnym.  Niezależnie od źródła certyfikatu SSL należy poprawnie skonfigurować następujące atrybuty certyfikatu:

* *Temat*: Ten atrybut musi być ustawiony na **.your-root-domain-here.com*
* *Nazwa alternatywna podmiotu:* Ten atrybut musi zawierać zarówno **.your-root-domain-here.com*, jak i **.scm.your-root-domain-here.com*.  Powodem drugiego wpisu jest to, że połączenia SSL z witryną SCM/Kudu skojarzoną z każdą aplikacją będą nawiązywać przy użyciu adresu formularza *your-app-name.scm.your-root-domain-here.com*.

Z ważnym certyfikatem SSL w ręku, potrzebne są dwa dodatkowe kroki przygotowawcze.  Certyfikat SSL musi zostać przekonwertowany/zapisany jako plik .pfx.  Pamiętaj, że plik .pfx musi zawierać wszystkie certyfikaty pośrednie i główne, a także musi być zabezpieczony hasłem.

Następnie wynikowy plik .pfx musi zostać przekonwertowany na ciąg base64, ponieważ certyfikat SSL zostanie przekazany przy użyciu szablonu usługi Azure Resource Manager.  Ponieważ szablony usługi Azure Resource Manager są plikami tekstowymi, plik .pfx musi zostać przekonwertowany na ciąg base64, aby mógł zostać dołączony jako parametr szablonu.

Poniższy fragment kodu programu Powershell przedstawia przykład generowania certyfikatu z podpisem własnym, eksportowania certyfikatu jako pliku pfx, konwertowania pliku pfx na ciąg zakodowany base64, a następnie zapisywania ciągu zakodowanego base64 do oddzielnego pliku.  Kod programu Powershell do kodowania base64 został zaadaptowany z [bloga skryptów programu Powershell.][examplebase64encoding]

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")

Po pomyślnym wygenerowaniu certyfikatu SSL i przekonwertowaniu na ciąg zakodowany base64 można użyć przykładowego szablonu usługi Azure Resource Manager w usłudze GitHub do [konfigurowania domyślnego certyfikatu SSL.][configuringDefaultSSLCertificate]

Parametry w pliku *azuredeploy.parameters.json* są wymienione poniżej:

* *appServiceEnvironmentName*: Nazwa skonfigurowanego środowiska ASE równoważenia obciążenia równoważącego.
* *existingAseLocation:* Ciąg tekstowy zawierający region platformy Azure, w którym wdrożono ase równoważenia obciążenia.  Na przykład: "Południowo-środkowe stany USA".
* *pfxBlobString*: Na podstawie64 zakodowana reprezentacja ciągu pliku .pfx.  Korzystając z fragmentu kodu pokazanego wcześniej, można skopiować ciąg zawarty w "exportedcert.pfx.b64" i wkleić go jako wartość atrybutu *pfxBlobString.*
* *hasło*: Hasło używane do zabezpieczenia pliku .pfx.
* *certificateThumbprint*: odcisk palca certyfikatu.  Jeśli ta wartość zostanie pobrana z programu Powershell (np. *$certificate. Odcisk palca* z wcześniejszego fragmentu kodu), można użyć wartości jako — jest.  Jeśli jednak wartość zostanie skopiowana z okna dialogowego certyfikatu systemu Windows, pamiętaj, aby usunąć obce spacje.  *CertyfikatThumbprint* powinien wyglądać mniej więcej tak: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *certificateName*: Przyjazny identyfikator ciągu według własnego wyboru używany do identyfikacji certyfikatu.  Nazwa jest używana jako część unikatowego identyfikatora usługi Azure Resource Manager dla jednostki *Microsoft.Web/certificates* reprezentującej certyfikat SSL.  Nazwa **musi kończyć się** następującym \_sufiksem: yourASENameHere_InternalLoadBalancingASE.  Ten sufiks jest używany przez portal jako wskaźnik, że certyfikat jest używany do zabezpieczania ase z włączoną funkcją ILB.

Poniżej przedstawiono skrócony przykład *pliku azuredeploy.parameters.json:*

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

Po *wypełnieniu pliku azuredeploy.parameters.json* domyślny certyfikat SSL można skonfigurować przy użyciu następującego fragmentu kodu programu Powershell.  Zmień paths pliku, aby dopasować, gdzie znajdują się pliki szablonów usługi Azure Resource Manager na komputerze.  Należy również pamiętać, aby podać własne wartości dla nazwy wdrożenia usługi Azure Resource Manager i nazwy grupy zasobów.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"

    New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Po przesłaniu szablonu usługi Azure Resource Manager zajmie około czterdziestu minut na ase front-end, aby zastosować zmiany.  Na przykład przy domyślnym rozmiarze ASE przy użyciu dwóch front-ends, szablon zajmie około jednej godziny i dwadzieścia minut, aby zakończyć.  Gdy szablon jest uruchomiony ASE nie będzie można skalować.  

Po zakończeniu szablonu aplikacje na ASE równoważenia obciążenia sieciowego są dostępne za pośrednictwem protokołu HTTPS, a połączenia będą zabezpieczone przy użyciu domyślnego certyfikatu SSL.  Domyślny certyfikat SSL będzie używany, gdy aplikacje na ase ILB są adresowane przy użyciu kombinacji nazwy aplikacji i domyślnej nazwy hosta.  Na *https://mycustomapp.internal-contoso.com* przykład można użyć domyślnego certyfikatu SSL dla **.internal-contoso.com*.

Jednak podobnie jak aplikacje uruchomione w publicznej usłudze wielodostępowej, deweloperzy mogą również konfigurować niestandardowe nazwy hostów dla poszczególnych aplikacji, a następnie konfigurować unikatowe powiązania certyfikatów SNI SSL dla poszczególnych aplikacji.  

## <a name="getting-started"></a>Wprowadzenie
Aby rozpocząć korzystanie ze środowisk usługi App Service, zobacz [Wprowadzenie do środowiska usługi aplikacji](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

