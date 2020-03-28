---
title: Zabezpiecz niestandardowy system DNS za pomocą powiązania SSL
description: Zabezpiecz dostęp HTTPS do domeny niestandardowej, tworząc powiązanie TLS/SSL z certyfikatem. Zwiększ bezpieczeństwo witryny, wymuszając protokół HTTPS lub TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 263b4e76d334aab82f6bbac9aa268a50f4dd3784
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239707"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Zabezpieczanie niestandardowej nazwy DNS przy użyciu powiązania SSL w usłudze Azure App Service

W tym artykule pokazano, jak zabezpieczyć [domenę niestandardową](app-service-web-tutorial-custom-domain.md) w aplikacji usługi [App Service](https://docs.microsoft.com/azure/app-service/) lub [aplikacji funkcji,](https://docs.microsoft.com/azure/azure-functions/) tworząc powiązanie certyfikatu. Po zakończeniu możesz uzyskać dostęp do aplikacji usługi `https://` App Service w punkcie końcowym `https://www.contoso.com`dla niestandardowej nazwy DNS (na przykład ). 

![Aplikacja internetowa z niestandardowym certyfikatem protokołu SSL](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Zabezpieczanie [domeny niestandardowej](app-service-web-tutorial-custom-domain.md) za pomocą certyfikatu obejmuje dwa etapy:

- [Dodaj certyfikat prywatny do usługi App Service,](configure-ssl-certificate.md) który spełnia wszystkie [wymagania dotyczące powiązań SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Utwórz powiązanie SSL do odpowiedniej domeny niestandardowej. Ten drugi krok jest objęty tym artykułem.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uaktualnienie warstwy cenowej aplikacji
> * Zabezpieczanie domeny niestandardowej certyfikatem
> * Wymuszanie protokołu HTTPS
> * Wymuszanie protokołu TLS 1.1/1.2
> * Automatyzacja zarządzania protokołami TLS za pomocą skryptów

## <a name="prerequisites"></a>Wymagania wstępne

Aby postępować zgodnie z tym przewodnikiem:

- [Utwórz aplikację usługi App Service](/azure/app-service/)
- [Mapowanie nazwy domeny do aplikacji](app-service-web-tutorial-custom-domain.md) lub [kupowanie i konfigurowanie jej na platformie Azure](manage-custom-dns-buy-domain.md)
- [Dodawanie certyfikatu prywatnego do aplikacji](configure-ssl-certificate.md)

> [!NOTE]
> Najprostszym sposobem dodania certyfikatu prywatnego jest [utworzenie bezpłatnego certyfikatu zarządzanego usługi App Service](configure-ssl-certificate.md#create-a-free-certificate-preview) (Wersja zapoznawcza).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Zabezpieczanie domeny niestandardowej

Wykonaj następujące czynności:

W <a href="https://portal.azure.com" target="_blank">witrynie Azure portal</a>, z lewego menu wybierz pozycję **App Services** > **\<app-name>**.

Z lewej strony aplikacji uruchom okno dialogowe **Powiązanie TLS/SSL** według:

- Wybieranie **domen** > niestandardowych Dodaj**powiązanie**
- Wybieranie >  **ustawień TLS/SSL**Dodaj**powiązanie TLS/SSL**

![Dodawanie powiązania do domeny](./media/configure-ssl-bindings/secure-domain-launch.png)

W **obszarze Domena niestandardowa**wybierz domenę niestandardową, dla której chcesz dodać powiązanie.

Jeśli aplikacja ma już certyfikat dla wybranej domeny niestandardowej, przejdź bezpośrednio do [tworzenia powiązania.](#create-binding) W przeciwnym razie, kontynuuj.

### <a name="add-a-certificate-for-custom-domain"></a>Dodawanie certyfikatu dla domeny niestandardowej

Jeśli aplikacja nie ma certyfikatu dla wybranej domeny niestandardowej, masz dwie opcje:

- **Przekaż certyfikat PFX** — postępuj zgodnie z przepływem pracy w [trybie Przekazywanie certyfikatu prywatnego](configure-ssl-certificate.md#upload-a-private-certificate), a następnie wybierz tę opcję tutaj.
- **Importowanie certyfikatu usługi app service** — postępuj zgodnie z przepływem pracy [przy importowaniu certyfikatu usługi App Service](configure-ssl-certificate.md#import-an-app-service-certificate), a następnie wybierz tę opcję tutaj.

> [!NOTE]
> Można również [utworzyć bezpłatny certyfikat](configure-ssl-certificate.md#create-a-free-certificate-preview) (wersja zapoznawcza) lub [zaimportować certyfikat usługi Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), ale należy to zrobić oddzielnie, a następnie powrócić do okna dialogowego **Powiązanie TLS/SSL.**

### <a name="create-binding"></a>Tworzenie powiązania

Poniższa tabela ułatwia konfigurowanie powiązania SSL w oknie dialogowym **Wiązania TLS/SSL,** a następnie kliknij przycisk **Dodaj powiązanie**.

| Ustawienie | Opis |
|-|-|
| Domena niestandardowa | Nazwa domeny, dla aby dodać powiązanie SSL. |
| Odcisk palca certyfikatu prywatnego | Certyfikat do powiązania. |
| Typ TLS/SSL | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** — można dodać wiele powiązań SSL SNI. Ta opcja umożliwia zabezpieczenie wielu domen na tym samym adresie IP za pomocą wielu certyfikatów protokołu SSL. Większość nowoczesnych przeglądarek (w tym Internet Explorer, Chrome, Firefox i Opera) obsługuje SNI (aby uzyskać więcej informacji, zobacz [Wskazanie nazwy serwera](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**PROTOKÓŁ SSL IP** — można dodać tylko jedno powiązanie PROTOKOŁU SSL IP. Ta opcja umożliwia zabezpieczenie dedykowanego publicznego adresu IP za pomocą tylko jednego certyfikatu protokołu SSL. Po skonfigurowaniu powiązania wykonaj kroki opisane w [rejestrze Remap A dla protokołu IP SSL](#remap-a-record-for-ip-ssl).<br/>Protokół IP SSL jest obsługiwany tylko w warstwach Produkcja lub Izolowane. </li></ul> |

Po zakończeniu operacji stan SSL domeny niestandardowej zostanie zmieniony na **Bezpieczny.**

![Wiązanie SSL zakończyło się sukcesem](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Stan **Bezpieczny** w **domenach niestandardowych** oznacza, że jest zabezpieczony certyfikatem, ale usługa App Service nie sprawdza, czy certyfikat jest podpisany samodzielnie lub wygasł, na przykład, co może również spowodować, że przeglądarki będą wyświetlać błąd lub ostrzeżenie.

## <a name="remap-a-record-for-ip-ssl"></a>Ponowne mapowanie rekordu A dla połączenia SSL z adresu IP

Jeśli nie używasz protokołu IP SSL w aplikacji, przejdź do [testuj protokół HTTPS dla domeny niestandardowej](#test-https).

Domyślnie aplikacja używa udostępnionego publicznego adresu IP. Po związaniu certyfikatu z protokołu IP SSL usługa App Service tworzy nowy, dedykowany adres IP dla aplikacji.

Jeśli rekord A został zamapowany do aplikacji, zaktualizuj rejestr domeny za pomocą tego nowego, dedykowanego adresu IP.

Strona **Domena niestandardowa** Twojej aplikacji zostanie zaktualizowana za pomocą nowego, dedykowanego adresu IP. [Skopiuj ten adres IP](app-service-web-tutorial-custom-domain.md#info), a następnie [ponownie mapuj rekord A](app-service-web-tutorial-custom-domain.md#map-an-a-record) na ten nowy adres IP.

## <a name="test-https"></a>Testowanie protokołu HTTPS

W różnych przeglądarkach przejdź `https://<your.custom.domain>` do, aby sprawdzić, czy obsługuje ona aplikację.

![Nawigacja w portalu do aplikacji platformy Azure](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Kod aplikacji można sprawdzić protokół za pomocą nagłówka "x-appservice-proto". Nagłówek będzie miał wartość `http` `https`lub . 

> [!NOTE]
> Jeśli Twoja aplikacja wyświetla błędy walidacji certyfikatu, prawdopodobnie używasz certyfikatu z podpisem własnym.
>
> Jeśli tak nie jest, certyfikaty pośrednie mogły zostać pominięte podczas eksportowania certyfikatu do pliku PFX.

## <a name="prevent-ip-changes"></a>Zapobieganie zmianom adresu IP

Twój przychodzący adres IP może ulec zmianie po usunięciu powiązania, nawet jeśli to powiązanie jest ip ssl. Jest to szczególnie ważne podczas odnawiania certyfikatu, który jest już w powiązaniu protokołu SSL IP. Aby uniknąć zmian w adresie IP aplikacji, wykonaj kolejno następujące kroki:

1. Przekaż nowy certyfikat.
2. Powiąż nowy certyfikat z wybraną domeną niestandardową bez usuwania starego certyfikatu. Ta akcja zastępuje powiązanie zamiast usuwania go.
3. Usuń stary certyfikat. 

## <a name="enforce-https"></a>Wymuszanie protokołu HTTPS

Domyślnie każda osoba nadal może uzyskać dostęp do Twojej aplikacji przy użyciu protokołu HTTP. Możesz przekierować wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w pozycji **Tylko HTTPS** wybierz opcję **Włączone**.

![Wymuszanie protokołu HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Po zakończeniu operacji przejdź do dowolnego adresu URL protokołu HTTP, który wskazuje Twoją aplikację. Przykład:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Wymuszanie wersji protokołu TLS

Aplikacja domyślnie umożliwia korzystanie z protokołu [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2, który jest zalecanym poziomem protokołu TLS przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Aby wymusić inne wersje protokołu TLS, wykonaj następujące kroki:

Na stronie aplikacji w obszarze nawigacji po lewej stronie wybierz pozycję **Ustawienia protokołu SSL**. Następnie w obszarze **wersji protokołu TLS** wybierz minimalną wersję protokołu TLS do użycia. To ustawienie kontroluje tylko wywołania przychodzące. 

![Wymuszanie protokołu TLS 1.1 lub 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Po ukończeniu operacji aplikacja odrzuca wszystkie połączenia z niższymi wersjami protokołu TLS.

## <a name="handle-ssl-termination"></a>Obsługa zakończenia SSL

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

Przewodniki konfiguracji specyficzne dla języka, takie jak przewodnik [konfiguracji Linux Node.js,](containers/configure-language-nodejs.md#detect-https-session) pokazuje, jak wykryć sesję HTTPS w kodzie aplikacji.

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Więcej zasobów

* [Używanie certyfikatu SSL w kodzie aplikacji](configure-ssl-certificate-in-code.md)
* [Często zadawane pytania: Certyfikaty usługi aplikacji](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
