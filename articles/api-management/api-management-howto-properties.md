---
title: Jak używać wartości o nazwie w zasadach usługi Azure API Management
description: Dowiedz się, jak używać wartości o nazwie w zasadach usługi Azure API Management.
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
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 9e1b1953520c5502668fbbae70a37a140253b035
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241688"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak używać wartości o nazwie w zasadach usługi Azure API Management
Zasady usługi API Management są zaawansowaną możliwością system, który pozwala witrynie Azure portal zmieniać zachowanie interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Instrukcje zasad można skonstruować przy użyciu wartości tekst dosłowny, wyrażenia zasad i nazwane wartości. 

Każde wystąpienie usługi API Management ma kolekcji właściwości pary klucz/wartość, która jest wywoływana o nazwie wartości, które są globalne do wystąpienia usługi. Te wartości o nazwie może służyć do zarządzania stałym ciągiem wartości we wszystkich Konfiguracja interfejsu API i zasady. Każda właściwość może mieć następujące atrybuty:

| Atrybut | Type | Opis |
| --- | --- | --- |
| `Display name` |string |Ciąg alfanumeryczny używany jako odwołanie do właściwości w zasadach. |
| `Value`        |string |Wartość właściwości. Nie może być pusta ani składać się wyłącznie z białych znaków. |
| `Secret`       |wartość logiczna|Określa, czy wartość jest wpis tajny i powinien być zaszyfrowany, czy nie.|
| `Tags`         |tablica ciągów |Opcjonalne — tagi, gdy zostanie podane, może służyć do filtrowania listy właściwości. |

![Nazwane wartości](./media/api-management-howto-properties/named-values.png)

Wartości właściwości mogą zawierać ciągi literałów i [wyrażenia zasad](/azure/api-management/api-management-policy-expressions). Na przykład, wartość `ExpressionProperty` to wyrażenie zasad, które zwraca ciąg zawierający bieżącej daty i godziny. Właściwość `ContosoHeaderValue` jest oznaczony jako klucz tajny, dzięki czemu jego wartość nie jest wyświetlana.

| Name (Nazwa) | Value | Secret | `Tags` |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Możliwość dodawania oraz edytowania właściwości

![Dodaj właściwość](./media/api-management-howto-properties/add-property.png)

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz **nazwane wartości**.
3. Naciśnij klawisz **+ Dodaj**.

   Nazwy i wartości są wymaganymi wartościami. Jeśli wartość tej właściwości jest klucz tajny, sprawdź, czy jest to pole wpisu tajnego. Wprowadź jeden lub więcej opcjonalnych tagów, aby ułatwić organizowanie nazwanych wartości, a następnie kliknij przycisk Zapisz.
4. Kliknij pozycję **Utwórz**.

Po utworzeniu właściwości można edytować go, klikając właściwości. Jeśli zmienisz nazwę właściwości, wszystkie zasady, które odwołują się tę właściwość są automatycznie aktualizowane do użycia nowej nazwy.

Aby uzyskać informacje na temat edytowania właściwości przy użyciu interfejsu API REST, zobacz [Edytuj właściwości przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Aby usunąć właściwość

Aby usunąć właściwość, kliknij przycisk **Usuń** widoczny obok właściwości do usunięcia.

> [!IMPORTANT]
> Jeśli właściwość jest wywoływany przez żadne zasady, nie można pomyślnie usunąć, dopóki nie usuniesz właściwości wszystkich zasad, które go używają.
> 
> 

Aby uzyskać informacje na temat usuwania właściwości przy użyciu interfejsu API REST, zobacz [Usuń właściwości przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Wyszukiwanie i filtrowanie wartości o nazwie

**Nazwane wartości** karta zawiera wyszukiwania i filtrowania możliwości, które pomogą Ci w zarządzaniu nazwanej wartości. Aby filtrować listę właściwości według nazwy właściwości, wprowadź wyszukiwany termin w **wyszukiwania właściwości** pola tekstowego. Aby wyświetlić wszystkie nazwane wartości, wyczyść **wyszukiwania właściwości** polu tekstowym i naciśnij klawisz enter.

Aby filtrować listę właściwości, według wartości tagów, wpisz jeden lub więcej tagów do **Filtruj według tagów** pola tekstowego. Aby wyświetlić wszystkie nazwane wartości, wyczyść **Filtruj według tagów** polu tekstowym i naciśnij klawisz enter.

## <a name="to-use-a-property"></a>Aby użyć właściwości

Aby korzystać z właściwości w zasadach, umieść nazwę właściwości wewnątrz double parę nawiasów klamrowych, takich jak `{{ContosoHeader}}`, jak pokazano w poniższym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym przykładzie `ContosoHeader` jest używana jako nazwa nagłówka w `set-header` zasady, a `ContosoHeaderValue` jest używana jako wartość nagłówka. W przypadku oceny tych zasad podczas żądania lub odpowiedzi do bramy usługi API Management `{{ContosoHeader}}` i `{{ContosoHeaderValue}}` zostaną zastąpione wartościami odpowiednich właściwości.

Nazwane wartości mogą być używane jako ukończone atrybut lub element wartości, jak pokazano w poprzednim przykładzie, ale także mogą zostać wstawiony albo łączyć się z częścią wyrażenia tekst dosłowny, jak pokazano w poniższym przykładzie: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Nazwane wartości może również zawierać wyrażeń zasad. W poniższym przykładzie `ExpressionProperty` jest używany.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

W przypadku oceny tych zasad `{{ExpressionProperty}}` jest zastępowana wartością jego: `@(DateTime.Now.ToString())`. Ponieważ wyrażenie zasad ma wartość, wyrażenie jest obliczane i zasady kontynuuje wykonywanie.

Możesz przetestować tę możliwość w portalu dla deweloperów, wywołując operację, która ma zasady z nazwanych wartości w zakresie. W poniższym przykładzie, operacja jest wywoływana z dwóch poprzedni przykład `set-header` zasad przy użyciu nazwanych wartości. Należy pamiętać, że odpowiedź zawiera dwa Nagłówki niestandardowe, które zostały skonfigurowane przy użyciu zasad za pomocą nazwanych wartości.

![Portal dla deweloperów][api-management-send-results]

Jeśli przyjrzymy się [śledzenia inspektora interfejsów API](api-management-howto-api-inspector.md) połączenia, który zawiera dwie poprzednie zasady próbki z nazwanych wartości, można zobaczyć dwa `set-header` zasad przy użyciu wartości właściwości, wstawić, a także wyrażenie zasad Ocena właściwości, który zawiera wyrażenie zasad.

![Śledzenie inspektora interfejsów API][api-management-api-inspector-trace]

Podczas gdy wartości właściwości mogą zawierać wyrażenia zasad, wartości właściwości nie może zawierać innych nazwanej wartości. Jeśli tekst zawierający odwołanie do właściwości jest używany dla wartości właściwości, takie jak `Property value text {{MyProperty}}`, odwołania do tej właściwości nie można zastąpić i będą dołączane jako część wartości właściwości.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o pracy z tymi zasadami
  * [Zasady usługi API Management](api-management-howto-policies.md)
  * [Dokumentacja zasad](/azure/api-management/api-management-policies)
  * [Wyrażenia zasad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

