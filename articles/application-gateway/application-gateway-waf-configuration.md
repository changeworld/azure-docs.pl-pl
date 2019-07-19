---
title: Limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — Azure Portal
description: Ten artykuł zawiera informacje na temat limitów rozmiaru żądań zapory aplikacji sieci Web i konfiguracji list wykluczeń w Application Gateway z Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 7/17/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 9e9472fbcd01cf40204063174b159638369d7429
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326666"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limity rozmiaru żądań zapory aplikacji sieci Web i listy wykluczeń

Zapora aplikacji sieci Web Application Gateway Azure (WAF) zapewnia ochronę aplikacji sieci Web. W tym artykule opisano limity rozmiaru żądania WAF i konfigurację list wykluczeń.

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądania WAF

![Limity rozmiaru żądań](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Zapora aplikacji sieci Web umożliwia skonfigurowanie limitów rozmiaru żądań w dolnej i górnej granicy. Dostępne są następujące dwa konfiguracje limitów rozmiaru:

- Pole Maksymalny rozmiar treści żądania jest określone w kilobajtach i kontroluje ogólny limit rozmiaru żądania, wykluczając wszystkie operacje przekazywania plików. To pole może mieć wartość z zakresu od 1 do KB do 128 KB. Wartość domyślna dla rozmiaru treści żądania to 128 KB.
- Pole limit przekazywania plików jest określone w MB i kontroluje maksymalny dozwolony rozmiar przekazywania plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB dla dużych wystąpień jednostki SKU, podczas gdy średnia jednostka SKU ma maksymalnie 100 MB. Wartość domyślna dla limitu przekazywania plików to 100 MB.

WAF oferuje również konfigurowalne pokrętło, aby włączyć lub wyłączyć inspekcję treści żądania. Domyślnie Inspekcja treści żądania jest włączona. Jeśli inspekcja treści żądania jest wyłączona, WAF nie oceni zawartości treści wiadomości HTTP. W takich przypadkach WAF nadal wymusza reguły WAF dla nagłówków, plików cookie i identyfikatora URI. Jeśli inspekcja treści żądania jest wyłączona, pole Maksymalny rozmiar treści żądania nie ma zastosowania i nie można go ustawić. Wyłączenie inspekcji treści żądania umożliwia wysłanie komunikatów o rozmiarze większym niż 128 KB do WAF, ale treść komunikatu nie jest sprawdzana pod kątem luk w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń WAF

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Listy wykluczeń WAF umożliwiają pominięcie niektórych atrybutów żądania z oceny WAF. Typowym przykładem jest Active Directory wstawione tokeny, które są używane dla pól uwierzytelniania lub hasła. Takie atrybuty są podatne na zawiera znaki specjalne, które mogą wyzwalać wynik fałszywie dodatni z reguł WAF. Po dodaniu atrybutu do listy wykluczeń WAF nie jest on uznawany za żadną ze skonfigurowanych i aktywnych reguł WAF. Listy wykluczeń są globalne w zakresie.

Następujące atrybuty można dodać do listy wykluczeń według nazwy. Wartości wybranego pola nie są oceniane względem reguł WAF, ale ich nazwy nadal są (patrz przykład 1 poniżej, wartość nagłówka User-Agent jest wykluczona z oceny WAF). Wykluczenie zawiera listę usuwania inspekcji wartości pola.

* Nagłówki żądań
* Pliki cookie żądania
* Nazwę atrybutu żądania (args) można dodać jako element wykluczenia, na przykład:

   * Nazwa pola formularza
   * Jednostka XML
   * Jednostka JSON
   * Argumenty ciągu zapytania URL

Można określić dokładny nagłówek żądania, treść, plik cookie lub dopasowanie atrybutu ciągu zapytania.  Alternatywnie możesz określić częściowe dopasowania. Reguły wykluczania są globalne w zakresie i są stosowane do wszystkich stron i wszystkich reguł.

Poniżej przedstawiono operatory kryteriów dopasowania obsługiwane:

- **Równa się**:  Ten operator jest używany do dokładnego dopasowania. Przykładowo, aby wybrać nagłówek o nazwie **bearerToken**, użyj operatora Equals z selektorem ustawionym jako **bearerToken**.
- **Rozpoczyna się od**: Ten operator dopasowuje wszystkie pola, które zaczynają się od określonej wartości selektora.
- **Kończy się**:  Ten operator dopasowuje wszystkie pola żądań, które kończą się określoną wartością selektora.
- **Zawiera**: Ten operator dopasowuje wszystkie pola żądania, które zawierają określoną wartość selektora.
- **Równa**się: Ten operator dopasowuje wszystkie pola żądania. * będzie wartością selektora.

We wszystkich przypadkach dopasowanie nie uwzględnia wielkości liter i wyrażenie regularne nie jest dozwolone jako selektory.

> [!NOTE]
> Aby uzyskać więcej informacji i uzyskać pomoc dotyczącą rozwiązywania problemów, zobacz [WAF Rozwiązywanie problemów](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Przykłady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W poniższych przykładach przedstawiono sposób korzystania z wykluczeń.

### <a name="example-1"></a>Przykład 1

W tym przykładzie chcesz wykluczyć nagłówek User-Agent. Nagłówek żądania User-Agent zawiera ciąg charakterystyczny, który pozwala elementom równorzędnym protokołu sieciowego identyfikować typ aplikacji, system operacyjny, dostawca oprogramowania lub wersję oprogramowania żądającego agenta użytkownika oprogramowania. Aby uzyskać więcej informacji, zobacz [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Istnieje wiele powodów, dla których można wyłączyć ocenianie tego nagłówka. Może być ciągiem, który WAF widzi i założono, że jest on złośliwy. Na przykład klasyczny atak SQL "x = x" w ciągu. W niektórych przypadkach może to być prawidłowy ruch. Może być konieczne wykluczenie tego nagłówka z oceny WAF.

Następujące polecenie cmdlet Azure PowerShell wyklucza nagłówek User-Agent z oceny:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Przykład 2

Ten przykład wyklucza wartość w parametrze *użytkownika* , który jest przesyłany w żądaniu za pośrednictwem adresu URL. Załóżmy na przykład, że jest to typowy w Twoim środowisku, aby pole użytkownika zawierało ciąg, który WAF przegląda jako złośliwą zawartość, więc zablokuje go.  W tym przypadku można wykluczyć parametr użytkownika, tak aby WAF nie obliczał żadnych wartości w polu.

Następujące polecenie cmdlet Azure PowerShell wyklucza parametr użytkownika z oceny:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Dlatego jeśli adres URL **http://www.contoso.com/?user%281%29=fdafdasfda** jest przesyłany do WAF, nie będzie on obliczany w ciągu **fdafdasfda**, ale nadal będzie obliczał nazwę parametru **użytkownika% SRM% 29**. 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień WAF można dowiedzieć się, jak wyświetlać dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging).
