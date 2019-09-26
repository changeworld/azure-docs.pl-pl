---
title: Wdróż Azure Security Center dla modułu IoT Edge | Microsoft Docs
description: Dowiedz się więcej na temat sposobu wdrażania Azure Security Center usługi IoT Security Agent na IoT Edge.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bb6a975d2a2fc2cc3e65fa8969f8b005be8b1417
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299719"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń na urządzeniu IoT Edge


**Azure Security Center dla modułu IoT** to kompleksowe rozwiązanie zabezpieczeń dla IoT Edge urządzeń.
Moduł zabezpieczeń zbiera, agreguje i analizuje pierwotne dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.
Aby dowiedzieć się więcej, zobacz [moduł zabezpieczeń dla IoT Edge](security-edge-architecture.md).

W tym artykule dowiesz się, jak wdrożyć moduł zabezpieczeń na urządzeniu IoT Edge.

## <a name="deploy-security-module"></a>Wdróż moduł zabezpieczeń

Wykonaj następujące kroki, aby wdrożyć Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

1. W IoT Hub upewnij się, że urządzenie jest [zarejestrowane jako urządzenie IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. Azure Security Center modułu IoT Edge wymaga, aby [platforma](https://linux.die.net/man/8/auditd) poddana inspekcji była zainstalowana na urządzeniu IoT Edge.

    - Zainstaluj platformę, uruchamiając następujące polecenie na urządzeniu IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Sprawdź, czy inspekcja jest aktywna, uruchamiając następujące polecenie: 
   
    `sudo systemctl status auditd`<br>
    - Oczekiwana odpowiedź:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Wdrażanie przy użyciu Azure Portal

1. W Azure Portal Otwórz **witrynę Marketplace**.

1. Wybierz pozycję **Internet rzeczy**, a następnie wyszukaj pozycję **Azure Security Center for IoT** i wybierz ją.

   ![Wybierz Azure Security Center dla IoT](media/howto/edge-onboarding-8.png)

1. Kliknij przycisk **Utwórz** , aby skonfigurować wdrożenie. 

1. Wybierz subskrypcję platformy Azure IoT Hub, a następnie wybierz **IoT Hub**.<br>Wybierz pozycję **Wdróż na urządzeniu** przeznaczonym dla jednego urządzenia lub wybierz pozycję **Wdróż w odpowiedniej skali** , aby wybrać miejsce docelowe wielu urządzeń, a następnie kliknij pozycję **Utwórz**. Aby uzyskać więcej informacji na temat wdrażania w skali, zobacz artykuł [jak wdrożyć](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >W przypadku wybrania **wdrożenia w odpowiedniej skali**należy dodać nazwę i szczegóły urządzenia przed przejściem do karty **Dodawanie modułów** w poniższych instrukcjach.     

Istnieją trzy kroki umożliwiające utworzenie wdrożenia IoT Edge dla Azure Security Center dla IoT. Poniższe sekcje przeprowadzą za pośrednictwem każdej z nich. 

#### <a name="step-1-add-modules"></a>Krok 1: Dodaj moduły

1. Na karcie **Dodawanie modułów** w obszarze **moduły wdrożenia** kliknij pozycję **AzureSecurityCenterforIoT**. 
   
1. Zmień **nazwę** na **azureiotsecurity**.
1. Zmień **Identyfikator URI obrazu** na **MCR.Microsoft.com/ascforiot/azureiotsecurity:1.0.0**.
1. Sprawdź, czy **opcja tworzenia kontenera** ma ustawioną wartość:      
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
1. Upewnij się, że wybrano **odpowiednie właściwości zestawu splotu modułu** i Zmień obiekt konfiguracji na:
      
    ``` json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
          } 
        }
    ```

1. Kliknij pozycję **Zapisz**.
1. Przewiń w dół karty i wybierz pozycję **Konfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**. 
   
1. Zmień **obraz** pod **węzłem Edge Hub** na **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC2**.

1. Sprawdź, czy **Opcje tworzenia** są ustawione na: 
         
    ``` json
    { 
    "HostConfig":{
                    "PortBindings":{
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

1. Na karcie **Określanie tras** upewnij się, że masz trasę (jawną lub niejawną), która przekaże komunikaty z modułu **azureiotsecurity** do **$upstream**. 
1. Kliknij przycisk **Dalej**.

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

#### <a name="step-3-review-deployment"></a>Krok 3: Przegląd wdrożenia

- Na karcie **Przegląd wdrożenia** Przejrzyj informacje dotyczące wdrożenia, a następnie wybierz pozycję **Prześlij** , aby zakończyć wdrażanie.

## <a name="diagnostic-steps"></a>Kroki diagnostyczne

Jeśli wystąpi problem, dzienniki kontenerów są najlepszym sposobem poznania stanu urządzenia modułu zabezpieczeń IoT Edge. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery są zainstalowane i działają zgodnie z oczekiwaniami

1. Uruchom następujące polecenie na urządzeniu IoT Edge:
    
    `sudo docker ps`
   
1. Sprawdź, czy są uruchomione następujące kontenery:
   
   | Name | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.0 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.9-rc2 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Jeśli nie ma minimalnych wymaganych kontenerów, sprawdź, czy IoT Edge manifest wdrożenia jest wyrównany z zalecanymi ustawieniami. Aby uzyskać więcej informacji, zobacz [wdrażanie modułu IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Inspekcja dzienników modułów pod kątem błędów
   
1. Uruchom następujące polecenie na urządzeniu IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Aby uzyskać pełne dzienniki, Dodaj następującą zmienną środowiskową do wdrożenia modułu **azureiotsecurity** : `logLevel=Debug`.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika krok po kroku dotyczący konfiguracji modułu. 
> [!div class="nextstepaction"]
> [Przewodnik po konfiguracji modułu](./how-to-agent-configuration.md)
