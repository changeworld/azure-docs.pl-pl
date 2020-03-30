---
title: Korzystanie z narzędzi zestawu SDK usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: W tym dokumencie przegląda narzędzia udostępniane w zestawach SDK usługi inicjowania obsługi urządzeń usługi Azure IoT Hub (DPS) w celu tworzenia
author: robinsh
ms.author: robinsh
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b817b3cfe47ed08cae9e7e0b1c2c24363f2ccfed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271527"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak korzystać z narzędzi udostępnianych w zestawach SDK w celu uproszczenia tworzenia programów inicjowania obsługi administracyjnej
Usługa inicjowania obsługi administracyjnej urządzeń w centrum IoT upraszcza proces inicjowania obsługi administracyjnej za pomocą automatycznego inicjowania obsługi administracyjnej typu "zero-touch", czyli [automatycznego inicjowania obsługi administracyjnej](concepts-auto-provisioning.md) typu "tylko w czasie".  Wymagane jest zaświadczenie zabezpieczeń w postaci certyfikatu X.509 lub modułu TPM (Trusted Platform Module).  Firma Microsoft współpracuje również z [innymi partnerami w dziedzinie sprzętu zabezpieczającego,](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) aby zwiększyć zaufanie do zabezpieczania wdrożenia IoT. Zrozumienie wymagań dotyczących zabezpieczeń sprzętu może być dość trudne dla deweloperów. Zestaw zestawów SDK usługi inicjowania obsługi administracyjnej usługi Azure IoT są dostarczane, dzięki czemu deweloperzy mogą używać warstwy wygody do pisania klientów, którzy rozmawiają z usługą inicjowania obsługi administracyjnej. Zestawy SDK zawierają również przykłady typowych scenariuszy, a także zestaw narzędzi, aby uprościć atest zabezpieczeń w rozwoju.

## <a name="trusted-platform-module-tpm-simulator"></a>Symulator modułu TPM (Trusted Platform Module)
[Moduł TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) może odwoływać się do standardu bezpiecznego przechowywania kluczy w celu uwierzytelnienia platformy lub może odwoływać się do interfejsu we/wy używanego do interakcji z modułami implementującymi standard. Moduły TPM mogą istnieć jako sprzęt dyskretny, zintegrowany sprzęt, oprogramowanie układowe lub oparte na oprogramowaniu.  W produkcji moduł TPM znajduje się na urządzeniu jako sprzęt dyskretny, zintegrowany sprzęt lub oparty na oprogramowaniu układowym. W fazie testowania programowy symulator modułu TPM jest dostarczany deweloperom.  Ten symulator jest dostępny tylko do tworzenia na platformie Windows na razie.

Kroki korzystania z symulatora modułu TPM to:
1. [Przygotuj środowisko programistyczne](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) i sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Przejdź do folderu symulatora modułu TPM w obszarze ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Uruchom plik Simulator.exe przed uruchomieniem dowolnej aplikacji klienckiej dla urządzenia inicjującego aprowizowanie.
4. Niech symulator działa w tle w całym procesie inicjowania obsługi administracyjnej, aby uzyskać identyfikator rejestracji i klucz poręczenia.  Obie wartości są prawidłowe tylko dla jednego wystąpienia uruchomienia.

## <a name="x509-certificate-generator"></a>Generator certyfikatów X.509
[Certyfikaty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) mogą służyć jako mechanizm zaświadczania w celu skalowania produkcji i upraszczania inicjowania obsługi administracyjnej urządzeń.  Certyfikat X.509 można uzyskać na [kilka sposobów:](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)
* W środowisku produkcyjnym zalecamy zakup certyfikatu urzędu certyfikacji X.509 od publicznego głównego urzędu certyfikacji.
* W środowisku testowym można wygenerować certyfikat główny X.509 lub łańcuch certyfikatów X.509 przy użyciu:
    * OpenSSL: Skrypty można używać do generowania certyfikatów:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Program PowerShell lub Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulator aparatu składu tożsamości urządzenia (DICE): DICE może być używany do identyfikacji urządzeń kryptograficznych i zaświadczania na podstawie protokołu TLS i certyfikatów klientów X.509.  [Dowiedz się](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) więcej o tożsamości urządzenia za pomocą DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Korzystanie z generatora certyfikatów X.509 z emulatorem DICE
SDK zapewniają generator certyfikatów X.509 z emulatorem DICE, znajdującym się w [java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Ten generator działa między platformami.  Wygenerowany certyfikat może służyć do tworzenia w innych językach.

Obecnie podczas gdy emulator DICE wyprowadza certyfikat główny, certyfikat pośredni, certyfikat liścia i skojarzony klucz prywatny.  Jednak certyfikat główny lub certyfikat pośredni nie może służyć do podpisywania oddzielnego certyfikatu liścia.  Jeśli zamierzasz przetestować scenariusz rejestracji grupy, w którym jeden certyfikat podpisywania jest używany do podpisywania certyfikatów liścia wielu urządzeń, można użyć OpenSSL do tworzenia łańcucha certyfikatów.

Aby wygenerować certyfikat X.509 przy użyciu tego generatora:
1. [Przygotuj środowisko programistyczne](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) i sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Zmień katalog główny na azure-iot-sdk-java.
3. Uruchom, ```mvn install -DskipTests=true``` aby pobrać wszystkie wymagane pakiety i skompilować zestaw SDK
4. Przejdź do katalogu głównego generatora certyfikatów ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```X.509 w .
5. Buduj z```mvn clean install```
6. Uruchom narzędzie przy użyciu następujących poleceń:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Po wyświetleniu monitu można opcjonalnie wprowadzić _nazwę pospolitą_ dla certyfikatów.
8. Narzędzie lokalnie generuje **certyfikat klienta,** **klucz prywatny certyfikatu klienta,** **certyfikat pośredni**i **certyfikat główny.**

**Certyfikat klienta** jest certyfikatem liścia na urządzeniu.  **Certyfikat klienta** i skojarzony **klucz prywatny certyfikatu klienta** są potrzebne w kliencie urządzenia. W zależności od wybranego języka mechanizm umieszczania tego w aplikacji klienckiej może być inny.  Aby uzyskać więcej informacji, zobacz [Przewodniki Szybki start](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) na tworzenie symulowanego urządzenia przy użyciu X.509, aby uzyskać więcej informacji.

Certyfikat główny lub pośredni może służyć do tworzenia grupy rejestracji lub rejestracji indywidualnej [programowo](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) lub za pomocą [portalu](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Następne kroki
* Tworzenie przy użyciu zestawu [SDK usługi Azure IoT]( https://github.com/Azure/azure-iot-sdks) dla usługi Azure IoT Hub i usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
