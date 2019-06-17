---
title: Wdrażanie usługi Azure Security Center dla modułu usługi IoT Edge | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o sposobie wdrażania usługi Azure Security Center dla agenta zabezpieczeń IoT w usłudze IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 85e342f08e5402e50e5b0dfd1fe2df90337f29ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254294"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń, na urządzeniu usługi IoT Edge

> [!IMPORTANT]
> Centrum zabezpieczeń Azure dla IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Usługa Azure Security Center (ASC) dla IoT** moduł dostarcza rozwiązanie kompleksowych funkcji zabezpieczeń urządzenia usługi IoT Edge.
Moduł zabezpieczeń służy do zbierania, agreguje i analizuje dane dotyczące zabezpieczeń pierwotnych z systemem operacyjnym i kontenera do zaleceń dotyczących zabezpieczeń z możliwością działania i alerty.
Aby dowiedzieć się więcej, zobacz [modułu zabezpieczeń dla usługi IoT Edge](security-edge-architecture.md).

W tym przewodniku dowiesz się, jak wdrożyć modułu zabezpieczeń, na urządzeniu usługi IoT Edge.

## <a name="deploy-security-module"></a>Wdrażanie modułu zabezpieczeń

Wykonaj następujące kroki, aby wdrożyć ASC modułu zabezpieczeń IoT, IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

- W usłudze IoT Hub upewnij się, że urządzenie jest [zarejestrowane jako urządzenia usługi IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Wymaga usługi ASC dla modułu usługi IoT Edge [AuditD framework](https://linux.die.net/man/8/auditd) jest zainstalowana na urządzeniu usługi IoT Edge.

    - Zainstaluj platformę, uruchamiając następujące polecenie na urządzeniu usługi IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Sprawdź, czy AuditD jest aktywny, uruchamiając następujące polecenie:
   
      `sudo systemctl status auditd`
      
        Oczekiwana reakcja jest `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu witryny Azure portal

1. W witrynie Azure portal Otwórz **Marketplace**.

1. Wybierz **Internet of Things**, a następnie wyszukaj **Centrum zabezpieczeń Azure dla IoT** i wybierz ją.

   ![Wybierz Centrum zabezpieczeń Azure dla IoT](media/howto/edge-onboarding-8.png)

1. Kliknij przycisk **Utwórz** umożliwia skonfigurowanie wdrażania. 

1. Wybierz platformę Azure **subskrypcji** Centrum IoT Hub, następnie wybierz swoje **usługi IoT Hub**.<br>Wybierz **wdrażanie na urządzeniu** na docelowe pojedyncze urządzenie, albo wybierz **Wdróż na dużą skalę** wiele urządzeń, a następnie kliknij przycisk **Utwórz**. Aby uzyskać więcej informacji o wdrażaniu na dużą skalę, zobacz [sposób wdrażania](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >W przypadku wybrania **Wdróż na dużą skalę**, Dodaj nazwę urządzenia i uzyskać szczegółowe informacje przed kontynuowaniem **Dodawanie modułów** kartę w poniższych instrukcjach.     

Istnieją trzy kroki, aby utworzyć wdrożenie usługi IoT Edge dla Centrum zabezpieczeń Azure dla IoT. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich. 

#### <a name="step-1-add-modules"></a>Krok 1: Dodaj moduły

1. Z **Dodawanie modułów** karcie **moduły wdrożeń** obszaru, kliknij przycisk **AzureSecurityCenterforIoT**. 
   
1. Zmiana **nazwa** do **azureiotsecurity**.
1. Zmiana **identyfikator URI obrazu** do **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Sprawdź **opcje tworzenia kontenera** ma wartość:      
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
1. Upewnij się, że **żądane właściwości zestawu modułu bliźniaczej reprezentacji** jest zaznaczone, a następnie zmień obiekt konfiguracji, aby:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Kliknij pozycję **Zapisz**.
1. Przewiń w dół kartę, a następnie wybierz pozycję **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**.
   
   >[!Note]
   > Czy **nie** Wyłącz komunikacji protokołu AMQP dla Centrum usługi IoT Edge.
   > Usługa Azure Security Center dla modułu IoT wymaga komunikacji protokołu AMQP z Centrum usługi IoT Edge.
   
1. Zmiana **obraz** w obszarze **krawędzi Centrum** do **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Usługa Azure Security Center dla modułu IoT wymaga rozwidlone wersję Centrum usługi Edge IoT, w oparciu o zestaw SDK w wersji 1.20.
   > Zmieniając obrazu usługi IoT Edge Hub, zlecasz urządzenia usługi IoT Edge, aby zastąpić najnowsza stabilna wersja rozwidlone wersję Centrum IoT Edge, co nie jest oficjalnie obsługiwane przez usługę IoT Edge.

1. Sprawdź **opcje tworzenia** jest ustawiona na: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Kliknij pozycję **Zapisz**.
   
1. Kliknij przycisk **Dalej**.

#### <a name="step-2-specify-routes"></a>Krok 2: Określanie tras 

1. W **określanie tras** kartę, należy ustawić **ASCForIoTToIoTHub** kierować do **"z/komunikatów/modułów/azureiotsecurity/\* do $nadrzędne"** i kliknij przycisk  **Następny**.

   ![Określanie tras](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Krok 3: Przegląd wdrożenia

1. W **wdrożenia Przejrzyj** kartę, przejrzyj informacje o wdrożeniu, a następnie wybierz **przesyłania** do ukończenia wdrażania.

## <a name="diagnostic-steps"></a>Czynności diagnostycznych

Jeśli wystąpi problem, dzienniki kontenerów są najlepszym sposobem, aby dowiedzieć się więcej o stanie urządzenia usługi IoT Edge zabezpieczeń modułu. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery są zainstalowane i działa zgodnie z oczekiwaniami

1. Na urządzeniu usługi IoT Edge, uruchom następujące polecenie:
    
     `sudo docker ps`
   
1. Sprawdź, czy uruchomiono następujących kontenerów:
   
   | Name (Nazwa) | IMAGE |
   | --- | --- |
   | azureiotsecurity | MCR.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | MCR.microsoft.com/ascforiot/edgehub:1.0.9-Preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Minimalna wymagana kontenery nie są obecne, sprawdź, jeśli manifest wdrożenia usługi IoT Edge jest powiązana z zalecanych ustawień. Aby uzyskać więcej informacji, zobacz [moduł usługi IoT Edge wdrażanie](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Przejrzyj dzienniki modułu dla błędów
   
1. Na urządzeniu usługi IoT Edge, uruchom następujące polecenie:

   `sudo docker logs azureiotsecurity`
   
1. Bardziej szczegółowy dzienników, dodaj następującą zmienną środowiskową, aby **azureiotsecurity** wdrażanie modułu: `logLevel=Debug`.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do Przewodnik z instrukcjami dotyczącymi konfiguracji modułu. 
> [!div class="nextstepaction"]
> [Przewodnik konfiguracji modułu](./how-to-agent-configuration.md)
