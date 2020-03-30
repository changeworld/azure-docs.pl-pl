---
title: Urządzenia reprovision w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Dowiedz się, jak ponownie aprowizować urządzenia za pomocą wystąpienia usługi aprowizacji urządzeń (DPS) i dlaczego może być konieczne to zrobić.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ded494debab19daa15a953715b1ab7b0b10ad18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974908"
---
# <a name="how-to-reprovision-devices"></a>Jak ponownie aprowizji urządzeń

Podczas cyklu życia rozwiązania IoT często przenosi urządzenia między centrami IoT. Przyczyny tego przeniesienia mogą obejmować następujące scenariusze:

* **Geolokalizacja:** Gdy urządzenie przemieszcza się między lokalizacjami, opóźnienie sieci jest zwiększane przez migrację urządzenia do centrum IoT hub bliżej każdej lokalizacji.

* **Multi-dzierżawy:** Urządzenie może być używane w ramach tego samego rozwiązania IoT, ale, ponownie przypisane lub wydzierżawione nowemu klientowi lub witrynie klienta. Ten nowy klient może być obsługiwany przy użyciu innego centrum IoT hub.

* **Zmiana rozwiązania:** urządzenie może zostać przeniesione do nowego lub zaktualizowanego rozwiązania IoT. To ponowne przypisanie może wymagać, aby urządzenie komunikował się z nowym centrum IoT hub, który jest podłączony do innych składników wewnętrznej bazy danych. 

* **Kwarantanna**: Podobnie jak zmiana rozwiązania. Urządzenie, które działa nieprawidłowo, zagrożone lub nieaktualne, może zostać ponownie przypisane do centrum IoT Hub, gdzie wszystko, co może zrobić, to zaktualizować i przywrócić zgodność. Gdy urządzenie działa poprawnie, jest następnie migrowane z powrotem do głównego koncentratora.

Aby uzyskać bardziej szczegółowe omówienie ponownego obsługi administracyjnej, zobacz [Koncepcje ponownego obsługi administracyjnej urządzenia usługi IoT Hub](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>Konfigurowanie zasad alokacji rejestracji

Zasady alokacji określają sposób przydzielania lub przypisywania urządzeń skojarzonych z rejestracją do centrum IoT hub po ponownym podziale obsługi administracyjnej.

Następujące kroki konfigurują zasady alokacji dla rejestracji urządzenia:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń.

2. Kliknij **pozycję Zarządzaj rejestracjami**i kliknij grupę rejestracji lub rejestrację indywidualną, którą chcesz skonfigurować do ponownego inicjowania obsługi administracyjnej. 

3. W obszarze **Wybierz sposób przypisywania urządzeń do koncentratorów**wybierz jedną z następujących zasad alokacji:

    * **Najniższe opóźnienie:** Ta zasada przypisuje urządzenia do połączonego centrum IoT Hub, co spowoduje najniższą komunikację opóźnienia między urządzeniem a centrum IoT Hub. Ta opcja umożliwia urządzeniu komunikowanie się z najbliższym centrum IoT na podstawie lokalizacji. 
    
    * **Równomiernie ważona dystrybucja:** ta zasada dystrybuuje urządzenia w połączonych centrach IoT na podstawie wagi alokacji przypisanej do każdego połączonego centrum IoT. Ta zasada umożliwia ładowanie urządzeń w grupie połączonych koncentratorów na podstawie wag alokacji ustawionych w tych koncentratorach. Jeśli inicjujesz inicjowanie obsługi administracyjnej tylko do jednego centrum IoT Hub, zalecamy to ustawienie. Jest to ustawienie domyślne. 
    
    * **Konfiguracja statyczna:** Ta zasada wymaga, aby żądane centrum IoT Hub było wyświetlane we wpisie rejestracji dla urządzenia, które ma zostać zainicjowane. Ta zasada umożliwia wyznaczenie jednego określonego centrum IoT hub, do którego chcesz przypisać urządzenia.

4. W obszarze **Wybierz centra IoT, do których można przypisać tę grupę,** wybierz połączone centra IoT, które mają zostać uwzględnione w zasadach alokacji. Opcjonalnie dodaj nowy połączony koncentrator Iot za pomocą przycisku **Łącz nowe centrum IoT** Hub.

    W przypadku zasad alokacji **najniższych opóźnień** wybrane koncentratory zostaną uwzględnione w ocenie opóźnienia w celu określenia najbliższego koncentratora dla przypisania urządzenia.

    Dzięki **zasadom równomiernie ważonej** alokacji dystrybucji urządzenia będą równoważone obciążeniem w wybranych koncentratorach na podstawie skonfigurowanych wag alokacji i bieżącego obciążenia urządzenia.

    Za pomocą zasady alokacji **konfiguracji statycznej** wybierz centrum IoT, do którego mają być przypisane urządzenia.

4. Kliknij **przycisk Zapisz**lub przejdź do następnej sekcji, aby ustawić zasady ponownego obsługi administracyjnej.

    ![Wybieranie zasad alokacji rejestracji](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Ustawianie zasad ponownego uchodnia

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń.

2. Kliknij **pozycję Zarządzaj rejestracjami**i kliknij grupę rejestracji lub rejestrację indywidualną, którą chcesz skonfigurować do ponownego inicjowania obsługi administracyjnej.

3. W obszarze **Wybierz sposób obsługi danych urządzenia podczas ponownego udostępniania do innego centrum IoT hub**wybierz jedną z następujących zasad ponownego udostępniania:

    * **Ponowne aprowizowanie i migracja danych:** Ta zasada podejmuje działania, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie inicjowania obsługi administracyjnej. W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, rejestracja urządzenia przy początkowym centrum IoT zostanie usunięta. Wszystkie informacje o stanie urządzenia z tego początkowego centrum IoT zostaną przeniesione do nowego centrum IoT Hub. Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**

    * **Ponowne powierzenie i zresetowanie do konfiguracji początkowej:** Ta zasada podejmuje działania, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie inicjowania obsługi administracyjnej. W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, rejestracja urządzenia przy początkowym centrum IoT zostanie usunięta. Początkowe dane konfiguracyjne odebrane przez wystąpienie usługi inicjowania obsługi administracyjnej po zainicjowaniu obsługi administracyjnej urządzenia są dostarczane do nowego centrum IoT Hub. Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**.

4. Kliknij **przycisk Zapisz,** aby włączyć ponowne aprowizowanie urządzenia na podstawie zmian.

    ![Wybieranie zasad alokacji rejestracji](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Wysyłanie żądania inicjowania obsługi administracyjnej z urządzenia

Aby urządzenia były ponownie udostępniane na podstawie zmian konfiguracji wprowadzonych w poprzednich sekcjach, urządzenia te muszą zażądać ponownego udostępnienia. 

To, jak często urządzenie przesyła żądanie inicjowania obsługi administracyjnej, zależy od scenariusza. Zaleca się jednak zaprogramowanie urządzeń do wysyłania żądania inicjowania obsługi administracyjnej do wystąpienia usługi inicjowania obsługi administracyjnej po ponownym uruchomieniu komputera i obsługiwanie [metody](../iot-hub/iot-hub-devguide-direct-methods.md) ręcznego wyzwalania inicjowania obsługi administracyjnej na żądanie. Inicjowanie obsługi administracyjnej może być również wyzwolone przez ustawienie [żądanej właściwości](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example). 

Zasady ponownego udostępniania we wpisie rejestracji określają, w jaki sposób wystąpienie usługi inicjowania obsługi administracyjnej urządzenia obsługuje te żądania inicjowania obsługi administracyjnej i czy dane stanu urządzenia powinny być migrowane podczas ponownego udostępniania. Te same zasady są dostępne dla poszczególnych rejestracji i grup rejestracji:

Na przykład kod wysyłania żądań inicjowania obsługi administracyjnej z urządzenia podczas sekwencji rozruchowej, zobacz [Automatyczne inicjowanie obsługi administracyjnej symulowanego urządzenia](quick-create-simulated-device.md).


## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej Reprovisioning, zobacz [Urządzenia usługi IoT Hub reprovisioning pojęcia](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej Deprovisioning, zobacz [Jak anulować aprovision urządzeń, które wcześniej były automatycznie aprowizacji](how-to-unprovision-devices.md) 











