---
title: Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — witryna Azure portal
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w konfiguracji w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65620278"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w konfiguracji.

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web

![Limity rozmiaru żądania](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Zapora aplikacji sieci Web pozwala skonfigurować limity rozmiaru żądania w ramach dolną i górną granicę. Dostępne są następujące konfiguracje limitów rozmiaru dwa:

- Pola rozmiar treści żądania maksymalna jest określona w kilobajtach i kontrolek, który przekazuje ogólną limit rozmiaru żądania, z wyłączeniem żadnych plików. To pole można dostosować w zakresie od co najmniej 1 KB do wartości maksymalnej 128 KB. Wartością domyślną dla rozmiaru treść żądania jest 128 KB.
- Pola limit przekazywania plików jest wyrażona w Megabajtach i określa maksymalny dozwolony rozmiar przekazywanych plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB dla jednostki SKU dużych wystąpień, gdy średnie jednostki SKU może zawierać maksymalnie 100 MB. Wartość domyślna dla pliku przekazywania limit wynosi 100 MB.

Zapora aplikacji sieci Web oferuje także można skonfigurować pokrętła, aby włączyć inspekcję treści żądania lub wyłączyć. Domyślnie włączona jest inspekcja treści żądania. Jeśli inspekcja treść żądania jest wyłączona, zapory aplikacji sieci Web nie szacuje zawartość treści komunikatu HTTP. W takich przypadkach zapory aplikacji sieci Web w dalszym ciągu wymuszania reguł zapory aplikacji sieci Web na nagłówki plików cookie i identyfikatora URI. Jeśli inspekcja treść żądania jest wyłączona, pole o rozmiarze treść żądania maksymalna nie ma zastosowania i nie można ustawić. Umożliwia wyłączenie inspekcji treść żądania dla wiadomości większych niż 128 KB do wysłania do zapory aplikacji sieci Web, ale treść komunikatu nie jest kontrolowane w zakresie luki w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń zapory aplikacji sieci Web

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Listy wykluczeń zapory aplikacji sieci Web pozwala na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest, że usługi Active Directory włożony tokenów, które są używane do uwierzytelniania lub pola hasła. Takie atrybuty są podatne na zawierać znaków specjalnych, co może powodować wyzwalanie wynik fałszywie dodatni z reguł zapory aplikacji sieci Web. Gdy atrybut zostanie dodany do listy wykluczeń zapory aplikacji sieci Web, nie jest uznawane za przez żadną regułę zapory aplikacji sieci Web skonfigurowanych i aktywne. Listy wykluczeń są globalne w zakresie.

Następujące atrybuty mogą być dodawane do listy wykluczeń:

* Nagłówki żądania
* Pliki cookie żądania
* Nazwa atrybutu żądania (argumenty)

   * Wieloczęściowych danych formularza
   * XML
   * JSON
   * Adres URL zapytania argumentów

Określ nagłówek żądania dokładnie, treści, pliku cookie lub dopasowanie atrybut ciągu zapytania.  Alternatywnie można opcjonalnie określić częściowego dopasowania. Wykluczenie jest zawsze włączona pole nagłówka, nigdy na jego wartość. Reguły wykluczania mają zakres globalny i mają zastosowanie do wszystkich stron i wszystkie reguły.

Operatory kryteria dopasowania obsługiwane są następujące:

- **Równa się**:  Ten operator jest używany dla dokładnego dopasowania. Przykład wybierania nagłówka o nazwie **bearerToken**, operatorem równa się za pomocą selektora ustawiony jako **bearerToken**.
- **Rozpoczyna się od**: Ten operator pasuje do wszystkich pól, rozpoczynających się od wartości określonej selektora.
- **Kończy się**:  Ten operator pasuje do wszystkich pól żądania, które kończy się wartość określony selektor.
- **Zawiera**: Ten operator pasuje do wszystkich pól żądania, które zawierają wartość określony selektor.
- **Równe wszystkim**: Ten operator pasuje do wszystkich pól żądania. * będzie wartości selektora.

We wszystkich przypadkach dopasowania jest uwzględniana wielkość liter i wyrażenie regularne nie są dozwolone jako selektorów.

### <a name="examples"></a>Przykłady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W poniższych przykładach pokazano użycie wykluczeń.

### <a name="example-1"></a>Przykład 1

W tym przykładzie chcesz wykluczyć nagłówka użytkownika agenta. Nagłówek żądania agenta użytkownika zawiera ciąg cech, który zezwala na elementy równorzędne protokołu sieciowego do identyfikowania typu aplikacji, system operacyjny, dostawca oprogramowania lub wersji oprogramowania agenta użytkownika żądania oprogramowania. Aby uzyskać więcej informacji, zobacz [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Może to być dowolna liczba przyczyn można wyłączyć oceny tego pliku nagłówkowego. Może to być ciąg, który zapory aplikacji sieci Web będzie widział i przyjęto założenie, że jest złośliwy. Na przykład klasycznego ataku SQL "x = x" w ciągu. W niektórych przypadkach może to być uzasadnione ruchu. Dlatego może być konieczne spod tego pliku nagłówkowego oceny zapory aplikacji sieci Web.

Następujące polecenie cmdlet programu Azure PowerShell z wyłączeniem nagłówka agenta użytkownika z wersji ewaluacyjnej:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Przykład 2

W tym przykładzie nie obejmuje wartości w *użytkownika* parametr, który jest przekazywany w żądaniu za pośrednictwem adresu URL. Na przykład załóżmy, że jest typowe w danym środowisku dla pola użytkownika, aby zawierać ciąg, który zapory aplikacji internetowych widoków jako złośliwej zawartości, więc blokuje ją.  Parametr użytkownika można wykluczyć w tym przypadku tak, aby ocenić nie zapory aplikacji internetowych, wszystko w tym polu.

Następujące polecenie cmdlet programu Azure PowerShell nie obejmuje parametr użytkownika z wersji ewaluacyjnej:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Więc jeśli adres URL **http://www.contoso.com/?user=fdafdasfda** jest przekazywany do zapory aplikacji sieci Web, nie ocenia ciąg **fdafdasfda**.

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu ustawień zapory aplikacji sieci Web, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).
