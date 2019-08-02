---
title: Wdróż Azure Security Center dla modułu IoT Edge (wersja zapoznawcza) | Microsoft Docs
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
ms.openlocfilehash: 7171923e4badb3355a64b63515d40e73fadca6b0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596360"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Wdrażanie modułu zabezpieczeń na urządzeniu IoT Edge

> [!IMPORTANT]
> Azure Security Center do obsługi urządzeń IoT Edge IoT jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center dla modułu IoT** to kompleksowe rozwiązanie zabezpieczeń dla IoT Edge urządzeń.
Moduł zabezpieczeń zbiera, agreguje i analizuje pierwotne dane zabezpieczeń z systemu operacyjnego i systemu kontenerów w celu podejmowania działań i alertów dotyczących zabezpieczeń.
Aby dowiedzieć się więcej, zobacz [moduł zabezpieczeń dla IoT Edge](security-edge-architecture.md).

W tym artykule dowiesz się, jak wdrożyć moduł zabezpieczeń na urządzeniu IoT Edge.

## <a name="deploy-security-module"></a>Wdróż moduł zabezpieczeń

Wykonaj następujące kroki, aby wdrożyć Azure Security Center dla modułu zabezpieczeń IoT dla IoT Edge.

### <a name="prerequisites"></a>Wymagania wstępne

- W IoT Hub upewnij się, że urządzenie jest [zarejestrowane jako urządzenie IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Azure Security Center modułu IoT Edge wymaga zainstalowania na urządzeniu IoT Edge [platformy](https://linux.die.net/man/8/auditd) z inspekcją.

    - Zainstaluj platformę, uruchamiając następujące polecenie na urządzeniu IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Sprawdź, czy inspekcja jest aktywna, uruchamiając następujące polecenie:
   
      `sudo systemctl status auditd`
      
        Oczekiwana odpowiedź to `active (running)`. 

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
1. Zmień **Identyfikator URI obrazu** na **MCR.Microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
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
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Kliknij polecenie **Zapisz**.
1. Przewiń w dół karty i wybierz pozycję **Konfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**.
   
   
1. Zmień **obraz** pod **węzłem Edge Hub** na **MCR.Microsoft.com/ascforiot/edgehub:1.0.9-Preview**.

   >[!Note]
   > Azure Security Center dla modułu IoT wymaga przetworzenia rozwidlenia IoT Edge centrum w oparciu o zestaw SDK 1,20.
   > Zmieniając obraz Centrum IoT Edge, nakazujesz IoT Edge urządzeniu zamienić najnowszą stabilną wersję za pomocą rozwidlenia systemu IoT Edge Hub, która nie jest oficjalnie obsługiwana przez usługę IoT Edge.

1. Sprawdź, czy **Opcje tworzenia** są ustawione na: 
         
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
      
1. Kliknij polecenie **Zapisz**.
   
1. Kliknij przycisk **Dalej**.

#### <a name="step-2-specify-routes"></a>Krok 2: Określanie tras 

1. Na karcie **Określanie tras** upewnij się, że masz trasę (jawną lub niejawną), która przekaże komunikaty z modułu **azureiotsecurity** do **$upstream**. 
1. Kliknij przycisk **Dalej**.

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream
    ~~~

#### <a name="step-3-review-deployment"></a>Krok 3: Przejrzyj wdrożenie

- Na karcie **Przegląd wdrożenia** Przejrzyj informacje dotyczące wdrożenia, a następnie wybierz pozycję **Prześlij** , aby zakończyć wdrażanie.

## <a name="diagnostic-steps"></a>Kroki diagnostyczne

Jeśli wystąpi problem, dzienniki kontenerów są najlepszym sposobem poznania stanu urządzenia modułu zabezpieczeń IoT Edge. Użyj poleceń i narzędzi w tej sekcji, aby zebrać informacje.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Sprawdź, czy wymagane kontenery są zainstalowane i działają zgodnie z oczekiwaniami

1. Uruchom następujące polecenie na urządzeniu IoT Edge:
    
     `sudo docker ps`
   
1. Sprawdź, czy są uruchomione następujące kontenery:
   
   | Name (Nazwa) | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
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
