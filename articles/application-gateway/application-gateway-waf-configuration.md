---
title: Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — witryna Azure portal
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w konfiguracji w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 9306280d00ec901633585aba2f23ed06b25b4e1e
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49115458"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web i wykluczenia listy (publiczna wersja zapoznawcza)

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w konfiguracji.

> [!IMPORTANT]
> Konfiguracja limitów rozmiarów żądania zapory aplikacji sieci Web i listy wykluczeń jest obecnie w publicznej wersji zapoznawczej. Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług i nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web
Zapora aplikacji sieci Web pozwala użytkownikom na Konfigurowanie ograniczeń rozmiar żądania w ramach dolną i górną granicę. Dostępne są następujące konfiguracje limitów rozmiaru dwa:

- Pole o rozmiarze treść żądania maksymalna jest określona w artykułów bazy wiedzy i kontrolek, które przekazuje ogólną limit rozmiaru żądania, z wyłączeniem żadnych plików. To pole można dostosować w zakresie od co najmniej 1 KB do wartości maksymalnej 128 KB. Wartością domyślną dla rozmiaru treść żądania jest 128 KB.
- Pola limit przekazywania plików jest wyrażona w Megabajtach i określa maksymalny dozwolony rozmiar przekazywanych plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB. Wartość domyślna dla pliku przekazywania limit wynosi 100 MB.

Zapora aplikacji sieci Web oferuje także można skonfigurować pokrętła, aby włączyć inspekcję treści żądania lub wyłączyć. Domyślnie włączona jest inspekcja treści żądania. Jeśli inspekcja treść żądania jest wyłączona, zapory aplikacji sieci Web nie może oszacować zawartość treści komunikatu HTTP. W takich przypadkach zapory aplikacji sieci Web w dalszym ciągu wymuszania reguł zapory aplikacji sieci Web na nagłówki plików cookie i identyfikatora URI. Jeśli inspekcja treść żądania jest wyłączona, pole o rozmiarze treść żądania maksymalna nie ma zastosowania i nie można ustawić. Wyłączenie inspekcji treść żądania umożliwia wiadomości większych niż 128 KB do wysłania do zapory aplikacji sieci Web. Treść komunikatu nie jest jednak kontrolowane w zakresie luki w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń zapory aplikacji sieci Web

Zapora aplikacji sieci Web listy wykluczeń Zezwalaj użytkownikom na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest, że usługi Active Directory włożony tokenów, które są używane do uwierzytelniania lub pola hasła. Takie atrybuty są podatne na zawierać znaków specjalnych, co może powodować wyzwalanie wynik fałszywie dodatni z reguł zapory aplikacji sieci Web. Gdy atrybut zostanie dodany do listy wykluczeń zapory aplikacji sieci Web, nie jest brana pod uwagę przez żadną regułę zapory aplikacji sieci Web skonfigurowanych i aktywne. Listy wykluczeń są globalne w zakresie.
Nagłówki żądania, treści żądania, pliki cookie żądania lub argumenty ciągu zapytania żądania można dodać do listy wykluczeń zapory aplikacji sieci Web. Jeśli jednostka ma dane formularza lub XML/JSON (pary klucz-wartość) żądania atrybutu wykluczeń typ może być używany.

Użytkownik może określić nagłówek żądania dokładnie, treści, plik cookie lub dopasowanie atrybut ciągu zapytania, lub, w Opcjonalnie można określić częściowego dopasowania.

Operatory kryteria dopasowania obsługiwane są następujące:

- **Równa się**: Ten operator jest używany dla dokładnego dopasowania. Przykład wybierania nagłówka o nazwie **bearerToken** operator jest równa użycia za pomocą selektora ustawiony jako **bearerToken**.
- **Rozpoczyna się od**: wszystkie pola, rozpoczynających się od wartości określonej selektor pasuje do tego operatora. 
- **Kończy się**: Ten operator pasuje do wszystkich pól żądania, które kończy się selektor określoną wartość. 
- **Zawiera**: Ten operator pasuje do wszystkich pól żądania, które zawiera selektor określonej wartości.

We wszystkich przypadkach dopasowania jest uwzględniana wielkość liter i wyrażenie regularne nie są dozwolone jako selektorów.

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu ustawień zapory aplikacji sieci Web, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).