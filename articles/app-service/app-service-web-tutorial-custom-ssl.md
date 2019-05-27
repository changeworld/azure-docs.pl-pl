---
title: Wiązanie istniejącego niestandardowego certyfikatu protokołu SSL — Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 0a5b8bdbcd5a05574d824e3f57cfc23967278e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138682"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>Samouczek: Wiązanie istniejącego niestandardowego certyfikatu SSL z usługą Azure App Service

Usługa Azure App Service oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten samouczek pokazuje, jak powiązać niestandardowy certyfikat protokołu SSL zakupiony od zaufanego urzędu certyfikacji z usługą [Azure App Service](overview.md). Po zakończeniu będzie można uzyskać dostęp do aplikacji w punkcie końcowym protokołu HTTPS niestandardowej domeny DNS.

![Aplikacja internetowa z niestandardowym certyfikatem protokołu SSL](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Wiązanie niestandardowego certyfikatu z usługą App Service
> * Odnawianie certyfikatów
> * Wymuszaj HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

> [!NOTE]
> Jeśli potrzebujesz niestandardowego certyfikatu protokołu SSL, możesz pobrać go bezpośrednio w witrynie Azure Portal i powiązać ze swoją aplikacją. Postępuj zgodnie z [samouczkiem Certyfikaty usługi App Service](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Zamapuj niestandardową nazwę DNS na swoją aplikację usługi App Service](app-service-web-tutorial-custom-domain.md)
- Uzyskaj certyfikat protokołu SSL z zaufanego urzędu certyfikacji
- Zachowaj klucz prywatny użyty do podpisania żądania certyfikatu protokołu SSL

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Wymagania dotyczące certyfikatu protokołu SSL

Aby używać certyfikatu w usłudze App Service, musi on spełniać wszystkie następujące wymagania:

* Podpisany przez zaufany urząd certyfikacji
* Wyeksportowany jako chroniony hasłem plik PFX
* Zawiera klucz prywatny o długości co najmniej 2048 bitów
* Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

> [!NOTE]
> **Certyfikaty kryptografii opartej na krzywej eliptycznej (ECC, Elliptic Curve Cryptography)** mogą współpracować z usługą App Service, ale nie są uwzględnione w tym artykule. Skontaktuj się ze swoim urzędem certyfikacji, aby uzyskać informacje o dokładnych krokach, które należy wykonać w celu utworzenia certyfikatów ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Wiązanie certyfikatu protokołu SSL

Wszystko jest gotowe do przekazania certyfikatu protokołu SSL do aplikacji.

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

### <a name="upload-your-ssl-certificate"></a>Przekazywanie certyfikatu protokołu SSL

Aby przekazać certyfikat protokołu SSL, kliknij pozycję **Ustawienia protokołu SSL** w lewym obszarze nawigacyjnym aplikacji.

Kliknij pozycję **Przekaż certyfikat**. 

W pozycji **Plik PFX certyfikatu** wybierz swój plik PFX. W polu **Hasło certyfikatu** wpisz hasło, które zostało utworzone podczas eksportowania pliku PFX.

Kliknij pozycję **Przekaż**.

![Przekaż certyfikat](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Po zakończeniu przekazywania certyfikatu przez usługę App Service zostanie on wyświetlony na stronie **Ustawienia protokołu SSL**.

![Przekazano certyfikat](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Wiązanie certyfikatu protokołu SSL

W sekcji **Powiązania SSL** kliknij pozycję **Dodaj powiązanie**.

Na stronie **Dodawanie powiązania protokołu SSL** użyj list rozwijanych, aby wybrać nazwę domeny do zabezpieczenia i certyfikat do używania.

> [!NOTE]
> Jeśli certyfikat został przekazany, ale nie widzisz nazw domen na liście rozwijanej **Nazwa hosta**, spróbuj odświeżyć stronę przeglądarki.
>
>

W obszarze **Typ protokołu SSL** wybierz, czy ma być używane **[Oznaczanie nazwy serwera (SNI, Server Name Indication)](https://en.wikipedia.org/wiki/Server_Name_Indication)**, czy też protokół SSL oparty na protokole IP.

- **SNI SSL** — można dodać wiele powiązań rozszerzenia SNI SSL. Ta opcja umożliwia zabezpieczenie wielu domen na tym samym adresie IP za pomocą wielu certyfikatów protokołu SSL. Większość nowoczesnych przeglądarek (w tym programy Internet Explorer, Chrome, Firefox i Opera) obsługuje funkcję SNI. Bardziej szczegółowe informacje dotyczące obsługi przeglądarek możesz znaleźć w artykule [Server Name Indication (Oznaczanie nazwy serwera)](https://wikipedia.org/wiki/Server_Name_Indication).
- **Połączenie IP SSL** — można dodać tylko jedno powiązanie SSL oparte na protokole IP. Ta opcja umożliwia zabezpieczenie dedykowanego publicznego adresu IP za pomocą tylko jednego certyfikatu protokołu SSL. Aby zabezpieczyć wiele domen, musisz zabezpieczyć je wszystkie przy użyciu tego samego certyfikatu protokołu SSL. Jest to tradycyjna opcja dla powiązania protokołu SSL.

Kliknij przycisk **Dodaj powiązanie**.

![Wiązanie certyfikatu protokołu SSL](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Gdy usługa App Service zakończy przekazywanie Twojego certyfikatu, zostanie on wyświetlony w sekcjach **Powiązania protokołu SSL**.

![Wiązanie certyfikatu z aplikacją internetową](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

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

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Odnawianie certyfikatów

Adres IP dla ruchu przychodzącego może ulec zmianie po usunięciu powiązania, nawet jeśli to powiązanie jest oparte na adresie IP. Jest to szczególnie ważne podczas odnawiania certyfikatu, który już znajduje się w powiązaniu opartym na adresie IP. Aby uniknąć zmian w adresie IP aplikacji, wykonaj kolejno następujące kroki:

1. Przekaż nowy certyfikat.
2. Powiąż nowy certyfikat z wybraną domeną niestandardową bez usuwania starego certyfikatu. Ta akcja zastępuje powiązanie zamiast usuwania go.
3. Usuń stary certyfikat. 

## <a name="enforce-https"></a>Wymuszaj HTTPS

Domyślnie każda osoba nadal może uzyskać dostęp do Twojej aplikacji przy użyciu protokołu HTTP. Możesz przekierować wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w pozycji **Tylko HTTPS** wybierz opcję **Włączone**.

![Wymuszaj HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

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

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Następujące polecenie dodaje powiązanie SNI SSL, używając odcisku palca z poprzedniego polecenia.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Następujące polecenie wymusza minimalną wersję 1.2 protokołu TLS.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
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
## <a name="public-certificates-optional"></a>Certyfikaty publiczne (opcjonalnie)
Jeśli aplikacja wymaga dostępu do zasobów zdalnych jako klient, a zasób zdalny wymaga uwierzytelniania certyfikatów, możesz przekazać do aplikacji [certyfikaty publiczne](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/). Certyfikaty publiczne nie są wymagane dla powiązań SSL aplikacji.

Aby uzyskać więcej szczegółów na temat ładowania i używania certyfikatu publicznego w aplikacji, zobacz [Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service](app-service-web-ssl-cert-load.md). Certyfikatów publicznych możesz też używać z aplikacjami w środowiskach App Service Environment. Jeśli potrzebujesz przechować certyfikat w magazynie certyfikatów, należy skorzystać z aplikacji w środowisku App Service Environment. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować certyfikaty publiczne dla aplikacji usługi App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Przekaż certyfikat publiczny](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Wiązanie niestandardowego certyfikatu z usługą App Service
> * Odnawianie certyfikatów
> * Wymuszaj HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

Przejdź do następnego samouczka, aby dowiedzieć się, jak używać usługi Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Dodawanie usługi Content Delivery Network (CDN) do usługi Azure App Service](../cdn/cdn-add-to-web-app.md)

Aby uzyskać więcej informacji, zobacz [Use an SSL certificate in your application code in Azure App Service (Używanie certyfikatu protokołu SSL w kodzie aplikacji w usłudze Azure App Service)](app-service-web-ssl-cert-load.md).
