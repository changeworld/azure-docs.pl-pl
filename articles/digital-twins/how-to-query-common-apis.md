---
title: Azure cyfrowego Twins typowych wzorców zapytań | Dokumentacja firmy Microsoft
description: Informacje o typowych wzorców zapytań interfejsów API zarządzania Twins cyfrowych platformy Azure.
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: v-adgera
ms.openlocfilehash: 57abb005f70661d23ea90eaf801b8e5407340bb7
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462034"
---
# <a name="how-to-query-azure-digital-twins-apis-for-common-tasks"></a>Jak wykonać zapytanie interfejsów API Twins cyfrowych platformy Azure do wykonywania typowych zadań

W tym artykule przedstawiono wzorców zapytań, aby pomóc Ci w realizacji typowych scenariuszy wystąpienia Twins cyfrowych platformy Azure. Założono, że wystąpienie Twins cyfrowego jest uruchomiony. Możesz użyć dowolnego klienta REST takiego jak narzędzie Postman. 

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]


## <a name="queries-for-spaces-and-types"></a>Zapytania dotyczące miejsca do magazynowania i typów

W tej sekcji przedstawiono przykładowe zapytania, aby uzyskać więcej informacji na temat usługi udostępnione miejsca do magazynowania. Tworzenie uwierzytelnionego żądania GET HTTP z przykładowe zapytania, zastępując symbole zastępcze wartości z konfiguracji. 

- Pobierz miejsca do magazynowania, które są węzły główne.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?$filter=ParentSpaceId eq null
    ```

- Uzyskiwanie miejsca według nazwy i obejmują urządzeń, czujników, obliczonych wartości i wartości z czujników. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=Focus Room A1&includes=fullpath,devices,sensors,values,sensorsvalues
    ```

- Miejsca do magazynowania i informacji o nich czujnika/urządzenia, których elementem nadrzędnym jest identyfikator danego obszaru i które są na poziomie 2 do 5 [względem danego miejsca](how-to-navigate-apis.md#api-navigation). 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?spaceId=YOUR_SPACE_ID&includes=fullpath,devices,sensors,values,sensorsvalues&traverse=Down&minLevel=1&minRelative=true&maxLevel=5&maxRelative=true
    ```

- Uzyskiwanie miejsca z danym Identyfikatorem i zawierać obliczonych i wartości z czujników.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?ids=YOUR_SPACE_ID&includes=Values,sensors,SensorsValues
    ```

- Pobierz klucze właściwości dla określonego obszaru.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/propertykeys?spaceId=YOUR_SPACE_ID
    ```

- Pobierz miejsca do magazynowania przy użyciu klucza właściwości o nazwie *AreaInSqMeters* i jego wartość wynosi 30. Możesz również ciąg operacje, na przykład get spacje zawierającego klucz właściwości, za pomocą `name = X contains Y`.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?propertyKey=AreaInSqMeters&propertyValue=30
    ```

- Pobieranie wszystkich nazw o nazwie *temperatury* wraz ze skojarzonymi zależnościami i ontologie.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/types?names=Temperature&includes=space,ontologies,description,fullpath
    ```


## <a name="queries-for-roles-and-role-assignments"></a>Zapytania dotyczące ról i przypisań ról

W tej sekcji przedstawiono niektóre zapytania, aby uzyskać więcej informacji na temat ról i ich przypisania. 

- Pobierz wszystkie role obsługiwane przez Twins cyfrowych platformy Azure.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```

- Pobierz wszystkie przypisania roli w ramach wystąpienia cyfrowego bliźniaczych reprezentacji. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=down
    ```

- Pobieranie przypisań ról w określonej ścieżce.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments?path=/A_SPATIAL_PATH
    ```

## <a name="queries-for-devices"></a>Zapytania dotyczące urządzeń

W tej sekcji przedstawiono kilka przykładów, jak używać interfejsów API zarządzania, aby uzyskać szczegółowe informacje o urządzeniach. Wszystkie wywołania interfejsu API muszą zostać uwierzytelnione żądania GET HTTP.

- Pobierz wszystkie urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices
    ```

- Znajdowanie wszystkich stanów urządzeń.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/devices/statuses
    ```

- Uzyskaj określonego urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID
    ```

- Pobierz wszystkie urządzenia dołączone do obszaru głównego.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?maxLevel=1
    ```

- Pobierz wszystkie urządzenia dołączone do miejsca do magazynowania na poziomie 2 do 4.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4
    ```

- Pobierz wszystkie urządzenia podłączone bezpośrednio do identyfikatora dla danego miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID
    ```

- Pobierz wszystkie urządzenia podłączone do określonego miejsca i jego obiektów podrzędnych.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down
    ```

- Pobierz wszystkie urządzenia podłączone do elementów podrzędnych miejsca, z wyjątkiem miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true
    ```

- Pobierz wszystkie urządzenia podłączone do bezpośrednie elementy podrzędne miejsca.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia podłączone do jednego z elementów nadrzędnych ze spacją.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Up&maxLevel=-1&maxRelative=true
    ```

- Pobierz wszystkie urządzenia podłączone do obiektów podrzędnych, miejsca, które są poziomu mniejszy lub równy 5.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Down&maxLevel=5
    ```

- Pobierz wszystkie urządzenia dołączone do miejsca do magazynowania, znajdujących się na tym samym poziomie jak obszaru o identyfikatorze *YOUR_SPACE_ID*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?spaceId=YOUR_SPACE_ID&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true
    ```

- Pobieranie parametrów połączenia urządzenia usługi IoT Hub dla urządzenia.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_ID?includes=ConnectionString
    ```

- Pobierz urządzenie o identyfikatorze danego sprzętu, obejmujących również czujniki dołączone.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=sensors
    ```

- Pobierz czujników dla typów danych, w tym przypadku *ruchu* i *temperatury*.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/sensors?dataTypes=Motion,Temperature
    ```

## <a name="queries-for-matchers-and-user-defined-functions"></a>Zapytania dotyczące dopasowujące jednostki i funkcji zdefiniowanych przez użytkownika 

- Pobierz wszystkie aprowizowane dopasowujące jednostki i ich identyfikatorów.

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers
    ```

- Uzyskaj szczegółowe informacje dotyczące konkretnego dopasowywania, łącznie ze spacjami i funkcji zdefiniowanych przez użytkownika, skojarzone z nią.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID?includes=description, conditions, fullpath, userdefinedfunctions, space
    ```

- Oceń dopasowywania względem czujnika i Włącz rejestrowanie na potrzeby debugowania. Zwracany tego komunikatu HTTP GET informuje, czy dopasowywania i czujnik należeć do typu danych. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_ID/evaluate/YOUR_SENSOR_ID?enableLogging=true
    ```

- Pobierz identyfikator funkcje zdefiniowane przez użytkownika. 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions
    ```

- Pobieranie zawartości określonej funkcji zdefiniowanych przez użytkownika 

   ```plaintext
    YOUR_MANAGEMENT_API_URL/userdefinedfunctions/YOUR_USER_DEFINED_FUNCTION_ID/contents
    ```


## <a name="queries-for-users"></a>Zapytania dotyczące użytkowników

W tej sekcji przedstawiono niektóre przykładowe zapytania interfejsu API zarządzania użytkownikami w reprezentacji urządzeń cyfrowych platformy Azure. Upewnij się żądanie HTTP GET, zastępując symbole zastępcze wartości z konfiguracji. 

- Pobieranie wszystkich użytkowników. 

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users
    ```

- Uzyskaj określonego użytkownika.

    ```plaintext
    YOUR_MANAGEMENT_API_URL/users/ANY_USER_ID
    ```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak można uwierzytelnić przy użyciu interfejsu API zarządzania, przeczytaj [uwierzytelniania za pomocą interfejsów API](./security-authenticating-apis.md).

Aby wyświetlić wszystkie punkty końcowe interfejsu API, przeczytaj [sposób używania cyfrowego Swagger Twins](./how-to-use-swagger.md).
