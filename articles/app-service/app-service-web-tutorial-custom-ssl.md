---
title: Przekazywanie i powiązywanie certyfikatu SSL-Azure App Service | Microsoft Docs
description: Dowiedz się, jak powiązać niestandardowy certyfikat SSL z aplikacją internetową, zapleczem aplikacji mobilnej lub aplikacją interfejsu API w Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177044"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Samouczek: przekazywanie i powiązywanie certyfikatów SSL z Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web. W tym samouczku pokazano, jak zabezpieczyć domenę niestandardową w App Service przy użyciu certyfikatu zakupionego od zaufanego urzędu certyfikacji. Przedstawiono w nim również sposób przekazywania wszelkich prywatnych i publicznych certyfikatów potrzebnych aplikacji. Po zakończeniu będzie można uzyskać dostęp do aplikacji w punkcie końcowym protokołu HTTPS niestandardowej domeny DNS.

![Aplikacja internetowa z niestandardowym certyfikatem SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Uaktualnij warstwę cenową aplikacji
> * Zabezpieczanie domeny niestandardowej przy użyciu certyfikatu
> * Przekaż certyfikat prywatny
> * Przekaż certyfikat publiczny
> * Odnów certyfikaty
> * Wymuszanie protokołu HTTPS
> * Wymuś protokół TLS 1.1/1.2
> * Automatyzowanie zarządzania protokołem TLS przy użyciu skryptów

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek:

- [Tworzenie aplikacji App Service](/azure/app-service/)
- [Mapowanie niestandardowej nazwy DNS na aplikację App Service (w](app-service-web-tutorial-custom-domain.md) przypadku zabezpieczania domeny niestandardowej)
- Pozyskiwanie certyfikatu z zaufanego urzędu certyfikacji
- Klucz prywatny używany do podpisywania żądania certyfikatu (dla certyfikatów prywatnych)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Przygotuj certyfikat prywatny

Aby zabezpieczyć domenę, certyfikat musi spełniać wszystkie następujące wymagania:

* Skonfigurowane do uwierzytelniania serwera
* Podpisany przez zaufany urząd certyfikacji
* Eksportowany jako chroniony hasłem plik PFX
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

> [!TIP]
> Jeśli musisz uzyskać niestandardowy certyfikat SSL, możesz uzyskać jeden w Azure Portal bezpośrednio i zaimportować go do aplikacji. Postępuj zgodnie z [samouczkiem App Service Certificates](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **Certyfikaty kryptografii krzywej eliptycznej (ECC)** mogą współpracować z App Service, ale nie są objęte tym artykułem. Aby utworzyć certyfikaty ECC, należy skontaktować się z urzędem certyfikacji.

Po uzyskaniu certyfikatu od dostawcy certyfikatów wykonaj kroki opisane w tej sekcji, aby przygotować go do App Service.

### <a name="merge-intermediate-certificates"></a>Scalanie certyfikatów pośrednich

Jeśli urząd certyfikacji zapewnia wiele certyfikatów w łańcuchu certyfikatów, należy scalić certyfikaty w pożądanej kolejności.

W tym celu otwórz każdy certyfikat otrzymany w edytorze tekstu.

Utwórz plik dla scalonego certyfikatu o nazwie _mergedcertificate. CRT_. W edytorze tekstów Skopiuj zawartość każdego certyfikatu do tego pliku. Kolejność certyfikatów powinna być zgodna z kolejnością w łańcuchu certyfikatów, rozpoczynając od certyfikatu i kończąc z certyfikatem głównym. Wygląda podobnie do poniższego przykładu:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Eksportowanie certyfikatu do PFX

Wyeksportuj scalony certyfikat SSL przy użyciu klucza prywatnego, z którym zostało wygenerowane żądanie certyfikatu.

Jeśli wygenerowałeś żądanie certyfikatu za pomocą OpenSSL, plik klucza prywatnego został utworzony. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zastąp symbole zastępcze _&lt;private-Key-file >_ i _&lt;merged-certificate-File >_ ze ścieżkami do klucza prywatnego i scalonym plikiem certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po wyświetleniu monitu Zdefiniuj hasło eksportu. To hasło będzie używane podczas przekazywania certyfikatu SSL w celu App Service późniejszej.

Jeśli używasz usług IIS lub _CertReq. exe_ do wygenerowania żądania certyfikatu, Zainstaluj certyfikat na komputerze lokalnym, a następnie [Wyeksportuj certyfikat do pliku PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Teraz możesz przekazać certyfikat do App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpieczanie domeny niestandardowej

> [!TIP]
> Jeśli musisz uzyskać niestandardowy certyfikat SSL, możesz uzyskać jeden w Azure Portal bezpośrednio i powiązać go z aplikacją. Postępuj zgodnie z [samouczkiem App Service Certificates](web-sites-purchase-ssl-web-site.md).

Aby zabezpieczyć [domenę niestandardową](app-service-web-tutorial-custom-domain.md) za pomocą certyfikatu innej firmy, należy przekazać [przygotowany certyfikat prywatny](#prepare-a-private-certificate) , a następnie powiązać go z domeną niestandardową, ale App Service uprościć proces. Wykonaj następujące czynności:

Kliknij pozycję **domeny niestandardowe** na lewym pasku nawigacyjnym aplikacji, a następnie kliknij pozycję **Dodaj powiązanie** dla domeny, która ma zostać zabezpieczona. Jeśli nie widzisz elementu **Dodaj powiązanie** dla domeny, jest on już zabezpieczony i powinien mieć **bezpieczny** stan SSL.

![Dodawanie powiązania do domeny](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Kliknij przycisk **Przekaż certyfikat**.

W **pliku certyfikatu PFX**wybierz plik PFX. W polu **hasło certyfikatu**wpisz hasło, które zostało utworzone podczas EKSPORTOWANIA pliku PFX.

Kliknij przycisk **Przekaż**.

![Przekaż certyfikat dla domeny](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Poczekaj na przekazanie certyfikatu przez platformę Azure i uruchomienie okna dialogowego powiązania SSL.

W oknie dialogowym powiązania protokołu SSL wybierz przekazany certyfikat i typ protokołu SSL, a następnie kliknij przycisk **Dodaj powiązanie**.

> [!NOTE]
> Obsługiwane są następujące typy połączeń SSL:
>
> - **[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** — można dodać wiele powiązań SSL opartych na SNI. Ta opcja zezwala wielu certyfikatom SSL na zabezpieczanie wielu domen na tym samym adresie IP. Większość nowoczesnych przeglądarek (w tym Internet Explorer, Chrome, Firefox i Opera) obsługuje SNI (Znajdź bardziej kompleksową pomoc techniczną dotyczącą przeglądarki w [oznaczanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **Protokół SSL oparty na** protokole IP — można dodać tylko jedno wiązanie SSL oparte na adresie IP. Ta opcja zezwala na zabezpieczenie dedykowanego publicznego adresu IP tylko jednemu certyfikatowi SSL. Aby zabezpieczyć wiele domen, należy zabezpieczyć je wszystkie przy użyciu tego samego certyfikatu SSL. Jest to tradycyjna opcja dla powiązania SSL.

![Powiązywanie protokołu SSL z domeną](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Stan protokołu SSL domeny powinien teraz zostać zmieniony na **bezpieczny**.

![Domena zabezpieczona](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> **Bezpiecznym** stanem w **domenach niestandardowych** jest zabezpieczenie certyfikatu, ale App Service nie sprawdza, czy certyfikat ma podpis własny lub wygasł, na przykład może spowodować, że przeglądarki wyświetlają błąd lub ostrzeżenie.

## <a name="remap-a-record-for-ip-ssl"></a>Ponowne mapowanie rekordu dla Połączenie SSL z adresu IP

Jeśli nie korzystasz z protokołu SSL opartego na protokole IP w aplikacji, przejdź do [testowania protokołu HTTPS dla domeny niestandardowej](#test).

Domyślnie aplikacja używa udostępnionego publicznego adresu IP. W przypadku powiązania certyfikatu z protokołem SSL opartym na protokole IP App Service tworzy nowy, dedykowany adres IP dla aplikacji.

Jeśli rekord A został zmapowany do aplikacji, należy zaktualizować rejestr domeny za pomocą tego nowego, dedykowanego adresu IP.

Strona **domena niestandardowa** aplikacji jest aktualizowana przy użyciu nowego, dedykowanego adresu IP. [Skopiuj ten adres IP](app-service-web-tutorial-custom-domain.md#info), a następnie ponownie [zamapuj rekord A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na ten nowy adres IP.

<a name="test"></a>

## <a name="test-https"></a>Testuj protokół HTTPS

Wszystko, co jest teraz, ma na celu upewnienie się, że protokół HTTPS działa dla domeny niestandardowej. W różnych przeglądarkach przejdź do `https://<your.custom.domain>`, aby zobaczyć, że obsługuje ona aplikację.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Jeśli aplikacja zawiera błędy walidacji certyfikatu, prawdopodobnie używasz certyfikatu z podpisem własnym.
>
> Jeśli tak się nie dzieje, po wyeksportowaniu certyfikatu do pliku PFX mogą zostać pozostawione certyfikaty pośrednie.

## <a name="renew-certificates"></a>Odnów certyfikaty

Adres IP ruchu przychodzącego może ulec zmianie po usunięciu powiązania, nawet jeśli to powiązanie jest oparte na adresie IP. Jest to szczególnie ważne w przypadku odnowienia certyfikatu, który znajduje się już w powiązaniu opartym na adresie IP. Aby uniknąć zmiany adresu IP aplikacji, wykonaj następujące kroki:

1. Przekaż nowy certyfikat.
2. Powiąż nowy certyfikat z wybraną domeną niestandardową bez usuwania starej. Ta akcja zastępuje powiązanie, zamiast usuwać stare.
3. Usuń stary certyfikat. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS

Domyślnie każdy może nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP. Wszystkie żądania HTTP można przekierować do portu HTTPS.

Na stronie aplikacji w lewym okienku nawigacji wybierz pozycję **Ustawienia protokołu SSL**. Następnie w polu **tylko https**wybierz pozycję **włączone**.

![Wymuszanie protokołu HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Po zakończeniu operacji przejdź do dowolnego adresu URL protokołu HTTP, który wskazuje aplikację. Na przykład:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Wymuś wersje protokołu TLS

Aplikacja domyślnie zezwala na [Protokół tls](https://wikipedia.org/wiki/Transport_Layer_Security) 1,2, który jest zalecanym poziomem protokołu TLS według standardów branżowych, takich jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Aby wymusić różne wersje protokołu TLS, wykonaj następujące kroki:

Na stronie aplikacji w lewym okienku nawigacji wybierz pozycję **Ustawienia protokołu SSL**. Następnie w polu **wersja protokołu TLS**Wybierz minimalną wersję protokołu TLS. To ustawienie steruje tylko wywołaniami przychodzącymi. 

![Wymuś protokół TLS 1,1 lub 1,2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Po zakończeniu operacji aplikacja odrzuca wszystkie połączenia z niższymi wersjami protokołu TLS.

## <a name="automate-with-scripts"></a>Automatyzacja za pomocą skryptów

Możesz zautomatyzować powiązania SSL dla aplikacji ze skryptami przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Następujące polecenie przekazuje wyeksportowany plik PFX i pobiera odcisk palca.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Poniższe polecenie dodaje powiązanie SSL oparte na SNI, używając odcisku palca z poprzedniego polecenia.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Następujące polecenie wymusza, aby aplikacja korzystała z protokołu HTTPS.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

Następujące polecenie wymusza minimalną wersję protokołu TLS 1,2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie przekazuje wyeksportowany plik PFX i dodaje powiązanie SSL oparte na SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Używanie certyfikatów w kodzie

Jeśli aplikacja musi połączyć się z zasobami zdalnymi, a zasób zdalny wymaga uwierzytelniania przy użyciu certyfikatu, można przekazać certyfikaty publiczne lub prywatne do aplikacji. Nie musisz wiązać tych certyfikatów z żadną domeną niestandardową w aplikacji. Aby uzyskać więcej informacji, zobacz [Używanie certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="checklist"]
> * Uaktualnij warstwę cenową aplikacji
> * Powiązywanie certyfikatu niestandardowego z App Service
> * Odnów certyfikaty
> * Wymuszanie protokołu HTTPS
> * Wymuś protokół TLS 1.1/1.2
> * Automatyzowanie zarządzania protokołem TLS przy użyciu skryptów

Przejdź do następnego samouczka, aby dowiedzieć się, jak korzystać z usługi Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Dodawanie Content Delivery Network (CDN) do Azure App Service](../cdn/cdn-add-to-web-app.md)

Aby uzyskać więcej informacji, zobacz [Używanie certyfikatu protokołu SSL w kodzie aplikacji w Azure App Service](app-service-web-ssl-cert-load.md).
