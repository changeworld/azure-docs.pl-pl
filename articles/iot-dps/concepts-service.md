---
title: Pojęcia dotyczące usługi Azure IoT Hub urządzenia inicjowania obsługi usługi | Dokumentacja firmy Microsoft
description: W tym artykule opisano inicjowania obsługi administracyjnej pojęcia dotyczące usługi specyficzne dla urządzeń z punktu dystrybucji i Centrum IoT
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d2bc58514ea716954ec3ac96151549168fedc2ed
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Pojęcia dotyczące inicjowania obsługi usługi IoT Hub urządzeń

Usługa inicjowania obsługi urządzeń Centrum IoT to usługa pomocnika do Centrum IoT, które są używane do konfigurowania urządzenia bezobsługową alokacji dla określonego Centrum IoT. Za pomocą usługi udostępniania urządzenia można [auto-provision](concepts-auto-provisioning.md) milionów urządzeń w sposób bezpieczny i skalowalności.

Inicjowanie obsługi administracyjnej urządzeń jest procesem dwie części. Pierwsza część jest nawiązywania początkowego połączenia między urządzeniem a rozwiązaniem IoT przez *rejestrowanie* urządzenia. Druga część stosuje poprawne *konfiguracji* do urządzenia, zależnie od konkretnych wymogów obowiązujących w rozwiązaniu. Po wykonaniu obu czynności urządzenie zostało całkowicie *elastycznie*. Usługa Device Provisioning automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko aprowizowania dla urządzenia.

Ten artykuł zawiera omówienie pojęć inicjowania obsługi administracyjnej najbardziej odpowiednią do zarządzania *usługi*. W tym artykule jest najodpowiedniejsze do osoby objętego [kroku konfiguracji chmury](about-iot-dps.md#cloud-setup-step) pobierania urządzenie jest gotowe do wdrożenia.

## <a name="service-operations-endpoint"></a>Punkt końcowy operacji usługi

Punkt końcowy usługi operacji jest punkt końcowy Zarządzanie ustawieniami usługi i konserwowania listy rejestracji. Ten punkt końcowy jest używana tylko przez administratora usługi; nie jest on używany przez urządzenia.

## <a name="device-provisioning-endpoint"></a>Urządzenie inicjowania obsługi administracyjnej punktu końcowego

Punkt końcowy inicjowania obsługi administracyjnej urządzeniu jest jeden punkt końcowy, wszystkie urządzenia używanego do automatycznego inicjowania obsługi. Adres URL jest taka sama dla wszystkich inicjowania obsługi administracyjnej wystąpień usługi, aby wyeliminować potrzebę reflash urządzeniami przy użyciu nowych informacji o połączeniu w scenariuszach łańcucha dostaw. [Identyfikator zakresu](#id-scope) zapewnia izolacji dzierżawców.

## <a name="linked-iot-hubs"></a>Połączone centra IoT Hub

Usługa inicjowania obsługi urządzeń można udostępnić tylko urządzenia do centra IoT, które zostały połączone z jej. Połączenie usługi inicjowania obsługi urządzeń Centrum IoT zapewnia usługi uprawnienia odczytu/zapisu w rejestrze urządzenia Centrum IoT; łącze usługi inicjowania obsługi urządzeń można zarejestrować identyfikator urządzenia i ustawienia początkowej konfiguracji w dwie urządzenia. Połączone centra IoT można w dowolnym regionie Azure. Koncentratory w innych subskrypcji może połączyć inicjowania obsługi usługi.

## <a name="allocation-policy"></a>Zasady alokacji

Ustawienie poziomu usługi określa, jak usługa inicjowania obsługi urządzeń przypisuje urządzenia do Centrum IoT. Są obsługiwane trzy zasady alokacji:
* **Równomiernie ważone dystrybucji**: połączone centra IoT prawdopodobnie jednakowo urządzenia udostępnione do nich. Ustawieniem domyślnym. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie.
* **Można uzyskać najmniejsze opóźnienia**: urządzeń są udostępniane w Centrum IoT z najniższym opóźnieniu na urządzeniu. Jeśli wiele połączone centra IoT zapewni tego samego uzyskać najmniejsze opóźnienia, inicjowania obsługi usługi skróty urządzeń między tymi koncentratorami
* **Statyczne konfiguracji za pomocą listy rejestracji**: specyfikacja żądaną Centrum IoT na liście rejestracji ma wyższy priorytet niż zasady alokacji poziomu usług.

## <a name="enrollment"></a>Rejestrowanie

Rejestracja jest rekord urządzeń lub grup urządzeń, które mogą rejestrować się za pośrednictwem automatycznego inicjowania obsługi administracyjnej. Rekord rejestracji zawiera informacje dotyczące urządzenia lub grupy urządzeń, w tym:
- [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) używany przez urządzenie
- opcjonalne żądanej konfiguracji początkowej
- żądany Centrum IoT
- Identyfikator żądanego urządzenia

Istnieją dwa rodzaje rejestracji obsługiwane przez usługi inicjowania obsługi urządzeń:

### <a name="enrollment-group"></a>Grupa rejestracji

Grupa rejestracji jest grupy urządzeń mających mechanizm określone poświadczenie. Wszystkie urządzenia w grupie rejestracji stanowi certyfikatów X.509, które zostały podpisane przez ten sam główny lub pośredni urząd certyfikacji. Grupy rejestracji można używać tylko mechanizm zaświadczania X.509. Nazwa grupy rejestracji i nazwę certyfikatu musi być alfanumeryczny, małe litery i może zawierać łączników.

> [!TIP]
> Firma Microsoft zaleca używanie grupy rejestracji dużej liczby urządzeń mających żądanej konfiguracji początkowej lub urządzeń wszystkich przejść do tej samej dzierżawy.

### <a name="individual-enrollment"></a>Poszczególne rejestracji

Wpis dla pojedynczego urządzenia, które mogą zarejestrować jest poszczególnych rejestracji. Poszczególne rejestracji może używać certyfikatów X.509 liścia lub tokeny sygnatury dostępu Współdzielonego (z modułem TPM fizycznych lub wirtualnych) jako mechanizmy zaświadczania. Identyfikator rejestracji w poszczególnych rejestracji jest alfanumeryczne, małe litery i może zawierać łączniki. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

> [!TIP]
> Zalecamy używanie poszczególnych rejestracji dla urządzeń, które wymagają szczególnej konfiguracji początkowej lub urządzeń, które tylko uwierzytelniania z użyciem tokeny sygnatury dostępu Współdzielonego za pośrednictwem zaświadczenia modułu TPM.

## <a name="registration"></a>Rejestracja

Rejestracja jest rekord pomyślnie rejestrowanie/inicjowania obsługi urządzeń do Centrum IoT za pomocą usługi udostępniania urządzeń. Rejestrację rekordów są tworzone automatycznie. można go usunąć, ale nie można zaktualizować.

## <a name="operations"></a>Operacje

Operacje są rozliczeniowym jednostki usługi inicjowania obsługi urządzeń. Jedna operacja jest pomyślne zakończenie jedną instrukcję do usługi. Operacje obejmują rejestrowanie i ponowne rejestrowanie urządzeń, a także zmiany po stronie serwera, takie jak dodawanie i aktualizowanie wpisów na liście rejestracji.
