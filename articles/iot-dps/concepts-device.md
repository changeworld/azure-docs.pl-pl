---
title: Pojęcia dotyczące urządzeń w inicjowaniu obsługi administracyjnej urządzeń platformy Azure | Dokumenty firmy Microsoft
description: Zawiera opis koncepcji inicjowania obsługi administracyjnej urządzeń specyficznych dla urządzeń z usługą aprowizacji urządzeń (DPS) i Centrum IoT
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285164"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service device concepts (Pojęcia dotyczące urządzenia dla usługi IoT Hub Device Provisioning Service)

Usługa inicjowania obsługi administracyjnej urządzeń w centrum IoT hub jest usługą pomocniczą dla usługi IoT Hub, której używasz do konfigurowania obsługi administracyjnej urządzeń bezdotykowych do określonego centrum IoT Hub. Usługa Device Provisioning umożliwia udostępnianie milionów urządzeń w bezpieczny i skalowalny sposób.

W tym artykule przedstawiono omówienie koncepcji *urządzenia* zaangażowanych w inicjowanie obsługi administracyjnej urządzeń. Ten artykuł jest najbardziej istotne dla personas zaangażowanych w [etapie produkcji](about-iot-dps.md#manufacturing-step) przygotowania urządzenia do wdrożenia.

## <a name="attestation-mechanism"></a>Mechanizm zaświadczania

Mechanizm zaświadczania jest metodą używaną do potwierdzania tożsamości urządzenia. Mechanizm zaświadczania jest również odpowiedni dla listy rejestracji, która informuje usługę inicjowania obsługi administracyjnej, która metoda zaświadczania ma być używana z danym urządzeniem.

> [!NOTE]
> Usługa IoT Hub używa "schematu uwierzytelniania" dla podobnej koncepcji w tej usłudze.

Usługa aprowizacji urządzeń obsługuje następujące formy zaświadczania:
* **Certyfikaty X.509** oparte na standardowym przepływie uwierzytelniania certyfikatów X.509.
* **Moduł TPM (Trusted Platform Module)** oparty na wyzwaniu typu nonce przy użyciu standardu modułu TPM dla kluczy do prezentowania podpisanego tokenu sygnatury dostępu współdzielonego (SAS). Nie wymaga to fizycznego modułu TPM na urządzeniu, ale usługa oczekuje, że będzie testować przy użyciu klucza poręczenia zgodnie ze [specyfikacją modułu TPM.](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)
* **Klucz symetryczny** oparty na [tokenach zabezpieczających](../iot-hub/iot-hub-devguide-security.md#security-tokens)podpisu dostępu współdzielonego (SAS), które zawierają podpis skrótowy i osadzony wygaśnięcie. Aby uzyskać więcej informacji, zobacz [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Sprzętowy moduł zabezpieczeń

Sprzętowy moduł zabezpieczeń (HSM) służy do bezpiecznego, sprzętowego przechowywania wpisów tajnych urządzeń i jest najbezpieczniejszą formą tajnego magazynu. Zarówno certyfikaty X.509, jak i tokeny sygnatury dostępu Współdzielonego mogą być przechowywane w modułie HSM. Moduły HSM mogą być używane z obu mechanizmów zaświadczania obsługuje usługę inicjowania obsługi administracyjnej.

> [!TIP]
> Zdecydowanie zaleca się używanie modułu HSM z urządzeniami do bezpiecznego przechowywania wpisów tajnych na urządzeniach.

Wpisy tajne urządzenia mogą być również przechowywane w oprogramowaniu (pamięci), ale jest to mniej bezpieczna forma magazynu niż moduł HSM.

## <a name="registration-id"></a>Identyfikator rejestracji

Identyfikator rejestracji służy do jednoznacznej identyfikacji urządzenia w usłudze inicjowania obsługi administracyjnej urządzeń. Identyfikator urządzenia musi być unikatowy w [zakresie identyfikatora](#id-scope)usługi inicjowania obsługi administracyjnej . Każde urządzenie musi mieć identyfikator rejestracji. Identyfikator rejestracji jest alfanumeryczny, niewrażliwy na wielkości i może zawierać znaki specjalne, w tym dwukropek, kropkę, podkreślenie i myślnik.

* W przypadku modułu TPM identyfikator rejestracji jest dostarczany przez sam moduł TPM.
* W przypadku zaświadczenia opartego na x.509 identyfikator rejestracyjny jest podany jako nazwa podmiotu certyfikatu.

## <a name="device-id"></a>Identyfikator urządzenia

Identyfikator urządzenia jest identyfikatorem wyświetlanym w centrum IoT Hub. Żądany identyfikator urządzenia może być ustawiony we wpisie rejestracji, ale nie jest wymagany do ustawienia. Ustawienie żądanego identyfikatora urządzenia jest obsługiwane tylko w poszczególnych rejestracjach. Jeśli na liście rejestracji nie określono żądanego identyfikatora urządzenia, identyfikator rejestracji jest używany jako identyfikator urządzenia podczas rejestrowania urządzenia. Dowiedz się więcej o [identyfikatorach urządzeń w Centrum IoT](../iot-hub/iot-hub-devguide-identity-registry.md)Hub .

## <a name="id-scope"></a>Zakres identyfikatora

Zakres identyfikatora jest przypisany do usługi inicjowania obsługi administracyjnej urządzeń, gdy jest tworzony przez użytkownika i jest używany do jednoznacznej identyfikacji określonej usługi inicjowania obsługi administracyjnej, za pośrednictwem których urządzenie będzie się rejestrować. Zakres identyfikatora jest generowany przez usługę i jest niezmienny, co gwarantuje unikatowość.

> [!NOTE]
> Unikatowość jest ważna w przypadku długotrwałych operacji wdrożeniowych oraz scenariuszy fuzji i przejęć.
