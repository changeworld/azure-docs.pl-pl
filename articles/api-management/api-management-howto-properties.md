---
title: Sposób użycia właściwości w ramach zasad usługi Azure API Management
description: Dowiedz się, jak używać właściwości w ramach zasad usługi Azure API Management.
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
ms.openlocfilehash: e0559380f6d686a4e559779c4271ea85106558d6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28197116"
---
# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Sposób użycia właściwości w ramach zasad usługi Azure API Management
Zarządzanie interfejsami API zasady są zaawansowanych możliwości systemu, który umożliwia portalu Azure do zmiany zachowania interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Deklaracji zasad może być skonstruowany przy użyciu wartości tekstowe literału, wyrażenie zasad i właściwości. 

Każde wystąpienie usługi Zarządzanie interfejsami API ma zbiór właściwości pary klucz wartość, które są globalne do wystąpienia usługi. Te właściwości mogą służyć do zarządzania stałych wartości ciągów we wszystkich Konfiguracja interfejsu API i zasady. Każda właściwość może mieć następujące atrybuty:

| Atrybut | Typ | Opis |
| --- | --- | --- |
| Nazwa wyświetlana |ciąg |Ciąg alfanumeryczny używany jako odwołanie do właściwości w zasadach. |
| Wartość |ciąg |Wartość właściwości. Nie może być pusta ani zawierać tylko odstępu. |
|Wpis tajny|wartość logiczna|Określa, czy wartość jest klucz tajny i powinny być szyfrowane, czy nie.|
| Tagi |Tablica ciągów |Opcjonalnie tagi, które udostępniane może służyć do filtrowania listy właściwości. |

![Nazwane wartości](./media/api-management-howto-properties/named-values.png)

Wartości właściwości mogą zawierać ciągi literału i [wyrażenie zasad](https://msdn.microsoft.com/library/azure/dn910913.aspx). Na przykład wartość `ExpressionProperty` jest wyrażenie zasad, która zwraca ciąg zawierający bieżącą datę i godzinę. Właściwość `ContosoHeaderValue` jest oznaczona jako klucz tajny, więc jego wartość nie jest wyświetlana.

| Name (Nazwa) | Wartość | Wpis tajny | Tagi |
| --- | --- | --- | --- |
| ContosoHeader |trackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>Można dodawać i edytować właściwości

![Dodaj właściwość](./media/api-management-howto-properties/add-property.png)

1. Wybierz **interfejsów API** zgodnie z **zarządzanie interfejsami API**.
2. Wybierz **nazwane wartości**.
3. Naciśnij klawisz **+ Dodaj**.

  Nazwy i wartości są wymagane wartości. Jeśli wartość tej właściwości jest klucz tajny, sprawdź, czy jest to tajny wyboru. Wprowadź jeden lub więcej tagów opcjonalne, aby ułatwić organizowanie właściwości, a następnie kliknij przycisk Zapisz.
4. Kliknij przycisk **Utwórz**.

Po utworzeniu właściwości można edytować go, klikając właściwości. Jeśli zmienisz nazwę właściwości, wszystkie zasady, które odwołują się do tej właściwości są automatycznie aktualizowane do użycia nowej nazwy.

Informacje dotyczące edytowania właściwości przy użyciu interfejsu API REST, zobacz [Edytuj właściwości przy użyciu interfejsu API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Aby usunąć właściwość

Aby usunąć właściwość, kliknij przycisk **usunąć** obok właściwości do usunięcia.

> [!IMPORTANT]
> Jeśli właściwość odwołuje się do niego żadnych zasad, nie można pomyślnie usunąć przed usunięciem właściwości z wszystkich zasad, które go używają.
> 
> 

Aby uzyskać informacje na temat usuwania właściwości przy użyciu interfejsu API REST, zobacz [usunąć właściwości przy użyciu interfejsu API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Do wyszukiwania i filtrowania właściwości

**Nazwane wartości** karta zawiera wyszukiwania i filtrowania funkcje ułatwiające zarządzanie właściwości. Aby filtrować listę właściwości według nazwy właściwości, wpisz wyszukiwany termin w **wyszukiwania właściwości** pola tekstowego. Aby wyświetlić wszystkie właściwości, wyczyść **wyszukiwania właściwości** pole tekstowe i naciśnij klawisz enter.

Aby filtrować listę właściwości, według wartości tagów, wprowadź jeden lub więcej tagów do **Filtruj według znaczników** pola tekstowego. Aby wyświetlić wszystkie właściwości, wyczyść **Filtruj według znaczników** pole tekstowe i naciśnij klawisz enter.

## <a name="to-use-a-property"></a>Aby użyć właściwości

Aby użyć właściwości w zasadach, umieść nazwą właściwości wewnątrz podwójne pary nawiasów klamrowych, takich jak `{{ContosoHeader}}`, jak pokazano w poniższym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym przykładzie `ContosoHeader` jest używana jako nazwa nagłówka w `set-header` zasad, a `ContosoHeaderValue` jest używany jako wartość nagłówka. Gdy ta zasada jest oceniane podczas żądania lub odpowiedzi na bramie usługi API Management `{{ContosoHeader}}` i `{{ContosoHeaderValue}}` są zamieniane na ich wartości odpowiednich właściwości.

Właściwości mogą być używane jako atrybut pełną lub wartości elementów, jak pokazano w poprzednim przykładzie, ale również mogą zostać wstawione do albo połączone z częścią wyrażenia literału tekstu, jak pokazano w poniższym przykładzie:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Właściwości może również zawierać wyrażenie zasad. W poniższym przykładzie `ExpressionProperty` jest używany.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

W przypadku oceny tych zasad `{{ExpressionProperty}}` zastępuje z wartością: `@(DateTime.Now.ToString())`. Ponieważ wartość jest wyrażenie zasad, wyrażenie jest obliczane i zasady kontynuuje jego wykonywania.

Ten limit można sprawdzić w portalu dla deweloperów, wywołując operację, która ma zasady z właściwości w zakresie. W poniższym przykładzie, operacja jest wywoływana z dwóch poprzedni przykład `set-header` zasad przy użyciu właściwości. Należy pamiętać, że odpowiedź zawiera dwa Nagłówki niestandardowe, które zostały skonfigurowane przy użyciu zasad z właściwościami.

![Portal dla deweloperów][api-management-send-results]

Jeśli przyjrzymy się [inspektora interfejsu API śledzenia](api-management-howto-api-inspector.md) dla wywołaniu, które obejmuje dwie zasady poprzedniej próbki z właściwościami, można wyświetlić dwa `set-header` zasady z wartościami właściwości dodaje oraz ocena wyrażenie zasad dla właściwości, który zawiera wyrażenie zasad.

![Interfejs API inspektora śledzenia][api-management-api-inspector-trace]

Gdy wartości właściwości mogą zawierać wyrażenia zasad, wartości właściwości nie może zawierać inne właściwości. Jeśli tekst zawierający odwołania do właściwości jest używana do wartości właściwości, takie jak `Property value text {{MyProperty}}`, odwołania do tej właściwości nie można zamienić i zostaną zawarte w ramach wartości właściwości.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o pracy z zasadami
  * [Zasady w usłudze API Management](api-management-howto-policies.md)
  * [Dokumentacja zasad](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Wyrażenia zasad](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

