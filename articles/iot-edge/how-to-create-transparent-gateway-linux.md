---
title: Tworzenie przezroczystej bramy za pomocą usługi Azure IoT Edge — Linux | Dokumentacja firmy Microsoft
description: Umożliwia tworzenie przezroczystej bramy, która pozwala na przetwarzanie informacji dla wielu urządzeń w usłudze Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f8ac885444c0ba52802024be9a78dfc0737e2673
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247687"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Tworzenie urządzenia usługi IoT Edge w systemie Linux, która pełni rolę przezroczystej bramy

Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z urządzenia usługi IoT Edge rolę przezroczystej bramy. W pozostałej części tego artykułu termin *brama usługi IoT Edge* odwołuje się do urządzenia usługi IoT Edge używany jako przezroczystej bramy. Aby uzyskać więcej informacji, zobacz [jak IoT Edge urządzenia mogą być używane jako brama][lnk-edge-as-gateway], który zawiera omówienie pojęć.

>[!NOTE]
>Obecnie:
> * Jeśli brama zostanie odłączony od usługi IoT Hub, podrzędnym urządzenia nie mogą uwierzytelniać za pomocą bramy.
> * Urządzenia z włączoną obsługą usługi Edge nie można nawiązać połączenia bramy usługi IoT Edge. 
> * Podrzędne urządzenia nie mogą używać przekazywania plików.

Trudnym o tworzeniu przezroczystej bramy jest możliwość bezpiecznego łączenia z bramy do podrzędnego urządzeń. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych do skonfigurowania nawiązywać bezpieczne połączenia TLS między tymi urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy.  Aby zachować bezpieczeństwo uzasadnione, podrzędne urządzenia należy się upewnić tożsamość urządzenia usługi Edge, ponieważ mają tylko urządzenia nawiązywania połączenia z bramami i potencjalnie złośliwych bramy.

Można utworzyć żadnej infrastruktury certyfikatów, umożliwiająca zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, że tę samą konfigurację certyfikatu, który zostanie wykorzystany do włączenia [zabezpieczeń urzędu certyfikacji X.509] [ lnk-iothub-x509] w usłudze IoT Hub, który obejmuje certyfikat X.509 urzędu certyfikacji, powiązanych z określonej usługi IoT hub (IoT hub właściciel urzędu certyfikacji ) oraz szeregu certyfikaty, podpisane za pomocą tego urzędu certyfikacji i urzędu certyfikacji dla urządzeń brzegowych.

![Instalator bramy][1]

Brama przedstawia swój certyfikat urzędu certyfikacji urządzenia Edge na urządzeniu podrzędnego podczas inicjowania połączenia. Podrzędne urządzenie sprawdza upewnij się, że certyfikat urzędu certyfikacji urządzenia Edge jest podpisany przez właściciela certyfikatu urzędu certyfikacji. Ten proces umożliwia podrzędnym urządzenia upewnić się, że brama pochodzi z zaufanego źródła.

W poniższych krokach objaśniono proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach.

## <a name="prerequisites"></a>Wymagania wstępne
1.  Zainstaluj środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu systemu Linux, którego chcesz użyć jako przezroczystej bramy.
   * [Linux x64][lnk-install-linux-x64]
   * [ARM32 systemu Linux][lnk-install-linux-arm]

2.  Uzyskaj skrypty w celu wygenerowania wymaganych certyfikatów nieprodukcyjnych przy użyciu następującego polecenia. Te skrypty pomocne podczas tworzenia wymagane certyfikaty, aby skonfigurować przezroczystej bramy. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Te skrypty użyj biblioteki OpenSSL, aby wygenerować wymagane certyfikaty i OpenSSL wymaga konfiguracji.
   
   1. Przejdź do katalogu, w którym chcesz pracować. W tym miejscu na będzie nazywamy to $WRKDIR.  Wszystkie pliki zostaną utworzone w tym katalogu.

      ciągłe dostarczanie $WRKDIR
   
   1. Skopiuj pliki konfiguracji i skrypt w katalogu roboczym.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Tworzenie certyfikatu
1.  Utwórz certyfikat urzędu certyfikacji właściciela i jeden certyfikat pośredniego. Te są umieszczane w `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   Dane wyjściowe wykonywania skryptów są następujące certyfikaty i klucze:
   * Certyfikaty
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Klucze
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Utwórz certyfikat urzędu certyfikacji urządzenia usługi Edge i klucza prywatnego za pomocą poniższego polecenia.

   >[!NOTE]
   > **NIE** Użyj nazwy, która jest taka sama jak nazwa hosta DNS bramy. To spowoduje, że certyfikatu klienta dla tych certyfikatów, nie powiedzie się.

   ```cmd
   ./certGen.sh create_edge_device_certificate "<gateway device name>"
   ```

   Dane wyjściowe wykonywania skryptów są następujące certyfikaty i klucza:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Tworzenie łańcucha certyfikatu
Tworzenie łańcucha certyfikatów od właściciela certyfikatu urzędu certyfikacji, pośredniego certyfikatu i certyfikat urzędu certyfikacji urządzeń brzegowych za pomocą poniższego polecenia. Umieszczenie ich w pliku łańcucha pozwala łatwo zainstalować na urządzeniu usługi Edge działający jako przezroczystej bramy.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Instalacja bramy
1.  Skopiuj następujące pliki z $WRKDIR dowolne miejsce na urządzeniu usługi Edge, będziemy odnosić się do tego jako $CERTDIR. Pomiń ten krok, jeśli certyfikaty są generowane na urządzeniu usługi Edge.

   * Certyfikat dostępu Warunkowego do urządzeń —  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia — `$WRKDIR/private/new-edge-device.key.pem`
   * Właściciel CA- `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
   
2. Otwórz plik konfiguracji usługi IoT Edge. Jest to plik chroniony, więc może zajść potrzeba użycia podwyższonych uprawnień w celu uzyskania dostępu do tego pliku.
   
   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

3.  Ustaw `certificate` właściwości w pliku yaml config Iot Edge demona do ścieżki gdzie umieścić pliki certyfikatu i klucza.

   ```yaml
   certificates:
     device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
     device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
     trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie EdgeHub do bramy
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Ta sekcja zawiera podczas tworzenia wdrożenia pozornie pusty; Jednak Centrum usługi Edge jest automatycznie dodawany do wszystkich wdrożeń, nawet jeśli nie mają żadnych modułów obecne. Centrum usługi Edge jest tylko moduł, który należy na urządzeniu usługi Edge ona pełnić rolę przezroczystej bramy, dzięki czemu Tworzenie pustego wdrożenia jest wystarczająca. 
1. W witrynie Azure Portal przejdź do centrum IoT Hub.
2. Przejdź do **usługi IoT Edge** i wybierz urządzenia usługi IoT Edge, która ma być używany jako brama.
3. Wybierz pozycję **Ustaw moduły**.
4. Wybierz opcję **Dalej**.
5. W kroku **Określanie tras** powinna być widoczna domyślna trasa, która wysyła wszystkie komunikaty ze wszystkich modułów do centrum IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. W kroku szablon recenzji wybierz **przesyłania**.

## <a name="installation-on-the-downstream-device"></a>Instalacja na urządzeniu podrzędne
Podrzędne urządzenie może pozostawać w dowolnej aplikacji przy użyciu [zestaw SDK urządzeń Azure IoT][lnk-devicesdk], takie jak proste co opisano w [Podłącz urządzenie do Centrum IoT hub przy użyciu platformy .NET] [ lnk-iothub-getstarted]. Aplikacji urządzenia podrzędnego musi ufać **właściciela urzędu certyfikacji** certyfikatu w celu weryfikowania połączeń TLS do urządzenia bramy. Zazwyczaj ten krok można wykonać na dwa sposoby: na poziomie systemu operacyjnego lub (w przypadku niektórych języków) na poziomie aplikacji.

### <a name="os-level"></a>Poziom systemu operacyjnego
Instalacji tego certyfikatu w magazynie certyfikatów systemu operacyjnego pozwoli wszystkie aplikacje, aby użyć właściciela certyfikatu urzędu certyfikacji jako zaufanego certyfikatu.

* Ubuntu - poniżej przedstawiono przykładowy sposób zainstalować certyfikat urzędu certyfikacji na hoście systemu Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Powinien zostać wyświetlony komunikat z informacją "Aktualizowanie certyfikaty /etc/ssl/certs... Dodano 1, 0 usunięte; Gotowe".

* Windows — poniżej przedstawiono przykładowy sposób zainstalować certyfikat urzędu certyfikacji na hoście Windows.
  * W menu start wpisz "Zarządzanie komputerem certyfikaty". To powinno wyświetlić narzędziem o nazwie `certlm`.
  * Przejdź do certyfikatów komputera lokalnego--> zaufanych certyfikatów głównych--> Certyfikaty--> po prawej stronie kliknij pozycję--> Wszystkie zadania--> Importuj, aby uruchomić Kreatora importu certyfikatów.
  * Postępuj zgodnie z instrukcjami, zgodnie z instrukcją i zaimportuj $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem pliku certyfikatu.
  * Po zakończeniu wyświetlony komunikat "Pomyślnie zaimportowane".

### <a name="application-level"></a>Poziom aplikacji
W przypadku aplikacji .NET można dodać poniższy fragment kodu można traktować jako zaufany certyfikat w formacie PEM. Zainicjować zmienną `certPath` z `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Podłącz urządzenie podrzędnego do bramy
Zestaw sdk urządzenia usługi IoT Hub musi zostać zainicjowany przy użyciu parametrów połączenia, odnoszące się do nazwy hosta urządzenia bramy. Jest to realizowane przez dołączenie `GatewayHostName` właściwość parametrów połączenia urządzenia. Na przykład poniżej przedstawiono przykładowe parametry połączenia dla urządzenia dla urządzenia, do której firma Microsoft dołączany `GatewayHostName` właściwości:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >To przykładowe polecenie, które testy, które wszystko, co zostało skonfiguruj poprawnie. Możesz sohuld powiedzenie komunikat "zweryfikowano OK".
   >
   >openssl s_client-connect - CAfile mygateway.contoso.com:8883 $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Routing komunikatów z urządzeń podrzędne
Komunikaty wysyłane z urządzeń podrzędne, podobnie jak komunikaty wysyłane przez moduły można kierować do środowiska uruchomieniowego usługi IoT Edge. Pozwala na przeprowadzanie analiz w module uruchomiona na bramie przed wysłaniem danych do chmury. Poniżej trasy będzie służyć do wysyłania komunikatów z urządzenia podrzędnego o nazwie `sensor` z nazwą modułu `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Zapoznaj się [artykułu kompozycji moduł] [ lnk-module-composition] więcej informacji na temat routingu komunikatów.

## <a name="next-steps"></a>Kolejne kroki
[Zrozumienie wymagań i narzędzia do tworzenia modułów usługi IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/quickstart-send-telemetry-dotnet.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
