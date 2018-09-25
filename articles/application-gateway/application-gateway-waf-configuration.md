---
title: Limity rozmiaru żądanie zapory aplikacji i listy wykluczeń w usłudze Azure Application Gateway — witryna Azure portal sieci Web | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w konfiguracji w usłudze Application Gateway przy użyciu witryny Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965509"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web i wykluczenia listy (publiczna wersja zapoznawcza)

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w konfiguracji.

> [!IMPORTANT]
> Konfiguracja limitów rozmiarów żądania zapory aplikacji sieci Web i listy wykluczeń jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostarczane bez umowy dotyczącej poziomu usług i nie jest zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane lub mogą mieć ograniczone możliwości. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web
Zapora aplikacji sieci Web pozwala użytkownikom na Konfigurowanie ograniczeń rozmiar żądania w ramach dolną i górną granicę. Dostępne są następujące konfiguracje limitów rozmiaru dwa:
- Pole o rozmiarze treść żądania maksymalna jest określona w artykułów bazy wiedzy i kontrolek, które przekazuje ogólną limit rozmiaru żądania, z wyłączeniem żadnych plików. To pole do zakresu od 1 KB minimalnej do maksymalnej wartości 128 KB. Wartością domyślną dla rozmiaru treść żądania jest 128 KB.
- Pola limit przekazywania plików jest wyrażona w Megabajtach i określa maksymalny dozwolony rozmiar przekazywanych plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB. Wartość domyślna dla pliku przekazywania limit wynosi 100 MB.

Zapora aplikacji sieci Web oferuje także można skonfigurować pokrętła, aby włączyć inspekcję treści żądania lub wyłączyć. Domyślnie włączona jest inspekcja treści żądania. Jeśli inspekcja treść żądania jest wyłączona, zapory aplikacji sieci Web nie może oszacować zawartość treści komunikatu HTTP. W takich przypadkach zapory aplikacji sieci Web w dalszym ciągu wymuszania reguł zapory aplikacji sieci Web na nagłówki plików cookie i identyfikatora URI. Jeśli inspekcja treść żądania jest wyłączona, pole o rozmiarze treść żądania maksymalna nie ma zastosowania i nie można ustawić. Wyłączenie inspekcji treść żądania umożliwia wiadomości większych niż 128 KB do wysłania do zapory aplikacji sieci Web. Jednak treść komunikatu nie jest sprawdzane pod kątem luk w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń zapory aplikacji sieci Web

Zapora aplikacji sieci Web listy wykluczeń Zezwalaj użytkownikom na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest, że usługi Active Directory włożony tokenów, które są używane do uwierzytelniania lub pola hasła. Takie atrybuty są podatne na zawierać znaków specjalnych, co może powodować wyzwalanie wynik fałszywie dodatni z reguł zapory aplikacji sieci Web. Gdy atrybut zostanie dodany do listy wykluczeń zapory aplikacji sieci Web, jego jest nie brana pod uwagę przez żadną regułę zapory aplikacji sieci Web skonfigurowanych i aktywne. Listy wykluczeń są globalne w zakresie.
Do listy wykluczeń zapory aplikacji sieci Web, można dodać nagłówków żądań, pliki cookie żądania lub argumenty ciągu zapytania żądania. Można określić dokładne żądania nagłówka, pliku cookie lub dopasowanie atrybut ciągu zapytania lub opcjonalnie można określić częściowego dopasowania. Operatory kryteria dopasowania obsługiwane są następujące: 
- **Równa się**: Ten operator jest używany dla dokładnego dopasowania. Przykład wybierania nagłówka o nazwie **bearerToken**"** Użyj równa operator za pomocą selektora ustawiony jako **bearerToken**. 
- **Rozpoczyna się od**: wszystkie pola, rozpoczynających się od wartości określonej selektor pasuje do tego operatora. 
- **Kończy się**: Ten operator pasuje do wszystkich pól żądania, które kończy się selektor określoną wartość. 
- **Zawiera**: Ten operator pasuje do wszystkich pól żądania, które zawiera selektor określonej wartości.

We wszystkich przypadkach dopasowania jest uwzględniana wielkość liter i wyrażenie regularne nie są dozwolone jako selektorów.

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu ustawień zapory aplikacji sieci Web, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).