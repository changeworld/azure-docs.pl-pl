---
title: Jak ponownie udostępnić urządzeń w usłudze Azure IoT Hub Device Provisioning Service | Dokumentacja firmy Microsoft
description: Jak ponownie udostępnić urządzenia z urządzeniem, inicjowanie obsługi administracyjnej wystąpienia usługi
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 92680a453d93c8dc0189c6ae376449a8e7a22076
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799137"
---
# <a name="how-to-reprovision-devices"></a>Jak ponownie udostępnić urządzeń

Podczas cyklu życia rozwiązania IoT jest często przenosić urządzenia między centra IoT Hub. Przyczyny tego przeniesienia może obejmować następujące scenariusze:

* **Używanie funkcji Geolokalizacji**: Przemieszcza się w urządzeniu między lokalizacjami, sieci opóźnieniu przez urządzenie poddane migracji do usługi IoT hub bliżej do każdej lokalizacji.

* **Wielodostępność**: Urządzenie może używane w ramach tego samego rozwiązania IoT, ale ponownie przypisane lub dzierżawy do nowego klienta lub klienta lokacji. Ten nowy klient może obsłużenie za pomocą innej usługi IoT hub.

* **Rozwiązanie, zmian**: Urządzenie można przenieść do nowych lub zaktualizowanych rozwiązania IoT. To ponowne przypisywanie może wymagać, że urządzenia komunikują się za pomocą nowego centrum IoT, który jest podłączony do innych składników zaplecza. 

* **Kwarantanna**: Podobnie jak zmiany rozwiązań. Urządzenie jest nieprawidłowo, których bezpieczeństwo zostało naruszone lub nieaktualne może zostać ponownie przypisane do usługi IoT hub, gdzie to wszystko, co można zrobić aktualizacji, a wrócić w zakresie zgodności. Gdy urządzenie działa poprawnie, następnie jest migrowana do jego głównej koncentratora.

Aby uzyskać bardziej szczegółowe omówienie reprovisioning, zobacz [reprovisioning pojęcia dotyczące urządzeń usługi IoT Hub](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurowanie rejestracji zasady alokacji

Zasady alokacji określa urządzeń skojarzonych z rejestracji będzie jak przydzielone, lub przypisane do usługi IoT hub, gdy ponownie aprowizować.

Poniższe kroki skonfigurować zasady alokacji do rejestracji urządzeń:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Device Provisioning.

2. Kliknij przycisk **Zarządzanie rejestracjami**i kliknij grupę rejestracji lub rejestracji indywidualnej, który chcesz skonfigurować reprovisioning. 

3. W obszarze **wybierz sposób przypisywania urządzeń do centrów**, wybierz jedną z następujących zasad alokacji:

    * **Najmniejsze opóźnienie**: Ta zasada przypisuje urządzenia połączone Centrum IoT, który spowoduje najniższy komunikacji opóźnienia między urządzeniem i Centrum IoT Hub. Ta opcja umożliwia urządzeniu komunikowanie się z najbliższego Centrum IoT hub na podstawie lokalizacji. 
    
    * **Dystrybucja z równymi wagami**: Ta zasada rozkłada urządzeń na połączone centra IoT Hub na podstawie wagi przydziału przypisanych do każdego połączonego Centrum IoT hub. Ta zasada umożliwia załadować saldo urządzeń w grupie połączone centra oparte na alokacji wagi, ustaw w tych centrach. W przypadku aprowizowania urządzeń tylko jednego centrum IoT, firma Microsoft zaleca ustawienie. Jest to ustawienie domyślne. 
    
    * **Konfiguracja statyczna**: Te zasady wymagają wymagane Centrum IoT Hub się na liście wpis rejestracji urządzenia w celu zainicjowania obsługi administracyjnej. Ta zasada umożliwia wyznaczenie w jednym centrum IoT określonych, którą chcesz przypisać urządzenia do.

4. W obszarze **wybierz centra IoT Hub do tej grupy mogą być przypisane do**, wybierz połączone centra IoT, które mają włączone za pomocą zasady alokacji. Opcjonalnie dodaj nową połączone Centrum Iot przy użyciu **Link nowego centrum IoT Hub** przycisku.

    Za pomocą **najmniejszego opóźnienia** zasad alokacji, koncentratorów, możesz wybrać, które zostaną uwzględnione w oceny opóźnienia ustalenie najbliższe Centrum w celu przypisania urządzenia.

    Za pomocą **dystrybucja z równymi wagami** zasady alokacji urządzeń będzie równoważone w centrach należy wybrać w oparciu o ich wagi w skonfigurowanym alokacji i ich bieżące obciążenie urządzenia.

    Za pomocą **statycznie** zasady alokacji wybierz Centrum IoT hub, przypisany do urządzenia.

4. Kliknij przycisk **Zapisz**, lub przejdź do następnej sekcji, aby ustawić zasady reprovisioning.

    ![Wybierz zasady alokacji rejestracji](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ustaw zasady reprovisioning

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do swojego wystąpienia usługi Device Provisioning.

2. Kliknij przycisk **Zarządzanie rejestracjami**i kliknij grupę rejestracji lub rejestracji indywidualnej, który chcesz skonfigurować reprovisioning.

3. W obszarze **wybierz sposób dane urządzenia, które mają być obsługiwane na ponowne zainicjowanie obsługi administracyjnej do innej usługi IoT hub**, wybierz jedną z następujących zasad reprovisioning:

    * **Ponownie zainicjować obsługę administracyjną i przeprowadzić migrację danych**: Ta zasada wykonuje akcję, gdy skojarzone z wpisu rejestracji urządzenia przesłać nowe żądanie obsługi administracyjnej. W zależności od konfiguracji wpisu rejestracji urządzenia może zostać ponownie przypisane do innego Centrum IoT hub. Jeśli urządzenie ulegają zmianie centra IoT Hub, Rejestracja urządzenia w początkowej Centrum IoT zostaną usunięte. Wszystkie informacje o stanie urządzenia z tej początkowej usługi IoT hub będą migrowani do nowego centrum IoT hub. Podczas migracji stanu urządzenia będą raportowane jako **przypisywanie**

    * **Ponownie zainicjować obsługę administracyjną i zresetowane do konfiguracji początkowej**: Ta zasada wykonuje akcję, gdy skojarzone z wpisu rejestracji urządzenia przesłać nowe żądanie obsługi administracyjnej. W zależności od konfiguracji wpisu rejestracji urządzenia może zostać ponownie przypisane do innego Centrum IoT hub. Jeśli urządzenie ulegają zmianie centra IoT Hub, Rejestracja urządzenia w początkowej Centrum IoT zostaną usunięte. Dane konfiguracji początkowej, podanym inicjowania obsługi administracyjnej wystąpienia usługi odbieranych, kiedy urządzenie zostało zaaprowizowane do nowego centrum IoT hub. Podczas migracji stanu urządzenia będą raportowane jako **przypisywanie**.

4. Kliknij przycisk **Zapisz** umożliwiające reprovisioning urządzenia na podstawie wprowadzonych zmian.

    ![Wybierz zasady alokacji rejestracji](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Wyślij żądanie aprowizacji z urządzenia

W kolejności, w przypadku urządzeń do ponownego udostępnienia oparte na zmiany konfiguracji wprowadzone w poprzednich sekcjach, te urządzenia należy zażądać reprovisioning. 

Jak często urządzenie przesyła żądanie aprowizacji jest zależny od scenariusza. Jednak zaleca się do urządzenia, aby wysłać żądanie inicjowania obsługi administracyjnej do aprowizacji wystąpienia usługi o ponowne uruchomienie komputera i obsłudze programu [metoda](../iot-hub/iot-hub-devguide-direct-methods.md) do ręcznego wyzwalania, aprowizacja na żądanie. Inicjowanie obsługi administracyjnej może być też wywoływane przez ustawienie [żądaną właściwość](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Reprovisioning zasady na wpis rejestracji Określa, jak wystąpienie usługi aprowizacji urządzeń obsługuje te żądania alokacji, a jeśli dane o stanie urządzenia powinny być migrowane podczas reprovisioning. Te same zasady są dostępne dla rejestracji indywidualnych i grup rejestracji:

Na przykład inicjowania obsługi żądań z urządzenia podczas sekwencji rozruchu, zobacz kod wysyła [automatycznej aprowizacji symulowanego urządzenia](quick-create-simulated-device.md).


## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej Reprovisioning, zobacz [reprovisioning pojęcia dotyczące urządzeń usługi IoT Hub](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej anulowania zastrzeżenia, zobacz [jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego](how-to-unprovision-devices.md) 











