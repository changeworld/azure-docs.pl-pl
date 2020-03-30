---
title: Rozwiązywanie problemów z przekierowaniem do adresu URL usługi app service
titleSuffix: Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z przekierowaniem, gdy brama aplikacji platformy Azure jest używana z usługą Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 961ed17bcef19b445c2546a557725bb6bd8653cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293537"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Rozwiązywanie problemów z usługą App Service w bramie aplikacji

Dowiedz się, jak zdiagnozować i rozwiązać problemy, które mogą wystąpić, gdy usługa Azure App Service jest używana jako obiekt docelowy zaplecza z bramą aplikacji platformy Azure.

## <a name="overview"></a>Omówienie

W tym artykule dowiesz się, jak rozwiązać następujące problemy:

> [!div class="checklist"]
> * Adres URL usługi aplikacji jest widoczna w przeglądarce, gdy istnieje przekierowanie.
> * Domena plików cookie usługi aplikacji ARRAffinity jest ustawiona na nazwę hosta usługi aplikacji, example.azurewebsites.net, zamiast oryginalnego hosta.

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, można przekierować klienta do innego adresu URL niż określony przez aplikację zaplecza. Można to zrobić, gdy usługa aplikacji jest hostowana za bramą aplikacji i wymaga od klienta przekierowania do ścieżki względnej. Przykładem jest przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2. 

Gdy usługa aplikacji wysyła odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji jego odpowiedzi, jak w żądaniu, które otrzymuje z bramy aplikacji. Na przykład klient sprawia, że żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzić przez bramę aplikacji contoso.com/path2. Nie chcesz pomijać bramy aplikacji.

Ten problem może się zdarzyć z następujących głównych powodów:

- Przekierowanie zostało skonfigurowane w usłudze aplikacji. Przekierowanie może być tak proste, jak dodanie końcowego ukośnika do żądania.
- Masz uwierzytelnianie usługi Azure Active Directory, co powoduje przekierowanie.

Ponadto podczas korzystania z usług aplikacji za bramą aplikacji nazwa domeny skojarzona z bramą aplikacji (example.com) różni się od nazwy domeny usługi aplikacji (na przykład example.azurewebsites.net). Wartość domeny dla pliku cookie ARRAffinity ustawionego przez usługę aplikacji zawiera example.azurewebsites.net nazwę domeny, co nie jest pożądane. Oryginalna nazwa hosta, example.com, powinna być wartością nazwy domeny w pliku cookie.

## <a name="sample-configuration"></a>Przykładowa konfiguracja

- Odbiornik HTTP: podstawowy lub wielostrona
- Pula adresów zaplecza: usługa aplikacji
- Ustawienia HTTP: **Wybierz nazwa hosta z włączonego adresu wewnętrznej bazy danych**
- Sonda: **Wybierz nazwa hosta z** włączonych ustawień HTTP

## <a name="cause"></a>Przyczyna

Usługa app service jest usługą wielodostępną, więc używa nagłówka hosta w żądaniu do kierowania żądania do właściwego punktu końcowego. Domyślna nazwa domeny usługi App Services, *.azurewebsites.net (powiedzmy contoso.azurewebsites.net), różni się od nazwy domeny bramy aplikacji (np. contoso.com). 

Oryginalne żądanie od klienta ma nazwę domeny bramy aplikacji, contoso.com, jako nazwę hosta. Należy skonfigurować bramę aplikacji, aby zmienić nazwę hosta w oryginalnym żądaniu na nazwę hosta usługi aplikacji, gdy kieruje żądanie do zaplecza usługi aplikacji. Użyj przełącznika **Wybierz nazwa hosta z adresu wewnętrznej bazy danych** w konfiguracji ustawień HTTP bramy aplikacji. Użyj przełącznika **Wybierz nazwa hosta z ustawień HTTP wewnętrznej bazy danych** w konfiguracji sondy kondycji.



![Brama aplikacji zmienia nazwę hosta](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Gdy usługa aplikacji wykonuje przekierowanie, używa zastąpiona nazwa hosta contoso.azurewebsites.net w nagłówku lokalizacji zamiast oryginalnej nazwy hosta contoso.com, chyba że skonfigurowano inaczej. Sprawdź następujące przykładowe nagłówki żądań i odpowiedzi.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
W poprzednim przykładzie należy zauważyć, że nagłówek odpowiedzi ma kod stanu 301 dla przekierowania. Nagłówek lokalizacji ma nazwę hosta usługi aplikacji zamiast oryginalnej nazwy `www.contoso.com`hosta .

## <a name="solution-rewrite-the-location-header"></a>Rozwiązanie: Przepisz nagłówek lokalizacji

Ustaw nazwę hosta w nagłówku lokalizacji na nazwę domeny bramy aplikacji. Aby to zrobić, należy utworzyć [regułę ponownego zapisu](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) z warunkiem, który ocenia, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Należy również wykonać akcję, aby przepisać nagłówek lokalizacji, aby mieć nazwę hosta bramy aplikacji. Aby uzyskać więcej informacji, zobacz [instrukcje dotyczące przepisywania nagłówka lokalizacji](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Obsługa przepisywania nagłówka HTTP jest dostępna tylko dla [Standard_v2 i WAF_v2 jednostki SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) bramy aplikacji. Jeśli używasz jednostki SKU w wersji 1, zaleca się [migrację z wersji 1 do wersji 2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2). Chcesz użyć przepisać i inne [zaawansowane funkcje,](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) które są dostępne z jednostką SKU w wersji 2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Rozwiązanie alternatywne: używanie niestandardowej nazwy domeny

Jeśli używasz jednostki SKU w wersji 1, nie można przepisać nagłówka lokalizacji. Ta funkcja jest dostępna tylko dla jednostki SKU w wersji 2. Aby rozwiązać problem przekierowania, przekaż tę samą nazwę hosta, którą brama aplikacji otrzymuje do usługi aplikacji, zamiast zastępowania hosta.

Usługa aplikacji teraz wykonuje przekierowanie (jeśli istnieje) w tym samym oryginalnym nagłówku hosta, który wskazuje bramę aplikacji, a nie własną.

Musisz posiadać domenę niestandardową i wykonać ten proces:

- Zarejestruj domenę na niestandardowej liście domen usługi aplikacji. Musisz mieć CNAME w domenie niestandardowej, który wskazuje nazwę FQDN usługi aplikacji. Aby uzyskać więcej informacji, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

    ![Niestandardowa lista domen usługi aplikacji](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Usługa aplikacji jest gotowa zaakceptować `www.contoso.com`nazwę hosta . Zmień wpis CNAME w systemie DNS, aby na przykład skierować go `appgw.eastus.cloudapp.azure.com`z powrotem do nazwy FQDN bramy aplikacji.

- Upewnij się, `www.contoso.com` że domena jest rozpoznawana w sieci FQDN bramy aplikacji podczas wykonywania kwerendy DNS.

- Ustaw niestandardową sondę tak, aby **wyłączyła pozycję Wybierz nazwa hosta z ustawień HTTP wewnętrznej bazy danych**. W witrynie Azure portal wyczyść pole wyboru w ustawieniach sondy. W programie PowerShell nie należy używać przełącznika **-PickHostNameFromBackendHttpSettings** w poleceniu **Set-AzApplicationGatewayProbeConfig.** W polu nazwa hosta sondy wprowadź nazwę FQDN usługi aplikacji, example.azurewebsites.net. Żądania sondy wysyłane z bramy aplikacji przenoszą tę fqdn w nagłówku hosta.

  > [!NOTE]
  > W następnym kroku upewnij się, że niestandardowa sonda nie jest skojarzona z ustawieniami http zaplecza. W tym momencie w ustawieniach HTTP nadal jest włączony przełącznik **Wybierznanazesze z adresu wewnętrznej.**

- Ustaw ustawienia HTTP bramy aplikacji, aby wyłączyć **pozycję Wybierz nazwa hosta z adresu wewnętrznej bazy**danych . W witrynie Azure portal wyczyść to pole wyboru. W programie PowerShell nie należy używać przełącznika **-PickHostNameFromBackendAddress** w poleceniu **Set-AzApplicationGatewayBackendHttpSettings.**

- Skojarz sondę niestandardową z powrotem z ustawieniami HTTP zaplecza i sprawdź, czy zaplecze jest w dobrej kondycji.

- Brama aplikacji powinna teraz przesyłać `www.contoso.com`dalej tę samą nazwę hosta do usługi aplikacji. Przekierowanie odbywa się na tej samej nazwie hosta. Sprawdź następujące przykładowe nagłówki żądań i odpowiedzi.

Aby zaimplementować poprzednie kroki przy użyciu programu PowerShell dla istniejącej konfiguracji, należy użyć przykładowego skryptu programu PowerShell, który jest następujący. Zwróć uwagę, jak nie użyliśmy przełączników **-PickHostname** w konfiguracji ustawień sondy i HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Następne kroki

Jeśli poprzednie kroki nie rozwiązały problemu, otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
