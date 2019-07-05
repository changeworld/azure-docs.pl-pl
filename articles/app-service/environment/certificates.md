---
title: Certyfikaty i środowisko usługi App Service — platformy Azure
description: Wyjaśniają wiele tematów związanych z certyfikatami na środowisko ASE
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba34638bbdb838adc6f1e61b1f8b07a6915815c0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540774"
---
# <a name="certificates-and-the-app-service-environment"></a>Certyfikaty i środowiska usługi App Service 

App Service Environment (ASE) to wdrożenie usługi Azure App Service, który jest uruchamiany w ramach Twoja sieć wirtualna platformy Azure. Można je było wdrożyć przy użyciu punktu końcowego dostępnej aplikacji internetowych lub punkt końcowy aplikacji, który znajduje się w sieci wirtualnej. W przypadku wdrożenia środowiska ASE z dostępnym punkcie końcowym internet tego wdrożenia jest nazywany zewnętrznego środowiska ASE. W przypadku wdrożenia środowiska ASE z punktem końcowym usługi w sieci wirtualnej, tego wdrożenia nazywa się środowisko ASE z wewnętrznym modułem równoważenia obciążenia. Dowiedz się więcej na temat środowiska ASE wewnętrznego modułu równoważenia obciążenia z [tworzenia i używania środowiska ASE z wewnętrznym modułem równoważenia obciążenia](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase) dokumentu.

Środowisko ASE jest system z jednym dzierżawcą. Ponieważ jest ona pojedynczej dzierżawy, istnieją niektóre funkcje dostępne tylko w przypadku środowiska ASE, które nie są dostępne w wielodostępnej usłudze App Service. 

## <a name="ilb-ase-certificates"></a>Certyfikaty środowiska ASE z wewnętrznym modułem równoważenia obciążenia 

Jeśli używasz zewnętrznego środowiska ASE, Twoje aplikacje są osiągane po [nazwa_aplikacji]. [asename]. p.azurewebsites.net. Domyślnie wszystkie środowiska ASE, nawet usług ILB ASE, są tworzone za pomocą certyfikatów, które należy wykonać w tym formacie. Jeśli masz środowisko ASE z wewnętrznym modułem równoważenia obciążenia, osiągnięcia skonfigurowanych na podstawie nazwy domeny, która została określona podczas tworzenia środowiska ASE wewnętrznego modułu równoważenia obciążenia aplikacji. Aplikacje do obsługi protokołu SSL, należy przekazać certyfikatów. Uzyskaj certyfikat protokołu SSL za pomocą wewnętrznych urzędów certyfikacji, zakup certyfikat od wystawcy zewnętrznego lub przy użyciu certyfikatu z podpisem własnym. 

Istnieją dwa sposoby konfigurowania certyfikatów przy użyciu środowiska ASE wewnętrznego modułu równoważenia obciążenia.  Można ustawić domyślny certyfikat wieloznaczny dla środowiska ASE wewnętrznego modułu równoważenia obciążenia lub ustaw opcję certyfikaty poszczególnych aplikacji sieci web w środowisku ASE.  Niezależnie od wybranego typu musi być prawidłowo skonfigurowane następujące atrybuty certyfikatu:

- **Temat:** Ten atrybut musi być równa *. [your głównego domeny — tutaj] Aby certyfikat wieloznaczny środowiska ASE z wewnętrznym modułem równoważenia obciążenia. W przypadku tworzenia certyfikatu dla aplikacji, następnie należy go [nazwa_aplikacji]. [your głównego domeny — tutaj]
- **Nazwa alternatywna podmiotu:** Ten atrybut musi zawierać zarówno *. [your głównego domeny — tutaj] i *.scm. [your głównego domeny — tutaj] Aby certyfikat wieloznaczny w środowisku ASE z wewnętrznym modułem równoważenia obciążenia. W przypadku tworzenia certyfikatu dla aplikacji, następnie należy go [nazwa_aplikacji]. [your głównego domeny — tutaj] i [nazwa_aplikacji] .scm. [your głównego domeny — tutaj].

Jako wariant trzeci można utworzyć certyfikat środowiska ASE z wewnętrznym modułem równoważenia obciążenia, który zawiera wszystkie nazwy poszczególnych aplikacji w sieci SAN certyfikatu zamiast odwołania symboli wieloznacznych. Ten problem przy użyciu tej metody polega na musisz wiedzieć na początku nazwy aplikacji, które są zaangażowane w środowisku ASE lub należy zachować aktualizacji certyfikatu środowiska ASE z wewnętrznym modułem równoważenia obciążenia.

### <a name="upload-certificate-to-ilb-ase"></a>Przekaż certyfikat do środowiska ASE z wewnętrznym modułem równoważenia obciążenia 

Po utworzeniu środowiska ASE z wewnętrznym modułem równoważenia obciążenia w portalu usługi musi być ustawiony dla środowiska ASE wewnętrznego modułu równoważenia obciążenia. Dopóki nie zostanie ustawiony certyfikat, środowisko ASE będzie wyświetlana transparentu nie ustawiono certyfikatu.  

Możesz przekazać certyfikat musi być plikiem pfx. Po przekazaniu certyfikatu ASE wykona operację skalowania, aby ustawić certyfikat. 

Nie można utworzyć środowisko ASE i przekaż certyfikat jako jedna akcja w portalu lub nawet w jednym szablonie. Jako osobną akcję można przekazać certyfikatu, przy użyciu szablonu, zgodnie z opisem w [utworzyć środowisko ASE na podstawie szablonu](./create-from-template.md) dokumentu.  

Jeśli chcesz utworzyć certyfikat z podpisem własnym szybko do testowania, można użyć następujących bitowej programu PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Podczas tworzenia własnym podpisany certyfikat, konieczne będzie upewnij się, w nazwie podmiotu ma format, CN = {ASE_NAME_HERE} _InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certyfikaty aplikacji 

Aplikacje, które są hostowane w środowisku ASE można użyć funkcji certyfikatu aplikacji, które są dostępne w wielodostępnej usłudze App Service. Te funkcje obejmują:  

- Certyfikatów SNI 
- Oparte na adresie IP SSL, który jest obsługiwany tylko z zewnętrznym środowiskiem ASE.  Środowisko ASE z wewnętrznym modułem równoważenia obciążenia nie obsługuje SSL opartego na protokole IP.
- Certyfikaty hostowanej usługi KeyVault 

Instrukcje dotyczące przekazywania certyfikatów i zarządzanie nimi te są dostępne w tym samouczku SSL usługi App https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl.  Jeśli po prostu skonfigurować certyfikaty Period z nazwą domeny niestandardowej, który został przypisany do aplikacji sieci web, te instrukcje będą wystarczające. Podczas przekazywania certyfikatu dla aplikacji sieci web środowiska ASE z wewnętrznym modułem równoważenia obciążenia za pomocą domyślnej nazwy domeny, określ witryny scm w sieci SAN, certyfikatu, jak wspomniano wcześniej. 

## <a name="tls-settings"></a>Ustawienia protokołu TLS 

Ustawienia protokołu TLS na poziomie aplikacji.  

## <a name="private-client-certificate"></a>Certyfikat klienta prywatne 

Typowy przypadek użycia jest skonfigurować aplikację jako klienta w modelu klient serwer. Możesz zabezpieczyć serwera przy użyciu certyfikatu prywatnego urzędu certyfikacji, musisz przekazać certyfikat klienta do swojej aplikacji.  Zgodnie z poniższymi instrukcjami załaduje certyfikatów truststore pracowników, w których uruchomiona jest aplikacja. Jeśli załadujesz certyfikatu do jednej aplikacji, można korzystania z innych aplikacji w tym samym planie usługi App Service bez przekazywania certyfikatu ponownie.

Aby przekazać certyfikat do aplikacji w środowisku ASE:

1. Generowanie *cer* pliku dla certyfikatu. 
2. Przejdź do aplikacji, która wymaga certyfikatu w witrynie Azure portal
3. Przejdź do ustawień protokołu SSL w aplikacji. Kliknij przycisk Przekaż certyfikat. Wybierz publiczne. Wybierz komputer lokalny. Podaj nazwę. Wyszukaj i wybierz swoje *cer* pliku. Wybieranie pozycji Przekaż. 
4. Skopiuj odcisk palca.
5. Przejdź do ustawień aplikacji. Utwórz WEBSITE_LOAD_ROOT_CERTIFICATES ustawienie aplikacji o odcisku palca, jako wartość. Jeśli masz wiele certyfikatów, można umieścić je w tego samego ustawienia, oddzielając je przecinkami, a bez białych znaków, takich jak 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certyfikat jest dostępny przez wszystkie aplikacje w tym samym planie usługi app service jako aplikację, która skonfigurowane ustawienie. Jeśli potrzebujesz powinna być dostępna dla aplikacji w różnych planu usługi App Service, należy powtórzyć operację ustawienia aplikacji w aplikacji, w tym planie usługi App Service. Aby sprawdzić, czy certyfikat jest ustawiona, przejdź do konsoli Kudu i wydać następujące polecenie w konsoli debugowania środowiska PowerShell:

    dir cert:\localmachine\root

Do testowania aplikacji, można utworzyć certyfikat z podpisem własnym i generowania *cer* pliku następujące polecenie programu PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

