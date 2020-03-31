---
title: Omówienie zabezpieczeń urzędu certyfikacji usługi Azure IoT Hub X.509 | Dokumenty firmy Microsoft
description: Omówienie — sposób uwierzytelniania urządzeń w centrum IoT Hub przy użyciu urzędów certyfikacji X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284514"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Uwierzytelnianie urządzeń za pomocą certyfikatów X.509 urzędu certyfikacji

W tym artykule opisano sposób używania certyfikatów urzędu certyfikacji X.509 (CA) do uwierzytelniania urządzeń łączących centrum IoT Hub.  W tym artykule dowiesz się:

* Jak uzyskać certyfikat urzędu certyfikacji X.509
* Jak zarejestrować certyfikat urzędu certyfikacji X.509 w centrum IoT Hub
* Jak podpisywać urządzenia przy użyciu certyfikatów X.509 urzędu certyfikacji
* Sposób uwierzytelniania urządzeń podpisanych za pomocą urzędu certyfikacji X.509

## <a name="overview"></a>Omówienie

Funkcja urzędu certyfikacji X.509 umożliwia uwierzytelnianie urządzeń w Centrum IoT przy użyciu urzędu certyfikacji (CA). Znacznie upraszcza to proces rejestracji urządzeń początkowych i logistykę łańcucha dostaw podczas produkcji urządzeń. [Dowiedz się więcej w tym artykule scenariusza o wartości używania certyfikatów urzędu certyfikacji X.509](iot-hub-x509ca-concept.md) do uwierzytelniania urządzeń.  Zachęcamy do przeczytania tego artykułu scenariusz przed kontynuowaniem, ponieważ wyjaśnia, dlaczego istnieją następujące kroki.

## <a name="prerequisite"></a>Wymagania wstępne

Korzystanie z funkcji urzędu certyfikacji X.509 wymaga konta Usługi IoT Hub.  [Dowiedz się, jak utworzyć wystąpienie usługi IoT Hub,](quickstart-send-telemetry-dotnet.md) jeśli jeszcze go nie masz.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak uzyskać certyfikat urzędu certyfikacji X.509

Certyfikat urzędu certyfikacji X.509 znajduje się na szczycie łańcucha certyfikatów dla każdego z twoich urządzeń.  Możesz go kupić lub utworzyć w zależności od tego, jak zamierzasz go używać.

W środowisku produkcyjnym zaleca się zakup certyfikatu urzędu certyfikacji X.509 od publicznego głównego urzędu certyfikacji. Zakup certyfikatu urzędu certyfikacji ma zaletę, że główny urząd certyfikacji działa jako zaufana strona trzecia, aby ręczyć za legalność urządzeń. Rozważ tę opcję, jeśli zamierzasz, aby twoje urządzenia były częścią otwartej sieci IoT, w której oczekuje się, że będą wchodzić w interakcje z produktami lub usługami innych firm.

Można również utworzyć urząd certyfikacji x.509 z podpisem własnym do eksperymentowania lub do użytku w zamkniętych sieciach IoT.

Niezależnie od tego, w jaki sposób uzyskujesz certyfikat urzędu certyfikacji X.509, upewnij się, że cały czas zachowuje on odpowiedni klucz prywatny w tajemnicy i jest chroniony.  Jest to konieczne dla budowania zaufania zaufania do uwierzytelniania urzędu certyfikacji X.509.

Dowiedz się, jak [utworzyć certyfikat urzędu certyfikacji z podpisem własnym,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)którego można użyć do eksperymentowania w tym opisie funkcji.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Podpisywanie urządzeń w łańcuchu zaufania certyfikatów

Właściciel certyfikatu urzędu certyfikacji X.509 może kryptograficznie podpisać pośredni urząd certyfikacji, który z kolei może podpisać inny pośredni urząd certyfikacji i tak dalej, dopóki ostatni pośredni urząd certyfikacji nie zakończy tego procesu podpisując urządzenie. Rezultatem jest kaskadowy łańcuch certyfikatów znany jako łańcuch zaufania certyfikatów. W prawdziwym życiu jest to delegacja zaufania do urządzeń do podpisywania. Ta delegowanie jest ważne, ponieważ ustanawia kryptograficznie zmienny łańcuch opieki i unika udostępniania kluczy podpisywania.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Certyfikat urządzenia (nazywany również certyfikatem typu liść) musi mieć ustawioną *nazwę podmiotu* na **identyfikator urządzenia,** który był używany podczas rejestrowania urządzenia IoT w centrum Azure IoT Hub. To ustawienie jest wymagane do uwierzytelniania.

Dowiedz się, jak [utworzyć łańcuch certyfikatów,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) tak jak miało to miejsce podczas podpisywania urządzeń.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zarejestrować certyfikat urzędu certyfikacji X.509 w centrum IoT Hub

Zarejestruj certyfikat urzędu certyfikacji X.509 w Urzędzie IoT Hub, gdzie będzie on używany do uwierzytelniania urządzeń podczas rejestracji i nawiązywać połączenia.  Rejestrowanie certyfikatu urzędu certyfikacji X.509 jest procesem dwuetapowym, który obejmuje przekazywanie plików certyfikatów i dowód posiadania.

Proces przekazywania polega na przesłaniu pliku zawierającego certyfikat.  Ten plik nigdy nie powinien zawierać żadnych kluczy prywatnych.

Krok dowodu posiadania obejmuje proces wyzwania kryptograficznego i odpowiedzi między tobą a Centrum IoT Hub.  Biorąc pod uwagę, że zawartość certyfikatu cyfrowego jest publiczna i dlatego jest podatna na podsłuchiwanie, usługa IoT Hub pragnie upewnić się, że certyfikat urzędu certyfikacji jest naprawdę posiadany.  Czyni to poprzez wygenerowanie losowego wyzwania, które należy podpisać za pomocą odpowiedniego klucza prywatnego certyfikatu urzędu certyfikacji.  Jeśli zachowałeś klucz prywatny w tajemnicy i był chroniony zgodnie z wcześniejszymi zaleceniami, tylko ty będziesz posiadał wiedzę, aby wykonać ten krok. Tajemnica kluczy prywatnych jest źródłem zaufania w tej metodzie.  Po podpisaniu wyzwania wykonaj ten krok, przesyłając plik zawierający wyniki.

Dowiedz się, jak [zarejestrować certyfikat urzędu certyfikacji](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Jak utworzyć urządzenie w Centrum IoT

Aby wykluczyć personifikacji urządzenia, Usługi IoT Hub wymaga poinformowania go, jakich urządzeń należy się spodziewać.  Można to zrobić, tworząc wpis urządzenia w rejestrze urządzeń usługi IoT Hub.  Ten proces jest zautomatyzowany podczas korzystania z [usługi inicjowania obsługi administracyjnej urządzeń usługi](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)IoT Hub. 

Dowiedz się, jak [ręcznie utworzyć urządzenie w Centrum IoT](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)Hub .

Tworzenie urządzenia X.509 dla centrum IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Urządzenia uwierzytelniające podpisane certyfikatami X.509 urzędu certyfikacji

Po zarejestrowaniu certyfikatu urzędu certyfikacji X.509 i zalogowaniu się do łańcucha zaufania certyfikatów nadal pozostaje uwierzytelnianie urządzenia podczas nawiązywania połączenia, nawet po raz pierwszy.  Po nawiązaniu połączenia z urządzeniem podpisanym przez urząd certyfikacji X.509 przekazuje łańcuch certyfikatów w celu weryfikacji. Łańcuch obejmuje wszystkie pośrednie certyfikaty urzędu certyfikacji i urządzeń.  Dzięki tym informacjom Centrum IoT hub uwierzytelnia urządzenie w dwuetapowym procesie.  Kryptograficznie sprawdza poprawność łańcucha certyfikatów pod kątem wewnętrznej spójności, a następnie wystawia wyzwanie weryfikacji posiadania urządzenia.  Usługa IoT Hub deklaruje autentyczność urządzenia w przypadku pomyślnej odpowiedzi na potwierdzenie posiadania z urządzenia.  Ta deklaracja zakłada, że klucz prywatny urządzenia jest chroniony i że tylko urządzenie może pomyślnie odpowiedzieć na to wyzwanie.  Zalecamy użycie bezpiecznych układów, takich jak sprzętowe moduły zabezpieczonych (HSM) w urządzeniach w celu ochrony kluczy prywatnych.

Pomyślne połączenie urządzenia z usługą IoT Hub kończy proces uwierzytelniania i wskazuje również na prawidłową konfigurację.

Dowiedz się tutaj, jak [wykonać ten krok połączenia urządzenia](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wartości uwierzytelniania urzędu certyfikacji X.509](iot-hub-x509ca-concept.md) w IoT.

Wprowadzenie do [usługi inicjowania obsługi urządzeń](https://docs.microsoft.com/azure/iot-dps/)usługi IoT Hub .
