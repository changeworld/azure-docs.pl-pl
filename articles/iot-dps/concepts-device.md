---
title: Pojęcia dotyczące urządzeń w aprowizacji urządzeń na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano specyficzne dla urządzeń z usługą Device Provisioning i IoT Hub koncepcje dotyczące aprowizacji urządzeń
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 2904da863707c5f653d774b0a480cc48c95c8d1c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049090"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Pojęcia dotyczące urządzeń IoT Hub Device Provisioning Service

IoT Hub Device Provisioning Service to usługa pomocnika usługi IoT Hub, możesz użyć do skonfigurowania urządzenia i bezobsługowa aprowizacja w określonej usługi IoT hub. Usługa Device Provisioning umożliwia udostępnianie milionów urządzeń w bezpieczny i skalowalny sposób.

Ten artykuł zawiera omówienie *urządzenia* pojęcia zaangażowane w Inicjowanie obsługi administracyjnej urządzeń. Ten artykuł jest najważniejsze dla osób zajmujących się [kroku produkcji](about-iot-dps.md#manufacturing-step) jakim urządzenie jest gotowe do wdrożenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metoda stosowana do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania ma również zastosowanie listy rejestracji, która informuje usługę aprowizacji metody poświadczenie do użycia z danego urządzenia.

> [!NOTE]
> Usługa IoT Hub używa "schemat uwierzytelniania" podobne pojęcie w tej usłudze.

Usługi Device Provisioning obsługuje następujące rodzaje zaświadczania:
* **Certyfikaty X.509** oparte na standardowych przepływ uwierzytelniania certyfikatu X.509.
* **Trusted Platform Module (TPM)** oparte na wezwanie nonce przy użyciu standardowego modułu TPM klucze do przedstawienia podpisany token sygnatury dostępu współdzielonego (SAS). Nie wymaga to fizyczny moduł TPM na urządzeniu, ale usługa oczekuje, że poświadczenia przy użyciu klucza poręczenia na [specyfikacji TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Klucz symetryczny** oparte na sygnatury dostępu współdzielonego (SAS) [tokeny zabezpieczające](../iot-hub/iot-hub-devguide-security.md#security-tokens), które obejmują sygnatury skrótu i osadzone wygaśnięcia. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń lub modułu HSM, służy do bezpieczny, sprzętowy magazyn kluczy tajnych urządzenia, a jest to najbezpieczniejsze forma wpisu tajnego magazynu. Tokeny sygnatur dostępu Współdzielonego i certyfikaty X.509 mogą być przechowywane w ramach sprzętowego modułu zabezpieczeń. Moduły HSM mogą być używane z obu mechanizmów zaświadczania inicjowania obsługi administracyjnej obsługuje usługi.

> [!TIP]
> Zdecydowanie zaleca się bezpieczne przechowywanie wpisów tajnych na urządzeniach, przy użyciu modułu HSM z urządzeniami.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest mniej bezpieczna opcja formularz magazynu niż moduł HSM.

## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji jest używany do jednoznacznego identyfikowania urządzenia w usłudze Device Provisioning. Identyfikator urządzenia musi być unikatowy w usłudze aprowizowania [zakres identyfikatorów](#id-scope). Każde urządzenie musi mieć identyfikator rejestracji Identyfikator rejestracji jest alfanumeryczne, małe litery i może zawierać łączników.

* W przypadku modułu TPM identyfikator rejestracji jest zapewniana przez samego modułu TPM.
* W przypadku zaświadczania oparte na X.509 identyfikator rejestracji jest dostarczany jako nazwę podmiotu certyfikatu.

## <a name="device-id"></a>Identyfikator urządzenia

Identyfikator urządzenia jest Identyfikatorem, która jest wyświetlana w Centrum IoT Hub. Identyfikator żądanego urządzenia może być ustawiona we wpisie rejestracji, ale nie jest to wymagane do skonfigurowania. Jeśli określono żadnego Identyfikatora żądane urządzenie na liście rejestracji, identyfikator rejestracji jest używany jako identyfikator urządzenia podczas rejestrowania urządzenia. Dowiedz się więcej o [identyfikatory w usłudze IoT Hub urządzeń](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Zakres identyfikatorów

Zakres identyfikatorów jest przypisany usługi Device Provisioning, gdy jest tworzony przez użytkownika i jest używany do jednoznacznego identyfikowania określonej usługi aprowizacji, którego urządzenia będą rejestrować się za pośrednictwem. Identyfikator zakresu jest generowany przez usługę i niezmienne, która gwarantuje unikatowość.

> [!NOTE]
> Unikatowość jest ważne dla scenariuszy łączenia i pozyskiwanie i długotrwałe operacje wdrażania.
