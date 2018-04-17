---
title: Zarządzanie za pomocą zestawów SDK programu Azure urządzenia inicjowania obsługi usługi rejestracji urządzeń | Dokumentacja firmy Microsoft
description: Jak zarządzać rejestracji urządzenia IoT Hub urządzenia inicjowania obsługi usługi przy użyciu zestawów SDK usługi
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 04/04/18
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 1ec86d319f529fe63b0924f4cfa0c2be178cd4d8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Sposób rejestracji urządzeń z zestawów SDK programu Azure urządzenia inicjowania obsługi usługi zarządzania
A *rejestracji urządzeń* tworzy rekord jednego urządzenia lub grupy urządzeń, które mogą w pewnym momencie zarejestrować w usłudze udostępniania urządzeń. Rekord rejestracji zawiera początkowej żądanej konfiguracji dla urządzeń w ramach rejestracji, łącznie z żądaną Centrum IoT. W tym artykule przedstawiono sposób zarządzania rejestracji urządzeń dla inicjowania obsługi usługi programowo przy użyciu usługi Azure IoT inicjowania obsługi usługi SDK.  Zestawy SDK są dostępne w witrynie GitHub, w tym samym repozytorium jako zestawy SDK IoT Azure.

## <a name="prerequisites"></a>Wymagania wstępne
* Uzyskaj parametry połączenia z wystąpieniem usługi inicjowania obsługi urządzeń.
* Uzyskaj urządzenie artefaktów zabezpieczeń dla [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) używany:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Poszczególne rejestracji: identyfikator rejestracji i klucza poręczenia modułu TPM z fizyczne urządzenie lub symulator modułu TPM.
        * Grupa rejestracji nie ma zastosowania do zaświadczenia modułu TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Poszczególne rejestracji: [certyfikatu liścia](/azure/iot-dps/concepts-security#leaf-certificate) z urządzenia fizycznego lub z zestawu SDK [selekcji](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) emulatora.
        * Grupa rejestracji: [/główny certyfikat urzędu certyfikacji](/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatu pośredniego](/azure/iot-dps/concepts-security#intermediate-certificate), użytego do utworzenia certyfikatu urządzenia na urządzeniu fizycznym.  Może być również generowany z emulatora selekcji zestawu SDK.
* Dokładne wywołań interfejsu API może się różnić z powodu różnic języka. Przejrzyj przykłady podane w witrynie GitHub, aby uzyskać szczegółowe informacje:
   * [Przykłady Java inicjowania obsługi administracyjnej klienta usługi](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Przykłady obsługi klienta usługi node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Przykłady .NET inicjowania obsługi administracyjnej klienta usługi](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzeń
Istnieją dwa sposoby mogą rejestrować urządzenia w usłudze inicjowania obsługi administracyjnej:

* **Grupy rejestracji** wpis dla grupy urządzeń, które współużytkują wspólnego mechanizmu zaświadczania certyfikatów X.509, podpisane przez [certyfikat główny](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatu pośredniego ](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Firma Microsoft zaleca używanie grupy rejestracji dużej liczby urządzeń mających żądanej konfiguracji początkowej lub urządzeń wszystkich przejść do tej samej dzierżawy. Należy pamiętać, że tylko mogą rejestrować urządzenia, które korzystają z mechanizmu zaświadczania X.509 jako *grup rejestracji*. 

    Można utworzyć grupę rejestracji z zestawów SDK, po tego przepływu pracy:

    1. Dla grupy rejestracji mechanizm zaświadczania używa certyfikatu głównego X.509.  Wywołanie interfejsu API zestawu SDK usługi ```X509Attestation.createFromRootCertificate``` certyfikat główny, aby utworzyć zaświadczanie o rejestracji.  Certyfikat X.509 znajduje się w pliku PEM lub jako ciąg.
    1. Utwórz nową ```EnrollmentGroup``` przy użyciu zmiennej ```attestation``` utworzony i unikatowe ```enrollmentGroupId```.  Opcjonalnie można ustawić parametry, takie jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Wywołanie interfejsu API zestawu SDK usługi ```createOrUpdateEnrollmentGroup``` do aplikacji zaplecza z ```EnrollmentGroup``` do utworzenia grupy rejestracji.

* **Poszczególnych rejestracji** wpis dla pojedynczego urządzenia, które mogą zarejestrować. Poszczególne rejestracji może używać certyfikatów X.509 lub tokeny sygnatury dostępu Współdzielonego (z modułem TPM fizycznych lub wirtualnych) jako mechanizmy zaświadczania. Zalecamy używanie poszczególnych rejestracji dla urządzeń, które wymagają szczególnej konfiguracji początkowej lub dla urządzeń, których można używać tylko tokeny sygnatury dostępu Współdzielonego, za pomocą modułu TPM lub wirtualne modułu TPM jako mechanizm zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Można utworzyć indywidualne rejestracji z zestawów SDK, ten przepływ pracy po:
    
    1. Wybierz użytkownika ```attestation``` mechanizm, który może być moduł TPM lub X.509.
        1. **Moduł TPM**: przy użyciu klucza poręczenia z fizyczne urządzenie lub symulator modułu TPM jako dane wejściowe, można wywołać interfejsu API zestawu SDK usługi ```TpmAttestation``` utworzyć zaświadczanie o rejestracji. 
        2. **X.509**: za pomocą certyfikatu klienta jako dane wejściowe, można wywołać interfejsu API zestawu SDK usługi ```X509Attestation.createFromClientCertificate``` utworzyć zaświadczanie o rejestracji.
    2. Utwórz nową ```IndividualEnrollment``` zmiennej przy użyciu ```attestation``` utworzony i unikatowe ```registrationId``` jako danych wejściowych, na urządzeniu lub generowana z symulatora modułu TPM.  Opcjonalnie można ustawić parametry, takie jak ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Wywołanie interfejsu API zestawu SDK usługi ```createOrUpdateIndividualEnrollment``` do aplikacji zaplecza z ```IndividualEnrollment``` do tworzenia indywidualnych rejestracji.

Po pomyślnym utworzeniu rejestracji urządzeń usługi udostępniania zwraca wynik w rejestracji. Ten przepływ pracy przedstawiono w przykładach [wspomniano](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aktualizacja wpisu rejestracji

Po utworzeniu wpisu rejestracji, możesz zaktualizować rejestrację.  Potencjalne scenariusze obejmują aktualizowanie żądanej właściwości, aktualizowania metody zaświadczania lub odwoływanie dostępu do urządzenia.  Istnieją różne interfejsów API dla poszczególnych rejestracji i rejestracji grupy, ale ma różnicy mechanizmu zaświadczania.

Można zaktualizować wpisu rejestracji po tego przepływu pracy:
* **Rejestracja indywidualna**:
    1. Pobrać najnowsze rejestracji z usługi inicjowania obsługi administracyjnej pierwszego z interfejsu API zestawu SDK usługi ```getIndividualEnrollment```.
    2. Zmodyfikuj parametr najnowsze rejestracji w razie potrzeby. 
    3. Przy użyciu najnowszych rejestracji, wywołania interfejsu API zestawu SDK usługi ```createOrUpdateIndividualEnrollment``` można zaktualizować wpisu rejestracji.
* **Rejestracja grupy**:
    1. Pobrać najnowsze rejestracji z usługi inicjowania obsługi administracyjnej pierwszego z interfejsu API zestawu SDK usługi ```getEnrollmentGroup```.
    2. Zmodyfikuj parametr najnowsze rejestracji w razie potrzeby.
    3. Przy użyciu najnowszych rejestracji, wywołania interfejsu API zestawu SDK usługi ```createOrUpdateEnrollmentGroup``` można zaktualizować wpisu rejestracji.

Ten przepływ pracy przedstawiono w przykładach [wspomniano](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Usuń wpis rejestracji

* **Poszczególne rejestracji** może zostać usunięta przez wywoływanie interfejsu API zestawu SDK usługi ```deleteIndividualEnrollment``` przy użyciu ```registrationId```.
* **Grupa rejestracji** może zostać usunięta przez wywoływanie interfejsu API zestawu SDK usługi ```deleteEnrollmentGroup``` przy użyciu ```enrollmentGroupId```.

Ten przepływ pracy przedstawiono w przykładach [wspomniano](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operacja zbiorcza na poszczególnych rejestracji

Można wykonać operacji zbiorczej do tworzenia, aktualizacji lub usuwania wiele poszczególnych rejestracji po tego przepływu pracy:

1. Tworzenie zmiennej, która zawiera wiele ```IndividualEnrollment```.  Implementacja tej zmiennej jest różne dla każdego języka.  Przejrzyj próbki operacji zbiorczej w serwisie GitHub szczegółowe informacje.
2. Wywołanie interfejsu API zestawu SDK usługi ```runBulkOperation``` z ```BulkOperationMode``` dla żądanej operacji i zmiennej użytkownika dla poszczególnych rejestracji. Cztery tryby są obsługiwane: tworzenie, aktualizowanie, updateIfMatchEtag i usunąć.

Po pomyślnym wykonaniu operacji, usługa inicjowania obsługi urządzeń zwróci wynik operacji zbiorczej.

Ten przepływ pracy przedstawiono w przykładach [wspomniano](#prerequisites).
