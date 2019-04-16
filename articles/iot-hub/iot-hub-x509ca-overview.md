---
title: Omówienie zabezpieczeń usługi Azure IoT Hub X.509 z urzędu certyfikacji | Dokumentacja firmy Microsoft
description: Przegląd — sposobu uwierzytelniania urządzeń do Centrum IoT przy użyciu urzędów certyfikacji X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: b7464e5cc052ecade4a10102de947d37a63c962a
ms.sourcegitcommit: e89b9a75e3710559a9d2c705801c306c4e3de16c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59571158"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Uwierzytelnianie urządzeń przy użyciu certyfikatu X.509 urzędu certyfikacji

W tym artykule opisano, jak używać certyfikatów X.509 urząd certyfikacji (CA) do uwierzytelniania urządzeń nawiązujących połączenie usługi IoT Hub.  W tym artykule dowiesz się:

* Jak uzyskać certyfikat X.509 urzędu certyfikacji
* Jak zarejestrować certyfikat X.509 urzędu certyfikacji do usługi IoT Hub
* Jak zarejestrować urządzenia za pomocą certyfikatów X.509 urzędu certyfikacji
* Sposób uwierzytelniania urządzeń podpisany przy użyciu X.509 urzędu certyfikacji

## <a name="overview"></a>Omówienie

Funkcja X.509 urzędu certyfikacji umożliwia uwierzytelnianie urządzenia w Centrum IoT przy użyciu urząd certyfikacji (CA). Proces rejestracji urządzenie początkowe i logistyki łańcucha dostaw znacznie upraszcza podczas produkcji urządzenia. [Dowiedz się więcej w tym artykule scenariusz o wartości z przy użyciu certyfikatów X.509 urzędu certyfikacji](iot-hub-x509ca-concept.md) uwierzytelniania urządzenia.  Firma Microsoft zachęca do przeczytaj ten artykuł scenariusz, przed kontynuowaniem, ponieważ wyjaśnia, dlaczego istnieje opisanych poniżej.

## <a name="prerequisite"></a>Wymagania wstępne

Korzystanie z funkcji X.509 urzędu certyfikacji wymaga, że masz konto usługi IoT Hub.  [Dowiedz się, jak utworzyć wystąpienie usługi IoT Hub](quickstart-send-telemetry-dotnet.md) Jeśli nie masz jeszcze jeden.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak uzyskać certyfikat X.509 urzędu certyfikacji

Certyfikat X.509 urzędu certyfikacji znajduje się na górze łańcucha certyfikatów dla każdego z urządzeń.  Możesz zakupić lub utworzyć w zależności od sposobu, w których zamierzasz używać go.

W środowisku produkcyjnym zaleca się zakupienie certyfikatu X.509 urzędu certyfikacji od głównego publicznego urzędu certyfikacji. Zakup certyfikatu urzędu certyfikacji ma tę zaletę główny urząd certyfikacji działający jako dla zaufanych osób trzecich ciesząca legalności urządzeń. Rozważ użycie tej opcji, jeśli jest planowane urządzenia jako część otwartej sieci IoT gdzie one może korzystać z innych firm, produktów lub usług.

Można również utworzyć podpisem własnym X.509 urzędu certyfikacji do eksperymentowania lub do wykorzystania w zamkniętym sieciach IoT.

Niezależnie od tego, jak uzyskać certyfikat X.509 urzędu certyfikacji, pamiętaj zachować jego odpowiedniego prywatnego klucza wpisu tajnego i chronione przez cały czas.  Jest to niezbędne do tworzenia relacji zaufania w przypadku uwierzytelniania X.509 urzędu certyfikacji zaufania.

Dowiedz się, jak [tworzenia certyfikatu urzędu certyfikacji z podpisem własnym](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), używane w ramach eksperymentów w całym opis tej funkcji.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Rejestrowania urządzenia w usłudze łańcuch zaufania certyfikatów

Właściciela certyfikatu X.509 urzędu certyfikacji kryptograficznie można zarejestrować pośrednich urzędów certyfikacji, który z kolei może zarejestrować innego pośredniego urzędu certyfikacji i tak dalej, aż do ostatniego pośredniego urzędu certyfikacji przerywa ten proces, rejestrując urządzenia. Wynik jest kaskadowy łańcucha certyfikatów, znane jako łańcuch zaufania certyfikatów. W realnym odgrywa jako delegowania zaufania do podpisywania urządzeń. To Delegowanie jest ważne, ponieważ ustanawia kryptograficznie zmiennej łańcuch nadzoru i pozwala uniknąć udostępniania kluczy podpisywania.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Certyfikat urządzenia (nazywane również certyfikat liścia) musi mieć *nazwy podmiotu* równa **identyfikator urządzenia** użytą podczas rejestrowania urządzeń IoT w usłudze Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

Tutaj dowiesz się jak [tworzenie łańcucha certyfikatów](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) co wykonywane podczas rejestrowania urządzenia.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zarejestrować certyfikat X.509 urzędu certyfikacji do usługi IoT Hub

Zarejestruj certyfikat X.509 urzędu certyfikacji do Centrum IoT, w którym będzie służyć do uwierzytelniania urządzeń podczas rejestracji i połączenia.  Rejestrowanie certyfikatu X.509 urzędu certyfikacji jest procesem dwuetapowym, który składa się z dowodu posiadania i przekazywania certyfikatu.

Przekazywanie pliku, który zawiera certyfikat w procesie przekazywania pociąga za sobą.  Ten plik nigdy nie powinien zawierać żadnych kluczy prywatnych.

Dowód przesyłany krok polega na kryptograficznych żądania i odpowiedzi procesu między Tobą i Centrum IoT Hub.  Biorąc pod uwagę, że certyfikat cyfrowy są publiczne, jak i w związku z tym podatne na podsłuchiwaniu, usługi IoT Hub chcesz upewnić się, tak naprawdę własny certyfikat urzędu certyfikacji.  Powinna to zrobić, generując wezwanie losowych, należy zalogować się przy użyciu odpowiedniego klucza prywatnego certyfikatu urzędu certyfikacji.  Jeśli pozostawiono klucza prywatnego wpisu tajnego i chronione poinformowany jak wcześniej, a następnie tylko będzie posiadać wiedzy w celu ukończenia tego kroku. Tajemnicy kluczy prywatnych jest źródłem zaufania w przypadku tej metody.  Po zarejestrowaniu wyzwanie, należy wykonać ten krok, przekazując plik zawierający wyniki.

Tutaj dowiesz się jak [zarejestrować certyfikat urzędu certyfikacji](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Jak utworzyć urządzenie w Centrum IoT Hub

Aby wykluczyć personifikacji urządzenia, Centrum IoT wymaga Niech wiedzieć, jakie urządzenia można oczekiwać.  Możesz to zrobić, tworząc wpis urządzenia w rejestrze urządzeń usługi IoT Hub.  Proces ten jest zautomatyzować, korzystając z usługi IoT Hub [usługi Device Provisioning](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Tutaj dowiesz się jak [ręcznie utworzyć urządzenie w usłudze IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Tworzenie urządzenia X.509 dla usługi IoT hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Uwierzytelnianie urządzeń podpisane za pomocą certyfikatów X.509 urzędu certyfikacji

Zarejestrowany certyfikat X.509 urzędu certyfikacji i urządzenia zalogowali się do łańcuch zaufania certyfikatów, co pozostanie, jest uwierzytelnianie urządzeń, gdy urządzenie nawiązuje połączenie, nawet po raz pierwszy.  Po zalogowaniu X.509 urzędu certyfikacji urządzenia przenośnego, przekazuje jej łańcuch certyfikatów do sprawdzania poprawności. Ciąg zawiera wszystkie certyfikaty pośrednie urzędu certyfikacji i urządzenia.  Dzięki tym informacjom usługi IoT Hub uwierzytelnia urządzenie w dwóch etapach.  Usługa IoT Hub kryptograficznie sprawdza poprawność łańcucha certyfikatów dla wewnętrznego sprawdzania spójności, a następnie generuje żądanie dowodu posiadania na urządzeniu.  Usługa IoT Hub deklaruje urządzenia autentyczne na odpowiedź oznaczająca Powodzenie dowodu posiadania z urządzenia.  Ta deklaracja zakłada ochronę klucza prywatnego na urządzeniu i że tylko urządzenie pomyślnie może odpowiadać temu wyzwaniu.  Firma Microsoft zaleca korzystanie z bezpiecznej mikroukładami takich jak sprzętu bezpiecznych modułów (HSM) na urządzeniach w celu ochrony kluczy prywatnych.

Połączenie urządzenia pomyślnie do Centrum IoT Hub jest również wskazuje prawidłową konfigurację i kończy proces uwierzytelniania.

Tutaj dowiesz się jak [ukończenia tego kroku połączenia urządzenia](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wartość uwierzytelniania X.509 urzędu certyfikacji](iot-hub-x509ca-concept.md) w IoT.

Rozpoczynanie pracy z usługą IoT Hub [usługi Device Provisioning](https://docs.microsoft.com/azure/iot-dps/).
