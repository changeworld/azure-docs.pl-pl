---
title: Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — witryna Azure portal
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w konfiguracji w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 11/6/2018
ms.author: victorh
ms.openlocfilehash: bed406f90c8da62919337c1fa9f30221b0ba8d90
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752728"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w konfiguracji.

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web

![Limity rozmiaru żądania](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Zapora aplikacji sieci Web pozwala użytkownikom na Konfigurowanie ograniczeń rozmiar żądania w ramach dolną i górną granicę. Dostępne są następujące konfiguracje limitów rozmiaru dwa:

- Pole o rozmiarze treść żądania maksymalna jest określona w artykułów bazy wiedzy i kontrolek, które przekazuje ogólną limit rozmiaru żądania, z wyłączeniem żadnych plików. To pole można dostosować w zakresie od co najmniej 1 KB do wartości maksymalnej 128 KB. Wartością domyślną dla rozmiaru treść żądania jest 128 KB.
- Pola limit przekazywania plików jest wyrażona w Megabajtach i określa maksymalny dozwolony rozmiar przekazywanych plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB. Wartość domyślna dla pliku przekazywania limit wynosi 100 MB.

Zapora aplikacji sieci Web oferuje także można skonfigurować pokrętła, aby włączyć inspekcję treści żądania lub wyłączyć. Domyślnie włączona jest inspekcja treści żądania. Jeśli inspekcja treść żądania jest wyłączona, zapory aplikacji sieci Web nie może oszacować zawartość treści komunikatu HTTP. W takich przypadkach zapory aplikacji sieci Web w dalszym ciągu wymuszania reguł zapory aplikacji sieci Web na nagłówki plików cookie i identyfikatora URI. Jeśli inspekcja treść żądania jest wyłączona, pole o rozmiarze treść żądania maksymalna nie ma zastosowania i nie można ustawić. Umożliwia wyłączenie inspekcji treść żądania dla wiadomości większych niż 128 KB do wysłania do zapory aplikacji sieci Web, ale treść komunikatu nie jest kontrolowane w zakresie luki w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń zapory aplikacji sieci Web

![exclusion.png zapory aplikacji sieci Web](media/application-gateway-waf-configuration/waf-exclusion.png)

Zapora aplikacji sieci Web listy wykluczeń Zezwalaj użytkownikom na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest, że usługi Active Directory włożony tokenów, które są używane do uwierzytelniania lub pola hasła. Takie atrybuty są podatne na zawierać znaków specjalnych, co może powodować wyzwalanie wynik fałszywie dodatni z reguł zapory aplikacji sieci Web. Gdy atrybut zostanie dodany do listy wykluczeń zapory aplikacji sieci Web, nie jest brana pod uwagę przez żadną regułę zapory aplikacji sieci Web skonfigurowanych i aktywne. Listy wykluczeń są globalne w zakresie.

Następujące atrybuty mogą być dodawane do listy wykluczeń:

* Nagłówki żądania
* Pliki cookie żądania
* Treść żądania

   * Wieloczęściowych danych formularza
   * XML
   * JSON

Użytkownik może określić nagłówek żądania dokładnie, treści, plik cookie lub dopasowanie atrybut ciągu zapytania, lub, w Opcjonalnie można określić częściowego dopasowania.

Operatory kryteria dopasowania obsługiwane są następujące:

- **Równa się**:  Ten operator jest używany dla dokładnego dopasowania. Przykład wybierania nagłówka o nazwie **bearerToken**, operatorem równa się za pomocą selektora ustawiony jako **bearerToken**.
- **Rozpoczyna się od**: Ten operator pasuje do wszystkich pól, rozpoczynających się od wartości określonej selektora.
- **Kończy się**:  Ten operator pasuje do wszystkich pól żądania, które kończy się wartość określony selektor.
- **Zawiera**: Ten operator pasuje do wszystkich pól żądania, które zawierają wartość określony selektor.

We wszystkich przypadkach dopasowania jest uwzględniana wielkość liter i wyrażenie regularne nie są dozwolone jako selektorów.

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu ustawień zapory aplikacji sieci Web, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).