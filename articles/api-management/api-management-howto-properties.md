---
title: Jak używać nazwanych wartości w zasadach usługi Azure API Management
description: Dowiedz się, jak używać nazwanych wartości w zasadach usługi Azure API Management.
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
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: 46f4e1b3df5f1c77a57d432297685d6d1a0a14a8
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405803"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak używać nazwanych wartości w zasadach usługi Azure API Management

Zasady API Management są zaawansowaną możliwością systemu, która umożliwia Azure Portal zmianę zachowania interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Instrukcje zasad można utworzyć przy użyciu literałów wartości tekstowych, wyrażeń zasad i nazwanych wartości.

Każde wystąpienie usługi API Management ma kolekcję właściwości par klucz/wartość, która jest nazywana wartościami nazwanymi, które są globalne dla wystąpienia usługi. Nie ma żadnego narzuconego limitu liczby elementów w kolekcji. Nazwane wartości mogą służyć do zarządzania stałymi wartościami ciągu w ramach wszystkich konfiguracji i zasad interfejsu API. Każda nazwana wartość może mieć następujące atrybuty:

| Atrybut      | Type            | Opis                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | ciąg          | Służy do odwoływania się do właściwości w zasadach. Ciąg od jednego do 256 znaków. Dozwolone są tylko litery, cyfry, kropki i kreski. |
| `Value`        | ciąg          | Wartość rzeczywista. Nie może być pusty ani zawierać tylko odstępów. Maksymalnie 4096 znaków.                                     |
| `Secret`       | boolean         | Określa, czy wartość jest kluczem tajnym i powinna być szyfrowana, czy nie.                                                            |
| `Tags`         | tablica ciągów | Służy do filtrowania listy właściwości. Do 32 tagów.                                                                                    |

![Nazwane wartości](./media/api-management-howto-properties/named-values.png)

Nazwane wartości mogą zawierać ciągi literałów i [wyrażenia zasad](/azure/api-management/api-management-policy-expressions). Na przykład wartość `Expression` jest wyrażenie zasad, które zwraca ciąg zawierający bieżącą datę i godzinę. Nazwana wartość `Credential` jest oznaczona jako wpis tajny, więc jej wartość nie jest domyślnie wyświetlana.

| Name (Nazwa)       | Value                      | Wpis tajny | `Tags`          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False  | Najważniejsze wartości |
| Poświadczenie | ••••••••••••••••••••••     | Prawda   | zabezpieczenia      |
| Wyrażenie | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Aby dodać i edytować Właściwość

![Dodaj właściwość](./media/api-management-howto-properties/add-property.png)

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz **nazwane wartości**.
3. Naciśnij klawisze **+ Dodaj**.

    Wartości Name i value są wymagane. Jeśli ta wartość właściwości jest kluczem tajnym, zaznacz pole wyboru to jest wpisem tajnym. Wprowadź jeden lub więcej tagów opcjonalnych, aby pomóc w organizowaniu nazwanych wartości, a następnie kliknij przycisk Zapisz.

4. Kliknij przycisk **Utwórz**.

Po utworzeniu właściwości można ją edytować, klikając właściwość. Jeśli zmienisz nazwę właściwości, wszystkie zasady, które odwołują się do tej właściwości są automatycznie aktualizowane, aby użyć nowej nazwy.

Aby uzyskać informacje na temat edytowania właściwości przy użyciu interfejsu API REST, zobacz [Edytowanie właściwości przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Aby usunąć Właściwość

Aby usunąć właściwość, kliknij przycisk **Usuń** obok właściwości do usunięcia.

> [!IMPORTANT]
> Jeśli do właściwości odwołują się zasady, nie będzie można jej pomyślnie usunąć do momentu usunięcia właściwości ze wszystkich zasad, które go używają.

Aby uzyskać informacje dotyczące usuwania właściwości przy użyciu interfejsu API REST, zobacz [usuwanie właściwości przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Aby wyszukać i filtrować nazwane wartości

Karta **nazwane wartości** zawiera funkcje wyszukiwania i filtrowania, które ułatwiają zarządzanie nazwanymi wartościami. Aby odfiltrować listę właściwości według nazwy właściwości, wprowadź wyszukiwany termin w polu tekstowym **Właściwości wyszukiwania** . Aby wyświetlić wszystkie nazwane wartości, usuń zaznaczenie pola tekstowego **Właściwości wyszukiwania** i naciśnij klawisz ENTER.

Aby filtrować listę właściwości według wartości tagów, wprowadź jeden lub więcej tagów do pola tekstowego **Filtruj według tagów** . Aby wyświetlić wszystkie nazwane wartości, usuń zaznaczenie pola tekstowego **Filtruj według tagów** i naciśnij klawisz ENTER.

## <a name="to-use-a-property"></a>Aby użyć właściwości

Aby użyć właściwości w zasadach, umieść ją w podwójnej parze nawiasów klamrowych `{{ContosoHeader}}`, jak pokazano w następującym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym przykładzie `ContosoHeader` jest używany jako nazwa nagłówka `set-header` w zasadach i `ContosoHeaderValue` jest używany jako wartość tego nagłówka. Kiedy te zasady są oceniane podczas żądania lub odpowiedzi bramy `{{ContosoHeader}}` API Management i `{{ContosoHeaderValue}}` są zastępowane odpowiednimi wartościami właściwości.

Nazwane wartości mogą być używane jako kompletne wartości atrybutów lub elementów, jak pokazano w poprzednim przykładzie, ale mogą być również wstawiane do lub połączone z częścią wyrażenia tekstu literału, jak pokazano w następującym przykładzie:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Nazwane wartości mogą również zawierać wyrażenia zasad. W poniższym przykładzie `ExpressionProperty` jest używany.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Jeśli te zasady zostaną ocenione, `{{ExpressionProperty}}` zostaną zastąpione jej wartością: `@(DateTime.Now.ToString())`. Ponieważ wartość jest wyrażeniem zasad, wyrażenie jest oceniane i zasady są wykonywane w ramach jego wykonania.

Możesz to przetestować w portalu dla deweloperów, wywołując operację, która ma zasady z nazwanymi wartościami w zakresie. W poniższym przykładzie operacja jest wywoływana z dwiema poprzednimi przykładowymi `set-header` zasadami z nazwanymi wartościami. Należy zauważyć, że odpowiedź zawiera dwa niestandardowe nagłówki, które zostały skonfigurowane przy użyciu zasad z nazwanymi wartościami.

![Portal deweloperów][api-management-send-results]

Jeśli przeszukiwany jest [ślad inspektora interfejsu API](api-management-howto-api-inspector.md) dla wywołania, które obejmuje dwie poprzednie przykładowe zasady z nazwanymi wartościami, można zobaczyć dwie `set-header` zasady z wartościami właściwości wstawionymi, a także obliczyć wyrażenie zasad dla właściwości, która zawiera wyrażenie zasad.

![Ślad Inspektora interfejsów API][api-management-api-inspector-trace]

Chociaż wartości właściwości mogą zawierać wyrażenia zasad, wartości właściwości nie mogą zawierać innych nazwanych wartości. Jeśli tekst zawierający odwołanie do właściwości jest używany dla wartości właściwości, na przykład `Property value text {{MyProperty}}`, to odwołanie do właściwości nie zostanie zastąpione i zostanie dołączone jako część wartości właściwości.

## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej na temat pracy z zasadami
    -   [Zasady w API Management](api-management-howto-policies.md)
    -   [Dokumentacja zasad](/azure/api-management/api-management-policies)
    -   [Wyrażenia zasad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
