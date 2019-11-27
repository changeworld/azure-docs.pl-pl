---
title: Przygotowywanie urządzenia i wdrożenia w środowisku produkcyjnym — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć rozwiązanie Azure IoT Edge od projektowania w środowisku produkcyjnym, w tym konfigurowania urządzeń przy użyciu odpowiednich certyfikatów i sporządzania planu wdrożenia w przyszłości.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457309"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Przygotowanie do wdrożenia rozwiązania usługi IoT Edge w środowisku produkcyjnym

Gdy wszystko będzie gotowe móc rozwiązania usługi IoT Edge, od projektowania do produkcji, upewnij się, że została ona skonfigurowana do wydajność.

Informacje zawarte w tym artykule nie są równe. Aby ułatwić określanie priorytetów, Każda sekcja rozpoczyna się od listy dzielącej pracę na dwie sekcje: **Ważne** do ukończenia przed przejściem do środowiska produkcyjnego lub **pomocne** dla użytkownika.

## <a name="device-configuration"></a>Konfiguracja urządzenia

Urządzenia usługi IoT Edge mogą być cokolwiek — od urządzeń Raspberry Pi laptopa do maszyny wirtualnej na serwerze. Masz dostęp do urządzenia fizycznie lub za pośrednictwem połączeń wirtualnych lub mogą być izolowane dłuższy czas. W obu przypadkach trzeba upewnić się, że jest odpowiednio skonfigurowany do pracy. 

* **Ważne**
    * Instalowanie certyfikatów w środowisku produkcyjnym
    * Plan zarządzania urządzeniami
    * Użyj Moby jako aparat kontenera

* **Pomaga**
    * Wybierz protokół nadrzędnego

### <a name="install-production-certificates"></a>Instalowanie certyfikatów w środowisku produkcyjnym

Każde urządzenie usługi IoT Edge w środowisku produkcyjnym, musi na nim zainstalowany certyfikat urzędu certyfikacji certyfikatu urządzenia. Następnie zadeklarowano ten certyfikat urzędu certyfikacji do środowiska uruchomieniowego usługi IoT Edge w pliku config.yaml. Programowanie i testowanie, IoT Edge środowisko uruchomieniowe tworzy certyfikatów tymczasowych, jeśli żadne certyfikaty nie został zadeklarowany w pliku config.yaml. Jednak te certyfikaty tymczasowe wygasają po upływie trzech miesięcy, a nie są bezpieczne dla scenariuszy produkcyjnych. 

Aby zrozumieć rolę certyfikatu urzędu certyfikacji, zobacz [jak Azure IoT Edge używa certyfikatów](iot-edge-certs.md).

Aby uzyskać więcej informacji na temat sposobu instalowania certyfikatów na urządzeniu IoT Edge i odwoływania się do nich z pliku config. YAML, zobacz [Konfigurowanie urządzenia IoT Edge do działania jako nieprzezroczyste bramy](how-to-create-transparent-gateway.md). Procedura konfigurowania certyfikatów są takie same, czy urządzenie ma być używany jako brama lub nie. Ten artykuł zawiera skrypty w celu wygenerowania certyfikatów przykładowe tylko do celów testowych. Nie używaj tych certyfikatów próbki w środowisku produkcyjnym. 

### <a name="have-a-device-management-plan"></a>Plan zarządzania urządzeniami

Przed wprowadzeniem dowolnego urządzenia w środowisku produkcyjnym należy wiedzieć, jak zamierzasz zarządzać przyszłych aktualizacji. Urządzenia usługi IoT Edge, aby uzyskać listę składników do aktualizacji może obejmować:

* Oprogramowania układowego urządzenia
* Biblioteki systemu operacyjnego
* Aparat kontenera, takich jak Moby
* Demon usługi IoT Edge
* Certyfikaty urzędu certyfikacji

Aby uzyskać więcej informacji, zobacz [aktualizacja środowiska uruchomieniowego IoT Edge](how-to-update-iot-edge.md). Aktualizowanie demon usługi IoT Edge bieżącej metody wymagają fizycznej lub dostępu SSH na urządzeniu usługi IoT Edge. Jeśli masz wiele urządzeń do zaktualizowania, rozważ dodanie kroków aktualizacji do skryptu lub użyj narzędzia automatyzacji, takiego jak rozwiązania ansible.

### <a name="use-moby-as-the-container-engine"></a>Użyj Moby jako aparat kontenera

Aparat kontenerów jest wymaganiem wstępnym dla dowolnego IoT Edge urządzenia. Tylko moby aparat jest obsługiwana w środowisku produkcyjnym. Jest ok, aby użyć tych aparatów do tworzenia aplikacji i innych silników kontenera, takich jak Docker, pracy z usługą IoT Edge. Aparat moby mogą być rozpowszechniane w przypadku korzystania z usługi Azure IoT Edge, a firma Microsoft zapewnia obsługę dla tego aparatu.

### <a name="choose-upstream-protocol"></a>Wybierz protokół nadrzędnego

Protokół (i w związku z tym port używany) dla komunikacji między strumieniami IoT Hub można skonfigurować zarówno dla agenta IoT Edge, jak i do centrum IoT Edge. Protokół domyślny to AMQP, ale możesz chcieć zmienić w zależności od konfiguracji sieci. 

Dwa moduły środowiska uruchomieniowego mają zmienną środowiskową **UpstreamProtocol** . Prawidłowe wartości dla zmiennej to: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Skonfiguruj zmienną UpstreamProtocol dla agenta IoT Edge w pliku config. YAML na samym urządzeniu. Na przykład jeśli urządzenie IoT Edge znajduje się za serwerem proxy, który blokuje porty AMQP, może być konieczne skonfigurowanie agenta IoT Edge do używania AMQP przez protokół WebSocket (AMQPWS) w celu nawiązania początkowego połączenia z IoT Hub. 

Po łączy urządzenia usługi IoT Edge, pamiętaj kontynuować konfigurowanie zmiennej UpstreamProtocol dla obu modułów środowiska uruchomieniowego w przyszłych wdrożeniach. Przykład tego procesu znajduje się w temacie [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Wdrożenie

* **Pomaga**
    * Być zgodne z protokołem nadrzędnego
    * Konfigurowanie magazynu hosta dla modułów systemowych
    * Zmniejszanie ilości miejsca w pamięci używanej przez Centrum IoT Edge
    * Nie należy używać wersji debugowania obrazów modułu

### <a name="be-consistent-with-upstream-protocol"></a>Być zgodne z protokołem nadrzędnego

W przypadku skonfigurowania agenta IoT Edge na urządzeniu IoT Edge w celu użycia innego protokołu niż domyślny AMQP należy zadeklarować ten sam protokół we wszystkich przyszłych wdrożeniach. Na przykład w przypadku urządzenia usługi IoT Edge za serwerem proxy, która blokuje porty protokołu AMQP, prawdopodobnie skonfigurowano urządzenia, aby połączyć za pośrednictwem protokołu AMQP przez WebSocket (AMQPWS). Podczas wdrażania modułów na urządzeniu Skonfiguruj ten sam protokół AMQPWS dla agenta IoT Edge i IoT Edge Hub, a w przeciwnym razie domyślne AMQP przesłonią ustawienia i uniemożliwią ponowne nawiązanie połączenia. 

Należy tylko skonfigurować zmienną środowiskową UpstreamProtocol dla agentów IoT Edge i IoT Edge modułów centrum. Wszelkie dodatkowe moduły przyjąć, niezależnie od protokołu jest ustawiana w moduły środowiska uruchomieniowego. 

Przykład tego procesu znajduje się w temacie [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurowanie magazynu hosta dla modułów systemowych

Moduły IoT Edge Hub i Agent używają magazynu lokalnego do utrzymania stanu i włączania obsługi komunikatów między modułami, urządzeniami i chmurą. Aby zapewnić lepszą niezawodność i wydajność, należy skonfigurować moduły systemowe do korzystania z magazynu w systemie plików hosta.

Aby uzyskać więcej informacji, zobacz [host Storage for System modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Zmniejszanie ilości miejsca w pamięci używanej przez Centrum IoT Edge

W przypadku wdrażania urządzeń z ograniczeniami z ograniczoną ilością dostępnej pamięci można skonfigurować IoT Edge Hub do uruchamiania w bardziej usprawnionej pojemności i zmniejszyć ilość miejsca na dysku. Te konfiguracje ograniczają wydajność centrum IoT Edge, jednak można znaleźć odpowiednie saldo, które działa dla danego rozwiązania. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nie Optymalizuj wydajność na urządzeniach z ograniczeniami

Centrum IoT Edge jest zoptymalizowane pod kątem wydajności domyślnie, dlatego próbuje przydzielić duże fragmenty pamięci. Ta konfiguracja może spowodować problemów ze stabilnością w mniejszych urządzeniach, takich jak Raspberry Pi. Jeśli wdrażasz urządzenia z ograniczonymi zasobami, możesz chcieć ustawić dla zmiennej środowiskowej **OptimizeForPerformance** **wartość false** w centrum IoT Edge. 

Gdy **OptimizeForPerformance** ma **wartość true**, kierownik protokołu MQTT używa PooledByteBufferAllocator, które ma lepszą wydajność, ale przydziela więcej pamięci. Program przydzielający nie działa prawidłowo w systemach operacyjnych 32 bitowych lub na urządzeniach z małą ilością pamięci. Ponadto w przypadku optymalizacji pod kątem wydajności program RocksDb przydziela więcej pamięci dla roli jako dostawcy magazynu lokalnego. 

Aby uzyskać więcej informacji, zobacz [problemy ze stabilnością urządzeń z ograniczoną](troubleshoot.md#stability-issues-on-resource-constrained-devices)ilością zasobów.

#### <a name="disable-unused-protocols"></a>Wyłącz protokoły nieużywane

Innym sposobem na zoptymalizowanie wydajności Centrum IoT Edge i zmniejszenie użycia pamięci jest wyłączenie głowic protokołu dla protokołów, które nie są używane w rozwiązaniu. 

Głowice protokołów są konfigurowane przez ustawienie logicznych zmiennych środowiskowych dla modułu IoT Edge Hub w manifestach wdrożenia. Dostępne są następujące trzy zmienne:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Wszystkie trzy zmienne mają *dwie podkreślenia* i mogą być ustawione na wartość true lub false. 

#### <a name="reduce-storage-time-for-messages"></a>Zmniejsz czas przechowywania wiadomości

Moduł IoT Edge Hub przechowuje komunikaty tymczasowo, jeśli nie można ich dostarczyć do IoT Hub z dowolnego powodu. Można skonfigurować, jak długo Centrum IoT Edge będzie przechowywane do niedostarczonych komunikatów przed ich wygaśnięciem. Jeśli masz problemy z pamięcią na urządzeniu, możesz obniżyć wartość **timeToLiveSecs** w postaci sznurka modułu IoT Edge Hub. 

Wartość domyślna parametru timeToLiveSecs jest 7200 sekund, czyli dwie godziny. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Nie należy używać wersji debugowania obrazów modułu

Podczas przenoszenia ze scenariuszy testowania scenariuszy produkcyjnych, pamiętaj, aby usunąć konfiguracje debugowania z manifesty wdrożenia. Sprawdź, czy żaden z obrazów modułu w manifestach wdrożenia ma sufiks **debugowania\.** . Jeśli dodano tworzenie opcji, aby udostępniać porty modułów do debugowania, Usuń tworzenia tych opcji oraz. 

## <a name="container-management"></a>Zarządzanie kontenerami

* **Ważne**
    * Zarządzanie dostępem do rejestru kontenerów
    * Zarządzanie wersjami za pomocą tagów

### <a name="manage-access-to-your-container-registry"></a>Zarządzanie dostępem do rejestru kontenerów

Przed wdrożeniem modułów do urządzenia usługi IoT Edge w środowisku produkcyjnym, upewnij się, kontrolowania dostępu do rejestru kontenerów, tak aby nieszablonowo nie może uzyskać dostęp lub zmieniania obrazów kontenerów. Za pomocą rejestru nie jest publiczny, prywatny kontenerów do zarządzania obrazami kontenerów. 

W samouczkach i innej dokumentacji firma Microsoft poinstruować przy użyciu tych samych poświadczeń rejestru kontenerów na urządzeniu usługi IoT Edge, jak używasz na komputerze deweloperskim. Te instrukcje są przeznaczone wyłącznie do ułatwia konfigurowanie środowisk testowania i programowania łatwiej i nie powinien być używany w scenariuszu produkcyjnym. Azure Container Registry zaleca [się uwierzytelnianie przy użyciu jednostek usługi](../container-registry/container-registry-auth-service-principal.md) , gdy aplikacje lub usługi pobierają obrazy kontenerów w zautomatyzowanym lub nienadzorowanym sposób, jako IoT Edge urządzenia. Tworzenie jednostki usługi z dostępem tylko do odczytu do usługi container registry, a następnie podaj nazwę tego użytkownika i hasło w pliku manifestu wdrożenia.

### <a name="use-tags-to-manage-versions"></a>Zarządzanie wersjami za pomocą tagów

Tag to koncepcja platformy Docker, której można użyć do rozróżnienia między wersjami kontenerów platformy Docker. Tagi to sufiksy podobne do **1,0** , które znajdują się na końcu repozytorium kontenera. Na przykład **MCR.Microsoft.com/azureiotedge-Agent:1.0**. Tagi są modyfikowalne i można ją zmienić na punkt do innego kontenera w dowolnym momencie, aby Twój zespół powinien zgody na Konwencji do wykonania podczas aktualizowania obrazów modułu przenoszenie do przodu. 

Tagi ułatwiają też wymusić aktualizacje na urządzeniach usługi IoT Edge. Po wypchnięciu zaktualizowaną wersję modułu do rejestru kontenerów, jego wartość tagu. Następnie wypchnąć nowe wdrożenie na urządzeniach z systemem z tagiem zwiększany. Aparat kontenera rozpozna zwiększona tag jako nową wersję i będzie pobierać najnowszą wersję modułu swoje urządzenie. 

Aby zapoznać się z przykładem Konwencji znacznika, zobacz [aktualizacja środowiska uruchomieniowego IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) , aby dowiedzieć się, jak IoT Edge używa znaczników stopniowanych i określonych tagów do śledzenia wersji. 

## <a name="networking"></a>Networking

* **Pomaga**
    * Przejrzyj konfigurację wychodzące/przychodzące
    * Zezwalaj na połączenia z urządzeń IoT Edge
    * Konfigurowanie komunikacji za pośrednictwem serwera proxy

### <a name="review-outboundinbound-configuration"></a>Przejrzyj konfigurację wychodzące/przychodzące

Kanałów komunikacji między Azure IoT Hub i IoT Edge zawsze są skonfigurowane jako ruchu wychodzącego. W przypadku większości scenariuszy IoT Edge tylko dla trzech połączenia są niezbędne. Aparat kontenera musi połączyć się z rejestru kontenerów (lub rejestrów), które zawiera obrazy modułu. Środowisko uruchomieniowe usługi IoT Edge musi połączyć się z usługą IoT Hub można pobrać informacji o konfiguracji urządzenia, a także do wysyłania wiadomości i danych telemetrycznych. I korzystając z automatyczną aprowizację, demon usługi IoT Edge należy nawiązać połączenie z usługą Device Provisioning. Aby uzyskać więcej informacji, zobacz [reguły konfiguracji zapory i portów](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Zezwalaj na połączenia z urządzeń IoT Edge

Jeśli konfiguracja sieci wymaga jawnie zezwolenia na połączenia wykonane z urządzeń IoT Edge, Przejrzyj następującą listę składników IoT Edge:

* **Agent IoT Edge** otwiera trwałe połączenie AMQP/MQTT z IoT Hub, prawdopodobnie za pośrednictwem usługi WebSockets. 
* **IoT Edge Hub** otwiera pojedyncze trwałe połączenie AMQP lub wiele połączeń MQTT do IoT Hub, które prawdopodobnie przekraczają usługi WebSockets. 
* **Demon IoT Edge** powoduje sporadyczne wywołania HTTPS do IoT Hub. 

We wszystkich trzech przypadkach nazwa DNS byłaby zgodna ze wzorcem \*. azure-devices.net. 

Dodatkowo **aparat kontenerów** wykonuje wywołania rejestrów kontenerów za pośrednictwem protokołu HTTPS. Aby pobrać obrazy kontenera środowisko uruchomieniowe usługi IoT Edge, nazwa DNS jest mcr.microsoft.com. Aparat container łączy do innych rejestrów, zgodnie z konfiguracją we wdrożeniu. 

Ta lista kontrolna jest punktem początkowym dla reguł zapory:

   | Adres URL (\* = symbol wieloznaczny) | Wychodzące porty TCP | Sposób użycia |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Rejestr kontenerów firmy Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Dostęp do punktu dystrybucji (opcjonalnie) |
   | \*. azurecr.io | 443 | Rejestry kontenerów osobistych i innych firm |
   | \*.blob.core.windows.net | 443 | Pobieranie Azure Container Registry różnic obrazu z magazynu obiektów BLOB  | 
   | \*. azure-devices.net | 5671, 8883, 443 | Dostęp IoT Hub |
   | \*. docker.io  | 443 | Dostęp do centrum platformy Docker (opcjonalnie) |

Niektóre z tych reguł zapory są dziedziczone z Azure Container Registry. Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurowanie komunikacji za pośrednictwem serwera proxy

Urządzenia mają zostać wdrożone w sieci, która korzysta z serwera proxy, muszą mieć możliwość komunikacji za pośrednictwem serwera proxy, aby dotrzeć do Centrum IoT i rejestry kontenerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Rozwiązanie Zarządzanie

* **Pomaga**
    * Konfigurowanie dzienników i Diagnostyka
    * Należy wziąć pod uwagę testy i potoków ciągłej integracji/ciągłego Dostarczania

### <a name="set-up-logs-and-diagnostics"></a>Konfigurowanie dzienników i Diagnostyka

W systemie Linux demon IoT Edge używa dzienników jako domyślnego sterownika rejestrowania. Za pomocą narzędzia wiersza polecenia `journalctl` można wykonywać zapytania dotyczące dzienników demona. Demon usługi IoT Edge na Windows, korzysta z diagnostyki programu PowerShell. Użyj `Get-IoTEdgeLog`, aby wykonywać zapytania dotyczące dzienników z demona. Moduły IoT Edge używają sterownika JSON do rejestrowania, co jest ustawieniem domyślnym.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Podczas testowania wdrożenia usługi IoT Edge, zwykle dostęp urządzenia do pobierania dzienników i rozwiązywanie problemów. W scenariuszu wdrażania możesz nie mieć tej opcji. Należy wziąć pod uwagę, jak możesz zacząć zbierać informacje dotyczące urządzeń w środowisku produkcyjnym. Jedną z opcji jest używać moduł rejestrowania, który zbiera informacje z innymi modułami i wysyła je do chmury. Jednym z przykładów modułu rejestrowania jest [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), lub możesz zaprojektować własny. 

### <a name="place-limits-on-log-size"></a>Limity umieszczenia rozmiaru dziennika

Domyślnie aparat kontenera Moby nie ustawia limitów rozmiaru dziennika kontenera. W miarę upływu czasu może to prowadzić do wypełniania urządzenia przy użyciu dzienników i uruchamiania miejsca na dysku. Aby tego uniknąć, należy wziąć pod uwagę następujące opcje:

**Opcja: Ustaw limity globalne, które mają zastosowanie do wszystkich modułów kontenera**

Można ograniczyć rozmiar wszystkich plików dziennika kontenerów w opcjach dziennika aparatu kontenera. Poniższy przykład ustawia sterownik dziennika do `json-file` (zalecane) z limitami rozmiaru i liczby plików:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Dodaj (lub Dołącz) te informacje do pliku o nazwie `daemon.json` i umieść go w odpowiedniej lokalizacji dla platformy urządzeń.

| Platforma | Lokalizacja |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić silnik kontenera.

**Opcja: Dopasuj ustawienia dziennika dla każdego modułu kontenera**

Można to zrobić w **opcjach** dla każdego modułu. Na przykład:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Dodatkowe opcje w systemach Linux**

* Skonfiguruj aparat kontenera do wysyłania dzienników do `systemd` [dziennika](https://docs.docker.com/config/containers/logging/journald/) przez ustawienie `journald` jako domyślnego sterownika rejestrowania. 

* Okresowo Usuwaj stare dzienniki z urządzenia, instalując narzędzie logrotate. Użyj następujących specyfikacji pliku: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Należy wziąć pod uwagę testy i potoków ciągłej integracji/ciągłego Dostarczania

Najbardziej efektywny sposób scenariusza wdrożenia usługi IoT Edge należy rozważyć włączenie wdrożeniu produkcyjnym do testowania i potoków ciągłej integracji/ciągłego Dostarczania. Usługa Azure IoT Edge obsługuje wiele platform ciągłej integracji/ciągłego wdrażania, w tym DevOps platformy Azure. Aby uzyskać więcej informacji, zobacz [ciągła integracja i ciągłe wdrażanie do Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [IoT Edge wdrożeniu automatycznym](module-deployment-monitoring.md).
* Zobacz, jak IoT Edge obsługuje [ciągłą integrację i ciągłe wdrażanie](how-to-ci-cd.md).
