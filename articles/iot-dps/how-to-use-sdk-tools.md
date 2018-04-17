---
title: Upraszczanie tworzenia przy użyciu narzędzi dostępnych w Azure Centrum inicjowania obsługi usługi zestawy SDK urządzenia IoT
description: Ten dokument przegląda z narzędzi dostępnych w Azure Centrum inicjowania obsługi usługi zestawy SDK urządzenia IoT dla rozwoju
services: iot-dps
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a7e1a55bcfc8c7a8f76416fb12edf712340fadd2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak użyć narzędzi dostępnych w zestawy SDK, aby uprościć programowanie do inicjowania obsługi
Usługa inicjowania obsługi urządzeń Centrum IoT upraszcza proces inicjowania obsługi administracyjnej z zero touch just in time [automatycznego inicjowania obsługi administracyjnej](concepts-auto-provisioning.md) w sposób bezpieczny i skalowalności.  Wymagane jest poświadczenie zabezpieczeń w formie certyfikatu X.509 lub modułu Trusted Platform Module (TPM).  Microsoft, jest również partnerstwo z [innych zabezpieczeń dostawców sprzętu będących partnerami](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) Aby zwiększyć zaufanie Zabezpieczanie wdrożenia IoT. Opis wymagań sprzętowych zabezpieczeń może być bardzo trudne dla deweloperów. Zestaw SDK usługi udostępniania IoT Azure są dostarczane, dzięki czemu deweloperzy mogą używać warstwy wygody dla klientów zapisu, którzy komunikują się inicjowania obsługi administracyjnej usługi. Zestawy SDK udostępniają przykłady dla typowych scenariuszy, a także zestaw narzędzi, aby uprościć poświadczenie zabezpieczeń do rozwoju.

## <a name="trusted-platform-module-tpm-simulator"></a>Zaufany symulatora TPM (Trusted Platform Module)
[Moduł TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) mogą odwoływać się do standardowego dla bezpieczne przechowywanie kluczy do uwierzytelniania platformy, lub może się odnosić do interfejsu we/wy służy do interakcji z modułami implementowania standardowego. Moduły TPM może istnieć jako odrębny sprzętu, sprzęt zintegrowane, oparte na oprogramowania sprzętowego lub programowego.  W środowisku produkcyjnym, modułu TPM znajduje się na urządzeniu, albo jako odrębny sprzętu, zintegrowane sprzętu lub oprogramowania układowego. W fazie testowania symulatora programowego modułu TPM zapewnia deweloperom.  Symulator ten jest dostępny tylko dla tworzenie aplikacji na platformie systemu Windows dla teraz.

Kroki do używania symulatora modułu TPM są:
1. [Przygotuj środowisko projektowe](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) i klonowania repozytorium GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Przejdź do folderu symulatora modułu TPM, w obszarze ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Uruchom Simulator.exe przed uruchomieniem żadnych aplikacji klienckiej dla inicjowania obsługi urządzenia.
4. Let symulatora uruchomione w tle podczas procesu inicjowania obsługi administracyjnej uzyskanie rejestracji identyfikator i klucz poręczenia.  Obie wartości są prawidłowe tylko dla jednego wystąpienia uruchomienia.

## <a name="x509-certificate-generator"></a>Generator certyfikatu X.509
[Certyfikaty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) może służyć jako mechanizmu zaświadczania do skalowania produkcyjnych i uprościć Inicjowanie obsługi administracyjnej urządzeń.  Brak [kilka sposobów](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) uzyskiwania certyfikatu X.509:
* W środowisku produkcyjnym zaleca się zakup certyfikatu X.509 urzędu certyfikacji od głównego publicznego urzędu certyfikacji.
* W środowisku testowym, można wygenerować certyfikatu X.509 głównego lub przy użyciu łańcucha certyfikatów X.509:
    * Biblioteki OpenSSL: to [jak przewodnik](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-create-certificates) przedstawiono przykładowe skrypty programu PowerShell, korzystających z [OpenSSL](https://www.openssl.org/) do tworzenia i podpisywania certyfikatów X.509.  Ponadto możesz również użyć skryptu w innych językach do generowania certyfikatu:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Program PowerShell](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Urządzenia emulatora aparat kompozycji tożsamości (GRUPOWANE): Selekcji może służyć do szyfrowania urządzenia tożsamości i zaświadczania na podstawie protokołu TLS i X.509 certyfikaty klienta.  [Dowiedz się](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) więcej informacji o tożsamości urządzenia z GRUPOWANE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Za pomocą generatora certyfikatu X.509 emulatorze selekcji
Zestawy SDK zapewniają generator certyfikatu X.509 z emulatora GRUPOWANE, znajduje się w [zestawu Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Tego generatora działa między platformami.  Wygenerowany certyfikat może służyć do tworzenia aplikacji w innych językach.

Obecnie, podczas gdy Emulator selekcji generuje certyfikat główny, certyfikatu pośredniego, certyfikatu liścia i skojarzony klucz prywatny.  Jednak certyfikatu głównego lub pośredniego certyfikatu nie może służyć do podpisywania certyfikatu oddzielne typu liść.  Jeśli zamierzasz grupy rejestracji scenariusza testu gdzie jeden certyfikat podpisywania jest używany do podpisywania certyfikatów liścia wielu urządzeń, można użyć biblioteki OpenSSL do tworzenia łańcucha certyfikatów.

Do wygenerowania certyfikatu X.509 przy użyciu tego generatora:
1. [Przygotuj środowisko projektowe](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment) i klonowania repozytorium GitHub:
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. Zmień katalog główny azure-iot-sdk-java.
3. Uruchom ```mvn install -DskipTests=true``` do pobrania wszystkich wymaganych pakietów i kompilowania zestawu SDK
4. Przejdź do katalogu głównego dla generatora certyfikatu X.509 w ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Kompiluj z ```mvn clean install```
6. Uruchom narzędzie przy użyciu następujących poleceń:
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. Po wyświetleniu monitu można opcjonalnie wprowadzić _nazwę pospolitą_ dla certyfikatów.
8. Generuje lokalnie narzędzie **certyfikatu klienta**, **klucza prywatnego certyfikatu klienta**, **certyfikatu pośredniego**i **certyfikatu głównego**.

**Certyfikat klienta** certyfikatu liścia na urządzeniu.  **Certyfikat klienta** oraz skojarzonych z nimi **klucza prywatnego certyfikatu klienta** klienta urządzenia nie są wymagane. W zależności od tego, jakie możesz wybrać język mechanizm umieścić w aplikacji klienckiej mogą się różnić.  Aby uzyskać więcej informacji, zobacz [Quickstarts](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) przy tworzeniu symulowane urządzenie przy użyciu X.509, aby uzyskać więcej informacji.

Certyfikat główny lub pośredni może służyć do tworzenia grupy rejestrowania lub rejestrowania poszczególnych [programowo](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) lub przy użyciu [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Kolejne kroki
* Za pomocą [zestawu SDK usługi Azure IoT]( https://github.com/Azure/azure-iot-sdks) Centrum IoT Azure i usługi inicjowania obsługi urządzeń Centrum IoT Azure