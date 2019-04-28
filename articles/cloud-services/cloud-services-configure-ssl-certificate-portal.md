---
title: Konfigurowanie certyfikatu SSL dla usługi w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, sposób określania punktu końcowego HTTPS dla roli sieci web oraz przekazywania certyfikatu SSL w celu zabezpieczenia aplikacji. Te przykłady użycia witryny Azure portal.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeconnoc
ms.openlocfilehash: 2a9879ebc55a5f25c1a358e386697dce1c55ec90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61434107"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurowanie protokołu SSL dla aplikacji na platformie Azure

Szyfrowanie SSL (Secure Socket Layer) to najczęściej stosowana metoda zabezpieczania danych wysyłanych przez Internet. W ramach tego często wykonywanego zadania omówiono sposób określania punktu końcowego HTTPS dla roli sieci Web oraz przekazywania certyfikatu SSL w celu zabezpieczenia aplikacji.

> [!NOTE]
> Procedury w tym zadaniu mają zastosowanie do usług Azure Cloud Services; dla usług App Service, zobacz [to](../app-service/app-service-web-tutorial-custom-ssl.md).
>

To zadanie używa w przypadku wdrożenia produkcyjnego. Informacje o używaniu wdrażania przejściowego znajduje się na końcu tego tematu.

Odczyt [to](cloud-services-how-to-create-deploy-portal.md) pierwsze, jeśli nie utworzono jeszcze usługi w chmurze.

## <a name="step-1-get-an-ssl-certificate"></a>Krok 1: Uzyskaj certyfikat protokołu SSL
Można skonfigurować protokołu SSL dla aplikacji, należy najpierw uzyskać certyfikat SSL, który został podpisany przez urząd certyfikacji (CA), zaufanych innych firm, który wystawia certyfikaty do tego celu. Jeśli nie masz już jeden, musisz go uzyskać od firmy, która sprzedaje certyfikaty SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL na platformie Azure:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzona na potrzeby wymiany kluczy, który można eksportować do pliku wymiany informacji osobistych (pfx).
* Nazwa podmiotu certyfikatu musi odpowiadać domena używana do uzyskania dostępu do usługi w chmurze. Nie można uzyskać certyfikatu SSL od urzędu certyfikacji (CA) dla domeny cloudapp.net. Należy uzyskać niestandardową nazwę domeny do użycia podczas dostęp do usługi. Gdy w przypadku żądania certyfikatu od urzędu certyfikacji nazwa podmiotu certyfikatu musi być zgodna z niestandardowej nazwy domeny umożliwia dostęp do Twoich aplikacji. Na przykład, jeśli nazwa domeny niestandardowej jest **contoso.com** może zażądać certyfikatu z urzędu certyfikacji dla ***. contoso.com** lub **www\.contoso.com**.
* Certyfikat należy użyć co najmniej szyfrowanie 2048-bitowe.

Do celów testowych możesz [tworzenie](cloud-services-certs-create.md) i używania certyfikatu z podpisem własnym. Certyfikat z podpisem własnym nie został uwierzytelniony przy użyciu urzędu certyfikacji i przy użyciu domeny cloudapp.net jako adres URL witryny sieci Web. Na przykład następujące zadanie używa certyfikatu z podpisem własnym jest nazwa pospolita (CN) użyta w certyfikacie **sslexample.cloudapp.net**.

Następnie musi zawierać informacje o certyfikacie w definicji usługi i pliki konfiguracji usługi.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Modyfikowanie plików definicji i konfiguracji usługi
Aplikacja musi być skonfigurowana do używania certyfikatu, a punkt końcowy HTTPS musi zostać dodany. W rezultacie definicji usługi i pliki konfiguracji usługi należy zaktualizować.

1. W środowisku deweloperskim, otwórz plik definicji usługi (CSDEF), Dodaj **certyfikaty** sekcji w ramach **WebRole** sekcji, a następnie podaj następujące informacje o certyfikacie (i Certyfikaty pośrednie):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   **Certyfikaty** sekcji zdefiniowano nazwy naszym certyfikatu, lokalizację i nazwę magazynu, w którym znajduje się.

   Uprawnienia (`permissionLevel` atrybutu) można ustawić na jeden z następujących wartości:

   | Wartość uprawnienia | Opis |
   | --- | --- |
   | limitedOrElevated |**(Ustawienie domyślne)**  Wszystkie procesy roli dostęp klucza prywatnego. |
   | z podwyższonym poziomem uprawnień |Tylko z podwyższonym poziomem uprawnień procesów dostęp klucza prywatnego. |

2. W pliku definicji usługi, dodać **InputEndpoint** elemencie **punktów końcowych** sekcji, aby włączyć protokół HTTPS:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. W pliku definicji usługi, dodać **powiązanie** elemencie **witryn** sekcji. Ten element dodaje powiązanie HTTPS, aby zmapować punkt końcowy do swojej witryny:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Ukończono wszystkie wymagane zmiany w pliku definicji usługi; jednak nadal konieczne dodanie informacji o certyfikacie do pliku konfiguracji usługi.
4. W pliku konfiguracji usługi (CSCFG) ServiceConfiguration.Cloud.cscfg, Dodaj **certyfikaty** wartość certyfikatu. Poniższy przykładowy kod zawiera szczegółowe informacje o **certyfikaty** sekcji, z wyjątkiem wartość odcisku palca.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(W tym przykładzie użyto **sha1** Algorytm odcisku palca. Określ wartość odpowiednią dla Algorytm odcisku palca certyfikatu).

Teraz, gdy definicja usługi i plików konfiguracyjnych usługi zostały zaktualizowane, pakietu wdrożenia pod kątem przekazywania na platformie Azure. Jeśli używasz **cspack**, nie używaj **/generateConfigurationFile** Flaga, jak który spowoduje zastąpienie informacji o certyfikacie, został wstawiony.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Przekaż certyfikat
Łączenie do witryny Azure portal i...

1. W **wszystkie zasoby** części portalu wybierz usługi w chmurze.

    ![Publikowanie usług cloud Services](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kliknij przycisk **certyfikaty**.

    ![Kliknij ikonę certyfikatów](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kliknij przycisk **przekazywanie** u góry obszaru certyfikatów.

    ![Kliknij element menu przekazywania](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Podaj **pliku**, **hasło**, następnie kliknij przycisk **przekazywanie** w dolnej części obszaru wprowadzania danych.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Nawiąż połączenie z wystąpieniem roli, przy użyciu protokołu HTTPS
Teraz, że wdrożenie jest uruchomiona na platformie Azure, możesz połączyć się do niej przy użyciu protokołu HTTPS.

1. Kliknij przycisk **adres URL witryny** otwarcie przeglądarki sieci web.

   ![Kliknij adres URL witryny](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. W przeglądarce sieci web należy zmodyfikować łącze do użycia **https** zamiast **http**, a następnie odwiedź stronę.

   > [!NOTE]
   > Jeśli używasz certyfikatu z podpisem własnym, po przejściu do punktu końcowego HTTPS, która jest skojarzona z certyfikatu z podpisem własnym, zobaczysz błąd certyfikatu w przeglądarce. Użycie certyfikatu podpisanego przez zaufany urząd certyfikacji eliminuje ten problem. w międzyczasie możesz zignorować ten błąd. (Inną możliwością jest można dodać certyfikatu z podpisem własnym do magazynu certyfikatów urzędu zaufanego certyfikatu przez użytkownika).
   >
   >

   ![Podgląd witryny](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Jeśli chcesz używać protokołu SSL dla wdrożenia przejściowego zamiast wdrożenia produkcyjnego, najpierw musisz określić adres URL używany do wdrażania przejściowego. Po wdrożeniu usługi w chmurze jest ustalany na adres URL do środowiska pomostowego **identyfikator wdrożenia** identyfikator GUID w następującym formacie: `https://deployment-id.cloudapp.net/`  
   >
   > Utwórz certyfikat z nazwa pospolita (CN) w taki sam adres URL na podstawie identyfikatora GUID (na przykład **328187776e774ceda8fc57609d404462.cloudapp.net**). Aby dodać certyfikat do swojej usługi w chmurze etapowe, użyj portalu. Następnie dodaj informacje o certyfikacie do plików CSDEF i CSCFG, przeprowadź ponowne pakowanie aplikacji i aktualizacji wdrożenia przygotowane do użycia nowego pakietu.
   >

## <a name="next-steps"></a>Kolejne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrożyć usługę w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Konfigurowanie [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Zarządzanie usługą chmurze](cloud-services-how-to-manage-portal.md).
