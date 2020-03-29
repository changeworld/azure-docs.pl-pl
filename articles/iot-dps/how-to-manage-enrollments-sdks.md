---
title: Zarządzanie rejestracjami urządzeń przy użyciu zestawów SDK dps platformy Azure
description: Jak zarządzać rejestracjami urządzeń w usłudze aprowizacji urządzeń usługi IoT Hub (DPS) przy użyciu sdk usług
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975078"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Jak zarządzać rejestracjami urządzeń za pomocą zestawów SDK usługi inicjowania obsługi administracyjnej urządzeń platformy Azure
*Rejestracja urządzenia* tworzy rekord pojedynczego urządzenia lub grupy urządzeń, które w pewnym momencie mogą zarejestrować się w usłudze inicjowania obsługi administracyjnej urządzeń. Rekord rejestracji zawiera początkową żądaną konfigurację dla urządzeń w ramach tej rejestracji, w tym żądaną ioT hub. W tym artykule pokazano, jak programowo zarządzać rejestracjami urządzeń dla usługi inicjowania obsługi administracyjnej przy użyciu zestawów SDK usługi inicjowania obsługi administracyjnej usługi Azure IoT.  Zestawy SDK są dostępne w usłudze GitHub w tym samym repozytorium co zestaw SDK usługi Azure IoT.

## <a name="prerequisites"></a>Wymagania wstępne
* Uzyskaj parametry połączenia z wystąpienia usługi inicjowania obsługi administracyjnej urządzenia.
* Uzyskaj artefakty zabezpieczeń urządzenia dla zastosowanego [mechanizmu zaświadczania:](concepts-security.md#attestation-mechanism)
    * [**Moduł zaufanej platformy (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Rejestracja indywidualna: identyfikator rejestracji i klucz poręczenia modułu TPM z urządzenia fizycznego lub z symulatora modułu TPM.
        * Grupa rejestracji nie ma zastosowania do zaświadczania tpm.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Rejestracja indywidualna: [certyfikat liść](/azure/iot-dps/concepts-security) z urządzenia fizycznego lub emulatora [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) SDK.
        * Grupa rejestracji: [certyfikat urzędu certyfikacji/głównego](/azure/iot-dps/concepts-security#root-certificate) lub [certyfikat pośredni](/azure/iot-dps/concepts-security#intermediate-certificate), używany do tworzenia certyfikatu urządzenia na urządzeniu fizycznym.  Może być również generowany z emulatora DICE SDK.
* Dokładne wywołania interfejsu API mogą być różne ze względu na różnice językowe. Szczegółowe informacje można znaleźć w przykładzie podanym w usłudze GitHub:
   * [Przykłady klienta usługi inicjowania obsługi administracyjnej w języku Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Przykłady klienta usługi inicjowania obsługi administracyjnej node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Przykłady klienta usługi inicjowania obsługi administracyjnej platformy .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzenia
Istnieją dwa sposoby rejestrowania urządzeń za pomocą usługi inicjowania obsługi administracyjnej:

* **Grupa Rejestracja** to wpis dla grupy urządzeń, które mają wspólny mechanizm zaświadczania certyfikatów X.509, podpisany [certyfikatem głównym](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) lub [certyfikatem pośrednim.](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate) Zaleca się używanie grupy rejestracji dla dużej liczby urządzeń, które współużytkują żądaną konfigurację początkową lub dla urządzeń przechodzących do tej samej dzierżawy. Należy zauważyć, że jako grupy rejestracji można rejestrować tylko urządzenia *korzystające*z mechanizmu zaświadczania X.509. 

    Po tym przepływie pracy można utworzyć grupę rejestracji z sksem SDK:

    1. W przypadku grupy rejestracji mechanizm zaświadczania używa certyfikatu głównego X.509.  Wywołanie interfejsu API ```X509Attestation.createFromRootCertificate``` SDK usługi z certyfikatem głównym w celu utworzenia zaświadczenia dla rejestracji.  Certyfikat główny X.509 znajduje się w pliku PEM lub jako ciąg.
    1. Utwórz ```EnrollmentGroup``` nową zmienną przy użyciu utworzonej ```attestation``` i unikatowej ```enrollmentGroupId```.  Opcjonalnie można ustawić parametry, ```IoTHubHostName``` ```ProvisioningStatus```takie jak ```Device ID```, , .
    2. Wywołanie interfejsu API ```createOrUpdateEnrollmentGroup``` SDK usługi ```EnrollmentGroup``` w aplikacji wewnętrznej bazy danych w celu utworzenia grupy rejestracji.

* **Rejestracja indywidualna** to wpis dla jednego urządzenia, które może się zarejestrować. Poszczególne rejestracje mogą używać certyfikatów X.509 lub tokenów Sygnatury dostępu Współdzielonego (z fizycznego lub wirtualnego modułu TPM) jako mechanizmów zaświadczania. Zaleca się używanie poszczególnych rejestracji dla urządzeń, które wymagają unikatowych konfiguracji początkowych lub dla urządzeń, które mogą używać tylko tokenów Sygnatury dostępu Współdzielonego za pośrednictwem modułu TPM lub wirtualnego modułu TPM jako mechanizmu zaświadczania. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

    Można utworzyć rejestrację indywidualną za pomocą szesnastów SDK następujących po tym przepływie pracy:
    
    1. Wybierz ```attestation``` mechanizm, który może być TPM lub X.509.
        1. **Moduł TPM**: Przy użyciu klucza poręczenia z urządzenia fizycznego lub z ```TpmAttestation``` symulatora modułu TPM jako dane wejściowe, można wywołać interfejs API SDK usługi, aby utworzyć zaświadczenie do rejestracji. 
        2. **X.509**: Przy użyciu certyfikatu klienta jako danych wejściowych można wywołać interfejs API ```X509Attestation.createFromClientCertificate``` SDK usługi, aby utworzyć zaświadczenie do rejestracji.
    2. Utwórz ```IndividualEnrollment``` nową zmienną ```attestation``` przy użyciu ```registrationId``` utworzonego i unikatowy jako dane wejściowe, który jest na urządzeniu lub generowane z symulatora modułu TPM.  Opcjonalnie można ustawić parametry, ```IoTHubHostName``` ```ProvisioningStatus```takie jak ```Device ID```, , .
    3. Wywołanie interfejsu API ```createOrUpdateIndividualEnrollment``` SDK usługi ```IndividualEnrollment``` w aplikacji wewnętrznej bazy danych w celu utworzenia rejestracji indywidualnej.

Po pomyślnym utworzeniu rejestracji usługa inicjowania obsługi administracyjnej urządzeń zwraca wynik rejestracji. Ten przepływ pracy jest pokazany w przykładach [wymienionych wcześniej](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aktualizowanie wpisu rejestracji

Po utworzeniu wpisu rejestracji można zaktualizować rejestrację.  Potencjalne scenariusze obejmują aktualizowanie żądanej właściwości, aktualizowanie metody zaświadczania lub odwoływanie dostępu do urządzenia.  Istnieją różne interfejsy API dla rejestracji indywidualnej i rejestracji grupowej, ale nie ma rozróżnienia dla mechanizmu zaświadczania.

Wpis rejestracji można zaktualizować po tym przepływie pracy:
* **Rejestracja indywidualna**:
    1. Najpierw pobierz najnowszą rejestrację z usługi inicjowania obsługi administracyjnej za pomocą interfejsu API ```getIndividualEnrollment```SDK usługi .
    2. W razie potrzeby zmodyfikuj parametr najnowszej rejestracji. 
    3. Korzystając z najnowszej rejestracji, zadzwoń ```createOrUpdateIndividualEnrollment``` do interfejsu API SDK usługi, aby zaktualizować wpis rejestracji.
* **Rejestracja grupowa**:
    1. Najpierw pobierz najnowszą rejestrację z usługi inicjowania obsługi administracyjnej za pomocą interfejsu API ```getEnrollmentGroup```SDK usługi .
    2. W razie potrzeby zmodyfikuj parametr najnowszej rejestracji.
    3. Korzystając z najnowszej rejestracji, zadzwoń ```createOrUpdateEnrollmentGroup``` do interfejsu API SDK usługi, aby zaktualizować wpis rejestracji.

Ten przepływ pracy jest pokazany w przykładach [wymienionych wcześniej](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Usuwanie wpisu rejestracji

* **Rejestrację indywidualną** można usunąć, wywołując ```deleteIndividualEnrollment``` ```registrationId```interfejs API SDK usługi przy użyciu programu .
* **Rejestrację grupy** można usunąć, wywołując ```deleteEnrollmentGroup``` interfejs ```enrollmentGroupId```API SDK usługi przy użyciu programu .

Ten przepływ pracy jest pokazany w przykładach [wymienionych wcześniej](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operacja zbiorcza w przypadku rejestracji indywidualnych

Można wykonać operację zbiorczą, aby utworzyć, zaktualizować lub usunąć wiele pojedynczych rejestracji po tym przepływie pracy:

1. Utwórz zmienną ```IndividualEnrollment```zawierającą wiele .  Implementacja tej zmiennej jest inna dla każdego języka.  Przejrzyj przykład operacji zbiorczej w usłudze GitHub, aby uzyskać szczegółowe informacje.
2. Wywołaj interfejs API ```runBulkOperation``` SDK usługi z żądaną operacją ```BulkOperationMode``` i zmienną dla poszczególnych rejestracji. Obsługiwane są cztery tryby: tworzenie, aktualizowanie, aktualizowanieIfMatchEtag i usuwanie.

Po pomyślnym wykonaniu operacji usługa inicjowania obsługi administracyjnej urządzeń zwróci wynik operacji zbiorczej.

Ten przepływ pracy jest pokazany w przykładach [wymienionych wcześniej](#prerequisites).
