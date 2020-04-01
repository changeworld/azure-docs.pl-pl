---
title: Powiązania certyfikatów
description: Wyjaśnij wiele tematów związanych z certyfikatami w środowisku usługi aplikacji. Dowiedz się, jak powiązania certyfikatów działają w aplikacjach z jedną dzierżawą w programie ASE.
author: ccompy
ms.assetid: 9e21a7e4-2436-4e81-bb05-4a6ba70eeaf7
ms.topic: article
ms.date: 08/29/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ba1d06ce83d50b6f0db84d1e423e66eae98f665d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477503"
---
# <a name="certificates-and-the-app-service-environment"></a>Certyfikaty i środowisko usługi aplikacji 

Środowisko usługi aplikacji(ASE) to wdrożenie usługi Azure App Service, która działa w sieci wirtualnej platformy Azure.The App Service Environment(ASE) is a deployment of the Azure App Service that runs within your Azure Virtual Network(VNet. Można go wdrożyć z punktem końcowym aplikacji dostępnej dla Internetu lub punktem końcowym aplikacji, który znajduje się w sieci wirtualnej. Jeśli wdrożysz ase z punktem końcowym dostępnym w Internecie, to wdrożenie jest nazywany zewnętrznym ASE. Jeśli wdrożysz ase z punktem końcowym w sieci wirtualnej, to wdrożenie jest nazywany ASE równoważenia obciążenia sieciowego. Więcej informacji na temat ASE równoważenia obciążenia sieciowego można dowiedzieć się z dokumentu [ASE równoważenia obciążenia i przyw.](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)

ASE jest jednym systemem dzierżawy. Ponieważ jest pojedyncza dzierżawa, istnieją pewne funkcje dostępne tylko z ASE, które nie są dostępne w usłudze aplikacji wielu dzierżawców. 

## <a name="ilb-ase-certificates"></a>Certyfikaty ASE równoważenia obciążenia sieciowego 

Jeśli używasz zewnętrznego ase, a następnie aplikacje są osiągane w [appname]. [asename].p.azurewebsites.net. Domyślnie wszystkie asy, nawet asy RÓWNOWAżenia obciążenia sieciowego, są tworzone z certyfikatami, które są zgodne z tym formatem. Gdy masz ASE ILB, aplikacje są osiągane na podstawie nazwy domeny, którą określisz podczas tworzenia ASE równoważenia obciążenia. Aby aplikacje obsługiwać TLS, należy przekazać certyfikaty. Uzyskaj ważny certyfikat TLS/SSL przy użyciu wewnętrznych urzędów certyfikacji, zakup certyfikatu od zewnętrznego wystawcy lub przy użyciu certyfikatu z podpisem własnym. 

Istnieją dwie opcje konfigurowania certyfikatów za pomocą narzędzia ASE równoważenia obciążenia sieciowego.  Można ustawić domyślny certyfikat symboli wieloznacznych dla środowiska ASE równoważenia obciążenia sieciowego lub ustawić certyfikaty w poszczególnych aplikacjach sieci web w środowisku ASE.  Niezależnie od dokonanego wyboru następujące atrybuty certyfikatu muszą być poprawnie skonfigurowane:

- **Przedmiot:** Ten atrybut musi być ustawiony na *. [twoja domena root-tutaj] dla certyfikatu ASE ilb z symbolami wieloznacznym. Jeśli tworzysz certyfikat dla aplikacji, powinien on być [appname]. [twoja domena root-tutaj]
- **Alternatywna nazwa tematu:** Ten atrybut musi zawierać oba *. [twoja domena-root-here] i *.scm. [twoja domena root-tutaj] dla certyfikatu ASE ilb ilb z symbolami wieloznacznym. Jeśli tworzysz certyfikat dla aplikacji, powinien on być [appname]. [twoja domena root-here] i [appname].scm. [twoja domena root-tutaj].

Jako trzeci wariant można utworzyć certyfikat ASE równoważenia obciążenia, który zawiera wszystkie nazwy poszczególnych aplikacji w sieci SAN certyfikatu zamiast przy użyciu odwołania symboli wieloznacznych. Problem z tą metodą polega na tym, że musisz znać z góry nazwy aplikacji, które umieszczasz w ASE lub musisz aktualizować certyfikat ASE ILB.

### <a name="upload-certificate-to-ilb-ase"></a>Przekazywanie certyfikatu do ase równoważenia obciążenia sieciowego 

Po utworzeniu ase ILB w portalu, certyfikat musi być ustawiony dla ILB ASE. Dopóki certyfikat nie zostanie ustawiony, program ASE wyświetli baner, który nie został ustawiony.  

Przekazany certyfikat musi być plikiem .pfx. Po przekazaniu certyfikatu program ASE wykona operację skalowania w celu skonfigurowania certyfikatu. 

Nie można utworzyć ase i przekazać certyfikat jako jedną akcję w portalu lub nawet w jednym szablonie. Jako osobną akcję można przekazać certyfikat przy użyciu szablonu zgodnie z opisem w dokumencie [Utwórz ASE z dokumentu szablonu.](./create-from-template.md)  

Jeśli chcesz szybko utworzyć certyfikat z podpisem własnym do testowania, możesz użyć następującego bitu programu PowerShell:

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
Podczas tworzenia certyfikatu z podpisem własnym należy upewnić się, że nazwa podmiotu ma format CN={ASE_NAME_HERE}_InternalLoadBalancingASE.

## <a name="application-certificates"></a>Certyfikaty aplikacji 

Aplikacje hostowane w ase można użyć funkcji certyfikatu zorientowanych na aplikację, które są dostępne w usłudze aplikacji wielu dzierżawców. Funkcje te obejmują:  

- Certyfikaty SNI 
- Protokół SSL oparty na protoke, który jest obsługiwany tylko z zewnętrznym ase.  Ase równoważenia obciążenia sieciowego nie obsługuje protokołu SSL opartego na protokosze IP.
- Certyfikaty hostowane przez keyvault 

Instrukcje przekazywania tych certyfikatów i zarządzania nimi są dostępne w obszarze [Dodaj certyfikat TLS/SSL w usłudze Azure App Service.](../configure-ssl-certificate.md)  Jeśli po prostu konfigurujesz certyfikaty tak, aby były zgodne z niestandardową nazwą domeny przypisaną do aplikacji sieci web, te instrukcje wystarczą. Jeśli przekazujesz certyfikat dla aplikacji sieci Web ASE równoważenia obciążenia sieciowego z domyślną nazwą domeny, określ witrynę scm w sieci SAN certyfikatu, jak wspomniano wcześniej. 

## <a name="tls-settings"></a>Ustawienia protokołu TLS 

Ustawienie TLS można skonfigurować na poziomie aplikacji.  

## <a name="private-client-certificate"></a>Certyfikat klienta prywatnego 

Typowym przypadkiem użycia jest skonfigurowanie aplikacji jako klienta w modelu klient-serwer. Jeśli serwer jest zabezpieczony za pomocą certyfikatu prywatnego urzędu certyfikacji, należy przekazać certyfikat klienta do aplikacji.  Poniższe instrukcje będą ładować certyfikaty do magazynu zaufania pracowników, na których jest uruchomiona aplikacja. Jeśli załadujesz certyfikat do jednej aplikacji, możesz go używać z innymi aplikacjami w tym samym planie usługi App Service bez ponownego przekazywania certyfikatu.

Aby przekazać certyfikat do aplikacji w ase:

1. Wygeneruj plik *cer* dla certyfikatu. 
2. Przejdź do aplikacji, która potrzebuje certyfikatu w witrynie Azure portal
3. Przejdź do ustawień SSL w aplikacji. Kliknij pozycję Przekaż certyfikat. wybierz pozycję Publiczny. Wybierz opcję Komputer lokalny. Podaj nazwę Przejrzyj i wybierz plik *cer.* Wybierz opcję przekaż. 
4. Skopiuj odcisk palca.
5. Przejdź do ustawień aplikacji. Utwórz WEBSITE_LOAD_ROOT_CERTIFICATES ustawienia aplikacji z odciskiem palca jako wartością. Jeśli masz wiele certyfikatów, możesz umieścić je w tym samym ustawieniu oddzielonym przecinkami i bez odstępów, takich jak 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Certyfikat będzie dostępny dla wszystkich aplikacji w tym samym planie usługi aplikacji co aplikacja, która skonfigurowała to ustawienie. Jeśli chcesz, aby był dostępny dla aplikacji w innym planie usługi app service, musisz powtórzyć operację ustawienia aplikacji w aplikacji w tym planie usługi app service. Aby sprawdzić, czy certyfikat jest ustawiony, przejdź do konsoli Kudu i wystosuj następujące polecenie w konsoli debugowania programu PowerShell:

    dir cert:\localmachine\root

Aby przeprowadzić testy, można utworzyć certyfikat z podpisem własnym i wygenerować plik *cer* z następującym programem PowerShell: 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com

    $certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.cer"
    export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT

