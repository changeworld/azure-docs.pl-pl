---
title: Omówienie zabezpieczeń usługi Azure IoT Hub X.509 certyfikacji | Dokumentacja firmy Microsoft
description: Przegląd — jak do uwierzytelniania urządzeń do Centrum IoT przy użyciu X.509 urzędów certyfikacji.
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 11f340ebe1719c680cce5f6ad34150d3717c57d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Uwierzytelnianie urządzenia za pomocą certyfikatów X.509 urzędu certyfikacji

W tym artykule opisano sposób używania certyfikatów X.509 certyfikatu urzędu certyfikacji do uwierzytelniania urządzeń nawiązujących połączenie Centrum IoT.  W tym artykule dowiesz się:

* Jak uzyskać certyfikat X.509 urzędu certyfikacji
* Jak zarejestrować certyfikat X.509 urzędu certyfikacji z Centrum IoT
* Jak zarejestrować urządzenia za pomocą certyfikatów X.509 urzędu certyfikacji
* Sposób uwierzytelniania urządzenia podpisany z urzędem certyfikacji X.509

## <a name="overview"></a>Przegląd

Funkcja X.509 urzędu certyfikacji umożliwia uwierzytelnianie urządzenia z Centrum IoT przy użyciu certyfikatu urzędu certyfikacji. Jego jest znacznie ułatwione procesu rejestracji urządzenie początkowe i logistyki łańcucha dostaw podczas produkcji urządzenia. [Dowiedz się więcej w tym artykule scenariuszu o wartości elementu za pomocą certyfikatów X.509 urzędu certyfikacji](iot-hub-x509ca-concept.md) uwierzytelniania urządzenia.  Firma Microsoft zachęca do przeczytaj ten artykuł scenariusz przed kontynuowaniem, jak wyjaśniono, dlaczego czynności, które wykonują istnieje.

## <a name="prerequisite"></a>Wymagania wstępne

Za pomocą funkcji X.509 urzędu certyfikacji wymaga konta Centrum IoT.  [Dowiedz się, jak utworzyć wystąpienia Centrum IoT](iot-hub-csharp-csharp-getstarted.md) Jeśli nie został wcześniej.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak uzyskać certyfikat X.509 urzędu certyfikacji

Certyfikat X.509 urzędu certyfikacji znajduje się na górze łańcucha certyfikatów dla każdego urządzenia.  Można zakupić lub utworzyć w zależności od tego, w jaki sposób chcesz użyć.

W środowisku produkcyjnym zaleca się zakup certyfikatu X.509 urzędu certyfikacji od głównego publicznego urzędu certyfikacji. Zakup certyfikatu urzędu certyfikacji przynosi korzyści związane z głównego urzędu certyfikacji działający jako zaufany innych firm do ciesząca dla legalności urządzenia. Rozważ użycie tej opcji, jeśli jest planowane urządzenia jako część sieci IoT których one może korzystać z innych produktów lub usług.

Można również utworzyć podpisem certyfikacji X.509 do eksperymenty lub do wykorzystania w sieciach IoT zamknięte.

Niezależnie od tego, jak możesz uzyskać certyfikat X.509 urzędu certyfikacji, upewnij się zachować jego odpowiedniego prywatnego klucza klucz tajny i chronione przez cały czas.  Jest to niezbędne do tworzenia zaufania do urzędu certyfikacji X.509 uwierzytelniania zaufania. 

Dowiedz się, jak [tworzenia certyfikatu urzędu certyfikacji z podpisem własnym](iot-hub-security-x509-create-certificates.md#createcerts), którego można użyć dla eksperymenty w tym opis funkcji.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Zaloguj się urządzeń w łańcuchu certyfikatów zaufania

Właściciela certyfikatu X.509 urzędu certyfikacji można zarejestrować kryptograficznie pośredniego urzędu certyfikacji, który z kolei można zarejestrować inną pośredniego urzędu certyfikacji i itd., aż do ostatniego pośredniego urzędu certyfikacji kończy ten proces po zarejestrowaniu urządzenia. Wynik jest kaskadowa łańcuch certyfikatów znany jako łańcuch zaufania certyfikatów. W świecie rzeczywistym odgrywa jako delegowania zaufania do podpisywania urządzeń. To Delegowanie jest ważne, ponieważ tworzą kryptograficznie zmiennej łańcucha nadzoru i pozwala uniknąć udostępniania kluczy podpisywania.

![img-Generic-CERT-Chain-of-Trust](./media/generic-cert-chain-of-trust.png)

W tej części wyjaśniono sposób [utworzyć łańcuch certyfikatów](iot-hub-security-x509-create-certificates.md#createcertchain) co wykonywane podczas rejestracji urządzeń.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zarejestrować certyfikat X.509 urzędu certyfikacji z Centrum IoT

Zarejestruj certyfikat X.509 urzędu certyfikacji z Centrum IoT, w którym będzie można użyć do uwierzytelniania urządzenia podczas rejestracji i połączenia.  Rejestrowanie certyfikatu X.509 urzędu certyfikacji jest procesem dwuetapowym, który obejmuje przekazywania pliku certyfikatu i dowodu posiadania.

Proces przekazywania pociąga za sobą przekazywania pliku, który zawiera certyfikat.  Ten plik nie powinien zawierać dowolnych kluczy prywatnych.

Potwierdzenie posiadania krok polega na kryptograficznych żądania i odpowiedzi procesu między użytkownikiem a Centrum IoT.  Biorąc pod uwagę, że zawartość certyfikatu cyfrowego jest publiczny i w związku z tym narażony na podsłuchiwaniu, Centrum IoT chcesz ustalić naprawdę własny certyfikat urzędu certyfikacji.  Powinna to zrobić przez Generowanie losowe żądania, które należy podpisać odpowiedni klucz prywatny certyfikatu urzędu certyfikacji.  Jeśli pozostawiono prywatnego klucza tajnego i chronione jako wcześniej poinformowany, następnie tylko będzie posiadać wiedzę do ukończenia tego kroku. Tajemnicy kluczy prywatnych jest źródłem zaufania w ramach tej metody.  Po utworzeniu żądania, ukończenia tego kroku, przekazując plik zawierający wyniki.

W tej części wyjaśniono sposób [zarejestrować certyfikat urzędu certyfikacji](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Jak utworzyć urządzenie w Centrum IoT

Aby wykluczyć personifikacji urządzenia, Centrum IoT wymaga powiadomienie go, które urządzenia mogą się spodziewać.  Można to zrobić, tworząc wpis urządzenia w rejestrze urządzeń Centrum IoT.  Ten proces jest zautomatyzowany, korzystając z Centrum IoT [usługi inicjowania obsługi urządzeń](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (punktu dystrybucji). 

W tej części wyjaśniono sposób [ręcznie utworzyć urządzenie w Centrum IoT](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Uwierzytelnianie urządzeń podpisane za pomocą certyfikatów X.509 urzędu certyfikacji

Zarejestrowany certyfikat X.509 urzędu certyfikacji i urządzenia z rejestracji w łańcuch zaufania certyfikatów co pozostaje jest uwierzytelnianie urządzenia w przypadku urządzenia przenośnego, nawet po raz pierwszy.  Po zalogowaniu urzędu certyfikacji X.509 urządzenia przenośnego, przekazuje on jego weryfikacji łańcucha certyfikatu. Łańcuch obejmuje wszystkie pośrednie certyfikaty urzędu certyfikacji i urządzenia.  Dzięki tym informacjom Centrum IoT uwierzytelnianie urządzenia w dwóch etapach.  Centrum IoT kryptograficznie weryfikuje łańcucha certyfikatu wewnętrznego sprawdzania spójności, a następnie generuje żądanie dowodu z posiadania na urządzeniu.  Centrum IoT deklaruje urządzenia autentyczne na odpowiedź oznaczająca Powodzenie dowód z posiadania z urządzenia.  Ta deklaracja przyjęto założenie, że urządzenia klucz prywatny jest chroniony i że tylko urządzenia pomyślnie mogą odpowiadać na ten problem.  Firma Microsoft zaleca użycie bezpiecznego mikroukłady jak sprzętu bezpiecznych modułów (HSM) na urządzeniach w celu ochrony kluczy prywatnych.

Połączenie urządzenia powiodło się z Centrum IoT realizuje proces uwierzytelniania i jest także świadczy o prawidłową konfigurację.

W tej części wyjaśniono sposób [ukończenia tego kroku połączenia urządzenia](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wartość uwierzytelniania urzędu certyfikacji X.509](iot-hub-x509ca-concept.md) w IoT.

Rozpoczynanie pracy z Centrum IoT [usługi inicjowania obsługi urządzeń](https://docs.microsoft.com/azure/iot-dps/).
