---
title: Pojęcia dotyczące usług w usłudze inicjowania obsługi urządzeń usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Zawiera opis koncepcji inicjowania obsługi administracyjnej usług specyficznych dla urządzeń z usługą inicjowania obsługi administracyjnej urządzeń (DPS) i centrum IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285216"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Pojęcia dotyczące usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub

Usługa inicjowania obsługi administracyjnej urządzeń w centrum IoT hub jest usługą pomocniczą dla usługi IoT Hub, której używasz do konfigurowania obsługi administracyjnej urządzeń bezdotykowych do określonego centrum IoT Hub. Dzięki usłudze aprowizacji urządzeń można [automatycznie aprowizować](concepts-auto-provisioning.md) miliony urządzeń w bezpieczny i skalowalny sposób.

Inicjowanie obsługi administracyjnej urządzeń jest procesem dwuczęściowym. Pierwsza część jest ustanowienie początkowego połączenia między urządzeniem a rozwiązaniem IoT przez *zarejestrowanie* urządzenia. Druga część to zastosowanie odpowiedniej *konfiguracji* do urządzenia w oparciu o specyficzne wymagania rozwiązania. Po zakończeniu obu kroków urządzenie zostało w pełni *aprowizgodnie.* Usługa Device Provisioning automatyzuje oba kroki, aby zapewnić bezproblemowe środowisko aprowizowania dla urządzenia.

W tym artykule przedstawiono omówienie pojęć inicjowania obsługi administracyjnej najbardziej stosowanych do zarządzania *usługą*. Ten artykuł jest najbardziej odpowiedni dla personas zaangażowanych w [etapie konfiguracji chmury,](about-iot-dps.md#cloud-setup-step) przygotowując urządzenie do wdrożenia.

## <a name="service-operations-endpoint"></a>Punkt końcowy operacji serwisowych

Punkt końcowy operacji usługi jest punktem końcowym do zarządzania ustawieniami usługi i utrzymywania listy rejestracji. Ten punkt końcowy jest używany tylko przez administratora usługi; nie jest używany przez urządzenia.

## <a name="device-provisioning-endpoint"></a>Punkt końcowy inicjowania obsługi administracyjnej urządzeń

Punkt końcowy inicjowania obsługi administracyjnej urządzenia jest pojedynczym punktem końcowym używanym przez wszystkie urządzenia do automatycznego inicjowania obsługi administracyjnej. Adres URL jest taki sam dla wszystkich wystąpień usługi inicjowania obsługi administracyjnej, aby wyeliminować konieczność ponownego łączenia urządzeń z nowymi informacjami o połączeniu w scenariuszach łańcucha dostaw. Zakres identyfikatora zapewnia izolację dzierżawy.

## <a name="linked-iot-hubs"></a>Połączone centra IoT

Usługa inicjowania obsługi administracyjnej urządzeń może aprowizować urządzenia tylko do centrów IoT, które zostały z nią połączone. Łączenie centrum IoT hub z wystąpieniem usługi inicjowania obsługi administracyjnej urządzeń daje usługi uprawnienia do odczytu/zapisu do rejestru urządzeń centrum IoT hub; za pomocą łącza usługa inicjowania obsługi urządzeń może zarejestrować identyfikator urządzenia i ustawić początkową konfigurację w bliźniaczej reprezentacji urządzenia. Połączone centra IoT mogą znajdować się w dowolnym regionie platformy Azure. Możesz połączyć koncentratory w innych subskrypcjach z usługą inicjowania obsługi administracyjnej.

## <a name="allocation-policy"></a>Zasady alokacji

Ustawienie poziomu usługi określające sposób przypisywania urządzeń do centrum IoT hub. Są obsługiwane trzy zasady alokacji:

* **Równomiernie ważona dystrybucja:** połączone koncentratory IoT są równie prawdopodobne, że urządzenia są do nich aprowektowane. Ustawienie domyślne. W przypadku aprowizowania urządzeń tylko do jednego centrum IoT można pozostawić to ustawienie.

* **Najniższe opóźnienie:** urządzenia są aprowizować do centrum IoT hub z najniższym opóźnieniem do urządzenia. Jeśli wiele połączonych centrów IoT zapewni takie samo najniższe opóźnienie, usługa inicjowania obsługi administracyjnej haszyszuje urządzenia w tych koncentratorach

* **Konfiguracja statyczna za pośrednictwem listy rejestracji:** specyfikacja żądanego centrum IoT na liście rejestracji ma pierwszeństwo przed zasadami alokacji na poziomie usługi.

## <a name="enrollment"></a>Rejestracja

Rejestracja to rekord urządzeń lub grup urządzeń, które mogą rejestrować się za pomocą automatycznego inicjowania obsługi administracyjnej. Rekord rejestracji zawiera informacje o urządzeniu lub grupie urządzeń, w tym:
- [mechanizm zaświadczania](concepts-security.md#attestation-mechanism) stosowany przez urządzenie
- opcjonalna początkowa żądana konfiguracja
- żądana piasta IoT
- żądany identyfikator urządzenia

Istnieją dwa typy rejestracji obsługiwanych przez usługę inicjowania obsługi administracyjnej urządzeń:

### <a name="enrollment-group"></a>Grupa rejestracji

Grupa rejestracji to grupa urządzeń, które współużytkują określony mechanizm zaświadczania. Grupy rejestracji obsługują zarówno X.509, jak i symetryczne. Wszystkie urządzenia w grupie rejestracji X.509 przedstawiają certyfikaty X.509, które zostały podpisane przez ten sam główny lub pośredni urząd certyfikacji (CA). Każde urządzenie w grupie rejestracji klucza symetrycznego przedstawia tokeny symetrycznego sygnatury dostępu Współdzielonego pochodzące z klucza symetrycznego grupy. Nazwa grupy rejestracji i nazwa certyfikatu muszą być alfanumeryczne, małe litery i mogą zawierać łączniki.

> [!TIP]
> Zaleca się używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową lub dla urządzeń przechodzących do tej samej dzierżawy.

### <a name="individual-enrollment"></a>Rejestracja indywidualna

Rejestracja indywidualna to wpis dla jednego urządzenia, które może się zarejestrować. Poszczególne rejestracje mogą używać certyfikatów listowych X.509 lub tokenów Sygnatury dostępu Współdzielonego (z fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Identyfikator rejestracji w indywidualnej rejestracji jest alfanumeryczny, małe litery i może zawierać łączniki. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

> [!TIP]
> Zaleca się używanie poszczególnych rejestracji dla urządzeń, które wymagają unikatowych konfiguracji początkowych, lub dla urządzeń, które mogą uwierzytelniać się tylko przy użyciu tokenów sygnatury dostępu Współdzielonego za pośrednictwem zaświadczania modułu TPM.

## <a name="registration"></a>Rejestracja

Rejestracja to rekord urządzenia pomyślnie rejestrującego/inicjującego inicjowanie obsługi administracyjnej do usługi IoT Hub za pośrednictwem usługi inicjowania obsługi administracyjnej urządzeń. Rekordy rejestracji są tworzone automatycznie; można je usunąć, ale nie można ich zaktualizować.

## <a name="operations"></a>Operacje

Operacje są jednostką rozliczeniową usługi inicjowania obsługi administracyjnej urządzeń. Jedną z operacji jest pomyślne zakończenie jednej instrukcji do usługi. Operacje obejmują rejestracje urządzeń i ponowną rejestrację; operacje obejmują również zmiany po stronie usługi, takie jak dodawanie wpisów listy rejestracji i aktualizowanie wpisów listy rejestracji.
