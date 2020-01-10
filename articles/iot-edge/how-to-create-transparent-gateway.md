---
title: Tworzenie przezroczystej bramy urządzenia — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie usługi Azure IoT Edge urządzenia rolę przezroczystej bramy, która może przetwarzać informacje z urządzeń podrzędne
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1deeb17e4d55c81d6161855ee2e6dc4766bdcdca
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772469"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy

Ten artykuł zawiera szczegółowe instrukcje dotyczące konfigurowania urządzenia IoT Edge jako przezroczystej bramy dla innych urządzeń w celu komunikowania się z IoT Hub. W tym artykule jest wykorzystywana *IoT Edge Brama* do odwoływania się do urządzenia IoT Edge skonfigurowanego jako nieprzezroczysta brama. Aby uzyskać więcej informacji, zobacz [jak urządzenie IoT Edge może być używane jako brama](./iot-edge-as-gateway.md).

>[!NOTE]
>Obecnie:
> * Urządzenia z włączoną obsługą usługi Edge nie można nawiązać połączenia bramy usługi IoT Edge. 
> * Podrzędne urządzenia nie mogą używać przekazywania plików.

Należy wykonać trzy ogólne kroki, aby skonfigurować pomyślne, przezroczyste połączenie bramy. W tym artykule omówiono pierwszy krok:

1. **Urządzenie bramy musi być w stanie bezpiecznie łączyć się z urządzeniami podrzędnymi, odbierać komunikaty z urządzeń podrzędnych i kierować komunikaty do odpowiednich miejsc docelowych.**
2. Urządzenie podrzędne musi mieć tożsamość urządzenia, aby można było uwierzytelnić się przy użyciu IoT Hub i wiedzieć o komunikacji za pomocą swojego urządzenia bramy. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Urządzenie podrzędne musi bezpiecznie połączyć się z urządzeniem bramy. Aby uzyskać więcej informacji, zobacz [połączyć podrzędny urządzenie bramy usługi Azure IoT Edge](how-to-connect-downstream-device.md).


Aby urządzenie działało jako brama, musi być w stanie bezpiecznie połączyć się z jego urządzeniami podrzędnymi. Usługa Azure IoT Edge umożliwia użycie infrastruktury kluczy publicznych (PKI) do skonfigurowania bezpiecznych połączeń między urządzeniami. W tym przypadku możemy zezwolenie podrzędnym urządzenia połączyć się z urządzenia usługi IoT Edge, działając jako przezroczystej bramy. Aby zachować uzasadnione zabezpieczenia, urządzenie podrzędne powinno potwierdzić tożsamość urządzenia bramy. To sprawdzenie tożsamości uniemożliwia urządzeniom łączenie się z potencjalnie złośliwymi bramami.

W przypadku niejawnego scenariusza bramy może być dowolna aplikacja lub platforma, która ma tożsamość utworzoną za pomocą usługi [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) w chmurze. W wielu przypadkach te aplikacje korzystają [zestaw SDK urządzeń Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Praktycznego podrzędne urządzenia można nawet aplikację działającą na samym urządzeniu bramy usługi IoT Edge. Jednak urządzenie IoT Edge nie może być niższe niż Brama IoT Edge. 

Można utworzyć żadnej infrastruktury certyfikatów, umożliwiająca zaufania wymagane dla topologii urządzenia bramy. W tym artykule przyjęto założenie, że ta sama konfiguracja certyfikatu zostanie użyta do włączenia [zabezpieczeń urzędu certyfikacji x. 509](../iot-hub/iot-hub-x509ca-overview.md) w IoT Hub, co obejmuje certyfikat certyfikatu x. 509 skojarzony z określonym Centrum IoT Hub (główny urząd certyfikacji usługi IoT Hub), szereg certyfikatów podpisanych za pomocą tego urzędu certyfikacji oraz Urząd certyfikacji dla IoT Edge urządzenia.

![Konfiguracja certyfikatu bramy](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do certyfikatu publicznego urzędu certyfikacji PKI, a nie musi być certyfikatem głównym urzędu certyfikacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji. 

Podczas inicjowania połączenia Brama przedstawia certyfikat urzędu certyfikacji urządzenia IoT Edge do urządzenia podrzędnego. Urządzenie podrzędne sprawdza, czy certyfikat urzędu certyfikacji urządzenia IoT Edge jest podpisany przez certyfikat głównego urzędu certyfikacji. Ten proces umożliwia urządzeniu podrzędnemu potwierdzenie, że brama pochodzi z zaufanego źródła.

Poniższe kroki przeprowadzą Cię przez proces tworzenia certyfikatów i instalowania ich w odpowiednich miejscach na bramie. Można użyć dowolnej maszyny do generowania certyfikatów, a następnie skopiuj je do urządzenia usługi IoT Edge. 

## <a name="prerequisites"></a>Wymagania wstępne

Urządzenie Azure IoT Edge skonfigurowane przy użyciu [certyfikatów produkcyjnych](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Wdróż edgeHub na bramie

Podczas pierwszej instalacji IoT Edge na urządzeniu zostanie automatycznie uruchomiony tylko jeden moduł systemowy: Agent IoT Edge. Po utworzeniu pierwszego wdrożenia więcej urządzenia jest również uruchamiany drugi moduł systemu IoT Edge centrum. 

Centrum IoT Edge jest odpowiedzialne za odbieranie komunikatów przychodzących z urządzeń podrzędnych i kierowanie ich do kolejnego miejsca docelowego. Jeśli moduł **edgeHub** nie jest uruchomiony na urządzeniu, Utwórz początkowe wdrożenie dla urządzenia. Wdrożenie będzie wyglądało puste, ponieważ nie dodasz żadnych modułów, ale upewni się, że oba moduły systemowe są uruchomione. 

Możesz sprawdzić, które moduły są uruchomione na urządzeniu, sprawdzając jego szczegóły dotyczące urządzeń w Azure Portal, wyświetlając stan urządzenia w Visual Studio lub Visual Studio Code lub uruchamiając polecenie `iotedge list` na samym urządzeniu. 

Jeśli moduł **edgeAgent** jest uruchomiony bez modułu **edgeHub** , wykonaj następujące czynności:

1. W witrynie Azure Portal przejdź do centrum IoT Hub.

2. Przejdź do **usługi IoT Edge** i wybierz urządzenia usługi IoT Edge, która ma być używany jako brama.

3. Wybierz pozycję **Ustaw moduły**.

4. Wybierz opcję **Dalej**.

5. W **określić trasy** strony powinny mieć domyślną trasę, która przesyła wszystkie komunikaty z wszystkich modułów do usługi IoT Hub. Jeśli tak nie jest, dodaj następujący kod, a następnie wybierz przycisk **Dalej**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. W **szablon przeglądu** wybierz opcję **przesyłania**.

## <a name="open-ports-on-gateway-device"></a>Otwórz porty na urządzeniu bramy

Standardowe urządzenia IoT Edge nie potrzebują żadnej łączności przychodzącej z funkcją, ponieważ cała komunikacja z IoT Hub odbywa się za pośrednictwem połączeń wychodzących. Urządzenia bramy różnią się, ponieważ muszą odbierać komunikaty z urządzeń podrzędnych. Jeśli Zapora jest między urządzeniami podrzędnymi a urządzeniem bramy, komunikacja musi być również możliwa za pomocą zapory.

Aby scenariusz bramy działał prawidłowo, należy otworzyć co najmniej jeden z obsługiwanych protokołów IoT Edge Hub dla ruchu przychodzącego z urządzeń podrzędnych. Obsługiwane protokoły to MQTT, AMQP, HTTPS, MQTT za pośrednictwem obiektów WebSockets oraz AMQP za pośrednictwem obiektów WebSockets. 

| Port | Protocol (Protokół) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Routing komunikatów z urządzeń podrzędne
Komunikaty wysyłane z urządzeń podrzędne, podobnie jak komunikaty wysyłane przez moduły można kierować do środowiska uruchomieniowego usługi IoT Edge. Ta funkcja umożliwia wykonywanie analiz w module uruchomionym w bramie przed wysłaniem danych do chmury. 

Obecnie sposobem, w który trasy wiadomości wysyłanych przez urządzenia podrzędnego jest różnicując je na wiadomości wysłane przez moduły. Komunikaty wysyłane przez wszystkie moduły zawierają właściwość systemu o nazwie **connectionModuleId** , ale nie obsługują komunikaty wysyłane przez urządzenia podrzędnego. Aby wykluczyć wszystkie komunikaty, które zawierają tę właściwość systemu, można użyć klauzuli WHERE trasy. 

Poniższa trasa to przykład, który wyśle komunikaty z dowolnego urządzenia podrzędnego do modułu o nazwie `ai_insights`, a następnie z `ai_insights` do IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Aby uzyskać więcej informacji na temat routingu wiadomości zobacz [wdrażać moduły i trasy ustanowić](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Włącz rozszerzoną operację offline

Począwszy od [wersji v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego IoT Edge, urządzenie bramy i urządzenia podrzędne łączące się z nim można skonfigurować do przedłużonej operacji w trybie offline. 

Dzięki tej możliwości lokalne moduły lub urządzenia podrzędne mogą ponownie uwierzytelniać się przy użyciu urządzenia z systemem IoT Edge i komunikować się ze sobą za pomocą komunikatów i metod nawet po rozłączeniu z Centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [opis rozszerzony możliwości w trybie offline dla usługi IoT Edge, urządzeń, moduły i urządzeń podrzędnych](offline-capabilities.md).

Aby włączyć rozszerzone możliwości trybu offline, należy ustanowić relację nadrzędny-podrzędny między urządzeniem bramy IoT Edge a urządzeniami podrzędnymi, które będą się z nim połączyć. Te kroki zostały omówione bardziej szczegółowo w temacie [uwierzytelnianie urządzenia podrzędnego w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenia usługi IoT Edge działa jako przezroczystej bramy, należy skonfigurować podrzędne urządzenia, aby ufać bramy i wysyłanie komunikatów do niego. Kontynuuj, aby [uwierzytelnić urządzenie podrzędne w usłudze Azure IoT Hub](how-to-authenticate-downstream-device.md) w celu wykonania następnych kroków w celu skonfigurowania niejawnego scenariusza bramy. 
