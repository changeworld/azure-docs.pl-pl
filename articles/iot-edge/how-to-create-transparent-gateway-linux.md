---
title: Utwórz przezroczyste bramę z krawędzi IoT Azure — Linux | Dokumentacja firmy Microsoft
description: Umożliwia utworzenie przezroczysty bramy, który może przetwarzać informacje dla wielu urządzeń krawędzi IoT Azure
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5a78d6fb8ee52f0daba80a77cc8a5e75c2e5248d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036519"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Utwórz urządzenie Linux IoT krawędzi, który działa jako brama przezroczyste

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania urządzenia IoT jako przezroczysty bramy. W pozostałej części niniejszego artykułu termin *brama brzegowa IoT* odwołuje się do urządzenia IoT używany jako przezroczysty bramy. Aby uzyskać szczegółowe informacje, zobacz [jak IoT urządzenia może służyć jako brama][lnk-edge-as-gateway], co daje omówienie pojęć.

>[!NOTE]
>Obecnie:
> * Jeśli brama jest odłączona od centrum IoT, podrzędne urządzeń nie można uwierzytelnić z bramą.
> * Urządzenia brzegowe IoT nie może połączyć się bram granicznych IoT.
> * Podrzędne urządzeń nie można użyć przekazywania pliku.

Etap o tworzeniu przezroczysty bramy jest bezpiecznego połączenia bramy na urządzeniach podrzędne. Krawędź IoT Azure umożliwia użycie infrastruktury kluczy publicznych do skonfigurowania bezpiecznego połączenia TLS między tymi urządzeniami. W takim przypadku firma Microsoft jest stosowanie podrzędne urządzenia do nawiązania połączenia urządzenia IoT działający jako brama przezroczysty.  Aby zachować bezpieczeństwo uzasadnione, podrzędne urządzenia powinien potwierdzenia tożsamości urządzenie brzegowe ponieważ mają tylko urządzenia nawiązywanie połączeń z bram i nie mogą okazać się złośliwe bramy.

Można utworzyć dowolnej infrastruktury certyfikatów, umożliwiającą zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, tę samą konfigurację certyfikatu, który ma zostać użyty do włączenia [zabezpieczeń urzędu certyfikacji X.509] [ lnk-iothub-x509] w Centrum IoT, które obejmuje certyfikat X.509 urzędu certyfikacji skojarzonego z określonym Centrum IoT (IoT hub właściciel urzędu certyfikacji ) oraz serii certyfikatów, podpisanych tego urzędu certyfikacji i urzędu certyfikacji dla urządzenia brzegowego.

![Instalator bramy][1]

Brama przedstawia informacje o jego krawędzi urządzenia urzędu certyfikacji, aby urządzenia podrzędne podczas inicjowania połączenia. Podrzędne urządzenie sprawdza upewnij się, że certyfikat urzędu certyfikacji urządzenia krawędzi jest podpisany przez właściciela certyfikatu urzędu certyfikacji. Dzięki temu podrzędne urządzeniu, aby upewnić się, że brama pochodzi z zaufanego źródła.

W poniższych krokach objaśniono proces tworzenia certyfikatów oraz instalowania ich w odpowiednich miejscach.

## <a name="prerequisites"></a>Wymagania wstępne
1.  Zainstaluj środowisko uruchomieniowe Azure IoT Edge na urządzeniu systemu Linux, który ma być używany jako brama przezroczysty.
   * [Linux x64][lnk-install-linux-x64]
   * [ARM32 systemu Linux][lnk-install-linux-arm]

2.  Uzyskaj skryptów do generowania wymaganych certyfikatów nieprodukcyjnych przy użyciu następującego polecenia. Skrypty te ułatwiają tworzenie certyfikaty niezbędne do skonfigurowania bramy przezroczysty. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Te skrypty przy użyciu biblioteki OpenSSL Generowanie wymaganych certyfikatów, a biblioteki OpenSSL wymaga konfiguracji.
   
   1. Przejdź do katalogu, w którym chcesz pracować. W tym miejscu na będzie nazywamy to $WRKDIR.  Wszystkie pliki zostaną utworzone w tym katalogu.

      CD $WRKDIR
   
   1. Skopiuj pliki konfiguracji i skrypt do katalogu roboczego.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Tworzenie certyfikatu
1.  Utwórz certyfikat urzędu certyfikacji właściciela i jednego certyfikatu pośredniego. Są one wszystkich umieszczane w `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   Dane wyjściowe wykonania skryptu są następujące certyfikaty i klucze:
   * Certyfikaty
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Klucze
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Utwórz certyfikat urzędu certyfikacji krawędzi urządzenia i klucz prywatny z poniższego polecenia.

   >[!NOTE]
   > **NIE** Użyj nazwy, która jest taka sama jak nazwa hosta DNS bramy. W ten sposób spowoduje certyfikatu klienta dla tych certyfikatów, aby zakończyć się niepowodzeniem.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   Dane wyjściowe wykonania skryptu są następujące certyfikaty i klucz:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Tworzenie łańcucha certyfikatu
Tworzenie łańcucha certyfikatów z właściciela certyfikatu urzędu certyfikacji, certyfikat pośredniego i certyfikat urzędu certyfikacji urządzenia krawędzi z poniższego polecenia. Umieszczenie go w pliku łańcucha pozwala łatwo zainstalować na urządzeniu krawędzi, działając jako brama przezroczysty.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Instalacja bramy
1.  Skopiuj następujące pliki z $WRKDIR dowolnego miejsca na urządzeniu krawędzi, będzie nazywamy który $CERTDIR. Jeśli certyfikaty są generowane na urządzeniu krawędzi pominąć ten krok.

   * Certyfikat urzędu certyfikacji urządzenia —  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Klucz prywatny urzędu certyfikacji urządzenia- `$WRKDIR/private/new-edge-device.key.pem`
   * Urząd certyfikacji - właściciela `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Ustaw `certificate` właściwości w pliku yaml programu config demon zabezpieczeń do ścieżki rozmieszczenia pliki certyfikatu i klucza.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Wdrażanie EdgeHub do bramy
Jedną z najważniejszych funkcji usługi Azure IoT Edge jest możliwość wdrażania modułów na urządzeniach usługi IoT Edge z poziomu chmury. Ta sekcja zawiera, należy utworzyć pusty pozornie wdrożenie; Jednak Centrum krawędzi jest dodawane do wszystkich wdrożeń, nawet jeśli nie mają żadnych modułów obecny automatcially. Koncentrator krawędzi jest tylko moduł, który należy na urządzenie brzegowe ona działać jako przezroczysty bramy, tworzenie pustego wdrożenia jest wystarczająca. 
1. W witrynie Azure Portal przejdź do centrum IoT Hub.
2. Przejdź do **krawędzi IoT** i wybierz urządzenia IoT krawędzi, który ma być używany jako brama.
3. Wybierz pozycję **Ustaw moduły**.
4. Wybierz opcję **Dalej**.
5. W **określić trasy** kroku powinny mieć domyślną trasę, która wysyła komunikaty ze wszystkich modułów do Centrum IoT. Jeśli nie, Dodaj następujący kod, a następnie wybierz **dalej**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. W kroku szablonu przeglądu, wybierz **przesyłania**.

## <a name="installation-on-the-downstream-device"></a>Instalacja na urządzeniu podrzędne
Podrzędne urządzenie może być dowolną aplikację przy użyciu [urządzenia Azure IoT SDK][lnk-devicesdk], takie jak opisano to proste w [Podłącz urządzenie do Centrum IoT przy użyciu platformy .NET] [ lnk-iothub-getstarted]. Aplikacji podrzędne urządzenie ma ufać **właściciela urzędu certyfikacji** certyfikatu w celu weryfikowania połączeń TLS do urządzenia bramy. Ten krok, zwykle można przeprowadzić na dwa sposoby: na poziomie systemu operacyjnego lub (w przypadku niektórych języków) na poziomie aplikacji.

### <a name="os-level"></a>Poziom systemu operacyjnego
Instalowanie certyfikatu w magazynie certyfikatów systemu operacyjnego umożliwi wszystkie aplikacje, aby użyć właściciela certyfikatu urzędu certyfikacji jako zaufanego certyfikatu.

* Ubuntu — w tym miejscu jest przykładem zainstalować certyfikat urzędu certyfikacji na hoście Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Powinien zostać wyświetlony komunikat informujący o tym, "aktualizowanie certyfikatów w /etc/ssl/certs... 1 dodane, usunięte 0; Gotowe".

* System Windows — [to](https://msdn.microsoft.com/en-us/library/cc750534.aspx) artykule szczegółowo przedstawiają, jak to zrobić na urządzeniu z systemem Windows przy użyciu Kreator importu certyfikatów. 

### <a name="application-level"></a>Poziomie aplikacji
Dla aplikacji .NET można dodać następującego fragmentu kodu zaufania certyfikatu w formacie PEM. Inicjowanie zmiennej `certPath` z `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Podłącz urządzenie podrzędne do bramy
Należy zainicjować sdk urządzenia IoT Hub parametrami połączenia odwołujących się do nazwy hosta z urządzeniem bramy. Jest to realizowane przez dodanie `GatewayHostName` właściwości parametrów połączenia urządzenia. Na przykład poniżej przedstawiono przykładowe parametry połączenia urządzenia dla urządzeń, do którego możemy dołączany `GatewayHostName` właściwości:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >To jest przykład polecenia, które testy, które wszystko zostało poprawnie. Możesz sohuld komunikat "zweryfikować OK".
   >
   >biblioteki openssl s_client-connect - CAfile mygateway.contoso.com:8883 $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Routing wiadomości z urządzeń podrzędne
Środowisko uruchomieniowe krawędzi IoT może kierować wiadomości wysłane z podrzędnego urządzeń, podobnie jak komunikaty wysyłane przez moduły. Dzięki temu można wykonywać analizy w module uruchomione w bramie przed wysłaniem danych do chmury. Poniżej trasy będzie służyć do wysyłania komunikatów z urządzeniem podrzędny o nazwie `sensor` na nazwę modułu `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Zobacz [artykuł kompozycji modułu] [lnk--budowy modułu] więcej szczegółów na przesyłanie wiadomości.

## <a name="next-steps"></a>Kolejne kroki
[Zrozumieć wymagania i narzędzi do tworzenia modułów krawędzi IoT][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
