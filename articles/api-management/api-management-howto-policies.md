---
title: Zasady usługi Azure API Management | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia, edytowania i konfigurowanie zasad usługi API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 99f756b5415811b3d4c2ee0167f98b31c905df1a
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793683"
---
# <a name="policies-in-azure-api-management"></a>Zasady usługi Azure API Management

W usłudze Azure API Management (APIM), zasady są zaawansowaną możliwością usługi systemu, która pozwala wydawcy zmieniać zachowanie interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Popularne instrukcje obejmują konwersję z formatu XML do formatu JSON i ograniczanie, aby ograniczyć liczbę wywołań przychodzących od dewelopera liczby wywołań. Gotowe, dostępnych jest wiele więcej zasad.

Zasady są stosowane w ramach bramy, która pośredniczy między konsumenta interfejsu API i zarządzany interfejs API. Brama odbiera wszystkie żądania i zazwyczaj przekazuje je w niezmienionej formie podstawowego interfejsu API. Jednak zasady można zastosować zmian, żądanie przychodzące i wychodzące odpowiedzi.

Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, takie jak [przepływ sterowania] [ Control flow] i [Ustaw zmienną] [ Set variable] zasady są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz [zaawansowane zasady] [ Advanced policies] i [wyrażenia zasad][Policy expressions].

## <a name="sections"> </a>Opis zasad konfiguracji

Definicja zasad jest proste dokumentu XML, który opisuje sekwencji instrukcji dla ruchu przychodzącego i wychodzącego. Plik XML można edytować bezpośrednio w oknie definicji. Po prawej stronie znajduje się lista instrukcji i instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione.

Klikając instrukcji włączone doda właściwy XML w lokalizacji kursora w definicji widoku. 

> [!NOTE]
> Jeśli nie włączono zasady, które chcesz dodać, upewnij się, że znajdują się w niewłaściwym zakresie dla tej zasady. Każda instrukcja zasad jest przeznaczony do użytku w określonych zakresach i sekcje zasad. Aby zapoznać się z sekcji zasad i zakresy dla zasad, zapoznaj się z **użycia** sekcji dla tej zasady w [informacje o zasadach][Policy Reference].
> 
> 

Konfiguracja jest podzielony na `inbound`, `backend`, `outbound`, i `on-error`. Serię instrukcji określonych zasad jest wykonywany w kolejności dla żądania i odpowiedzi.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Jeśli występuje błąd podczas przetwarzania żądania, wszelkie pozostałe kroki `inbound`, `backend`, lub `outbound` sekcje zostaną pominięte, a wykonywanie przeskakuje do instrukcji w `on-error` sekcji. Umieszczając instrukcje zasad w `on-error` sekcji możesz przejrzeć ten błąd, za pomocą `context.LastError` właściwości, zbadaj i dostosowywanie za pomocą odpowiedzi błędu `set-body` zasad i skonfiguruj, co się stanie, jeśli wystąpi błąd. Brak kody błędów wbudowanych kroków i błędy, które mogą wystąpić podczas przetwarzania zasad. Aby uzyskać więcej informacji, zobacz [obsługi błędów w zasady usługi API Management](/azure/api-management/api-management-error-handling-policies).

## <a name="scopes"> </a>Jak skonfigurować zasady

Aby uzyskać informacje na temat sposobu konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

## <a name="policy-reference"></a>Informacje o zasadach

Zobacz [informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia.

## <a name="policy-samples"></a>Przykłady zasad

Zobacz [Przykłady zasad](policy-samples.md) więcej przykładów kodu.

## <a name="examples"></a>Przykłady

### <a name="apply-policies-specified-at-different-scopes"></a>Zastosuj zasady określone w różnych zakresach

Jeśli masz zasady na poziomie globalnym i zasady skonfigurowane dla interfejsu API, następnie podczas każdego użycia tego konkretnego interfejsu API obie zasady zostaną zastosowane. Usługa API Management umożliwia deterministyczne kolejność deklaracji zasad połączone za pośrednictwem elementu podstawowego. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

W definicji zasad przykładzie powyżej `cross-domain` instrukcji jest wykonywany przed następować wyższe zasady, które z kolei, `find-and-replace` zasad. 

### <a name="restrict-incoming-requests"></a>Ograniczanie żądań przychodzących

Aby dodać nowy raport do ograniczania żądań przychodzących do określonych adresów IP, umieść kursor wewnątrz treści `inbound` — element XML i kliknij przycisk **wywołujący ograniczenie adresów IP** instrukcji.

![Zasady ograniczeń][policies-restrict]

Spowoduje to dodanie fragmentu kodu XML do `inbound` element, który zawiera wskazówki dotyczące sposobu konfigurowania instrukcji.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ogranicz przychodzące żądania i zaakceptować tylko te z adresu IP 1.2.3.4 zmodyfikować plik XML w następujący sposób:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
