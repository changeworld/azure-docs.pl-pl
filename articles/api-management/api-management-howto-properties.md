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
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260925"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Jak używać nazwanych wartości w zasadach usługi Azure API Management

Zasady zarządzania interfejsami API są zaawansowane możliwości systemu, które umożliwiają witrynie Azure Portal, aby zmienić zachowanie interfejsu API za pomocą konfiguracji. Zasady to zbiór instrukcji, które są wykonywane sekwencyjnie podczas żądania lub odpowiedzi interfejsu API. Instrukcje zasad mogą być konstruowane przy użyciu dosłownych wartości tekstowych, wyrażeń zasad i nazwanych wartości.

Każde wystąpienie usługi zarządzania interfejsami API ma kolekcję par klucz/wartość, która jest nazywana wartościami o nazwie, które są globalne dla wystąpienia usługi. Nie ma nałożonego limitu liczby elementów w kolekcji. Nazwane wartości mogą służyć do zarządzania wartościami ciągów stałych we wszystkich konfiguracjach i zasadach interfejsu API. Każda nazwana wartość może mieć następujące atrybuty:

| Atrybut      | Typ            | Opis                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | ciąg          | Służy do odwoływania się do nazwanej wartości w zasadach. Ciąg od jednego do 256 znaków. Dozwolone są tylko litery, cyfry, kropki i kreski. |
| `Value`        | ciąg          | Wartość rzeczywista. Nie może być pusty lub składać się tylko z odstępów. Maksymalnie 4096 znaków.                                        |
| `Secret`       | wartość logiczna         | Określa, czy wartość jest kluczem tajnym i powinny być szyfrowane, czy nie.                                                               |
| `Tags`         | tablica ciągów | Służy do filtrowania nazwanej listy wartości. Do 32 tagów.                                                                                    |

![Nazwane wartości](./media/api-management-howto-properties/named-values.png)

Nazwane wartości mogą zawierać ciągi literałów i [wyrażenia zasad](/azure/api-management/api-management-policy-expressions). Na przykład wartość `Expression` jest wyrażeniem zasad, które zwraca ciąg zawierający bieżącą datę i godzinę. Nazwana `Credential` wartość jest oznaczona jako klucz tajny, więc jego wartość nie jest domyślnie wyświetlana.

| Nazwa       | Wartość                      | Wpis tajny | Tagi          |
| ---------- | -------------------------- | ------ | ------------- |
| Wartość      | 42                         | False  | życiowe liczby |
| Poświadczenie | ••••••••••••••••••••••     | True   | security      |
| Wyrażenie | @(DateTime.Now.ToString()) | False  |               |

> [!NOTE]
> Zamiast nazwanych wartości przechowywanych w usłudze zarządzania interfejsami API można używać wartości przechowywanych w usłudze [Usługi Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) jak pokazano w tym [przykładzie.](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)

## <a name="to-add-and-edit-a-named-value"></a>Aby dodać i edytować nazwaną wartość

![Dodawanie nazwanej wartości](./media/api-management-howto-properties/add-property.png)

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Wybierz **pozycję Nazwane wartości**.
3. Naciśnij **+Dodaj**.

    Nazwa i wartość są wartościami wymaganymi. Jeśli wartość jest kluczem tajnym, zaznacz pole wyboru _To jest tajne._ Wprowadź jeden lub więcej tagów opcjonalnych, aby ułatwić organizowanie nazwanych wartości, a następnie kliknij przycisk Zapisz.

4. Kliknij przycisk **Utwórz**.

Po utworzeniu nazwanej wartości można ją edytować, klikając ją, klikając ją. Jeśli zmienisz nazwę nazwanej wartości, wszystkie zasady, które odwołują się do nazwanej wartości są automatycznie aktualizowane w celu użycia nowej nazwy.

Aby uzyskać informacje na temat edytowania nazwanej wartości za pomocą interfejsu API REST, zobacz [Edytowanie nazwanej wartości za pomocą interfejsu API REST](/rest/api/apimanagement/2019-12-01/property?patch).

## <a name="to-delete-a-named-value"></a>Aby usunąć nazwaną wartość

Aby usunąć nazwaną wartość, kliknij przycisk **Usuń** obok nazwanej wartości, aby usunąć.

> [!IMPORTANT]
> Jeśli nazwana wartość jest odwoływana przez wszystkie zasady, nie będzie można jej pomyślnie usunąć, dopóki nie usuniesz nazwanej wartości ze wszystkich zasad, które jej używają.

Aby uzyskać informacje na temat usuwania nazwanej wartości za pomocą interfejsu API REST, zobacz [Usuwanie nazwanej wartości przy użyciu interfejsu API REST](/rest/api/apimanagement/2019-12-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Aby wyszukać i filtrować nazwane wartości

Karta **Nazwane wartości** zawiera funkcje wyszukiwania i filtrowania ułatwiające zarządzanie nazwanymi wartościami. Aby filtrować listę nazwanych wartości według nazwy, wprowadź wyszukiwany termin w polu tekstowym **właściwości Wyszukiwanie.** Aby wyświetlić wszystkie nazwane wartości, wyczyść pole tekstowe **właściwości Wyszukaj** i naciśnij klawisz Enter.

Aby filtrować listę według znaczników, wprowadź jeden lub więcej znaczników w polach tekstowych **Filtruj według znaczników.** Aby wyświetlić wszystkie nazwane wartości, wyczyść pole **tekstowe Filtruj według znaczników** i naciśnij klawisz Enter.

## <a name="to-use-a-named-value"></a>Aby użyć nazwanej wartości

Aby użyć nazwanej wartości w zasadach, umieść jej nazwę `{{ContosoHeader}}`wewnątrz podwójnej pary nawiasów klamrowych, takiej jak pokazano w poniższym przykładzie:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

W tym `ContosoHeader` przykładzie jest używany jako nazwa `set-header` nagłówka `ContosoHeaderValue` w zasadach i jest używany jako wartość tego nagłówka. Gdy ta zasada jest oceniana podczas żądania lub `{{ContosoHeader}}` `{{ContosoHeaderValue}}` odpowiedzi na bramę usługi API Management i są zastępowane odpowiednimi wartościami.

Nazwane wartości mogą być używane jako pełne wartości atrybutów lub elementów, jak pokazano w poprzednim przykładzie, ale mogą być również wstawiane do lub połączone z częścią dosłownego wyrażenia tekstowego, jak pokazano w poniższym przykładzie:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Nazwane wartości mogą również zawierać wyrażenia zasad. W poniższym przykładzie `ExpressionProperty` jest używany.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Po dokonaniu oceny `{{ExpressionProperty}}` ta zasada jest `@(DateTime.Now.ToString())`zastępowana jej wartością: . Ponieważ wartość jest wyrażeniem zasad, wyrażenie jest oceniane, a zasada jest kontynuowana z jego wykonaniem.

Można to przetestować w portalu dewelopera, wywołując operację, która ma zasady z nazwanymi wartościami w zakresie. W poniższym przykładzie operacja jest wywoływana `set-header` z dwóch poprzednich przykładowych zasad z nazwanymi wartościami. Należy zauważyć, że odpowiedź zawiera dwa niestandardowe nagłówki, które zostały skonfigurowane przy użyciu zasad z nazwanymi wartościami.

![Portal dla deweloperów][api-management-send-results]

Jeśli spojrzeć na [śledzenia Inspektora interfejsu API](api-management-howto-api-inspector.md) dla wywołania, który zawiera dwie `set-header` poprzednie przykładowe zasady z nazwanych wartości, można zobaczyć dwie zasady z wstawionymi nazwanymi wartościami, a także oceny wyrażenia zasad dla nazwanej wartości, która zawierała wyrażenie zasad.

![Śledzenie inspektora API][api-management-api-inspector-trace]

Nazwane wartości mogą zawierać wyrażenia zasad, ale nie mogą zawierać innych nazwanych wartości. Jeśli dla wartości używany jest tekst zawierający odwołanie `Text: {{MyProperty}}`do nazwanej wartości, na przykład , odwołanie to nie zostanie rozpoznane i zastąpione.

## <a name="next-steps"></a>Następne kroki

-   Dowiedz się więcej o pracy z zasadami
    -   [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
    -   [Dokumentacja zasad](/azure/api-management/api-management-policies)
    -   [Wyrażenia zasad](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
