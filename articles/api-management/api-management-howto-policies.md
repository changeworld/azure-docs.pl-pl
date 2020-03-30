---
title: Zasady w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć, edytować i konfigurować zasady w usłudze API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: c10939b50a66cd608d27a71f02d959fbc2380f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072303"
---
# <a name="policies-in-azure-api-management"></a>Zasady w usłudze Azure API Management

W usłudze Azure API Management (APIM) zasady są zaawansowana funkcja systemu, które umożliwiają wydawcy, aby zmienić zachowanie interfejsu API za pomocą konfiguracji. Zasady są kolekcją instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują konwersję formatu z XML do JSON i ograniczenie szybkości połączeń w celu ograniczenia liczby połączeń przychodzących od dewelopera. Wiele innych zasad jest dostępnych po wyjęciu z pudełka.

Zasady są stosowane wewnątrz bramy, która znajduje się między konsumentem interfejsu API a zarządzanym interfejsem API. Brama odbiera wszystkie żądania i zwykle przekazuje je w stanie zapytania do bazowego interfejsu API. Jednak zasady mogą stosować zmiany zarówno do żądania przychodzącego i odpowiedzi wychodzącej.

Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, np. [Przepływ sterowania][Control flow] i [Ustawianie zmiennej][Set variable], są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz tematy [Advanced policies][Advanced policies] (Zaawansowane zasady) i [Policy expressions][Policy expressions] (Wyrażenia zasad).

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Opis konfiguracji zasad

Definicja zasad jest prostym dokumentem XML, który opisuje sekwencję instrukcji przychodzących i wychodzących. Kod XML można edytować bezpośrednio w oknie definicji. Lista instrukcji jest dostarczana po prawej stronie, a instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione.

Kliknięcie włączonej instrukcji spowoduje dodanie odpowiedniego kodu XML w lokalizacji kursora w widoku definicji. 

> [!NOTE]
> Jeśli zasady, które chcesz dodać, nie są włączone, upewnij się, że jesteś w odpowiednim zakresie dla tej zasady. Każda instrukcja zasad jest przeznaczona do użytku w określonych zakresach i sekcjach zasad. Aby przejrzeć sekcje zasad i zakresy zasad, sprawdź sekcję **Użycie** dla tej zasady w sekcji [Odwołanie do zasad][Policy Reference].
> 
> 

Konfiguracja jest podzielona `backend` `outbound`na `on-error` `inbound`, , i . Seria określonych instrukcji zasad jest wykonywana w kolejności dla żądania i odpowiedzi.

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

Jeśli wystąpi błąd podczas przetwarzania żądania, wszystkie pozostałe `inbound`kroki `backend`w `outbound` , lub sekcje są pomijane i `on-error` wykonywanie przeskakuje do instrukcji w sekcji. Umieszczając instrukcje zasad `on-error` w sekcji można przejrzeć `context.LastError` błąd przy użyciu właściwości, sprawdzić `set-body` i dostosować odpowiedź na błąd przy użyciu zasad i skonfigurować, co się stanie, jeśli wystąpi błąd. Istnieją kody błędów dla wbudowanych kroków i błędów, które mogą wystąpić podczas przetwarzania instrukcji zasad. Aby uzyskać więcej informacji, zobacz [Obsługa błędów w zasadach zarządzania interfejsami API](/azure/api-management/api-management-error-handling-policies).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Jak skonfigurować zasady

Aby uzyskać informacje dotyczące konfigurowania zasad, zobacz [Ustawianie lub edytowanie zasad](set-edit-policies.md).

## <a name="policy-reference"></a>Referencje dotyczące zasad

Pełna lista instrukcji dotyczących zasad i ich ustawień można znaleźć w [przypadku odwołania do zasad.](api-management-policy-reference.md)

## <a name="policy-samples"></a>Przykłady zasad

Zobacz [przykłady zasad,](policy-samples.md) aby uzyskać więcej przykładów kodu.

## <a name="examples"></a>Przykłady

### <a name="apply-policies-specified-at-different-scopes"></a>Stosowanie zasad określonych w różnych zakresach

Jeśli masz zasady na poziomie globalnym i zasady skonfigurowane dla interfejsu API, a następnie za każdym razem, gdy ten konkretny interfejs API jest używany obie zasady zostaną zastosowane. Zarządzanie interfejsem API umożliwia deterministyczne porządkowanie połączonych instrukcji zasad za pośrednictwem elementu podstawowego. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

W powyższej definicji `cross-domain` zasad przykładowej instrukcja będzie wykonywana przed wszelkimi `find-and-replace` wyższymi zasadami, po których z kolei następuje zasada. 

### <a name="restrict-incoming-requests"></a>Ograniczanie żądań przychodzących

Aby dodać nową instrukcję, aby ograniczyć przychodzące żądania do określonych adresów IP, umieść kursor tylko wewnątrz zawartości elementu `inbound` XML i kliknij instrukcję **Ogranicz adresy IP wywołującego.**

![Zasady ograniczeń][policies-restrict]

Spowoduje to dodanie fragmentu kodu XML do `inbound` elementu, który zawiera wskazówki dotyczące konfigurowania instrukcji.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Aby ograniczyć przychodzące żądania i zaakceptować tylko te z adresu IP 1.2.3.4, zmodyfikuj kod XML w następujący sposób:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
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
