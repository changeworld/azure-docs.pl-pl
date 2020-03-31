---
title: Usługa inicjowania obsługi administracyjnej urządzeń usługi Usługi Azure IoT Hub — pojęcia dotyczące urządzeń
description: W tym artykule opisano koncepcje ponownego udostępniania obsługi administracyjnej urządzeń dla usługi inicjowania obsługi administracyjnej (DPS) usługi inicjowania obsługi administracyjnej usługi usługi azure ioT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2bf369b784cddf307abc59d2b8766fc8a87e0985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975350"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Koncepcje ponownego obsługi administracyjnej urządzenia usługi IoT Hub

Podczas cyklu życia rozwiązania IoT często przenosi urządzenia między centrami IoT. Przyczyny tego przeniesienia mogą obejmować następujące scenariusze:

* **Geolokalizacja / GeoLatency**: Gdy urządzenie przemieszcza się między lokalizacjami, opóźnienie sieci jest poprawiane przez migrację urządzenia do bliższego centrum IoT Hub.

* **Multi-dzierżawy:** Urządzenie może być używane w ramach tego samego rozwiązania IoT i ponownie przypisany do nowego klienta lub witryny klienta. Ten nowy klient może być obsługiwany przy użyciu innego centrum IoT hub.

* **Zmiana rozwiązania:** urządzenie może zostać przeniesione do nowego lub zaktualizowanego rozwiązania IoT. To ponowne przypisanie może wymagać od urządzenia komunikowania się z nowym centrum IoT hub, który jest podłączony do innych składników zaplecza.

* **Kwarantanna**: Podobnie jak zmiana rozwiązania. Urządzenie, które działa nieprawidłowo, zagrożone lub nieaktualne, może zostać ponownie przypisane do centrum IoT Hub, które może aktualizować i zwracać zgodność. Gdy urządzenie działa poprawnie, jest następnie migrowane z powrotem do głównego centrum.

Ponowne udostępnianie obsługi w ramach usługi inicjowania obsługi administracyjnej urządzeń odpowiada tym potrzebom. Urządzenia mogą być automatycznie przypisywane do nowych centrów IoT na podstawie zasady ponownego obsługi administracyjnej, która jest skonfigurowana we wpisie rejestracji urządzenia.

## <a name="device-state-data"></a>Dane o stanie urządzenia

Dane o stanie urządzenia składa się z [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i możliwości urządzenia. Te dane są przechowywane w wystąpieniu usługi inicjowania obsługi administracyjnej urządzeń i w centrum IoT, do których urządzenie jest przypisane.

![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-provisioning.png)

Gdy urządzenie jest początkowo aprowidyfikowane za pomocą wystąpienia usługi inicjowania obsługi administracyjnej urządzeń, wykonywane są następujące kroki:

1. Urządzenie wysyła żądanie inicjowania obsługi administracyjnej do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń. Wystąpienie usługi uwierzytelnia tożsamość urządzenia na podstawie wpisu rejestracji i tworzy początkową konfigurację danych o stanie urządzenia. Wystąpienie usługi przypisuje urządzenie do centrum IoT na podstawie konfiguracji rejestracji i zwraca przypisanie centrum IoT do urządzenia.

2. Wystąpienie usługi inicjowania obsługi administracyjnej udostępnia kopię wszystkich początkowych danych o stanie urządzenia do przypisanego centrum IoT Hub. Urządzenie łączy się z przypisanym centrum IoT i rozpoczyna operacje.

Z biegiem czasu dane o stanie urządzenia w centrum IoT hub mogą być aktualizowane przez [operacje urządzenia](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) i [operacje zaplecza.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) Początkowe informacje o stanie urządzenia przechowywane w wystąpieniu usługi instruktażu urządzeń pozostają nietknięte. Te nietknięte dane o stanie urządzenia są konfiguracją początkową.

![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

W zależności od scenariusza, gdy urządzenie przenosi się między centrami IoT, może być również konieczne migrowanie stanu urządzenia zaktualizowanego w poprzednim centrum IoT do nowego centrum IoT hub. Ta migracja jest obsługiwana przez ponowne udostępnianie zasad w usłudze inicjowania obsługi administracyjnej urządzeń.

## <a name="reprovisioning-policies"></a>Ponowne przewidywanie zasad

W zależności od scenariusza urządzenie zwykle wysyła żądanie do wystąpienia usługi inicjowania obsługi administracyjnej po ponownym uruchomieniu komputera. Obsługuje również metodę ręcznego wyzwalania inicjowania obsługi administracyjnej na żądanie. Zasady ponownego udostępniania we wpisie rejestracji określają, w jaki sposób wystąpienie usługi inicjowania obsługi administracyjnej urządzenia obsługuje te żądania inicjowania obsługi administracyjnej. Zasady określają również, czy dane o stanie urządzenia powinny być migrowane podczas ponownego inicjowania obsługi administracyjnej. Te same zasady są dostępne dla poszczególnych rejestracji i grup rejestracji:

* **Ponowne aprowizowanie i migracja danych:** Ta zasada jest wartością domyślną dla nowych wpisów rejestracji. Ta zasada wykonuje akcję, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, rejestracja urządzenia przy początkowym centrum IoT zostanie usunięta. Zaktualizowane informacje o stanie urządzenia z tego początkowego centrum IoT zostaną przeniesione do nowego centrum IoT hub (2). Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**.

    ![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Ponowne powierzenie i zresetowanie do konfiguracji początkowej:** Ta zasada podejmuje działania, gdy urządzenia skojarzone z wpisem rejestracji przesyłają nowe żądanie inicjowania obsługi administracyjnej (1). W zależności od konfiguracji wpisu rejestracji urządzenie może zostać ponownie przypisane do innego centrum IoT Hub. Jeśli urządzenie zmienia centra IoT, rejestracja urządzenia przy początkowym centrum IoT zostanie usunięta. Początkowe dane konfiguracyjne odebrane przez wystąpienie usługi inicjowania obsługi administracyjnej po zainicjowaniu obsługi administracyjnej urządzenia są dostarczane do nowego centrum IoT hub (2). Podczas migracji stan urządzenia będzie zgłaszany jako **Przypisywanie**.

    Ta zasada jest często używana do przywracania ustawień fabrycznych bez zmiany centrów IoT.

    ![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nigdy nie ponownie aprowizować:** urządzenie nigdy nie jest ponownie przypisany do innego koncentratora. Ta zasada jest przewidziana do zarządzania zgodnością z powrotem.

### <a name="managing-backwards-compatibility"></a>Zarządzanie zgodnością z przeszywem

Przed wrześniem 2018 r. przypisania urządzeń do centrów IoT miały lepkie zachowanie. Gdy urządzenie wróciło przez proces inicjowania obsługi administracyjnej, zostanie przypisane tylko do tego samego centrum IoT hub.

W przypadku rozwiązań, które miały zależność od tego zachowania, usługa inicjowania obsługi administracyjnej zawiera zgodność wsteczną. To zachowanie jest obecnie utrzymywane dla urządzeń zgodnie z następującymi kryteriami:

1. Urządzenia łączą się z wersją interfejsu API przed udostępnieniem natywnej obsługi ponownego udostępniania w usłudze inicjowania obsługi administracyjnej urządzeń. Zapoznaj się z poniższą tabelą interfejsu API.

2. Wpis rejestracji dla urządzeń nie ma ustawionej zasady ponownego obsługi administracyjnej.

Ta zgodność zapewnia, że wcześniej wdrożone urządzenia doświadczają tego samego zachowania, które jest obecne podczas początkowych testów. Aby zachować poprzednie zachowanie, nie zapisuj zasad ponownego obsługi administracyjnej do tych rejestracji. Jeśli ustawiono zasady ponownego obsługi administracyjnej, zasady ponownego obsługi administracyjnej mają pierwszeństwo przed zachowaniem. Zezwalając zasady ponownego obsługi administracyjnej mieć pierwszeństwo, klienci mogą aktualizować zachowanie urządzenia bez konieczności ponownego zagęszczenia urządzenia.

Poniższy schemat blokowy pomaga pokazać, kiedy zachowanie jest obecne:

![wykres blokowy zgodności z do tyłu](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

W poniższej tabeli przedstawiono wersje interfejsu API przed dostępnością natywnej obsługi ponownego udostępniania w usłudze inicjowania obsługi administracyjnej urządzeń:

| Interfejs API REST | C SDK | Zestaw SDK dla języka Python |  Zestaw SDK dla języka Node | Zestaw SDK Java | Zestaw SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 i wcześniej](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 i wcześniej](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 i wcześniej](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 lub wcześniej](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 lub wcześniej](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 lub wcześniej](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Te wartości i łącza mogą ulec zmianie. Jest to tylko próba symbolu zastępczego, aby określić, gdzie wersje mogą być określone przez klienta i jakie będą oczekiwane wersje.

## <a name="next-steps"></a>Następne kroki

* [Jak ponownie aprowizji urządzeń](how-to-reprovision.md)
