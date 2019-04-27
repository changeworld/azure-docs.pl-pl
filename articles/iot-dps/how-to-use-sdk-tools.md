---
title: Użyj narzędzi dostępnych w Centrum urządzenie inicjowania obsługi usługi zestawami SDK Azure IoT można Uproszczenie projektowania aplikacji
description: W tym dokumencie przeglądy narzędzi dostępnych w Centrum urządzenie inicjowania obsługi usługi zestawami SDK Azure IoT do tworzenia aplikacji
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627325"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Jak używać narzędzi dostępnych w zestawach SDK można Uproszczenie projektowania aplikacji do udostępniania
IoT Hub Device Provisioning Service upraszcza proces inicjowania obsługi administracyjnej za pomocą bezobsługowe, just-in-time [automatycznej aprowizacji](concepts-auto-provisioning.md) w sposób bezpieczny i ekonomiczny.  Poświadczenie zabezpieczeń w formie certyfikatu X.509 lub Trusted Platform Module (TPM) jest wymagana.  Microsoft jest również partnerstwo z [innych partnerów sprzętowych zabezpieczeń](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) Aby zwiększyć zaufanie Zabezpieczanie wdrożenia IoT. Zrozumienie wymagań dotyczących zabezpieczeń sprzętu może być bardzo trudne dla deweloperów. Zestaw SDK usługi aprowizacji IoT platformy Azure są dostarczane, dzięki czemu deweloperzy mogą używać warstwy jako udogodnienie dla klientów pisania, komunikujące się z usługą aprowizowania. Zestawy SDK udostępniają przykłady dla typowych scenariuszy, a także zestaw narzędzi można uproszczenie zabezpieczeń zaświadczania w trakcie opracowywania.

## <a name="trusted-platform-module-tpm-simulator"></a>Zaufany symulator Platform Module (TPM)
[Moduł TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) mogą odwoływać się do warstwy standardowa w zakresie bezpiecznego magazynowania klucze do uwierzytelniania platformy lub może odnosić się do interfejsu operacji We/Wy, używane do interakcji z modułów, implementacja standardowej. Moduły TPM może istnieć jako osobne sprzętu, sprzęt zintegrowane, oparte na oprogramowania układowego lub opartych na oprogramowaniu.  W środowisku produkcyjnym, modułu TPM znajduje się na urządzeniu, albo jako odrębny sprzętu, zintegrowane sprzętu lub oprogramowania układowego. W fazie testowania symulatora modułu TPM opartych na oprogramowaniu towarzyszy dla deweloperów.  Ten symulator jest dostępna tylko podczas programowania na platformie Windows na teraz.

Dostępne są następujące kroki dotyczące korzystania z symulatora modułu TPM:
1. [Przygotowywanie środowiska deweloperskiego](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) i sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Przejdź do folderu symulatora modułu TPM, w obszarze ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Uruchom Simulator.exe przed uruchomieniem każda aplikacja kliencka aprowizacji urządzenia.
4. Pozwól symulator uruchomione w tle w całym procesie inicjowania obsługi administracyjnej, aby uzyskać identyfikator rejestracji i kluczem poręczenia.  Obie wartości są prawidłowe tylko dla jednego wystąpienia uruchomienia.

## <a name="x509-certificate-generator"></a>Generator certyfikatów X.509
[Certyfikaty X.509](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) może służyć jako mechanizmu zaświadczania skalować produkcji i uprościć Inicjowanie obsługi administracyjnej urządzeń.  Istnieją [kilka sposobów](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) uzyskać certyfikat X.509:
* W środowisku produkcyjnym zaleca się zakup certyfikatu X.509 urzędu certyfikacji z publicznym głównym urzędu certyfikacji.
* W środowisku testowym, można wygenerować certyfikat główny X.509 lub przy użyciu łańcucha certyfikatów X.509:
    * OpenSSL: Skrypty można użyć do generowania certyfikatu:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [Programu PowerShell lub Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * Emulator aparatem (DICE) kompozycji tożsamości urządzenia: DICE może służyć do tożsamości urządzenia kryptograficznych i zaświadczania na podstawie protokołu TLS i X.509 certyfikaty klienta.  [Dowiedz się,](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) więcej informacji na temat tożsamości urządzenia z aparatem DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Za pomocą generator certyfikatów X.509 z emulatorem DICE
Zestawy SDK zapewniają generator certyfikatów X.509 z emulatorem DICE, znajduje się w [zestawu Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator).  Tego generatora działa dla wielu platform.  Wygenerowany certyfikat może służyć do tworzenia aplikacji w innych językach.

Obecnie, podczas gdy emulatorem DICE generuje certyfikat główny, pośredniego certyfikatu, certyfikat liścia i skojarzony klucz prywatny.  Jednak certyfikat głównego lub pośredniego certyfikatu nie może służyć do podpisywania certyfikatu liścia oddzielne.  Aby przeprowadzić test w scenariuszu z grupą rejestracji gdy jeden certyfikat podpisywania jest używany do podpisywania certyfikatów liścia wielu urządzeń, można użyć biblioteki OpenSSL do tworzenia łańcucha certyfikatów.

Aby wygenerować certyfikat X.509, za pomocą tego generatora:
1. [Przygotowywanie środowiska deweloperskiego](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) i sklonuj repozytorium GitHub:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Zmień katalogu głównego azure-iot-sdk-java.
3. Uruchom ```mvn install -DskipTests=true``` pobrać wszystkie wymagane pakiety i Skompiluj zestaw SDK
4. Przejdź do katalogu głównego dla Generator certyfikatów X.509 w ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Kompilowanie z ```mvn clean install```
6. Uruchom narzędzie przy użyciu następujących poleceń:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Po wyświetleniu monitu można opcjonalnie wprowadzić _nazwę pospolitą_ dla certyfikatów.
8. To narzędzie generuje lokalnie **certyfikat klienta**, **klucz prywatny certyfikatu klienta**, **pośredniego certyfikatu**i **certyfikat główny**.

**Certyfikat klienta** certyfikat liścia na urządzeniu.  **Certyfikat klienta** oraz skojarzonych z nimi **klucz prywatny certyfikatu klienta** potrzebnych dla klienta urządzenia. Zależności od tego, jakiego języka, możesz wybrać mechanizm należy umieścić w aplikacji klienckiej może się różnić.  Aby uzyskać więcej informacji, zobacz [przewodników Szybki Start](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) podczas tworzenia symulowanego urządzenia za pomocą X.509, aby uzyskać więcej informacji.

Certyfikat główny lub pośredni może służyć do tworzenia grupy rejestracji lub rejestracji indywidualnej [programowo](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) lub za pomocą [portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

## <a name="next-steps"></a>Kolejne kroki
* Programowanie przy użyciu [zestawu SDK Azure IoT]( https://github.com/Azure/azure-iot-sdks) dla usługi Azure IoT Hub i Azure IoT Hub Device Provisioning Service
