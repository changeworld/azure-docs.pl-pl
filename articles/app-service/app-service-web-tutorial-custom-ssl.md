---
title: Przekazany i powiązany certyfikat SSL — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak powiązać niestandardowy certyfikat protokołu SSL ze swoją aplikacją internetową, zapleczem aplikacji mobilnej lub aplikacją interfejsu API w usłudze Azure App Service.
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
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e0ee1e7c652ddf4126fc9658bf17d3e919d7a5c8
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475375"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Samouczek: Przekazywanie i utworzenia powiązania certyfikatów SSL w usłudze Azure App Service

Usługa [Azure App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. W tym samouczku dowiesz się, jak zabezpieczyć domeny niestandardowej w usłudze App Service przy użyciu certyfikatu zakupionego od zaufanego urzędu certyfikacji. On również dowiesz się, jak przekazywać żadnych prywatnych i publicznych certyfikatów wymagań aplikacji. Po zakończeniu będzie można uzyskać dostęp do aplikacji w punkcie końcowym protokołu HTTPS niestandardowej domeny DNS.

![Aplikacja internetowa z niestandardowym certyfikatem protokołu SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Zabezpieczanie domenę niestandardową przy użyciu certyfikatu
> * Przekaż certyfikat prywatny
> * Przekazywanie certyfikatu publicznego
> * Odnawianie certyfikatów
> * Wymuszanie protokołu HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Mapowanie niestandardowej nazwy DNS na aplikację usługi App Service](app-service-web-tutorial-custom-domain.md) (jeśli jest to zabezpieczenie domeny niestandardowej)
- Uzyskiwanie certyfikatu z zaufanego urzędu certyfikacji
- Klucz prywatny, użytego do podpisania żądania certyfikatu (Aby uzyskać certyfikaty prywatne)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Przygotowanie certyfikatu prywatnego

Aby zabezpieczyć domenę, certyfikat musi spełniać następujące wymagania:

* Skonfigurowany na potrzeby uwierzytelniania serwera
* Podpisany przez zaufany urząd certyfikacji
* Wyeksportowany jako chroniony hasłem plik PFX
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

> [!TIP]
> Jeśli potrzebujesz uzyskać niestandardowy certyfikat protokołu SSL, można kupić w witrynie Azure portal bezpośrednio i zaimportować go do swojej aplikacji. Postępuj zgodnie z [samouczkiem Certyfikaty usługi App Service](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **Certyfikaty kryptografii opartej na krzywej eliptycznej (ECC, Elliptic Curve Cryptography)** mogą współpracować z usługą App Service, ale nie są uwzględnione w tym artykule. Skontaktuj się ze swoim urzędem certyfikacji, aby uzyskać informacje o dokładnych krokach, które należy wykonać w celu utworzenia certyfikatów ECC.

Po uzyskaniu certyfikatu za pośrednictwem swojego usługodawcy certyfikat, wykonaj kroki opisane w tej sekcji, aby przygotować go do usługi App Service.

### <a name="merge-intermediate-certificates"></a>Scalanie certyfikatów pośrednich

Jeśli Twój urząd certyfikacji dał Ci wiele certyfikatów w łańcuchu certyfikatów, musisz kolejno scalić certyfikaty.

Aby to zrobić, otwórz każdy otrzymany certyfikat w edytorze tekstów.

Utwórz plik scalonego certyfikatu o nazwie _mergedcertificate.crt_. W edytorze tekstów skopiuj zawartość każdego certyfikatu do tego pliku. Kolejność certyfikatów powinna być zgodna z kolejnością w łańcuchu certyfikatów, poczynając od Twojego certyfikatu i kończąc na certyfikacie głównym. Wygląda to następująco:

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

### <a name="export-certificate-to-pfx"></a>Eksportowanie certyfikatu do pliku PFX

Wyeksportuj scalony certyfikat protokołu SSL z kluczem prywatnym, przy użyciu którego zostało wygenerowane żądanie certyfikatu.

Jeśli żądanie certyfikatu zostało wygenerowane przy użyciu biblioteki OpenSSL, został utworzony plik klucza prywatnego. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie. Zamień symbole zastępcze _&lt;private-key-file>_ i _&lt;merged-certificate-file>_ ścieżkami do Twojego klucza prywatnego i pliku scalonego certyfikatu.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Po wyświetleniu monitu określ hasło eksportu. To hasło będzie później używane podczas przekazywania certyfikatu protokołu SSL do usługi App Service.

Jeśli używasz usług IIS lub programu _Certreq.exe_ do wygenerowania swojego żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym, a następnie [wyeksportuj certyfikat do pliku PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Wszystko jest teraz gotowy Przekaż certyfikat do usługi App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpieczanie domenę niestandardową

> [!TIP]
> Jeśli potrzebujesz niestandardowego certyfikatu protokołu SSL, możesz pobrać go bezpośrednio w witrynie Azure Portal i powiązać ze swoją aplikacją. Postępuj zgodnie z [samouczkiem Certyfikaty usługi App Service](web-sites-purchase-ssl-web-site.md).

Aby zabezpieczyć [domeny niestandardowej](app-service-web-tutorial-custom-domain.md) certyfikatem innej firmy, możesz przekazać [przygotowane certyfikatu prywatnego](#prepare-a-private-certificate) i następnie powiązać go z domeny niestandardowej, ale usługi App Service upraszcza proces. Wykonaj następujące czynności:

Kliknij przycisk **domen niestandardowych** w lewym obszarze nawigacji aplikacji, następnie kliknij przycisk **Dodaj powiązanie** w domenie, które chcesz zabezpieczyć. Jeśli nie widzisz **Dodaj powiązanie** dla domeny, następnie jest już bezpieczne i powinny mieć **bezpiecznego** stan SSL.

![Dodaj powiązanie do domeny](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Kliknij pozycję **Przekaż certyfikat**.

W pozycji **Plik PFX certyfikatu** wybierz swój plik PFX. W polu **Hasło certyfikatu** wpisz hasło, które zostało utworzone podczas eksportowania pliku PFX.

Kliknij pozycję **Przekaż**.

![Przekaż certyfikat dla domeny](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Poczekaj, aż platformy Azure, aby przekazać certyfikat i Uruchom okno dialogowe powiązania SSL.

W oknie dialogowym powiązania SSL wybierz certyfikat przekazany i typ protokołu SSL, a następnie kliknij przycisk **Dodawanie powiązania**.

> [!NOTE]
> Obsługiwane są następujące typy protokołu SSL:
>
> - **[Połączenie SSL oparte na SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)**  -powiązania SSL oparte na SNI wiele, mogą zostać dodane. Ta opcja umożliwia zabezpieczenie wielu domen na tym samym adresie IP za pomocą wielu certyfikatów protokołu SSL. Większość nowoczesnych przeglądarek (w tym programy Internet Explorer, Chrome, Firefox i Opera) obsługuje funkcję SNI. Bardziej szczegółowe informacje dotyczące obsługi przeglądarek możesz znaleźć w artykule [Server Name Indication (Oznaczanie nazwy serwera)](https://wikipedia.org/wiki/Server_Name_Indication).
> - **Połączenie IP SSL** — można dodać tylko jedno powiązanie SSL oparte na protokole IP. Ta opcja umożliwia zabezpieczenie dedykowanego publicznego adresu IP za pomocą tylko jednego certyfikatu protokołu SSL. Aby zabezpieczyć wiele domen, musisz zabezpieczyć je wszystkie przy użyciu tego samego certyfikatu protokołu SSL. Jest to tradycyjna opcja dla powiązania protokołu SSL.

![Powiązanie SSL do domeny](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Teraz należy je zmienić stan SSL domeny **bezpiecznego**.

![Domeny chronione](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> A **bezpiecznego** stanu w **domen niestandardowych** oznacza, że jest zabezpieczony za pomocą certyfikatu, ale usługi App Service nie zostanie zaewidencjonowane, jeśli certyfikat z podpisem własnym lub wygasła, na przykład, które mogą również spowodować, że w przeglądarkach Pokaż błąd lub ostrzeżenie.

## <a name="remap-a-record-for-ip-ssl"></a>Ponowne mapowanie rekordu A dla połączenia SSL z adresu IP

Jeśli w swojej aplikacji nie używasz protokołu SSL opartego na protokole IP, przejdź do sekcji [Testowanie protokołu HTTPS dla domeny niestandardowej](#test).

Domyślnie aplikacja używa udostępnionego publicznego adresu IP. Gdy powiążesz certyfikat z protokołem SSL opartym na protokole IP, usługa App Service utworzy nowy, dedykowany adres IP dla Twojej aplikacji.

Jeśli rekord A jest mapowany na aplikację, zaktualizuj rejestr domeny przy użyciu tego nowego, dedykowanego adresu IP.

Strona **Domena niestandardowa** Twojej aplikacji zostanie zaktualizowana za pomocą nowego, dedykowanego adresu IP. [Skopiuj ten adres IP](app-service-web-tutorial-custom-domain.md#info), a następnie [ponownie mapuj rekord A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na ten nowy adres IP.

<a name="test"></a>

## <a name="test-https"></a>Testowanie protokołu HTTPS

Teraz pozostało tylko upewnienie się, że protokół HTTPS działa dla domeny niestandardowej. W różnych przeglądarkach przejdź na adres `https://<your.custom.domain>`, aby sprawdzić, czy Twoja aplikacja jest udostępniana.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Jeśli Twoja aplikacja wyświetla błędy walidacji certyfikatu, prawdopodobnie używasz certyfikatu z podpisem własnym.
>
> Jeśli tak nie jest, certyfikaty pośrednie mogły zostać pominięte podczas eksportowania certyfikatu do pliku PFX.

## <a name="renew-certificates"></a>Odnawianie certyfikatów

Adres IP dla ruchu przychodzącego może ulec zmianie po usunięciu powiązania, nawet jeśli to powiązanie jest oparte na adresie IP. Jest to szczególnie ważne podczas odnawiania certyfikatu, który już znajduje się w powiązaniu opartym na adresie IP. Aby uniknąć zmian w adresie IP aplikacji, wykonaj kolejno następujące kroki:

1. Przekaż nowy certyfikat.
2. Powiąż nowy certyfikat z wybraną domeną niestandardową bez usuwania starego certyfikatu. Ta akcja zastępuje powiązanie zamiast usuwania go.
3. Usuń stary certyfikat. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS

Domyślnie każda osoba nadal może uzyskać dostęp do Twojej aplikacji przy użyciu protokołu HTTP. Możesz przekierować wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w pozycji **Tylko HTTPS** wybierz opcję **Włączone**.

![Wymuszanie protokołu HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Po zakończeniu operacji przejdź do dowolnego adresu URL protokołu HTTP, który wskazuje Twoją aplikację. Na przykład:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Wymuszanie wersji protokołu TLS

Aplikacja domyślnie umożliwia korzystanie z protokołu [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, który jest zalecanym poziomem protokołu TLS przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Aby wymusić inne wersje protokołu TLS, wykonaj następujące kroki:

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w obszarze **wersji protokołu TLS** wybierz minimalną wersję protokołu TLS do użycia. To ustawienie kontroluje tylko wywołania przychodzące. 

![Wymuszanie protokołu TLS 1.1 lub 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Po ukończeniu operacji aplikacja odrzuca wszystkie połączenia z niższymi wersjami protokołu TLS.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Tworzenie powiązań protokołu SSL dla Twojej aplikacji możesz zautomatyzować za pomocą skryptów, korzystając z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).

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

Następujące polecenie dodaje powiązanie SNI SSL, używając odcisku palca z poprzedniego polecenia.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Następujące polecenie wymusza minimalną wersję 1.2 protokołu TLS.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Następujące polecenie przekazuje wyeksportowany plik PFX i dodaje powiązanie SNI SSL.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Użyj certyfikatów w kodzie

Jeśli Twoja aplikacja wymaga nawiązania połączenia z zasobami zdalnymi, a zasób zdalny wymaga uwierzytelniania certyfikatu, możesz przekazać certyfikaty publiczne lub prywatne, do swojej aplikacji. Nie potrzebujesz powiązać te certyfikaty do dowolnej domeny niestandardowej w swojej aplikacji. Aby uzyskać więcej informacji, zobacz [Use an SSL certificate in your application code in Azure App Service (Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service)](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Wiązanie niestandardowego certyfikatu z usługą App Service
> * Odnawianie certyfikatów
> * Wymuszanie protokołu HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

Przejdź do następnego samouczka, aby dowiedzieć się, jak używać usługi Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Dodawanie usługi Content Delivery Network (CDN) do usługi Azure App Service](../cdn/cdn-add-to-web-app.md)

Aby uzyskać więcej informacji, zobacz [Use an SSL certificate in your application code in Azure App Service (Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service)](app-service-web-ssl-cert-load.md).
