---
title: Zarządzanie certyfikatami urządzeń — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Utwórz certyfikaty testowe, zainstaluj je i zarządzaj nimi na urządzeniu usługi Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539210"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Zarządzanie certyfikatami na urządzeniu IoT Edge

Wszystkie urządzenia usługi IoT Edge używają certyfikatów do tworzenia bezpiecznych połączeń między czasem wykonywania a dowolnymi modułami uruchomionymi na urządzeniu. Urządzenia usługi IoT Edge działające jako bramy używają tych samych certyfikatów do łączenia się z ich urządzeniami podrzędnymi.

## <a name="install-production-certificates"></a>Instalowanie certyfikatów produkcyjnych

Podczas pierwszej instalacji usługi IoT Edge i aprowizowania urządzenia urządzenie jest skonfigurowane z certyfikatami tymczasowymi, dzięki czemu można przetestować usługę.
Te tymczasowe certyfikaty wygasają w ciągu 90 dni lub można je zresetować po ponownym uruchomieniu komputera.
Gdy będziesz gotowy, aby przenieść swoje urządzenia do scenariusza produkcyjnego lub chcesz utworzyć scenariusz bramy, należy podać własne certyfikaty.
W tym artykule przedstawiono kroki instalowania certyfikatów na urządzeniach usługi IoT Edge.

Aby dowiedzieć się więcej o różnych typach certyfikatów i ich rolach w scenariuszu usługi IoT Edge, zobacz [Opis sposobu używania certyfikatów przez usługę Azure IoT Edge.](iot-edge-certs.md)

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do najwyższego certyfikatu urzędu publicznego łańcucha certyfikatów dla rozwiązania IoT. Nie trzeba używać katalogu głównego certyfikatu syndykatu urzędu certyfikacji ani katalogu głównego urzędu certyfikacji organizacji. W wielu przypadkach jest to w rzeczywistości pośredni certyfikat publiczny urzędu certyfikacji.

### <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie IoT Edge z systemem [Windows](how-to-install-iot-edge-windows.md) lub [Linux](how-to-install-iot-edge-linux.md).
* Posiada certyfikat głównego urzędu certyfikacji (CA), podpisany samodzielnie lub zakupiony od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, Verisign, DigiCert lub GlobalSign.

Jeśli nie masz jeszcze głównego urzędu certyfikacji, ale chcesz wypróbować funkcje usługi IoT Edge, które wymagają certyfikatów produkcyjnych (takich jak scenariusze bramy), możesz [utworzyć certyfikaty demonstracyjne w celu przetestowania funkcji urządzenia Usługi IoT Edge.](how-to-create-test-certificates.md)

### <a name="create-production-certificates"></a>Tworzenie certyfikatów produkcyjnych

Do utworzenia następujących plików należy użyć własnego urzędu certyfikacji:

* Główny urząd certyfikacji
* Certyfikat urzędu certyfikacji urządzenia
* Klucz prywatny urzędu certyfikacji urządzenia

W tym artykule to, co nazywamy głównym urzędem *certyfikacji,* nie jest najwyższym urzędem certyfikacji dla organizacji. Jest to najwyższy urząd certyfikacji dla scenariusza usługi IoT Edge, którego moduł centrum usługi IoT Edge, moduły użytkownika i wszystkie urządzenia podrzędne używają do ustanawiania zaufania między sobą.

Aby wyświetlić przykład tych certyfikatów, przejrzyj skrypty, które tworzą certyfikaty demonstracyjne w [temacie Zarządzanie testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalowanie certyfikatów na urządzeniu

Zainstaluj łańcuch certyfikatów na urządzeniu Usługi IoT Edge i skonfiguruj środowisko uruchomieniowe usługi IoT Edge w celu odwoływania się do nowych certyfikatów.

Jeśli na przykład do [tworzenia certyfikatów demonstracyjnych](how-to-create-test-certificates.md)użyto przykładowych skryptów, skopiuj następujące pliki na urządzenie IoT-Edge:

* Certyfikat urzędu certyfikacji urządzenia:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Klucz prywatny urzędu certyfikacji urządzenia:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Główny urząd certyfikacji:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Skopiuj trzy pliki certyfikatów i kluczy na urządzenie Usługi IoT Edge.

   Aby przenieść pliki certyfikatów, można użyć usługi takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcji, takiej jak secure copy [protocol.](https://www.ssh.com/ssh/scp/)  Jeśli certyfikaty zostały wygenerowane na samym urządzeniu IoT Edge, można pominąć ten krok i użyć ścieżki do katalogu roboczego.

1. Otwórz plik konfiguracyjny demona zabezpieczeń IoT Edge.

   * Windows:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Ustaw właściwości **certyfikatu** w pliku config.yaml na pełną ścieżkę do certyfikatu i plików kluczy na urządzeniu Usługi IoT Edge. Usuń `#` znak przed właściwościami certyfikatu, aby usunąć komentarz do czterech wierszy. Upewnij się, że **certyfikaty:** wiersz nie ma poprzedniego odstępu i że elementy zagnieżdżone są wcięte przez dwie spacje. Przykład:

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * W systemie Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Na urządzeniach z systemem Linux upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu dla katalogu zawierającego certyfikaty.

1. Jeśli wcześniej na urządzeniu były używane inne certyfikaty dla ioT Edge, usuń pliki z następujących dwóch katalogów przed uruchomieniem lub ponownym uruchomieniem IoT Edge:

   * Okna: `C:\ProgramData\iotedge\hsm\certs` i`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` i`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Dostosowywanie okresu istnienia certyfikatu

IoT Edge automatycznie generuje certyfikaty na urządzeniu w kilku przypadkach, w tym:

* Jeśli podczas instalowania i aprowizowania usługi IoT Edge nie podasz własnych certyfikatów produkcyjnych, menedżer zabezpieczeń usługi IoT Edge automatycznie wygeneruje **certyfikat urzędu certyfikacji urządzenia.** Ten certyfikat z podpisem własnym jest przeznaczony tylko do scenariuszy deweloperskich i testowych, a nie do produkcji. Certyfikat wygasa po 90 dniach.
* Menedżer zabezpieczeń usług IoT Edge generuje również **certyfikat urzędu certyfikacji obciążenia** podpisany certyfikatem urzędu certyfikacji urządzenia

Aby uzyskać więcej informacji na temat funkcji różnych certyfikatów na urządzeniu usługi IoT Edge, zobacz [Opis sposobu używania certyfikatów usługi Azure IoT Edge.](iot-edge-certs.md)

W przypadku tych dwóch automatycznie generowanych certyfikatów można skonfigurować liczbę dni okresu istnienia certyfikatów w **auto_generated_ca_lifetime_days.**

>[!NOTE]
>Istnieje trzeci automatycznie wygenerowany certyfikat, który tworzy menedżer zabezpieczeń usługi IoT Edge, **certyfikat serwera centrum usługi IoT Edge**. Ten certyfikat ma zawsze 90 dni, ale jest automatycznie odnawiany przed wygaśnięciem. Wartość **auto_generated_ca_lifetime_days** nie ma wpływu na ten certyfikat.

Aby skonfigurować wygaśnięcie certyfikatu na wartość inną niż domyślne 90 dni, dodaj wartość w dniach do sekcji **certyfikaty** pliku config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Jeśli podano certyfikaty urzędu certyfikacji własnego urządzenia, ta wartość nadal ma zastosowanie do certyfikatu urzędu certyfikacji obciążenia, pod warunkiem że ustawiona wartość okresu istnienia jest krótsza niż okres istnienia certyfikatu urzędu certyfikacji urządzenia.

Po określeniu flagi w pliku config.yaml należy wykonać następujące czynności:

1. Usuń zawartość folderu. `hsm`

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Uruchom ponownie usługę Usługi IoT Edge.

   W systemie Windows:

   ```powershell
   Restart-Service iotedge
   ```

   W systemie Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Potwierdź ustawienie okresu istnienia.

   W systemie Windows:

   ```powershell
   iotedge check --verbose
   ```

   W systemie Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Sprawdź dane **wyjściowe gotowości produkcyjnej: sprawdź certyfikaty,** który wyświetla liczbę dni do wygaśnięcia automatycznie wygenerowanych certyfikatów urzędu certyfikacji urządzenia.

## <a name="next-steps"></a>Następne kroki

Instalowanie certyfikatów na urządzeniu Z brzegiem IoT jest niezbędnym krokiem przed wdrożeniem rozwiązania w procesach produkcyjnych. Dowiedz się więcej o tym, jak [przygotować się do wdrożenia rozwiązania IoT Edge w produkcji.](production-checklist.md)
