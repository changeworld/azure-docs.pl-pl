---
title: Jak ustawić lub edytować zasady usługi Azure API Management | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak ustawić lub edytować zasady usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071706"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Jak ustawić i edytować zasady usługi Azure API Management

Definicja zasad jest dokumentem XML, który opisuje sekwencję instrukcji przychodzących i wychodzących. Kod XML można edytować bezpośrednio w oknie definicji. Można również wybrać wstępnie zdefiniowane zasady z listy, która jest podana po prawej stronie okna zasad. Instrukcje mające zastosowanie do bieżącego zakresu są włączone i wyróżnione. Kliknięcie włączonej instrukcji dodaje odpowiedni kod XML w lokalizacji kursora w widoku definicji. 

Aby uzyskać szczegółowe informacje na temat zasad, zobacz [Zasady w usłudze Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ustawianie lub edytowanie zasad

Aby ustawić lub edytować zasady, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi APIM.
3. Kliknij kartę **Interfejsy API**.

    ![Edycja zasad](./media/set-edit-policies/code-editor.png)

4. Wybierz jeden z wcześniej zaimportowanych interfejsów API.
5. Wybierz kartę **Projekt**.
6. Wybierz operację, do której chcesz zastosować zasady. Jeśli chcesz zastosować zasadę do wszystkich operacji, wybierz opcję **Wszystkie operacje**.
7. Wybierz **</>** ikonę (edytor kodu) w sekcji **Przetwarzanie przychodzące** lub **Przetwarzanie wychodzące.**
8. Wklej żądany kod zasad do jednego z odpowiednich bloków.

    ```XML
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Konfigurowanie zakresu

Zasady można skonfigurować globalnie lub w zakresie produktu, interfejsu API lub operacji. Aby rozpocząć konfigurowanie zasad, należy najpierw wybrać zakres, w którym zasady powinny być stosowane.

Zakresy zasad są oceniane w następującej kolejności:

1. Zakres globalny
2. Zakres produktu
3. Zakres interfejsu API
4. Zakres działania

Instrukcje w ramach zasad są oceniane `base` zgodnie z położeniem elementu, jeśli jest obecny. Zasady globalne nie ma zasad `<base>` nadrzędnych i przy użyciu elementu w nim nie ma wpływu.

Aby wyświetlić zasady w bieżącym zakresie w edytorze zasad, kliknij pozycję **Przeliczanie zasad efektywnych dla wybranego zakresu**.

### <a name="global-scope"></a>Zakres globalny

Zakres globalny jest skonfigurowany dla **wszystkich interfejsów API** w wystąpieniu interfejsu APIM.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do wystąpienia interfejsu APIM.
2. Kliknij **pozycję Wszystkie interfejsy API**.

    ![Zakres globalny](./media/api-management-howto-policies/global-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

    Zmiany są natychmiast propagowane do bramy zarządzania interfejsami API.

### <a name="product-scope"></a>Zakres produktu

Zakres produktu jest skonfigurowany dla wybranego produktu.

1. Kliknij **pozycję Produkty**.

    ![Zakres produktu](./media/api-management-howto-policies/product-scope.png)

2. Wybierz produkt, do którego chcesz zastosować zasady.
3. Kliknij **pozycję Zasady**.
4. Dodawanie lub edytowanie zasad.
5. Naciśnij pozycję **Zapisz**. 

### <a name="api-scope"></a>Zakres interfejsu API

Zakres interfejsu API jest skonfigurowany dla **wszystkich operacji** wybranego interfejsu API.

1. Wybierz **interfejs API,** do którego chcesz zastosować zasady.

    ![Zakres interfejsu API](./media/api-management-howto-policies/api-scope.png)

2. Wybierz **wszystkie operacje**
3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

### <a name="operation-scope"></a>Zakres działania 

Zakres działania jest skonfigurowany dla wybranej operacji.

1. Wybierz **interfejs API**.
2. Wybierz operację, do której chcesz zastosować zasady.

    ![Zakres działania](./media/api-management-howto-policies/operation-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

## <a name="next-steps"></a>Następne kroki

Zobacz następujące tematy pokrewne:

+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
