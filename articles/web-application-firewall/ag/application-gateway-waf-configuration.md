---
title: Limity rozmiaru żądania zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — Witryna Azure portal
description: Ten artykuł zawiera informacje na temat limitów rozmiaru żądania zapory aplikacji sieci Web i konfiguracji list wykluczeń w bramie aplikacji za pomocą witryny Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526794"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limity rozmiaru żądania zapory aplikacji sieci Web i listy wykluczeń

Zapora aplikacji bramy aplikacji azure (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądania WAF i konfigurację list wykluczeń. Te ustawienia znajdują się w zasadach WAF skojarzonych z bramą aplikacji. Aby dowiedzieć się więcej o zasadach WAF, zobacz [Zapora aplikacji sieci Web platformy Azure w bramie aplikacji platformy Azure](ag-overview.md) i [tworzenie zasad zapory aplikacji sieci Web dla bramy aplikacji](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>Listy wykluczeń WAF

![Limity rozmiaru żądania](../media/application-gateway-waf-configuration/waf-policy.png)

Listy wykluczeń WAF umożliwiają pominięcie niektórych atrybutów żądania z oceny WAF. Typowym przykładem są tokeny wstawione usługi Active Directory, które są używane do uwierzytelniania lub pól haseł. Takie atrybuty są podatne na zawierają znaki specjalne, które mogą wyzwolić fałszywie dodatni z reguł WAF. Po dodaniu atrybutu do listy wykluczeń WAF nie jest on uwzględniany przez żadną skonfigurowaną i aktywną regułę WAF. Listy wykluczeń mają zasięg globalny.

Następujące atrybuty można dodać do list wykluczeń według nazwy. Wartości wybranego pola nie są oceniane względem reguł WAF, ale ich nazwy nadal są (zobacz przykład 1 poniżej, wartość nagłówka agenta użytkownika jest wykluczona z oceny WAF). Listy wykluczeń usuwają inspekcję wartości pola.

* Nagłówki żądań
* Żądaj plików cookie
* Nazwa atrybutu żądania (args) można dodać jako element wykluczający, taki jak:

   * Nazwa pola formularza
   * Encja XML
   * Jednostka JSON
   * Args ciąg kwerendy adresu URL

Można określić dokładne dopasowanie nagłówka żądania, treści, pliku cookie lub ciągu zapytania.  Lub opcjonalnie można określić częściowe dopasowania. Reguły wykluczeń mają zasięg globalny i mają zastosowanie do wszystkich stron i wszystkich reguł.

Poniżej przedstawiono obsługiwane operatory kryteriów dopasowania:

- **Równa się:** Ten operator jest używany do dokładnego dopasowania. Na przykład do wybrania nagłówka o nazwie **bearerToken**użyj operatora równa się z selektorem ustawionym jako **bearerToken**.
- **Rozpoczyna się**od: Ten operator dopasowuje wszystkie pola rozpoczynające się od określonej wartości selektora.
- **Kończy się:** Ten operator dopasowuje wszystkie pola żądania, które kończą się określoną wartością selektora.
- **Zawiera**: Ten operator dopasowuje wszystkie pola żądania, które zawierają określoną wartość selektora.
- **Równa się :** Ten operator dopasowuje wszystkie pola żądania. * będzie wartością selektora.

We wszystkich przypadkach dopasowywanie jest bez uwzględniania wielkości liter, a wyrażenie regularne nie jest dozwolone jako selektory.

> [!NOTE]
> Aby uzyskać więcej informacji i pomocy dotyczącej rozwiązywania problemów, zobacz [Rozwiązywanie problemów z WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Przykłady

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Poniższe przykłady pokazują użycie wykluczeń.

#### <a name="example-1"></a>Przykład 1

W tym przykładzie chcesz wykluczyć nagłówek agenta użytkownika. Nagłówek żądania agenta użytkownika zawiera charakterystyczny ciąg, który umożliwia równorzędnym protokołu sieciowego identyfikowanie typu aplikacji, systemu operacyjnego, dostawcy oprogramowania lub wersji oprogramowania agenta użytkownika oprogramowania żądającego. Aby uzyskać więcej informacji, zobacz [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Może istnieć wiele powodów, aby wyłączyć ocenę tego nagłówka. Może istnieć ciąg, który WAF widzi i zakłada, że jest złośliwy. Na przykład klasyczny atak SQL "x=x" w ciągu. W niektórych przypadkach może to być legalny ruch. Dlatego może być konieczne wykluczenie tego nagłówka z oceny WAF.

Następujące polecenie cmdlet programu Azure PowerShell wyklucza nagłówek agenta użytkownika z oceny:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Przykład 2

W tym przykładzie nie obejmuje wartości w parametrze *użytkownika,* który jest przekazywany w żądaniu za pośrednictwem adresu URL. Załóżmy na przykład, że w środowisku jest często zawieranie ciągu, który w polu WAF jest przeznaczony jako złośliwa zawartość, więc blokuje go.  W takim przypadku można wykluczyć parametr użytkownika, aby w polu nie oceniał niczego.

Następujące polecenie cmdlet programu Azure PowerShell wyklucza parametr użytkownika z oceny:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Więc jeśli `http://www.contoso.com/?user%281%29=fdafdasfda` adres URL jest przekazywany do WAF, nie oceni ciąg **fdafdasfda**, ale nadal będzie oceniać nazwę parametru **użytkownika%281%29**. 

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądania WAF



Zapora aplikacji sieci Web umożliwia skonfigurowanie limitów rozmiaru żądania w dolnej i górnej granicy. Dostępne są następujące dwie konfiguracje limitów rozmiaru:

- Maksymalne pole rozmiaru treści żądania jest określone w kilobajtach i kontroluje ogólny limit rozmiaru żądania, z wyłączeniem przekazywania plików. To pole może wynosić od wartości maksymalnej od 1 KB do wartości maksymalnej 128 KB. Wartość domyślna dla rozmiaru treści żądania wynosi 128 KB.
- Pole limitu przekazywania plików jest określone w MB i reguluje maksymalny dozwolony rozmiar przekazywania pliku. To pole może mieć minimalną wartość 1 MB i następujące wartości maksymalne:

   - 100 MB dla bram średnich WAF w wersji 1
   - 500 MB dla dużych bram WAF w wersji 1
   - 750 MB bram WAF w wersji 2 

 Domyślna wartość limitu przekazywania plików wynosi 100 MB.

WAF oferuje również konfigurowalne pokrętło do włączania lub wyłączania kontroli ciała żądania. Domyślnie kontrola jednostki żądania jest włączona. Jeśli inspekcja treści żądania jest wyłączona, WAF nie ocenia zawartości treści wiadomości HTTP. W takich przypadkach WAF nadal wymusza reguły WAF dla nagłówków, plików cookie i identyfikatora URI. Jeśli inspekcja jednostki żądania jest wyłączona, pole maksymalnego rozmiaru treści żądania nie ma zastosowania i nie można go ustawić. Wyłączenie inspekcji jednostki żądania umożliwia wysyłanie wiadomości o rozmiarze większym niż 128 KB do usługi WAF, ale treść wiadomości nie jest sprawdzana pod kątem luk w zabezpieczeniach.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu ustawień WAF można dowiedzieć się, jak wyświetlać dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Diagnostyka bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
