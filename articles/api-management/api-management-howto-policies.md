---
title: Zasady w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Informacje o sposobie tworzenia, edytowania i konfigurowania zasad w usłudze API Management.
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
ms.openlocfilehash: 54fbba197f6609731ffaf3ff15143a28e70a955f
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
ms.locfileid: "29712872"
---
# <a name="policies-in-azure-api-management"></a>Zasady w usłudze Azure API Management

W konsoli usługi Azure API Management (APIM) zasady są zaawansowanych możliwości systemu, który umożliwia wydawcy, aby zmienić zachowanie interfejsu API za pomocą konfiguracji. Zasady są zbiór instrukcji, które są wykonywane sekwencyjnie na żądanie lub odpowiedź interfejsu API. Popularne instrukcje obejmują Konwersja formatu z pliku XML do formatu JSON i Wywołaj szybkość ograniczenie, aby ograniczyć ilość przychodzących od dewelopera. Wiele zasad są dostępne poza pole.

Zasady są stosowane wewnątrz bramy, która znajduje się pomiędzy konsumenta interfejsu API i zarządzanego interfejsu API. Brama odbiera wszystkie żądania i zwykle przekazuje je niezmieniony do podstawowej interfejsu API. Jednak zasady można zastosować zmiany do żądania przychodzące i wychodzące odpowiedzi.

Wyrażenia zasad mogą służyć jako wartości atrybutów lub wartości tekstowe w dowolnej z zasad usługi API Management, o ile w zasadach nie określono inaczej. Niektóre zasady, takie jak [sterowania przepływem] [ Control flow] i [zmiennej zestaw] [ Set variable] zasady są oparte na wyrażeniach zasad. Aby uzyskać więcej informacji, zobacz [zaawansowane zasady] [ Advanced policies] i [wyrażenie zasad][Policy expressions].

## <a name="sections"> </a>Opis zasad konfiguracji

Definicja zasad jest proste dokument XML, który opisuje sekwencji instrukcji dla ruchu przychodzącego i wychodzącego. Plik XML można edytować bezpośrednio w oknie definicji. Lista instrukcji znajduje się po prawej stronie i instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione.

Kliknięcie instrukcji włączone spowoduje dodanie odpowiednich XML w lokalizacji kursora w definicji widoku. 

> [!NOTE]
> Zasady, które chcesz dodać nie jest włączona, upewnij się, że jesteś w niewłaściwym zakresie dla tej zasady. Każda instrukcja zasad jest przeznaczony do użytku w określonych zakresach i sekcje zasad. Aby zapoznać się z sekcji zasad i zakresy dla zasady, sprawdź **użycia** sekcji dla tej zasady w [informacje o zasadach][Policy Reference].
> 
> 

Konfiguracja jest podzielona na `inbound`, `backend`, `outbound`, i `on-error`. Serię instrukcji określonych zasad jest wykonywany w kolejności na żądania i odpowiedzi.

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

Jeśli występuje błąd podczas przetwarzania żądania, wszystkie pozostałe kroki `inbound`, `backend`, lub `outbound` sekcje są pomijane i wykonywania przechodzi do instrukcji w `on-error` sekcji. Przez umieszczenie deklaracji zasad w `on-error` możesz przejrzeć kod błędu przy użyciu sekcji `context.LastError` właściwość, sprawdzić i dostosować za pomocą odpowiedzi błędu `set-body` zasad i skonfigurować, co się stanie w przypadku wystąpienia błędu. Brak kody błędów dla wbudowanych kroków i błędy, które mogą wystąpić podczas przetwarzania zasady. Aby uzyskać więcej informacji, zobacz [obsługi błędów w zasad interfejsu API zarządzania](https://msdn.microsoft.com/library/azure/mt629506.aspx).

## <a name="scopes"> </a>Jak skonfigurować zasady

Aby uzyskać informacje na temat sposobu konfigurowania zasad, zobacz [ustawić lub edytować zasady](set-edit-policies.md).

## <a name="policy-reference"></a>Odwołanie do zasad

Zobacz [informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia.

## <a name="policy-samples"></a>Przykłady zasad

Zobacz [Przykłady zasad](policy-samples.md) więcej przykładów kodu.

## <a name="examples"></a>Przykłady

### <a name="apply-policies-specified-at-different-scopes"></a>Zastosowanie zasad określonych w innych zakresach

Jeśli masz zasady na poziomie globalnym i zasady skonfigurowane dla interfejsu API, następnie każdorazowe użycie tego konkretnego interfejsu API obie zasady zostaną zastosowane. Zarządzanie interfejsami API umożliwia deterministyczne kolejność deklaracji zasad połączonych za pośrednictwem elementu podstawowego. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

W definicji zasad przykład powyżej `cross-domain` instrukcji jest wykonywany przed następować wszystkie wyższej zasady, które z kolei, `find-and-replace` zasad. 

### <a name="restrict-incoming-requests"></a>Ograniczanie żądań przychodzących

Aby dodać nowe oświadczenie ograniczanie żądań przychodzących do określonych adresów IP, umieść kursor wewnątrz treści `inbound` — element XML i kliknij przycisk **wywołującego Ogranicz adresów IP** instrukcji.

![Zasady ograniczeń][policies-restrict]

Spowoduje to dodanie fragment kodu XML dotyczący `inbound` element, który zawiera wskazówki dotyczące sposobu konfigurowania instrukcji.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Ogranicza liczby żądań przychodzących i zaakceptować tylko te z adresu IP 1.2.3.4 zmodyfikować kod XML w następujący sposób:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
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
