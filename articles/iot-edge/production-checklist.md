---
title: Przygotowywanie urządzenia i wdrożenia w środowisku produkcyjnym — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązania usługi Azure IoT Edge od projektowania do produkcji, w tym konfigurowania urządzeń odpowiednie certyfikaty i tworzenie planu wdrożenia aktualizacji przyszłego kodu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: cb72949c0bb543885498b1b997fa0b4a644c204a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956964"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Przygotowanie do wdrożenia rozwiązania usługi IoT Edge w środowisku produkcyjnym

Gdy wszystko będzie gotowe móc rozwiązania usługi IoT Edge, od projektowania do produkcji, upewnij się, że została ona skonfigurowana do wydajność.

Informacje zawarte w tym artykule nie jest równe. Aby określić priorytety, każda sekcja rozpoczyna się od list, które najlepiej podzielić na dwie sekcje: **ważne** do wykonania przed przejściem do produkcji, lub **przydatne** można dowiedzieć.

## <a name="device-configuration"></a>Konfiguracja urządzenia

Urządzenia usługi IoT Edge mogą być cokolwiek — od urządzeń Raspberry Pi laptopa do maszyny wirtualnej na serwerze. Masz dostęp do urządzenia fizycznie lub za pośrednictwem połączeń wirtualnych lub mogą być izolowane dłuższy czas. W obu przypadkach należy upewnić się, że jest skonfigurowana do wykonywania odpowiednio. 

* **Ważne**
    * Instalowanie certyfikatów w środowisku produkcyjnym
    * Plan zarządzania urządzeniami
    * Użyj Moby jako aparat kontenera

* **Przydatne**
    * Wybierz protokół nadrzędnego

### <a name="install-production-certificates"></a>Instalowanie certyfikatów w środowisku produkcyjnym

Każde urządzenie usługi IoT Edge w środowisku produkcyjnym, musi na nim zainstalowany certyfikat urzędu certyfikacji certyfikatu urządzenia. Następnie zadeklarowano ten certyfikat urzędu certyfikacji do środowiska uruchomieniowego usługi IoT Edge w pliku config.yaml. Programowanie i testowanie, IoT Edge środowisko uruchomieniowe tworzy certyfikatów tymczasowych, jeśli żadne certyfikaty nie został zadeklarowany w pliku config.yaml. Jednak te certyfikaty tymczasowe wygasają po upływie trzech miesięcy, a nie są bezpieczne dla scenariuszy produkcyjnych. 

Aby poznać roli certyfikatu urządzenia urzędu certyfikacji, zobacz [jak usługi Azure IoT Edge korzysta z certyfikatów](iot-edge-certs.md).

Aby uzyskać więcej informacji na temat sposobu instalowania certyfikatów na urządzeniu usługi IoT Edge i odwoływać się do nich z pliku config.yaml, zobacz [Konfigurowanie urządzenia usługi IoT Edge, aby pełnić rolę przezroczystej bramy](how-to-create-transparent-gateway.md). Procedura konfigurowania certyfikatów są takie same, czy urządzenie ma być używany jako brama lub nie. Ten artykuł zawiera skrypty w celu wygenerowania certyfikatów przykładowe tylko do celów testowych. Nie używaj tych certyfikatów próbki w środowisku produkcyjnym. 

### <a name="have-a-device-management-plan"></a>Plan zarządzania urządzeniami

Przed wprowadzeniem dowolnego urządzenia w środowisku produkcyjnym należy wiedzieć, jak zamierzasz zarządzać przyszłych aktualizacji. Urządzenia usługi IoT Edge, aby uzyskać listę składników do aktualizacji może obejmować:

* Oprogramowania układowego urządzenia
* Biblioteki systemu operacyjnego
* Aparat kontenera, takich jak Moby
* Demon usługi IoT Edge
* Certyfikaty urzędu certyfikacji

Aby uzyskać kroki, aby zaktualizować demon usługi IoT Edge, zobacz [aktualizacji środowiska uruchomieniowego usługi IoT Edge](how-to-update-iot-edge.md). Aktualizowanie demon usługi IoT Edge bieżącej metody wymagają fizycznej lub dostępu SSH na urządzeniu usługi IoT Edge. Jeśli masz wiele urządzeń do aktualizacji, Rozważ dodanie kroki aktualizacji do skryptu, lub użyj narzędzia automatyzacji, takiego jak Ansible do przeprowadzania aktualizacji na dużą skalę.

### <a name="use-moby-as-the-container-engine"></a>Użyj Moby jako aparat kontenera

Aparat kontenera na urządzeniu jest wymaganiem wstępnym dla wszystkich urządzeń usługi IoT Edge. Tylko moby aparat jest obsługiwana w środowisku produkcyjnym. Jest ok, aby użyć tych aparatów do tworzenia aplikacji i innych silników kontenera, takich jak Docker, pracy z usługą IoT Edge. Aparat moby mogą być rozpowszechniane w przypadku korzystania z usługi Azure IoT Edge, a firma Microsoft zapewnia obsługę dla tego aparatu. Za pomocą aparatów innych kontenerów na urządzenia usługi IoT Edge nie jest obsługiwana.

### <a name="choose-upstream-protocol"></a>Wybierz protokół nadrzędnego

Protokół (i w związku z tym portem używanym) dla nadrzędnego komunikacji z Centrum IoT Hub można skonfigurować zarówno dla agenta usługi Edge i Centrum usługi Edge. Protokół domyślny to AMQP, ale możesz chcieć zmienić w zależności od konfiguracji sieci. 

Masz dwa środowiska uruchomieniowego moduły zarówno **UpstreamProtocol** zmiennej środowiskowej. Prawidłowe wartości dla zmiennej to: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Skonfiguruj zmienną UpstreamProtocol dla agenta usługi Edge w pliku config.yaml na samym urządzeniu. Na przykład w przypadku urządzenia usługi IoT Edge za serwerem proxy, która blokuje porty protokołu AMQP, może być konieczne skonfigurowanie agenta usługi Edge użycie połączeń AMQP przez WebSocket (AMQPWS) do nawiązywania początkowego połączenia usługi IoT Hub. 

Po łączy urządzenia usługi IoT Edge, pamiętaj kontynuować konfigurowanie zmiennej UpstreamProtocol dla obu modułów środowiska uruchomieniowego w przyszłych wdrożeniach. Przykładem tego procesu znajduje się w [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Wdrożenie

* **Przydatne**
    * Być zgodne z protokołem nadrzędnego
    * Zmniejsz obszar pamięci wykorzystywany przez Centrum usługi Edge
    * Nie należy używać wersji debugowania obrazów modułu

### <a name="be-consistent-with-upstream-protocol"></a>Być zgodne z protokołem nadrzędnego

Jeśli skonfigurowano agenta usługi Edge na urządzeniu usługi IoT Edge, aby używać protokołu innego niż domyślne, protokołu AMQP powinny deklarować tego samego protokołu we wszystkich wdrożeniach kolejne. Na przykład w przypadku urządzenia usługi IoT Edge za serwerem proxy, która blokuje porty protokołu AMQP, prawdopodobnie skonfigurowano urządzenia, aby połączyć za pośrednictwem protokołu AMQP przez WebSocket (AMQPWS). Jeśli podczas wdrażania modułów na urządzeniu nie Konfiguruj tego samego protokołu APQPWS agent usługi Edge i Centrum usługi Edge, domyślne AMQP zastępują ustawienia co uniemożliwia ponownie nawiązać połączenie. 

Musisz skonfigurować zmienną środowiskową UpstreamProtocol agent usługi Edge i moduły Centrum usługi Edge. Wszelkie dodatkowe moduły przyjąć, niezależnie od protokołu jest ustawiana w moduły środowiska uruchomieniowego. 

Przykładem tego procesu znajduje się w [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Zmniejsz obszar pamięci wykorzystywany przez Centrum usługi Edge

Jeśli wdrażasz ograniczone urządzeń przy użyciu ograniczonych dostępnej pamięci, można skonfigurować Centrum usługi Edge umożliwiające uruchomienie usprawnić pojemność i użycie mniej miejsca na dysku. Te konfiguracje jednak ograniczyć wydajność Centrum usługi Edge, więc znaleźć równowagę działającego dla Twojego rozwiązania. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nie Optymalizuj wydajność na urządzeniach z ograniczeniami

Centrum usługi Edge jest zoptymalizowana pod kątem wydajności domyślnie, dzięki czemu próbuje przydzielić dużych bloków pamięci. Ta konfiguracja może spowodować problemów ze stabilnością w mniejszych urządzeniach, takich jak Raspberry Pi. Jeśli wdrażasz urządzeń za pomocą ograniczone zasoby, warto ustawić **OptimizeForPerformance** zmiennej środowiskowej, aby **false** Centrum Edge. 

Aby uzyskać więcej informacji, zobacz [problemów ze stabilnością zasobu ograniczonego urządzeń](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Wyłącz protokoły nieużywane

Innym sposobem zoptymalizowania wydajności centrum usługi Edge i zmniejszyć jego użycie pamięci jest wyłączyć głowic protokołu dla wszystkich protokołów, których nie używasz w rozwiązaniu. 

Protokół głowic są konfigurowane przez Ustawianie zmiennych środowiskowych logiczną dla moduł Centrum usługi Edge w swojej manifesty wdrożenia. Dostępne są następujące trzy zmienne:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Wszystkie trzy zmienne mają *dwoma podkreśleniami* i można go ustawić na wartość true lub false. 

#### <a name="reduce-storage-time-for-messages"></a>Zmniejsz czas przechowywania wiadomości

Moduł Centrum usługi Edge tymczasowo przechowuje komunikaty, jeśli nie można dostarczyć do usługi IoT Hub z dowolnego powodu. Można skonfigurować, jak długo Centrum Edge hub utrzymuje niedostarczone wiadomości przed zezwoleniem im wygaśnie. Jeśli masz wątpliwości pamięci na urządzeniu, możesz obniżyć **timeToLiveSecs** wartość w bliźniaczej reprezentacji modułu Centrum usługi Edge. 

Wartość domyślna parametru timeToLiveSecs jest 7200 sekund, czyli dwie godziny. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Nie należy używać wersji debugowania obrazów modułu

Podczas przenoszenia ze scenariuszy testowania scenariuszy produkcyjnych, pamiętaj, aby usunąć konfiguracje debugowania z manifesty wdrożenia. Sprawdź, czy żaden z obrazów modułu w manifesty wdrożenia mają  **\.debugowania** sufiks. Jeśli dodano tworzenie opcji, aby udostępniać porty modułów do debugowania, Usuń tworzenia tych opcji oraz. 

## <a name="container-management"></a>Zarządzanie kontenerami

* **Ważne**
    * Zarządzanie dostępem do rejestru kontenerów
    * Zarządzanie wersjami za pomocą tagów

### <a name="manage-access-to-your-container-registry"></a>Zarządzanie dostępem do rejestru kontenerów

Przed wdrożeniem modułów do urządzenia usługi IoT Edge w środowisku produkcyjnym, upewnij się, kontrolowania dostępu do rejestru kontenerów, tak aby nieszablonowo nie może uzyskać dostęp lub zmieniania obrazów kontenerów. Za pomocą rejestru nie jest publiczny, prywatny kontenerów do zarządzania obrazami kontenerów. 

W samouczkach i innej dokumentacji firma Microsoft poinstruować przy użyciu tych samych poświadczeń rejestru kontenerów na urządzeniu usługi IoT Edge, jak używasz na komputerze deweloperskim. Te instrukcje są przeznaczone wyłącznie do ułatwia konfigurowanie środowisk testowania i programowania łatwiej i nie powinien być używany w scenariuszu produkcyjnym. Usługa Azure Container Registry zaleca [uwierzytelniania za pomocą jednostki usługi](../container-registry/container-registry-auth-service-principal.md) po aplikacje lub usługi Wypychaj obrazy kontenerów w automatycznych lub w inny sposób sposób, jak urządzenia usługi IoT Edge. Tworzenie jednostki usługi z dostępem tylko do odczytu do usługi container registry, a następnie podaj nazwę tego użytkownika i hasło w pliku manifestu wdrożenia.

### <a name="use-tags-to-manage-versions"></a>Zarządzanie wersjami za pomocą tagów

Tag jest pojęciem platformy docker, używaną do rozróżniania między wersjami kontenerów platformy docker. Tagi są sufiksy, takich jak **1.0** , przejdź na koniec repozytorium kontenerów. Na przykład **mcr.microsoft.com/azureiotedge-agent:1.0**. Tagi są modyfikowalne i można ją zmienić na punkt do innego kontenera w dowolnym momencie, aby Twój zespół powinien zgody na Konwencji do wykonania podczas aktualizowania obrazów modułu przenoszenie do przodu. 

Tagi ułatwiają też wymusić aktualizacje na urządzeniach usługi IoT Edge. Po wypchnięciu zaktualizowaną wersję modułu do rejestru kontenerów, jego wartość tagu. Następnie wypchnąć nowe wdrożenie na urządzeniach z systemem z tagiem zwiększany. Aparat kontenera rozpozna zwiększona tag jako nową wersję i będzie pobierać najnowszą wersję modułu swoje urządzenie. 

Na przykład tag Konwencji zobacz [aktualizacji środowiska uruchomieniowego usługi IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) Aby dowiedzieć się, jak usługi IoT Edge korzysta stopniowe tagów i określonymi tagami śledzenia wersji. 

## <a name="networking"></a>Networking

* **Przydatne**
    * Przejrzyj konfigurację wychodzące/przychodzące
    * Lista dozwolonych połączeń
    * Konfigurowanie komunikacji za pośrednictwem serwera proxy

### <a name="review-outboundinbound-configuration"></a>Przejrzyj konfigurację wychodzące/przychodzące

Kanałów komunikacji między Azure IoT Hub i IoT Edge zawsze są skonfigurowane jako ruchu wychodzącego. W przypadku większości scenariuszy IoT Edge tylko dla trzech połączenia są niezbędne. Aparat kontenera musi połączyć się z rejestru kontenerów (lub rejestrów), które zawiera obrazy modułu. Środowisko uruchomieniowe usługi IoT Edge musi połączyć się z usługą IoT Hub można pobrać informacji o konfiguracji urządzenia, a także do wysyłania wiadomości i danych telemetrycznych. I korzystając z automatyczną aprowizację, demon usługi IoT Edge należy nawiązać połączenie z usługą Device Provisioning. Aby uzyskać więcej informacji, zobacz [Zapora i reguły konfiguracji portów](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Lista dozwolonych połączeń

Jeśli ustawienia sieciowe wymaga jawnie dozwolonych połączeń wprowadzone z urządzenia usługi IoT Edge, przejrzyj poniższą listę składników usługi IoT Edge:

* **Agent usługi IoT Edge** otwiera trwałe połączenie AMQP/MQTT Centrum IoT, prawdopodobnie przez protokół WebSockets. 
* **Centrum usługi IoT Edge** otwiera przez pojedyncze połączenie AMQP trwałego lub wiele połączeń protokołu MQTT do usługi IoT Hub prawdopodobnie przez protokół WebSockets. 
* **Demon usługi IoT Edge** wywołań sporadyczne HTTPS do usługi IoT Hub. 

We wszystkich trzech przypadkach nazwy DNS będzie pasuje do wzorca \*.azure-devices.net. 

Ponadto **kontenera aparatu** wykonywania wywołań do rejestrów kontenerów przy użyciu protokołu HTTPS. Aby pobrać obrazy kontenera środowisko uruchomieniowe usługi IoT Edge, nazwa DNS jest mcr.microsoft.com. Aparat container łączy do innych rejestrów, zgodnie z konfiguracją we wdrożeniu. 

Ta lista kontrolna stanowi punkt wyjścia dla reguł zapory:

   | Adres URL (\* = symboli wieloznacznych) | Porty TCP ruchu wychodzącego | Sposób użycia |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Rejestr kontenerów firmy Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Usługa DPS dostępu (opcjonalnie) |
   | \*. azurecr.io | 443 | Rejestry kontenerów strony osobistych, jak i 3 |
   | \*.blob.core.windows.net | 443 | Pobierz obraz różnic | 
   | \*.azure-devices.net | 5671, 8883, 443 | Dostęp do usługi IoT Hub |
   | \*.docker.io  | 443 | Dostęp do Centrum docker (opcjonalnie) |

### <a name="configure-communication-through-a-proxy"></a>Konfigurowanie komunikacji za pośrednictwem serwera proxy

Urządzenia mają zostać wdrożone w sieci, która korzysta z serwera proxy, muszą mieć możliwość komunikacji za pośrednictwem serwera proxy, aby dotrzeć do Centrum IoT i rejestry kontenerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Rozwiązanie Zarządzanie

* **Przydatne**
    * Konfigurowanie dzienników i Diagnostyka
    * Należy wziąć pod uwagę testy i potoków ciągłej integracji/ciągłego Dostarczania

### <a name="set-up-logs-and-diagnostics"></a>Konfigurowanie dzienników i Diagnostyka

W systemie Linux demon usługi IoT Edge korzysta z arkuszy jako domyślne rejestrowanie sterownika. Można użyć narzędzia wiersza polecenia `journalctl` kwerendy demona dzienniki. Demon usługi IoT Edge na Windows, korzysta z diagnostyki programu PowerShell. Użyj `Get-IoTEdgeLog` dzienniki zapytania z demona. Moduły usługi IoT Edge na użytek sterownik JSON rejestrowania, co jest ustawieniem domyślnym.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Podczas testowania wdrożenia usługi IoT Edge, zwykle dostęp urządzenia do pobierania dzienników i rozwiązywanie problemów. W scenariuszu wdrażania możesz nie mieć tej opcji. Należy wziąć pod uwagę, jak możesz zacząć zbierać informacje dotyczące urządzeń w środowisku produkcyjnym. Jedną z opcji jest używać moduł rejestrowania, który zbiera informacje z innymi modułami i wysyła je do chmury. Jednym z przykładów moduł rejestrowania jest [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), lub projektować własne. 

### <a name="place-limits-on-log-size"></a>Ogranicza rozmiar dziennika

Domyślnie aparat container Moby nie ustawia limity rozmiaru dziennika kontenera. Wraz z upływem czasu może to prowadzić do urządzenia zapełnia się za pomocą dzienników i wyczerpuje się miejsce na dysku. Należy wziąć pod uwagę następujące opcje, aby zapobiec takiej sytuacji:

**Opcja: Ustaw limity globalne, które są stosowane do wszystkich modułów kontenera**

Można ograniczyć rozmiar wszystkich logfiles kontenera za pomocą kontenera aparatu dziennika opcji. Poniższy przykład ustawia sterownik dziennika `json-file` (zalecane), za pomocą ograniczenia dotyczące rozmiaru i liczby plików:

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

Dodaj (lub dołączyć) te informacje w pliku o nazwie `daemon.json` i umieść go w odpowiedniej lokalizacji dla danej platformy urządzenia.

| Platforma | Lokalizacja |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aparat kontenera.

**Opcja: Dostosuj ustawienia dziennika dla każdego modułu kontenera**

Możecie więc w **CreateOptions, można żądań** każdego modułu. Na przykład:

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


**Dodatkowe opcje w systemach Linux**

* Konfigurowanie aparatu kontenera, aby wysłać dzienniki do `systemd` [dziennika](https://docs.docker.com/config/containers/logging/journald/) , ustawiając `journald` jako domyślnego sterownika rejestrowania. 

* Okresowo należy usunąć stare dzienniki z urządzenia, instalując narzędzia logrotate. Użyj następujących specyfikacji pliku: 

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

Najbardziej efektywny sposób scenariusza wdrożenia usługi IoT Edge należy rozważyć włączenie wdrożeniu produkcyjnym do testowania i potoków ciągłej integracji/ciągłego Dostarczania. Usługa Azure IoT Edge obsługuje wiele platform ciągłej integracji/ciągłego wdrażania, w tym DevOps platformy Azure. Aby uzyskać więcej informacji, zobacz [ciągłą integrację i ciągłe wdrażanie w usłudze Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [automatycznego wdrażania usługi IoT Edge](module-deployment-monitoring.md).
* Zobacz, jak usługi IoT Edge obsługuje [ciągłej integracji i ciągłego wdrażania](how-to-ci-cd.md).
