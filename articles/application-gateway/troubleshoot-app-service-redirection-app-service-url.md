---
title: Rozwiązywanie problemów z usługą Azure Application Gateway przy użyciu App Service — przekierowanie do adresu URL App Service
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z przekierowaniami, gdy usługa Azure Application Gateway jest używana z usługą Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347883"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Rozwiązywanie problemów z App Service w programie Application Gateway

Dowiedz się, jak diagnozować i rozwiązywać problemy występujące, gdy serwer aplikacji jest używany jako miejsce docelowe zaplecza z Application Gateway

## <a name="overview"></a>Przegląd

W tym artykule opisano sposób rozwiązywania następujących problemów:

> [!div class="checklist"]
> * Adres URL App Service wyświetlany w przeglądarce w przypadku przekierowania
> * Domena plików cookie ARRAffinity App Service ustawiona na App Service hostname (example.azurewebsites.net) zamiast oryginalnego hosta

Gdy aplikacja zaplecza wysyła odpowiedź przekierowania, możesz chcieć przekierować klienta na inny adres URL niż określony przez aplikację zaplecza. Można na przykład wykonać tę czynność, gdy usługa App Service jest hostowana za bramą aplikacji i wymaga, aby klient przetworzył przekierowanie do swojej ścieżki względnej. (Na przykład przekierowanie z contoso.azurewebsites.net/path1 do contoso.azurewebsites.net/path2). Gdy usługa App Service wyśle odpowiedź przekierowania, używa tej samej nazwy hosta w nagłówku lokalizacji swojej odpowiedzi jako tej w żądaniu odbieranym od bramy aplikacji. Klient wyśle żądanie bezpośrednio do contoso.azurewebsites.net/path2 zamiast przechodzenia przez bramę Application Gateway (contoso.com/path2). Pomijanie bramy aplikacji nie jest pożądane.

Ten problem może wystąpić z następujących powodów:

- Skonfigurowano przekierowanie na App Service. Przekierowanie może być proste, dodając końcowy ukośnik do żądania.
- Masz uwierzytelnianie usługi Azure AD, które powoduje przekierowanie.

Ponadto, jeśli używasz App Services za Application Gateway, nazwa domeny skojarzona z Application Gateway (example.com) różni się od nazwy domeny App Service (Powiedz example.azurewebsites.net), z tego powodu należy zauważyć, że wartość domeny dla plików cookie ARRAffinity ustawiona przez App Service będzie zawierać nazwę domeny "example.azurewebsites.net", która nie jest pożądana. Oryginalna nazwa hosta (example.com) powinna być wartością nazwy domeny w pliku cookie.

## <a name="sample-configuration"></a>Przykładowa konfiguracja

- Odbiornik HTTP: Podstawowa lub wielolokacja
- Pula adresów zaplecza: App Service
- Ustawienia protokołu HTTP: Włączono "Wybieranie nazwy hosta z adresu zaplecza"
- Badane Włączono "Wybieranie nazwy hosta z ustawień protokołu HTTP"

## <a name="cause"></a>Przyczyna

Ponieważ App Service jest usługą wielodostępnym, używa nagłówka hosta w żądaniu, aby skierować żądanie do właściwego punktu końcowego. Jednak domyślna nazwa domeny usług App Services, *. azurewebsites.net (Powiedz contoso.azurewebsites.net), różni się od nazwy domeny bramy aplikacji (Powiedz contoso.com). Ponieważ oryginalne żądanie od klienta ma nazwę hosta usługi Application Gateway (contoso.com), należy skonfigurować bramę aplikacji tak, aby zmienić nazwę hosta w oryginalnym żądaniu dla hosta usług aplikacji, gdy kieruje żądanie do zaplecze usługi App Service.  Można to osiągnąć za pomocą przełącznika "Wybierz nazwę hosta z adresu zaplecza" w konfiguracji ustawienia HTTP Application Gateway i przełącznika "Wybierz nazwę hosta z ustawień protokołu HTTP zaplecza" w konfiguracji sondy kondycji.



![appService-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Z tego powodu, gdy App Service przekierowania, w nagłówku lokalizacji zostanie użyta nazwa hosta przesłonięta "contoso.azurewebsites.net", a nie oryginalna nazwa hosta "contoso.com", chyba że została skonfigurowana inaczej. Możesz sprawdzić przykładowe nagłówki żądania i odpowiedzi poniżej.
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
W powyższym przykładzie można zauważyć, że nagłówek odpowiedzi ma kod stanu 301 dla przekierowania, a nagłówek lokalizacji ma App Service nazwę hosta, a nie oryginalną nazwę hosta "www.contoso.com".

## <a name="solution-rewrite-the-location-header"></a>Rozwiązanie: Zapisz ponownie nagłówek lokalizacji

W nagłówku lokalizacji musi być ustawiona nazwa hosta dla nazwy domeny bramy aplikacji. W tym celu należy utworzyć [regułę ponownego zapisywania](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) z warunkiem, który oblicza, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.NET i wykonuje akcję ponownego zapisania nagłówka lokalizacji w celu utworzenia nazwy hosta bramy aplikacji.  Zapoznaj się z instrukcjami dotyczącymi [ponownego zapisywania nagłówka lokalizacji](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url).

> [!NOTE]
> Obsługa ponownego zapisywania nagłówka HTTP jest dostępna tylko dla [jednostki SKU Standard_V2 i WAF_v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) Application Gateway. W przypadku korzystania z jednostki SKU w wersji 1 zdecydowanie zalecamy Migrowanie [z wersji od 1 do v2](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2) , aby można było korzystać z funkcji ponownego zapisywania i innych [zaawansowanych możliwości](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku) dostępnych w wersji 2 jednostki SKU.

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>Alternatywne rozwiązanie: Użyj niestandardowej domeny usługi App Service zamiast domyślnej nazwy domeny

W przypadku korzystania z jednostki SKU w wersji 1 nie będzie można ponownie zapisać nagłówka lokalizacji, ponieważ ta funkcja jest dostępna tylko dla jednostki SKU w wersji 2. W związku z tym, aby rozwiązać problem z przekierowaniem, należy przekazać tę samą nazwę hosta, która Application Gateway otrzymuje również do App Service, a nie do przesłonięcia hosta.

Po wykonaniu tej czynności App Service przekierowania (jeśli istnieją) w tym samym oryginalnym nagłówku hosta, który wskazuje na Application Gateway, a nie własny.

Aby to osiągnąć, musisz być członkiem domeny niestandardowej i postępować zgodnie z procesem opisanym poniżej.

- Zarejestruj domenę na liście domen niestandardowych App Service. W tym celu w domenie niestandardowej musi znajdować się rekord CNAME wskazujący na App Service nazwy FQDN. Aby uzyskać więcej informacji, zobacz [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appService-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Po wykonaniu tej czynności App Service jest gotowy do zaakceptowania nazwy hosta "www.contoso.com". Teraz Zmień wpis CNAME w systemie DNS, aby wskazywał z powrotem do Application Gateway FQDN. Na przykład "appgw.eastus.cloudapp.azure.com".

- Upewnij się, że domena "www.contoso.com" jest rozpoznawana jako Application Gateway FQDN podczas wykonywania zapytania DNS.

- Ustaw niestandardową sondę, aby wyłączyć "Wybieranie nazwy hosta z ustawień protokołu HTTP zaplecza". Można to zrobić z poziomu portalu, usuwając zaznaczenie pola wyboru w ustawieniach sondy i w programie PowerShell, nie używając przełącznika-PickHostNameFromBackendHttpSettings w poleceniu Set-AzApplicationGatewayProbeConfig. W polu Nazwa hosta sondy wprowadź App Service nazwę FQDN "example.azurewebsites.net", ponieważ żądania sondowania wysyłane z Application Gateway będą miały tę wartość w nagłówku hosta.

  > [!NOTE]
  > Podczas wykonywania następnego kroku upewnij się, że niestandardowa Sonda nie jest skojarzona z ustawieniami protokołu HTTP zaplecza, ponieważ ustawienia protokołu HTTP nadal mają włączony przełącznik "Wybierz nazwę hosta z adresu zaplecza" w tym momencie.

- Ustaw ustawienia HTTP Application Gateway, aby wyłączyć "Wybieranie nazwy hosta z adresu zaplecza". Można to zrobić z poziomu portalu, usuwając zaznaczenie pola wyboru i w programie PowerShell, nie używając przełącznika-PickHostNameFromBackendAddress w poleceniu Set-AzApplicationGatewayBackendHttpSettings.

- Skojarz niestandardową sondę z powrotem z ustawieniami protokołu HTTP zaplecza i Sprawdź kondycję zaplecza, jeśli jest w dobrej kondycji.

- Po wykonaniu tej czynności Application Gateway powinien teraz przekazać tej samej nazwie hosta "www.contoso.com" do App Service, a przekierowanie będzie odbywać się na tej samej nazwie hosta. Możesz sprawdzić przykładowe nagłówki żądania i odpowiedzi poniżej.

Aby zaimplementować kroki wymienione powyżej przy użyciu programu PowerShell dla istniejącej instalacji, wykonaj poniższe przykładowe skrypty programu PowerShell. Należy pamiętać, że nie używamy przełączników-PickHostname w konfiguracji ustawień sondowania i HTTP.

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

Jeśli powyższe kroki nie rozwiążą problemu, Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/).
