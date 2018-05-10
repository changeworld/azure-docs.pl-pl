---
title: Jak ustawiać lub edytować zasady usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak można ustawiać lub edytować zasady usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: aaf86a440328e27c8c47b809536951eeaf2104b9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Jak ustawiać lub edytować zasady usługi Azure API Management

Definicja zasad jest dokumentu XML opisujący sekwencji instrukcji dla ruchu przychodzącego i wychodzącego. Plik XML można edytować bezpośrednio w oknie definicji. Wstępnie zdefiniowanych zasad można również wybrać z listy, który został dostarczony po prawej stronie okna zasady. Instrukcje dotyczące bieżącego zakresu są włączone i wyróżnione. Kliknięcie przycisku instrukcji włączone dodaje odpowiedni kod XML w lokalizacji kursora w definicji widoku. 

Aby uzyskać szczegółowe informacje na temat zasad, zobacz [zasad w usłudze Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ustawiać lub edytować zasad

Można ustawiać lub edytować zasad, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi APIM.
3. Kliknij przycisk **interfejsów API** kartę.
4. Wybierz jeden z interfejsów API, które zostały wcześniej zaimportowane.
5. Wybierz **projekt** kartę.
6. Wybierz operację, do której chcesz zastosować te zasady. Aby zastosować zasady do wszystkich operacji, należy zaznaczyć **wszystkie operacje**.
7. Kliknij trójkąt **przychodzących** lub **wychodzących** ołówki.
8. Wybierz **edytora kodu** elementu.

    ![Edycja zasad](./media/set-edit-policies/set-edit-policies01.png)

9. Wklej kod odpowiednie zasady w jednym z odpowiednie bloki.
         
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
 
## <a name="configure-scope"></a>Konfigurowanie zakresu

Zasady można skonfigurować globalnie lub w zakresie produktu, interfejsu API lub operacji. Aby rozpocząć konfigurowanie zasad, musisz wybrać zakres, w którym mają dotyczyć zasady.

Zakresy zasad są oceniane w następującej kolejności:

1. Globalne
2. Zakres produktu
3. Zakres interfejsu API
4. Operacja zakresu

Instrukcje w ramach zasady są oceniane według położenia `base` elementu, jeśli jest obecny. Globalne zasady nie ma nadrzędnego zasad i za pomocą `<base>` element w nim nie ma wpływu.

Aby wyświetlić zasady w bieżącym zakresie w edytorze zasad, kliknij **ponownie Oblicz skutecznych zasad dla wybranego zakresu**.

### <a name="global-scope"></a>Globalne

Zasięg globalny jest skonfigurowany dla **wszystkich interfejsów API** APIM wystąpienia.

1. Zaloguj się do [portalu Azure](https://portal.azure.com/) i przejdź do swojego wystąpienia APIM.
2. Kliknij przycisk **wszystkich interfejsów API**.

    ![Globalne](./media/api-management-howto-policies/global-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij klawisz **zapisać**. 

    Zmiany są propagowane do bramy usługi API Management natychmiast.

### <a name="product-scope"></a>Zakres produktu

Zakres produktu jest skonfigurowany dla wybranego produktu.

1. Kliknij przycisk **produkty**.

    ![Zakres produktu](./media/api-management-howto-policies/product-scope.png)

2. Wybierz produkt, do którego chcesz zastosować zasady.
3. Kliknij przycisk **zasad**.
4. Dodawanie lub edytowanie zasad.
5. Naciśnij klawisz **zapisać**. 

### <a name="api-scope"></a>Zakres interfejsu API

Interfejs API zakres jest skonfigurowany dla **wszystkie operacje** wybranego interfejsu API.

1. Wybierz **interfejsu API** mają dotyczyć zasady.

    ![Zakres interfejsu API](./media/api-management-howto-policies/api-scope.png)

2. Wybierz **wszystkie operacje**
3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij klawisz **zapisać**. 

### <a name="operation-scope"></a>Operacja zakresu 

Operacja zakres jest skonfigurowany dla wybranej operacji.

1. Wybierz **interfejsu API**.
2. Wybierz operację, aby stosować zasady.

    ![Operacja zakresu](./media/api-management-howto-policies/operation-scope.png)

3. Kliknij ikonę trójkąta.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij klawisz **zapisać**. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz poniższe tematy pokrewne:

+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)