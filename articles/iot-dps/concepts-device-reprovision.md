---
title: Pojęcia dotyczące urządzeń dla reprovisioning dla usługi Azure IoT Hub Device Provisioning | Dokumentacja firmy Microsoft
description: W tym artykule opisano urządzenie reprovisioning pojęć dla usługi Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: fa8cb29f145c7658227f93d08a990c98563a0cfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730018"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Pojęcia reprovisioning urządzeń usługi IoT Hub

Podczas cyklu życia rozwiązania IoT jest często przenosić urządzenia między centra IoT Hub. Przyczyny tego przeniesienia może obejmować następujące scenariusze:

* **Używanie funkcji Geolokalizacji / GeoLatency**: Przemieszcza się w urządzeniu między lokalizacjami, sieci opóźnieniu przez urządzenie poddane migracji do bliżej usługi IoT hub.

* **Wielodostępność**: Urządzenie może być używane w ramach tego samego rozwiązania IoT i przypisanie do nowego klienta lub klienta lokacji. Ten nowy klient może obsłużenie za pomocą innej usługi IoT hub.

* **Rozwiązanie, zmian**: Urządzenie można przenieść do nowych lub zaktualizowanych rozwiązania IoT. To ponowne przypisywanie może wymagać urządzenia do komunikowania się za pomocą nowego centrum IoT, który jest podłączony do innych składników zaplecza.

* **Kwarantanna**: Podobnie jak zmiany rozwiązań. Urządzenia, które działa poprawnie, naruszenia zabezpieczeń lub nieaktualne może zostać ponownie przypisane do usługi IoT hub, która może być tylko zaktualizować i wrócić w zakresie zgodności. Gdy urządzenie działa poprawnie, następnie została zmigrowana do jego głównej koncentratora.

Reprovisioning obsługę adresów usługi Device Provisioning tych potrzeb. Urządzenia można je automatycznie przypisać nowe centra IoT Hub na podstawie zasad reprovisioning, który jest skonfigurowany dla wpisu rejestracji urządzenia.

## <a name="device-state-data"></a>Dane o stanie urządzenia

Dane o stanie urządzenia składa się z [bliźniaczej reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md) i urządzeniami. Te dane są przechowywane w wystąpieniu usługi Device Provisioning i IoT hub, przypisana do urządzenia.

![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-provisioning.png)

Gdy urządzenia początkowo był zaopatrzony wystąpienie usługi Device Provisioning, są wykonywane następujące czynności:

1. Urządzenie wysyła żądanie aprowizacji z wystąpieniem usługi Device Provisioning. Wystąpienie usługi uwierzytelniania tożsamości urządzenia, w oparciu o wpis rejestracji i tworzy wstępną konfigurację dane o stanie urządzenia. Wystąpienie usługi przypisuje urządzeniu do usługi IoT hub na podstawie konfiguracji rejestracji i zwraca przypisania Centrum IoT na urządzeniu.

2. Inicjowania obsługi administracyjnej wystąpienia usługi powoduje kopiowania danych stanu początkowego urządzenia przypisane Centrum IoT hub. Urządzenie przypisane Centrum IoT hub umożliwia nawiązywanie połączeń i rozpoczyna się operacji.

Wraz z upływem czasu, dane o stanie urządzenia w Centrum IoT hub może zostać zaktualizowany przez [operacje dotyczące urządzenia](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) i [operacji zaplecza](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Informacje o stanie urządzenia początkowej przechowywane w wystąpieniu usługi Device Provisioning pozostaje bez zmian. Te dane stanu urządzenia w charakterze są początkowej konfiguracji.

![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

W zależności od scenariusza przemieszcza się w urządzeniu między centra IoT Hub może również być niezbędne do przeprowadzenia migracji stanu urządzenia zaktualizowane na poprzednim Centrum IoT, za pośrednictwem nowego centrum IoT hub. Ta migracja jest obsługiwana przez reprovisioning zasad w usłudze Device Provisioning.

## <a name="reprovisioning-policies"></a>Reprovisioning zasad

W zależności od scenariusza urządzenia zwykle wysyła żądanie do aprowizacji wystąpienia usługi podczas ponownego uruchamiania komputera. Obsługuje ona również metodę, aby ręcznie wyzwolić udostępniania na żądanie. Reprovisioning zasady na wpis rejestracji Określa, jak obsługuje te żądania aprowizacji wystąpienia usługi aprowizacji urządzeń. Zasady określają również, czy podczas reprovisioning powinny być migrowane dane o stanie urządzenia. Te same zasady są dostępne dla rejestracji indywidualnych i grup rejestracji:

* **Ponownie zainicjować obsługę administracyjną i przeprowadzić migrację danych**: Ta zasada jest domyślnie do nowych wpisów rejestracji. Ta zasada wykonuje akcję, gdy skojarzone z wpisu rejestracji urządzenia przesłać nowe żądanie (1). W zależności od konfiguracji wpisu rejestracji urządzenia może zostać ponownie przypisane do innego Centrum IoT hub. Jeśli urządzenie ulegają zmianie centra IoT Hub, Rejestracja urządzenia w początkowej Centrum IoT zostaną usunięte. Informacje o stanie urządzenia zaktualizowane z tej początkowej usługi IoT hub będą migrowani do nowego centrum IoT hub (2). Podczas migracji stanu urządzenia będą raportowane jako **przypisywanie**.

    ![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Ponownie zainicjować obsługę administracyjną i zresetowane do konfiguracji początkowej**: Ta zasada wykonuje akcję, gdy skojarzone z wpisu rejestracji urządzenia przesłać nowe żądanie obsługi administracyjnej (1). W zależności od konfiguracji wpisu rejestracji urządzenia może zostać ponownie przypisane do innego Centrum IoT hub. Jeśli urządzenie ulegają zmianie centra IoT Hub, Rejestracja urządzenia w początkowej Centrum IoT zostaną usunięte. Dane konfiguracji początkowej, podanym inicjowania obsługi administracyjnej wystąpienia usługi odbieranych, kiedy urządzenie zostało zaaprowizowane do nowego centrum IoT hub (2). Podczas migracji stanu urządzenia będą raportowane jako **przypisywanie**.

    Ta zasada jest często używany dla do ustawień fabrycznych bez wprowadzania zmian w centrach IoT.

    ![Inicjowanie obsługi administracyjnej za pomocą usługi aprowizacji urządzeń](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nigdy nie ponownie zainicjować obsługę administracyjną**: Urządzenie nigdy nie zostanie przypisany do innego koncentratora. Ta zasada zapewnia zarządzanie wstecznej zgodności.

### <a name="managing-backwards-compatibility"></a>Zarządzanie wstecznej zgodności

Przed września 2018 r. przypisania urządzeń do centrów IoT Hub było umocowany zachowanie. Gdy urządzenie poruszanie się wstecz w procesie inicjowania obsługi administracyjnej, będzie można przypisać tylko do tej samej usługi IoT hub.

Rozwiązania, które miały zależności na to zachowanie usługę aprowizacji obejmuje wstecznej zgodności. To zachowanie jest obecnie zachowywana na potrzeby urządzeń według następujących kryteriów:

1. Połącz urządzenia z wersją interfejsu API przed dostępności macierzystą obsługę reprovisioning w usłudze Device Provisioning. Skorzystaj z interfejsu API w tabeli poniżej.

2. Wpis rejestracji dla urządzeń nie ma dla nich ustawionych zasad reprovisioning.

Ta zgodność gwarantuje, że wcześniej wdrożona środowisko urządzenia takie samo zachowanie, która występuje podczas początkowej fazie testowania. Aby zachować poprzednie zachowanie, nie zapisuj reprovisioning zasad do tych rejestracji. Jeśli ustawiono zasady reprovisioning, reprovisioning zasady mają pierwszeństwo przed zachowaniem. Zezwolenie na reprovisioning zasady mają wyższy priorytet, klienci mogą zaktualizować zachowanie urządzeń bez konieczności odtwarzanie z obrazu urządzenia.

Następujące schemat blokowy pomaga do wyświetlenia, gdy obecny jest zachowanie:

![wstecznej zgodności schematu blokowego](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

W poniższej tabeli przedstawiono wersje interfejsu API przed dostępności macierzystą obsługę reprovisioning w usłudze Device Provisioning:

| Interfejs API REST | ZESTAW SDK JĘZYKA C | Zestaw SDK dla języka Python |  Zestaw SDK dla języka Node | Zestaw SDK Java | Zestaw SDK .NET |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 i starszych wersji](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 i starsze wersje](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 i starsze wersje](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 lub starszy](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 lub starszy](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 lub wcześniejszej](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Te wartości i łącza są prawdopodobnie będą zmieniane. Jest to tylko próba symbolu zastępczego ustalenie, gdzie można ustalić wersji przez klienta i to, co będzie oczekiwanej wersji.

## <a name="next-steps"></a>Kolejne kroki

* [Jak ponownie udostępnić urządzeń](how-to-reprovision.md)
