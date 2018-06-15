---
title: Konfigurowanie protokołu SSL dla usługi w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak określić punkt końcowy HTTPS dla roli sieci web i jak można przekazać certyfikatu SSL do zabezpieczania aplikacji. Te przykłady, użyj portalu Azure.
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: 371ba204-48b6-41af-ab9f-ed1d64efe704
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.openlocfilehash: 0e053ad7f1033317948b6ef0856984b21e56e425
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2017
ms.locfileid: "24859780"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Konfigurowanie protokołu SSL dla aplikacji na platformie Azure

Szyfrowanie SSL (Secure Socket Layer) to najczęściej stosowana metoda zabezpieczania danych wysyłanych przez Internet. W ramach tego często wykonywanego zadania omówiono sposób określania punktu końcowego HTTPS dla roli sieci Web oraz przekazywania certyfikatu SSL w celu zabezpieczenia aplikacji.

> [!NOTE]
> Procedury w tym zadaniu mają zastosowanie do usług Azure Cloud Services; dla usług aplikacji, zobacz [to](../app-service/app-service-web-tutorial-custom-ssl.md).
>

To zadanie używa wdrożenia produkcyjnego. Informacji na temat używania tymczasowej wdrożenia znajduje się na końcu tego tematu.

Odczyt [to](cloud-services-how-to-create-deploy-portal.md) pierwszy, jeśli nie utworzono jeszcze usługi w chmurze.

## <a name="step-1-get-an-ssl-certificate"></a>Krok 1: Uzyskiwanie certyfikatu SSL.
Aby skonfigurować protokół SSL dla aplikacji, należy najpierw uzyskać certyfikat SSL, który został podpisany przez urząd certyfikacji, zaufanych innej firmy, który wystawia certyfikaty w tym celu. Jeśli nie masz już jeden, należy uzyskać go z firmą, która sprzedaje certyfikatów SSL.

Certyfikat musi spełniać następujące wymagania dotyczące certyfikatów SSL na platformie Azure:

* Certyfikat musi zawierać klucz prywatny.
* Certyfikat musi zostać utworzony dla wymiany kluczy, można eksportować do pliku wymiany informacji osobistych (pfx).
* Nazwa podmiotu certyfikatu musi odpowiadać domeny używane do uzyskania dostępu do usługi w chmurze. Nie można uzyskać certyfikatu SSL z urzędu certyfikacji (CA) dla domeny cloudapp.net. Należy uzyskać niestandardowej nazwy domeny do użycia podczas połączenia się z usługą. Podczas żądania certyfikatu z urzędu certyfikacji, nazwa podmiotu certyfikatu musi odpowiadać nazwie domeny niestandardowej, umożliwiające dostęp do aplikacji. Na przykład jeśli nazwą domeny niestandardowej jest **contoso.com** może zażądać certyfikatu od urzędu certyfikacji dla ***. contoso.com** lub **www.contoso.com**.
* Certyfikat należy użyć co najmniej 2048-bitowego szyfrowania.

Do celów testowych możesz [utworzyć](cloud-services-certs-create.md) i użycia certyfikatu z podpisem własnym. Certyfikatu z podpisem własnym nie jest uwierzytelniony za pośrednictwem urzędu certyfikacji i może używać domeny cloudapp.net jako adres URL witryny sieci Web. Na przykład następujące zadanie używa certyfikatu z podpisem własnym jest nazwa pospolita (CN) użyta w certyfikacie **sslexample.cloudapp.net**.

Następnie musi zawierać informacje o certyfikacie w definicji usługi oraz pliki konfiguracji usługi.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Krok 2: Modyfikowanie plików definicji i konfigurację usługi
Aplikacji musi być skonfigurowana do używania certyfikatu, a punkt końcowy HTTPS muszą zostać dodane. W związku z tym definicji usługi i pliki konfiguracyjne usługi muszą zostać zaktualizowane.

1. W środowisku projektowania, otwórz plik definicji usługi (CSDEF), Dodaj **certyfikaty** sekcji w **sieć Web** sekcji i obejmują następujące informacje dotyczące certyfikatu (i certyfikaty pośrednie):

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

   **Certyfikaty** sekcja definiuje nazwę naszych certyfikatu, jego lokalizację i nazwę magazynu, w którym znajduje się.

   Uprawnienia (`permisionLevel` atrybut) może być ustawione na jedną z następujących wartości:

   | Wartość uprawnienia | Opis |
   | --- | --- |
   | limitedOrElevated |**(Ustawienie domyślne)**  Wszystkie procesy roli można uzyskać dostępu do klucza prywatnego. |
   | z podwyższonym poziomem uprawnień |Tylko procesów z podwyższonym poziomem uprawnień można uzyskać dostępu do klucza prywatnego. |

2. W pliku definicji usługi, dodać **InputEndpoint** w elemencie **punkty końcowe** sekcji, aby włączyć protokół HTTPS:

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

3. W pliku definicji usługi, dodać **powiązanie** w elemencie **witryny** sekcji. Ten element dodaje powiązanie HTTPS do mapowania punktu końcowego do swojej witryny:

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

   Zostały ukończone wszystkie wymagane zmiany w pliku definicji usługi; jednak nadal konieczne jest dodanie informacji o certyfikacie w pliku konfiguracji usługi.
4. W pliku konfiguracji usługi (CSCFG), ServiceConfiguration.Cloud.cscfg, Dodaj **certyfikaty** wartości z tego certyfikatu. Poniższy przykładowy kod zawiera szczegółowe informacje o **certyfikaty** części, z wyjątkiem wartość odcisku palca.

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

(W tym przykładzie użyto **sha1** dla algorytmu odcisk palca. Określ wartość odpowiednią dla algorytmu odcisk palca certyfikatu).

Teraz, definicji usługi i pliki konfiguracji usługi zostały zaktualizowane, pakiet wdrożenia do przekazywania do platformy Azure. Jeśli używasz **cspack**, nie używaj **/generateConfigurationFile** Flaga, jako który zastąpi wstawione informacje o certyfikacie.

## <a name="step-3-upload-a-certificate"></a>Krok 3: Przekaż certyfikat
Połączyć się z portalem Azure i...

1. W **wszystkie zasoby** sekcji portalu, wybierz usługi w chmurze.

    ![Publikowanie usługi w chmurze](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Kliknij przycisk **certyfikaty**.

    ![Kliknij ikonę certyfikatów](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Kliknij przycisk **przekazać** u góry obszaru certyfikatów.

    ![Kliknij element menu przekazywania](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Podaj **pliku**, **hasło**, następnie kliknij przycisk **przekazać** w dolnej części obszaru wprowadzania danych.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Krok 4: Łączenie z wystąpienia roli przy użyciu protokołu HTTPS
Teraz, że wdrożenie jest uruchomiona na platformie Azure, można połączyć się przy użyciu protokołu HTTPS.

1. Kliknij przycisk **adres URL witryny** otwarcie przeglądarki sieci web.

   ![Kliknij adres URL witryny](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. W przeglądarce sieci web Zmień łącze do użycia **https** zamiast **http**, a następnie odwiedź stronę.

   > [!NOTE]
   > Jeśli używasz certyfikatu z podpisem własnym, po przejściu do punktu końcowego protokołu HTTPS, który został skojarzony z certyfikatu z podpisem własnym może zostać wyświetlony błąd certyfikatu w przeglądarce. Przy użyciu certyfikatu podpisanego przez zaufany urząd certyfikacji eliminuje ten problem. w międzyczasie możesz zignorować błąd. (Inną opcją jest można dodać certyfikatu z podpisem własnym do magazynu certyfikatów urzędu zaufanego certyfikatu użytkownika).
   >
   >

   ![Wersja zapoznawcza witryny](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Jeśli chcesz używać protokołu SSL dla tymczasowej wdrożenia zamiast wdrożenia produkcyjnego, musisz najpierw ustalić adresu URL używany do wdrażania przejściowego. Po wdrożeniu usługi w chmurze, adres URL do środowiska pomostowego jest określany przez **identyfikator wdrożenia** identyfikator GUID w następującym formacie:`https://deployment-id.cloudapp.net/`  
   >
   > Utwórz certyfikat z nazwa pospolita (CN) taki sam adres URL na podstawie identyfikatora GUID (na przykład **328187776e774ceda8fc57609d404462.cloudapp.net**). Aby dodać certyfikat do usługi w chmurze przemieszczanego, należy korzystać z portalu. Następnie dodaj informacje o certyfikacie do pliki CSDEF i CSCFG, ponownie utworzyć pakiet aplikacji i aktualizacji przemieszczanego wdrożenia do użycia nowego pakietu.
   >

## <a name="next-steps"></a>Następne kroki
* [Konfiguracja ogólna usługi w chmurze](cloud-services-how-to-configure-portal.md).
* Dowiedz się, jak [wdrażania usługi w chmurze](cloud-services-how-to-create-deploy-portal.md).
* Skonfiguruj [niestandardowej nazwy domeny](cloud-services-custom-domain-name-portal.md).
* [Usługi w chmurze zarządzanie](cloud-services-how-to-manage-portal.md).
