---
title: Generowanie certyfikatu z podpisem własnym przy obliczu niestandardowego głównego urzędu certyfikacji
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak wygenerować certyfikat z podpisem z podpisem z podpisem własnym usługi Azure Application Gateway przy zastosowaniu niestandardowego głównego urzędu certyfikacji
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311941"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generowanie certyfikatu z podpisem z podpisem z podpisem bramy aplikacji platformy Azure przy zastosowaniu niestandardowego głównego urzędu certyfikacji

Jednostka SKU bramy aplikacji w wersji 2 wprowadza użycie zaufanych certyfikatów głównych w celu umożliwienia serwerów wewnętrznej bazy danych. Spowoduje to usunięcie certyfikatów uwierzytelniania, które były wymagane w jednostce SKU w wersji 1. *Certyfikat główny* jest zakodowany x.509(Base-64. CER) formatować certyfikat główny z serwera certyfikatów wewnętrznej bazy danych. Identyfikuje główny urząd certyfikacji (CA), który wystawił certyfikat serwera, a certyfikat serwera jest następnie używany do komunikacji TLS/SSL.

Usługa Application Gateway domyślnie ufa certyfikatowi witryny sieci Web, jeśli jest podpisany przez dobrze znany urząd certyfikacji (na przykład GoDaddy lub DigiCert). W takim przypadku nie trzeba jawnie przekazywać certyfikatu głównego. Aby uzyskać więcej informacji, zobacz [Omówienie zakończenia protokołu TLS i zakończenia tls z bramą aplikacji](ssl-overview.md). Jeśli jednak masz środowisko deweloper/test i nie chcesz kupować zweryfikowanego certyfikatu podpisanego przez urząd certyfikacji, możesz utworzyć własny niestandardowy urząd certyfikacji i utworzyć z nim certyfikat z podpisem własnym. 

> [!NOTE]
> Certyfikaty z podpisem własnym nie są domyślnie zaufane i mogą być trudne do utrzymania. Ponadto mogą używać przestarzałych pakietów mieszania i szyfrowania, które mogą nie być silne. Aby uzyskać większe bezpieczeństwo, kup certyfikat podpisany przez dobrze znany urząd certyfikacji.

W tym artykule dowiesz się, jak:

- Tworzenie własnego niestandardowego urzędu certyfikacji
- Tworzenie certyfikatu z podpisem własnym podpisanego przez niestandardowy urząd certyfikacji
- Przekazywanie certyfikatu głównego z podpisem własnym do bramy aplikacji w celu uwierzytelnienia serwera zaplecza

## <a name="prerequisites"></a>Wymagania wstępne

- **[OpenSSL](https://www.openssl.org/) na komputerze z systemem Windows lub Linux** 

   Chociaż mogą istnieć inne narzędzia dostępne do zarządzania certyfikatami, w tym samouczku używa openssl. OpenSSL można znaleźć w pakiecie z wieloma dystrybucjami Linuksa, takimi jak Ubuntu.
- **Serwer www**

   Na przykład Apache, IIS lub NGINX do testowania certyfikatów.

- **Jednostka SKU bramy aplikacji w wersji 2**
   
  Jeśli nie masz istniejącej bramy aplikacji, zobacz [Szybki start: Bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway — Portal Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Tworzenie głównego certyfikatu urzędu certyfikacji

Utwórz główny certyfikat urzędu certyfikacji przy użyciu openssl.

### <a name="create-the-root-key"></a>Tworzenie klucza głównego

1. Zaloguj się na komputerze, na którym jest zainstalowany openssl i uruchom następujące polecenie. Spowoduje to utworzenie klucza chronionego hasłem.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. W wierszu polecenia wpisz silne hasło. Na przykład co najmniej dziewięć znaków, przy użyciu wielkich, wielkich liter, liczb i symboli.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Tworzenie certyfikatu głównego i samodzielne podpisywanie go

1. Użyj następujących poleceń, aby wygenerować csr i certyfikat.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Poprzednie polecenia tworzą certyfikat główny. Użyjesz tego do podpisania certyfikatu serwera.

1. Po wyświetleniu monitu wpisz hasło klucza głównego i informacje organizacyjne dla niestandardowego urzędu certyfikacji, takie jak Kraj, Stan, Organizacja Organizacyjna i w pełni kwalifikowana nazwa domeny (jest to domena wystawcy).

   ![tworzenie certyfikatu głównego](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Tworzenie certyfikatu serwera

Następnie utworzysz certyfikat serwera przy użyciu openssl.

### <a name="create-the-certificates-key"></a>Tworzenie klucza certyfikatu

Użyj następującego polecenia, aby wygenerować klucz certyfikatu serwera.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Tworzenie csr (żądanie podpisania certyfikatu)

Csr jest kluczem publicznym, który jest nadawanie urzędu certyfikacji podczas żądania certyfikatu. Urząd certyfikacji wystawia certyfikat dla tego konkretnego żądania.

> [!NOTE]
> CN (Nazwa pospolita) dla certyfikatu serwera musi się różnić od domeny wystawcy. Na przykład w tym przypadku CN dla `www.contoso.com` wystawcy jest i `www.fabrikam.com`CN certyfikatu serwera jest .


1. Użyj następującego polecenia, aby wygenerować csr:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Po wyświetleniu monitu wpisz hasło klucza głównego oraz informacje organizacyjne niestandardowego urzędu certyfikacji: kraj, stan, organizacja organizacyjna i w pełni kwalifikowaną nazwę domeny. Jest to domena strony internetowej i powinna być inna niż emitenta.

   ![Certyfikat serwera](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generowanie certyfikatu za pomocą csr i klucza i podpisywanie go za pomocą klucza głównego urzędu certyfikacji

1. Aby utworzyć certyfikat, użyj następującego polecenia:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Weryfikowanie nowo utworzonego certyfikatu

1. Użyj następującego polecenia, aby wydrukować dane wyjściowe pliku CRT i zweryfikować jego zawartość:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Weryfikacja certyfikatu](media/self-signed-certificates/verify-cert.png)

1. Sprawdź pliki w katalogu i upewnij się, że masz następujące pliki:

   - plik contoso.crt
   - plik contoso.key
   - fabrikam.crt
   - plik fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Konfigurowanie certyfikatu w ustawieniach TLS serwera sieci Web

Na serwerze sieci Web skonfiguruj tls przy użyciu plików fabrikam.crt i fabrikam.key. Jeśli serwer www nie może zabrać dwóch plików, można je połączyć z pojedynczym plikiem pem lub pfx za pomocą poleceń OpenSSL.

### <a name="iis"></a>IIS

Aby uzyskać instrukcje dotyczące importowania certyfikatu i przekazywania ich jako certyfikatu serwera w serwisach IIS, zobacz [JAK: Instalowanie zaimportowanych certyfikatów na serwerze sieci Web w systemie Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Aby zapoznać się z instrukcjami wiązania TLS, zobacz [Jak skonfigurować ssl w serwisie IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Następująca konfiguracja jest przykładowym [hostem wirtualnym skonfigurowanym dla SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) w Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Następująca konfiguracja jest przykładowym [blokiem serwera NGINX](https://nginx.org/docs/http/configuring_https_servers.html) z konfiguracją TLS:

![NGINX z TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Dostęp do serwera w celu weryfikacji konfiguracji

1. Dodaj certyfikat główny do zaufanego magazynu głównego komputera. Po wejściu na stronę internetową upewnij się, że cały łańcuch certyfikatów jest widoczny w przeglądarce.

   ![Zaufane certyfikaty główne](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Zakłada się, że system DNS został skonfigurowany do wskazywanie nazwy serwera sieci web (w tym przykładzie www.fabrikam.com) na adres IP serwera sieci Web. Jeśli nie, możesz edytować [plik hosts,](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) aby rozwiązać nazwę.
1. Przejdź do witryny sieci Web i kliknij ikonę kłódki w polu adresu przeglądarki, aby zweryfikować informacje o witrynie i certyfikacie.

## <a name="verify-the-configuration-with-openssl"></a>Sprawdź konfigurację za pomocą openssl

Możesz też użyć openssl, aby zweryfikować certyfikat.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Weryfikacja certyfikatu OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Przekazywanie certyfikatu głównego do ustawień HTTP bramy aplikacji

Aby przekazać certyfikat w bramie aplikacji, należy wyeksportować certyfikat crt do zakodowanego formatu .cer Base-64. Ponieważ plik .crt zawiera już klucz publiczny w formacie zakodowanym base-64, po prostu zmień nazwę rozszerzenia pliku z .crt na .cer. 

### <a name="azure-portal"></a>Azure Portal

Aby przekazać zaufany certyfikat główny z portalu, wybierz **ustawienia HTTP** i wybierz protokół **HTTPS.**

![Dodawanie certyfikatu za pomocą portalu](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Możesz też użyć interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell do przekazania certyfikatu głównego. Poniższy kod jest przykładem programu Azure PowerShell.

> [!NOTE]
> Poniższy przykład dodaje zaufany certyfikat główny do bramy aplikacji, tworzy nowe ustawienie HTTP i dodaje nową regułę, zakładając, że pula wewnętrznej bazy danych i odbiornik już istnieją.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Weryfikowanie kondycji wewnętrznej bazy danych bramy aplikacji

1. Kliknij widok **kondycji wewnętrznej bazy** danych bramy aplikacji, aby sprawdzić, czy sonda jest w dobrej kondycji.
1. Powinieneś zobaczyć, że stan jest **w dobrej kondycji** dla sondy HTTPS.

![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o SSL\TLS w bramie aplikacji, zobacz [Omówienie zakończenia protokołu TLS i zakończenia tls z bramą aplikacji](ssl-overview.md).

