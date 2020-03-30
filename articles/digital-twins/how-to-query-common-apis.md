---
title: Typowe wzorce zapytań — cyfrowe bliźniacze platformy Azure | Dokumenty firmy Microsoft
description: Poznaj kilka typowych wzorców zapytań interfejsu API dla interfejsów API zarządzania usługą Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 133c0e0dcc07afb85a0f3af9ae51d2207abac293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589117"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Jak wysyłać zapytania do interfejsów API usług Azure Digital Twins w przypadku typowych zadań

W tym artykule przedstawiono wzorce zapytań ułatwiające wykonywanie typowych scenariuszy dla wystąpienia usługi Azure Digital Twins. Zakłada się, że wystąpienie digital twins jest już uruchomiony. Można użyć dowolnego klienta REST, takiego jak Listonosz. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Kwerendy dotyczące spawów i typów

W tej sekcji przedstawiono przykładowe zapytania, aby uzyskać więcej informacji na temat aprowizowanych przestrzeni. Wysyłaj uwierzytelnione żądania GET HTTP za pomocą przykładowych zapytań, zastępując symbole zastępcze wartościami z konfiguracji. 

- Pobierz spacje, które są węzłami głównymi.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Uzyskaj miejsce według nazwy i uwzględnij urządzenia, czujniki, obliczone wartości i wartości czujników. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Pobierz miejsca i ich informacje o urządzeniu/czujniku, którego elementem nadrzędnym jest dany identyfikator miejsca i które znajdują się na poziomach od dwóch do pięciu [w stosunku do danej przestrzeni.](how-to-navigate-apis.md#api-navigation) 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Uzyskaj miejsce z podanym identyfikatorem i uwzględnij wartości obliczone i czujniki.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Pobierz klucze właściwości dla określonego miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Pobierz spacje z kluczem właściwości o nazwie *AreaInSqMeters,* a jego wartość wynosi 30. Można również wykonać operacje ciągu, na przykład uzyskać `name = X contains Y`spacje zawierające klucz właściwości z .

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Pobierz wszystkie nazwy z nazwą *Temperatura* i skojarzone zależności i ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Kwerendy dotyczące ról i przypisań ról

W tej sekcji przedstawiono niektóre zapytania, aby uzyskać więcej informacji na temat ról i ich przydziałów. 

- Pobierz wszystkie role obsługiwane przez usługę Azure Digital Twins.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Pobierz wszystkie przypisania ról w wystąpieniu cyfrowych bliźniąt. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Pobierz przypisania ról na określonej ścieżce.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Zapytania dotyczące urządzeń

W tej sekcji przedstawiono kilka przykładów, w jaki sposób można użyć interfejsów API zarządzania, aby uzyskać określone informacje o urządzeniach. Wszystkie wywołania interfejsu API muszą być uwierzytelnione GET HTTP żądań.

- Pobierz wszystkie urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Znajdź wszystkie stany urządzeń.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Uzyskaj konkretne urządzenie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Pobierz wszystkie urządzenia dołączone do przestrzeni głównej.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Wszystkie urządzenia są podłączane do przestrzeni na poziomach od 2 do 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Pobierz wszystkie urządzenia bezpośrednio podłączone do określonego identyfikatora miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Pobierz wszystkie urządzenia dołączone do określonej przestrzeni i jej elementów podrzędnych.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Pobierz wszystkie urządzenia dołączone do elementów podrzędnych spacji, z wyłączeniem tej przestrzeni.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Pobierz wszystkie urządzenia podłączone do bezpośrednich dzieci przestrzeni.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia dołączone do jednego z przodków przestrzeni.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia dołączone do elementów podrzędnych spacji, które są na poziomie mniejszym lub równym 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Pobierz wszystkie urządzenia podłączone do pomieszczeń, które znajdują się na tym samym poziomie co miejsce z *identyfikatorem YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Pobierz ciąg połączenia urządzenia Usługi IoT Hub dla urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Pobierz urządzenie z podanym identyfikatorem sprzętu, w tym podłączonymi czujnikami.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Pobierz czujniki dla poszczególnych typów danych, w tym przypadku *Ruchu* i *temperatury*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Zapytania dotyczące matcherów i funkcji zdefiniowanych przez użytkownika 

- Pobierz wszystkie aprowizacji matchers i ich identyfikatory.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Uzyskaj szczegółowe informacje o określonej matcher, w tym spacje i funkcja zdefiniowana przez użytkownika skojarzone z nim.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Oceń matcher względem czujnika i włącz rejestrowanie do celów debugowania. Zwrot tego komunikatu HTTP GET informuje, czy matcher i czujnik należą do typu danych. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Uzyskaj identyfikator funkcji zdefiniowanych przez użytkownika. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Pobierz zawartość określonej funkcji zdefiniowanej przez użytkownika 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Zapytania dla użytkowników

W tej sekcji przedstawiono niektóre przykładowe zapytania interfejsu API dotyczące zarządzania użytkownikami w usłudze Azure Digital Twins. Należy ustawić żądanie HTTP GET, zastępując symbole zastępcze wartościami z konfiguracji. 

- Pochówek wszystkich użytkowników. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Uzyskaj określonego użytkownika.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API zarządzania, przeczytaj artykuł [Uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).

Aby dowiedzieć się więcej o punktach końcowych interfejsu API, przeczytaj [artykuł Jak używać funkcji Digital Twins Swagger](./how-to-use-swagger.md).
