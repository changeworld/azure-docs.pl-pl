---
title: Wdrażanie modułu zabezpieczeń IoT Edge
description: Dowiedz się, jak wdrożyć agenta zabezpieczeń Usługi Azure Security Center dla IoT w usłudze IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4dd7ca8f926862487b9505731c0662e68ee3d7c0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311281"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń na urządzeniu IoT Edge

**Moduł Usługi Azure Security Center dla IoT** zapewnia kompleksowe rozwiązanie zabezpieczające dla urządzeń usługi IoT Edge.
Moduł zabezpieczeń zbiera, agreguje i analizuje surowe dane zabezpieczeń z systemu operacyjnego i systemu kontenerów na użyteczne zalecenia dotyczące zabezpieczeń i alerty.
Aby dowiedzieć się więcej, zobacz [Moduł zabezpieczeń dla ioT Edge](security-edge-architecture.md).

Z tego artykułu dowiesz się, jak wdrożyć moduł zabezpieczeń na urządzeniu Z aplikacją IoT Edge.

## <a name="deploy-security-module"></a>Wdrażanie modułu zabezpieczeń

Poniższe kroki można wykonać, aby wdrożyć moduł zabezpieczeń Usługi Azure Security Center dla usługi IoT dla usługi IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

1. Upewnij się, że w centrum IoT Hub urządzenie jest [zarejestrowane jako urządzenie Usługi IoT Edge.](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)

1. Moduł usługi Azure Security Center dla usługi IoT Edge wymaga [zainstalowania struktury AuditD](https://linux.die.net/man/8/auditd) na urządzeniu usługi IoT Edge.

    - Zainstaluj strukturę, uruchamiając następujące polecenie na urządzeniu usługi IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Sprawdź, czy inspekcja jest aktywna, uruchamiając następujące polecenie:

    `sudo systemctl status auditd`<br>
    - Oczekiwana odpowiedź to:`active (running)`

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu portalu Azure

1. W witrynie Azure portal otwórz **witrynę Marketplace**.

1. Wybierz **internet rzeczy**, a następnie wyszukaj usługę Azure Security Center dla **IoT** i wybierz go.

   ![Wybieranie usługi Azure Security Center dla IoT](media/howto/edge-onboarding-8.png)

1. Kliknij **przycisk Utwórz,** aby skonfigurować wdrożenie.

1. Wybierz **subskrypcję** usługi Azure centrum IoT Hub, a następnie wybierz **centrum IoT Hub**.<br>Wybierz **pozycję Wdrażanie na urządzeniu,** aby kierować reklamy na pojedyncze urządzenie, lub wybierz **pozycję Wdrażanie na skali,** aby kierować reklamy na wiele urządzeń, a następnie kliknij przycisk **Utwórz**. Aby uzyskać więcej informacji na temat wdrażania na dużą skalę, zobacz [Jak wdrożyć](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor).

    >[!Note]
    >Jeśli wybrano **opcję Wdrażanie w skali,** dodaj nazwę i szczegóły urządzenia przed kontynuowaniem do karty **Dodaj moduły** w poniższych instrukcjach.

Wykonaj każdy krok, aby ukończyć wdrożenie usługi IoT Edge dla usługi Azure Security Center dla IoT.

#### <a name="step-1-modules"></a>Krok 1: Moduły

1. Wybierz moduł **AzureSecurityCenterforIoT.**
1. Na karcie **Ustawienia modułu** zmień **nazwę** na **azureiotsecurity**.
1. Na karcie **Zmienne środowiska** dodaj zmienną w razie potrzeby (na przykład poziom debugowania).
1. Na karcie **Opcje tworzenia kontenera** dodaj następującą konfigurację:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Na karcie **Ustawienia bliźniaczej reprezentacji modułu** dodaj następującą konfigurację:

    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
    ```

1. Wybierz pozycję **Update** (Aktualizuj).

#### <a name="step-2-runtime-settings"></a>Krok 2: Ustawienia środowiska wykonawczego

1. Wybierz **pozycję Ustawienia środowiska wykonawczego**.
1. W obszarze **Koncentrator krawędzi**zmień **obraz** na **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Sprawdź, czy **opcje tworzenia** jest ustawiona na następującą konfigurację:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Wybierz pozycję **Zapisz**.

1. Wybierz opcję **Dalej**.

#### <a name="step-3-specify-routes"></a>Krok 3: Określanie tras

1. Na **określ trasy** kartę, upewnij się, że masz trasę (jawne lub niejawne), która będzie przesyłać dalej wiadomości z **modułu azureiotsecurity** **do $upstream** zgodnie z poniższymi przykładami. Tylko wtedy, gdy trasa jest na miejscu, wybierz **przycisk Dalej**.

   Przykładowe trasy:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Wybierz opcję **Dalej**.

#### <a name="step-4-review-deployment"></a>Krok 4: Przegląd wdrażania

- Na karcie **Przejrzyj wdrażanie** przejrzyj informacje o wdrożeniu, a następnie wybierz pozycję **Utwórz,** aby ukończyć wdrożenie.

## <a name="diagnostic-steps"></a>Kroki diagnostyczne

Jeśli wystąpi problem, dzienniki kontenerów są najlepszym sposobem, aby dowiedzieć się o stanie urządzenia modułu zabezpieczeń usługi IoT Edge. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery są zainstalowane i działają zgodnie z oczekiwaniami

1. Uruchom następujące polecenie na urządzeniu IoT Edge:

    `sudo docker ps`

1. Sprawdź, czy uruchomione są następujące kontenery:

   | Nazwa | OBRAZ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub (hub) | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Jeśli minimalne wymagane kontenery nie są obecne, sprawdź, czy manifest wdrażania usługi IoT Edge jest wyrównany z zalecanymi ustawieniami. Aby uzyskać więcej informacji, zobacz [Wdrażanie modułu usługi IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Sprawdź dzienniki modułów pod kątem błędów

1. Uruchom następujące polecenie na urządzeniu IoT Edge:

   `sudo docker logs azureiotsecurity`

1. Aby uzyskać więcej pełnych dzienników, dodaj następującą zmienną środowiskową `logLevel=Debug`do wdrożenia modułu **azureiotsecurity:** .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika how-to dla konfiguracji modułu.
> [!div class="nextstepaction"]
> [Instrukcja obsługi konfiguracji modułu](./how-to-agent-configuration.md)
