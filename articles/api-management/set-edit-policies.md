---
title: Ustawianie lub edytowanie zasad usługi Azure API Management | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak można ustawiać lub edytować zasady usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097201"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Ustawianie lub edytowanie zasad usługi Azure API Management

Definicja zasad jest dokument XML, który opisuje sekwencji instrukcji dla ruchu przychodzącego i wychodzącego. Plik XML można edytować bezpośrednio w oknie definicji. Możesz również wybrać wstępnie zdefiniowanych zasad z listy, który znajduje się po prawej stronie okna zasady. Instrukcje, które są stosowane do bieżącego zakresu są włączone i wyróżnione. Kliknięcie instrukcji włączone powoduje dodanie odpowiedniego pliku XML w lokalizacji kursora w definicji widoku. 

Aby uzyskać szczegółowe informacje na temat zasad, zobacz [zasad w usłudze Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Ustawianie lub edytowanie zasad

Można ustawiać lub edytować zasady, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do swojego wystąpienia usługi APIM.
3. Kliknij kartę **Interfejsy API**.

    ![Edycja zasad](./media/set-edit-policies/code-editor.png)

4. Wybierz jeden z wcześniej zaimportowanych interfejsów API.
5. Wybierz kartę **Projekt**.
6. Wybierz operację, do której chcesz zastosować zasady. Aby zastosować zasady do wszystkich operacji, należy zaznaczyć **wszystkie operacje**.
7. Wybierz **</>** ikonę (Edytor kodu) w **przychodzące przetwarzanie** lub **przetwarzanie danych wychodzących** sekcji.
8. Wklej kod żądane zasady do jednego z odpowiednich bloków.
         
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

Zasady można skonfigurować globalnie lub w zakresie produktu, interfejsu API lub operacji. Aby rozpocząć konfigurowanie zasad, musisz najpierw wybrać zakres, w którym mają być stosowane zasady.

Zakresy zasad są obliczane w następującej kolejności:

1. Globalne
2. Zakres produktu
3. Zakres interfejsu API
4. Zakres operacji

Instrukcje w ramach zasad są oceniane według położenia `base` elementu, jeśli jest obecny. Zasady globalne nie ma elementu nadrzędnego zasad i za pomocą `<base>` element w nim nie ma wpływu.

Aby wyświetlić zasady w bieżącym zakresie w edytorze zasad, kliknij **ponownie Oblicz efektywnych zasad dla wybranego zakresu**.

### <a name="global-scope"></a>Globalne

Globalny zakres jest skonfigurowany dla **wszystkie interfejsy API** w swojego wystąpienia usługi APIM.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i przejdź do swojego wystąpienia usługi APIM.
2. Kliknij przycisk **wszystkie interfejsy API**.

    ![Globalne](./media/api-management-howto-policies/global-scope.png)

3. Kliknij ikonę trójkąt.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

    Zmiany są natychmiast propagowane do bramy usługi API Management.

### <a name="product-scope"></a>Zakres produktu

Zakres produktu jest skonfigurowany dla wybranego produktu.

1. Kliknij przycisk **produktów**.

    ![Zakres produktu](./media/api-management-howto-policies/product-scope.png)

2. Wybierz produkt, do której chcesz zastosować zasady.
3. Kliknij przycisk **zasady**.
4. Dodawanie lub edytowanie zasad.
5. Naciśnij pozycję **Zapisz**. 

### <a name="api-scope"></a>Zakres interfejsu API

Zakres interfejsu API jest skonfigurowany dla **wszystkie operacje** wybranego interfejsu API.

1. Wybierz **API** mają dotyczyć zasady.

    ![Zakres interfejsu API](./media/api-management-howto-policies/api-scope.png)

2. Wybierz pozycję **Wszystkie operacje**.
3. Kliknij ikonę trójkąt.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

### <a name="operation-scope"></a>Zakres operacji 

Zakres operacji jest skonfigurowany dla wybranej operacji.

1. Wybierz **API**.
2. Wybierz operację, aby stosować zasady umożliwiające.

    ![Zakres operacji](./media/api-management-howto-policies/operation-scope.png)

3. Kliknij ikonę trójkąt.
4. Wybierz pozycję **Edytor kodu**.
5. Dodawanie lub edytowanie zasad.
6. Naciśnij pozycję **Zapisz**. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz następujące tematy pokrewne:

+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)