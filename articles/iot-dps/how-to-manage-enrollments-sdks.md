---
title: Zarządzanie rejestracjami urządzeń przy użyciu SDK usługi aprowizacji urządzeń platformy Azure | Dokumentacja firmy Microsoft
description: Jak zarządzanie rejestracjami urządzeń w IoT Hub Device Provisioning Service przy użyciu zestawów SDK usługi
author: yzhong94
ms.author: yizhon
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: c73a40e46d86632732454ae16ea4f83e3ffa0281
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627273"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Jak zarządzać rejestracjami urządzeń przy użyciu SDK usługi aprowizacji urządzeń platformy Azure
A *rejestracji urządzeń* tworzy rekord pojedynczego urządzenia lub grupy urządzeń, które mogą w pewnym momencie zarejestrować w usłudze Device Provisioning Service. Rekord rejestracji zawiera odpowiednią konfigurację wstępną dla urządzeń w ramach rejestracji, w tym wymagane Centrum IoT hub. Ten artykuł pokazuje, jak zarządzanie rejestracjami urządzeń w Twojej usługi aprowizacji, programowo przy użyciu inicjowania obsługi administracyjnej z zestawami SDK Azure IoT.  Zestawy SDK są dostępne w serwisie GitHub, w tym samym repozytorium, co zestawami SDK Azure IoT.

## <a name="prerequisites"></a>Wymagania wstępne
* Uzyskaj parametry połączenia z wystąpienia usługi Device Provisioning.
* Uzyskiwanie urządzenia artefaktów zabezpieczeń na potrzeby [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) używane:
    * [**Trusted Platform Module (TPM)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Rejestracja indywidualna: Identyfikator rejestracji i kluczem poręczenia modułu TPM z urządzenia fizycznego lub symulatora modułu TPM.
        * Grupy rejestracji nie ma zastosowania do zaświadczenia modułu TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Rejestracja indywidualna: [Certyfikatu liścia](/azure/iot-dps/concepts-security) z urządzenia fizycznego lub zestawu SDK [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulatora.
        * Grupy rejestracji: [/Główny certyfikat urzędu certyfikacji](/azure/iot-dps/concepts-security#root-certificate) lub [pośredniego certyfikatu](/azure/iot-dps/concepts-security#intermediate-certificate), który jest używany w celu wygenerowania certyfikatu urządzenia na urządzeniu fizycznym.  Mogą być też generowane z emulatorem DICE zestawu SDK.
* Dokładne wywołań interfejsu API może się różnić z powodu różnic w języku. Przejrzyj przykłady podane w serwisie GitHub, aby uzyskać szczegółowe informacje:
   * [Przykłady klienta usługi aprowizacji języka Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Przykłady dla platformy node.js inicjowania obsługi administracyjnej klienta usługi](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Przykłady klienta usługi aprowizacji platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń
Istnieją dwa sposoby, które mogą rejestrować urządzenia z usługą aprowizacji:

* **Grupy rejestracji** wpis dla grupy urządzeń, które współużytkują wspólne mechanizm zaświadczania certyfikatów X.509, podpisane przez [certyfikat główny](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [pośredniego certyfikatu ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Zalecamy używanie grupę rejestracji dla dużej liczby urządzeń, które współużytkują wybranej konfiguracji początkowej lub urządzeń wszystkich przejść do tej samej dzierżawy. Należy pamiętać, że możesz tylko rejestrować urządzenia, które korzystają z mechanizmu zaświadczania X.509 jako *grup rejestracji*. 

    Można utworzyć grupę rejestracji przy użyciu zestawów SDK następującego przepływu pracy:

    1. Grupy rejestracji mechanizm zaświadczania używa X.509 certyfikatu głównego.  Wywoływanie interfejsu API zestawu SDK usługi ```X509Attestation.createFromRootCertificate``` przy użyciu certyfikatu głównego, aby utworzyć zaświadczania dla rejestracji.  Certyfikat główny X.509 znajduje się w pliku PEM lub jako ciąg.
    1. Utwórz nową ```EnrollmentGroup``` przy użyciu zmiennej ```attestation``` utworzony i unikatowy ```enrollmentGroupId```.  Opcjonalnie można ustawić parametrów, takich jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Wywoływanie interfejsu API zestawu SDK usługi ```createOrUpdateEnrollmentGroup``` w aplikacji zaplecza za pomocą ```EnrollmentGroup``` można utworzyć grupę rejestracji.

* **Rejestracji indywidualnej** wpis dla pojedynczego urządzenia, które może zarejestrować. Rejestracje indywidualne mogą używać certyfikatów X.509 lub tokenów SAS (z fizycznym lub wirtualnym module TPM) jako mechanizmów zaświadczania. Firma Microsoft zaleca używanie rejestracji indywidualnych w przypadku urządzeń, które wymagają unikatowej konfiguracji początkowej lub w przypadku urządzeń, których można używać tylko tokenów SAS za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Możesz utworzyć rejestrację indywidualną z zestawami SDK następującego przepływu pracy:
    
    1. Wybierz swoje ```attestation``` mechanizm, który może być modułu TPM lub X.509.
        1. **MODUŁ TPM**: Przy użyciu klucza poręczenia, z urządzenia fizycznego lub symulatora modułu TPM jako dane wejściowe, wywołując interfejs API zestawu SDK usługi ```TpmAttestation``` utworzyć zaświadczania dla rejestracji. 
        2. **X.509**: Za pomocą certyfikatu klienta jako dane wejściowe, wywołując interfejs API zestawu SDK usługi ```X509Attestation.createFromClientCertificate``` utworzyć zaświadczania dla rejestracji.
    2. Utwórz nową ```IndividualEnrollment``` zmiennej przy użyciu ```attestation``` utworzony i unikatowy ```registrationId``` jako dane wejściowe, który znajduje się na urządzeniu lub wygenerowane z symulatora modułu TPM.  Opcjonalnie można ustawić parametrów, takich jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Wywoływanie interfejsu API zestawu SDK usługi ```createOrUpdateIndividualEnrollment``` w aplikacji zaplecza za pomocą ```IndividualEnrollment``` do tworzenia rejestracji indywidualnej.

Po pomyślnym utworzeniu rejestracji usługi Device Provisioning Service zwraca wynik rejestracji. Ten przepływ pracy jest przedstawiona w przykładach [wspomniano](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Zaktualizuj wpis rejestracji

Po utworzeniu wpisu rejestracji można zaktualizować rejestrację.  Potencjalne scenariusze obejmują aktualizowanie żądaną właściwość, aktualizowanie metoda zaświadczania lub odbieranie prawa dostępu do urządzenia.  Istnieją różne interfejsy API dla indywidualnej rejestracji i rejestracji grupowej, ale ma rozróżnienia dla mechanizmu zaświadczania.

Możesz zaktualizować wpis rejestracji następującego przepływu pracy:
* **Rejestracja indywidualna**:
    1. Uzyskiwanie najnowszych rejestracji usługa aprowizacji pierwszy przy użyciu interfejsu API zestawu SDK usługi ```getIndividualEnrollment```.
    2. Zmodyfikuj parametr najnowsze rejestracji zgodnie z potrzebami. 
    3. Przy użyciu najnowszych rejestracji, Wywołaj interfejs API zestawu SDK usługi ```createOrUpdateIndividualEnrollment``` można zaktualizować swój wpis rejestracji.
* **Rejestrację grupową**:
    1. Uzyskiwanie najnowszych rejestracji usługa aprowizacji pierwszy przy użyciu interfejsu API zestawu SDK usługi ```getEnrollmentGroup```.
    2. Zmodyfikuj parametr najnowsze rejestracji zgodnie z potrzebami.
    3. Przy użyciu najnowszych rejestracji, Wywołaj interfejs API zestawu SDK usługi ```createOrUpdateEnrollmentGroup``` można zaktualizować swój wpis rejestracji.

Ten przepływ pracy jest przedstawiona w przykładach [wspomniano](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Usuń wpis rejestracji

* **Rejestracja indywidualna** można usunąć, wywołując interfejs API zestawu SDK usługi ```deleteIndividualEnrollment``` przy użyciu ```registrationId```.
* **Rejestrację grupową** można usunąć, wywołując interfejs API zestawu SDK usługi ```deleteEnrollmentGroup``` przy użyciu ```enrollmentGroupId```.

Ten przepływ pracy jest przedstawiona w przykładach [wspomniano](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operacja zbiorcza rejestracje indywidualne

Można wykonać operacji zbiorczej do tworzenia, aktualizacji lub usuwania wielu rejestracje indywidualne następującego przepływu pracy:

1. Tworzenie zmiennej, która zawiera wiele ```IndividualEnrollment```.  Implementacja ta zmienna różni się dla każdego języka.  Przejrzyj przykład operacji zbiorczej w witrynie GitHub, aby uzyskać szczegółowe informacje.
2. Wywoływanie interfejsu API zestawu SDK usługi ```runBulkOperation``` z ```BulkOperationMode``` dla żądanej operacji i zmiennej dla rejestracji indywidualnych. Cztery tryby są obsługiwane: tworzenie, aktualizowanie, updateIfMatchEtag i usuwanie.

Po pomyślnym wykonaniu operacji, usługi Device Provisioning zwróci wynik operacji zbiorczej.

Ten przepływ pracy jest przedstawiona w przykładach [wspomniano](#prerequisites).
