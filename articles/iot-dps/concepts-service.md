---
title: Pojęcia dotyczące usługi Azure IoT Hub Device Provisioning Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano inicjowania obsługi administracyjnej pojęcia dotyczące usługi specyficzne dla urządzeń z usługą Device Provisioning i IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047487"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Pojęcia dotyczące IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service to usługa pomocnika usługi IoT Hub, możesz użyć do skonfigurowania urządzenia i bezobsługowa aprowizacja w określonej usługi IoT hub. Przy użyciu usługi Device Provisioning, można [automatycznej obsługi](concepts-auto-provisioning.md) milionów urządzeń w bezpieczny i skalowalny sposób.

Inicjowanie obsługi administracyjnej urządzeń jest procesem dwóch części. Pierwsza część jest ustanowienie początkowego połączenia między urządzeniem i rozwiązań IoT przez *rejestrowanie* urządzenia. Druga część jest stosowanie właściwego *konfiguracji* na urządzeniu, w zależności od określonych wymagań rozwiązania. Po wykonaniu obu kroków urządzenie zostało całkowicie *aprowizowane*. Usługa Device Provisioning automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko aprowizowania dla urządzenia.

Ten artykuł zawiera omówienie koncepcji aprowizacji, najlepiej dostosowanymi do zarządzania *usługi*. Ten artykuł jest najważniejsze dla osób zajmujących się [kroku konfiguracji chmury](about-iot-dps.md#cloud-setup-step) jakim urządzenie jest gotowe do wdrożenia.

## <a name="service-operations-endpoint"></a>Punkt końcowy usługi operations

Punkt końcowy usługi operations jest punkt końcowy zarządzania ustawienia usługi i konserwacji na liście rejestracji. Ten punkt końcowy jest używany tylko przez administratora usługi; nie jest używana przez urządzenia.

## <a name="device-provisioning-endpoint"></a>Punkt końcowy aprowizacji urządzenia

Punkt końcowy aprowizacji urządzenia jest w jednym punkcie końcowym wszystkie urządzenia na użytek automatycznego aprowizowania. Adres URL jest taka sama dla wszystkich inicjowania obsługi administracyjnej wystąpień usługi, aby wyeliminować konieczność reflash urządzeń przy użyciu nowych informacji o połączeniu w scenariuszach łańcucha dostaw. Zakres identyfikatorów zapewnia izolację dzierżawy.

## <a name="linked-iot-hubs"></a>Połączone centra IoT Hub

Usługi Device Provisioning Service można aprowizować tylko urządzeń do centrów IoT Hub, połączone do niego. Łączenie Centrum IoT hub na wystąpienie usługi Device Provisioning service zapewnia usługi uprawnienia odczytu/zapisu do rejestru urządzeń usługi IoT hub; za pomocą linku usługi Device Provisioning service można zarejestrować identyfikator urządzenia i ustawienia początkowej konfiguracji w bliźniaczej reprezentacji urządzenia. Połączone centra IoT Hub może występować w dowolnym regionie systemu Azure. Możesz połączyć koncentratory w innych subskrypcjach, do usługi aprowizacji.

## <a name="allocation-policy"></a>Zasady alokacji

Ustawienie poziomie usługi określa, jak usługi Device Provisioning Service przypisuje urządzeń do usługi IoT hub. Są obsługiwane trzy zasady alokacji:

* **Dystrybucja z równymi wagami**: połączone centra IoT Hub jest jednakowo prawdopodobna, urządzeń, które mu udostępnionych. Ustawieniem domyślnym. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie.

* **Najmniejsze opóźnienie**: urządzenia są aprowizowane do Centrum IoT hub z najmniejszym opóźnieniem do urządzenia. Jeśli wiele połączone centra IoT Hub będzie podać takie samo opóźnienie najniższy, inicjowania obsługi usługi wyznacza wartość skrótu urządzeń w tych centrach

* **Konfiguracja statyczna za pośrednictwem listy rejestracji**: określenie żądanego Centrum IoT na liście rejestracji ma wyższy priorytet niż zasady alokacji na poziomie usługi.

## <a name="enrollment"></a>Rejestracja

Rejestracja to rekord urządzeń lub grup urządzeń, które może zarejestrować się za pomocą automatycznej aprowizacji. Rekord rejestracji zawiera informacje o urządzeniu lub grupie urządzeń, w tym:
- [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) wykorzystywany przez to urządzenie
- opcjonalne początkowe wymaganą konfiguracją
- wymagane Centrum IoT hub
- Identyfikator żądanego urządzenia

Istnieją dwa rodzaje rejestracji obsługiwane przez usługę Device Provisioning:

### <a name="enrollment-group"></a>Grupy rejestracji

Grupy rejestracji jest grupy urządzeń, które współużytkują specyficzny mechanizm zaświadczania. Wszystkie urządzenia w grupie rejestracji przedstawiają certyfikatów X.509, które zostały podpisane przez ten sam główny lub pośredni urząd certyfikacji (CA). Grupy rejestracji można używać tylko mechanizm zaświadczania X.509. Nazwa grupy i nazwę certyfikatu musi być alfanumeryczne, małe litery i może zawierać łączników.

> [!TIP]
> Zalecamy używanie grupę rejestracji dla dużej liczby urządzeń, które współużytkują wybranej konfiguracji początkowej lub urządzeń wszystkich przejść do tej samej dzierżawy.

### <a name="individual-enrollment"></a>Rejestracja indywidualna

Wpis dla jednego urządzenia, które może zarejestrować jest rejestracji indywidualnej. Rejestracje indywidualne mogą używać certyfikatów liścia X.509 lub tokenów SAS (z fizycznym lub wirtualnym module TPM) jako mechanizmów zaświadczania. Identyfikator rejestracji w rejestracji indywidualnej alfanumeryczne, małe litery i może zawierać łączników. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

> [!TIP]
> Firma Microsoft zaleca używanie rejestracji indywidualnych w przypadku urządzeń, które wymagają unikatowej konfiguracji początkowej lub urządzeń, które mogą tylko uwierzytelniać przy użyciu tokenów SAS za pośrednictwem zaświadczenia modułu TPM.

## <a name="registration"></a>Rejestracja

Rejestracja to rekord urządzenia pomyślnie rejestrowanie/Inicjowanie obsługi administracyjnej do usługi IoT Hub przy użyciu usługi Device Provisioning Service. Rejestrację rekordów są tworzone automatycznie. można je usunąć, ale nie można ich zaktualizować.

## <a name="operations"></a>Operacje

Operacje to jednostka rozliczeniowa usługi aprowizacji urządzeń. Jedna operacja jest pomyślne zakończenie jedną instrukcję do usługi. Operacje obejmują rejestrowanie i ponowne rejestrowanie urządzeń, a także zmiany po stronie serwera, takie jak dodawanie i aktualizowanie wpisów na liście rejestracji.
