---
title: Instalowanie certyfikatów na urządzeniu — Azure IoT Edge | Microsoft Docs
description: Utwórz certyfikaty testowe i Dowiedz się, jak zainstalować je na urządzeniu Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149902"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Instalowanie certyfikatów produkcyjnych na urządzeniu IoT Edge

Wszystkie urządzenia IoT Edge używają certyfikatów do tworzenia bezpiecznych połączeń między środowiskiem uruchomieniowym i wszystkimi modułami uruchomionymi na urządzeniu.
IoT Edge urządzenia działające jako bramy używają tych samych certyfikatów do łączenia się z urządzeniami podrzędnymi.

Podczas pierwszej instalacji IoT Edge i aprowizacji urządzenia urządzenie zostanie skonfigurowane z certyfikatami tymczasowymi, aby umożliwić przetestowanie usługi.
Te certyfikaty tymczasowe wygasną przez 90 dni lub można je zresetować przez ponowne uruchomienie komputera.
Gdy wszystko będzie gotowe do przeniesienia urządzeń do scenariusza produkcyjnego lub chcesz utworzyć scenariusz bramy, musisz podać własne certyfikaty.
W tym artykule przedstawiono procedurę instalowania certyfikatów na IoT Edge urządzeniach.

Aby dowiedzieć się więcej o różnych typach certyfikatów i ich rolach w IoT Edge scenariuszu, zobacz [Opis sposobu, w jaki Azure IoT Edge używa certyfikatów](iot-edge-certs.md).

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do certyfikatu publicznego urzędu łańcucha certyfikatów dla rozwiązania IoT. Nie trzeba używać certyfikatu głównego urzędu certyfikacji z certyfikatem lub administratorem urzędu certyfikacji w organizacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie IoT Edge uruchomione w [systemie Windows](how-to-install-iot-edge-windows.md) lub [Linux](how-to-install-iot-edge-linux.md).
* Posiadanie certyfikatu głównego urzędu certyfikacji (CA) z podpisem własnym lub zakupionego od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, VeriSign, DigiCert lub GlobalSign.

Jeśli nie masz jeszcze głównego urzędu certyfikacji, ale chcesz wypróbować IoT Edge funkcje wymagające certyfikatów produkcyjnych (na przykład scenariusze bramy), możesz [utworzyć certyfikaty demonstracyjne do testowania funkcji IoT Edge urządzeń](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Tworzenie certyfikatów produkcyjnych

Aby utworzyć następujące pliki, należy użyć własnego urzędu certyfikacji:

* Główny urząd certyfikacji
* Certyfikat dostępu Warunkowego do urządzeń
* Klucz prywatny urzędu certyfikacji urządzenia

Ten artykuł zawiera informacje o tym, jako że *główny urząd certyfikacji* nie jest najwyższym urzędem certyfikatu dla organizacji. Jest to najwyższy urząd certyfikacji dla scenariusza IoT Edge, do którego moduł IoT Edge Hub, moduły użytkownika i wszystkie urządzenia podrzędne używają do ustanawiania relacji zaufania między sobą.

Aby zapoznać się z przykładem tych certyfikatów, przejrzyj skrypty, które tworzą certyfikaty demonstracyjne w [zarządzaniu testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Instalowanie certyfikatów na urządzeniu

Zainstaluj łańcuch certyfikatów na urządzeniu IoT Edge i skonfiguruj środowisko uruchomieniowe IoT Edge, aby odwoływać się do nowych certyfikatów.

Na przykład jeśli użyto przykładowych skryptów do [tworzenia certyfikatów demonstracyjnych](how-to-create-test-certificates.md), trzy pliki, które należy skopiować na urządzenie IoT Edge, są następujące:

* Certyfikat urzędu certyfikacji urządzenia: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Klucz prywatny urzędu certyfikacji urządzenia: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Główny urząd certyfikacji: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Skopiuj trzy pliki certyfikatów i kluczy na urządzenie IoT Edge.

   Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .  Jeśli certyfikaty zostały wygenerowane na urządzeniu IoT Edge, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

2. Otwórz plik konfiguracji demona zabezpieczeń usługi IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ustaw właściwości **certyfikatu** w pliku config. YAML na identyfikator URI pliku certyfikatu i plików kluczy na urządzeniu IoT Edge. Usuń znak `#` przed właściwościami certyfikatu, aby usunąć komentarz z czterech wierszy. Upewnij się, że w wierszu **Certyfikaty:** nie ma powyższego odstępu, a elementy zagnieżdżone są wcięte o dwie spacje. Na przykład:

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * W systemie Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Na urządzeniach z systemem Linux upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty.

5. Jeśli przed uruchomieniem lub ponownym IoT Edge uruchomieniem usługi IoT Edge na urządzeniu zostały wcześniej użyte inne certyfikaty, usuń je z następujących dwóch katalogów:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` i `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` i `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>Następne kroki

Zainstalowanie certyfikatów na urządzeniu IoT Edge jest czynnością wymaganą przed wdrożeniem rozwiązania w środowisku produkcyjnym. Dowiedz się więcej o [tym, jak przygotować się do wdrożenia rozwiązania IoT Edge w środowisku produkcyjnym](production-checklist.md).
