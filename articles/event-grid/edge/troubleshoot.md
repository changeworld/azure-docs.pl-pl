---
title: Rozwiązywanie problemów — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów w siatce zdarzeń w umyw ioT edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100154"
---
# <a name="common-issues"></a>Typowe problemy

Jeśli występują problemy z używaniem usługi Azure Event Grid w usłudze IoT Edge w twoim środowisku, użyj tego artykułu jako przewodnika do rozwiązywania problemów i rozwiązywania problemów.

## <a name="view-event-grid-module-logs"></a>Wyświetlanie dzienników modułów siatki zdarzeń

Aby rozwiązać problem, może być konieczne dostęp do dzienników modułów usługi Event Grid. Aby to zrobić, na maszynie Wirtualnej, na której moduł jest wdrażany, uruchom następujące polecenie:

W systemie Windows

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Na Linuksie,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nie można wykonać żądań HTTPS

* Najpierw upewnij się, że moduł siatki zdarzeń ma **przychodzących:serverAuth:tlsPolicy** **ustawiona** na **ścisłe**lub włączone .

* Jeśli jego komunikacji moduł do modułu, upewnij się, że dokonujesz wywołania na porcie **4438** i nazwa modułu pasuje do tego, co jest wdrażane. 

  Na przykład, jeśli moduł Event Grid został wdrożony z nazwą **eventgridmodule,** adres URL powinien być **https://eventgridmodule:4438**. Upewnij się, że obudowa i numer portu są poprawne.
    
* Jeśli pochodzi z modułu nienawiązanego do IoT, upewnij się, że port siatki zdarzeń jest mapowany na komputerze hosta podczas wdrażania, na przykład,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Nie można wykonać żądań HTTP

* Najpierw upewnij się, że moduł siatki zdarzeń ma **przychodzących:serverAuth:tlsPolicy** **ustawiona** na włączone lub **wyłączone**.

* Jeśli jego komunikacji moduł do modułu, upewnij się, że dokonujesz wywołania na porcie **5888** i nazwa modułu pasuje do tego, co jest wdrażane. 

  Na przykład, jeśli moduł Event Grid został wdrożony z nazwą **eventgridmodule,** adres URL powinien być **http://eventgridmodule:5888**. Upewnij się, że obudowa i numer portu są poprawne.
    
* Jeśli pochodzi z modułu nienawiązanego do IoT, upewnij się, że port siatki zdarzeń jest mapowany na komputerze hosta podczas wdrażania, na przykład,

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>Łańcuch certyfikatów został wydany przez urząd, który nie jest zaufany

Domyślnie moduł Siatki zdarzeń jest skonfigurowany do uwierzytelniania klientów z certyfikatem wystawionym przez demon zabezpieczeń IoT Edge. Upewnij się, że klient przedstawia certyfikat, który jest zakorzeniony w tym łańcuchu.

Klasa **IoTSecurity** w [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) pokazuje, jak pobrać certyfikaty z demona IoT Edge Security i używać go do konfigurowania połączeń wychodzących.

Jeśli jest to środowisko nieprodukcyjne, można wyłączyć uwierzytelnianie klienta. Szczegółowe informacje na ten temat można znaleźć w stronie o [zabezpieczeniach i uwierzytelnianiu.](security-authentication.md)

## <a name="debug-events-not-received-by-subscriber"></a>Zdarzenia debugowania nie odebrane przez subskrybenta

Typowe powody to:

* Zdarzenie nigdy nie zostało pomyślnie zaksięgowane. Kod stanu HTTP 200(OK) powinien zostać odebrany podczas księgowania zdarzenia w module siatki zdarzeń.

* Sprawdź subskrypcję wydarzenia, aby zweryfikować:
    * Adres URL punktu końcowego jest prawidłowy
    * Wszelkie filtry w subskrypcji nie powodują zdarzenia do "upuszczenia".

* Sprawdź, czy moduł subskrybenta jest uruchomiony

* Zaloguj się do maszyny Wirtualnej, gdzie moduł siatki zdarzeń jest wdrażany i wyświetlić jego dzienniki.

* Włącz rejestrowanie na dostarczanie, ustawiając **broker:logDeliverySuccess=true** i ponownie rozmieszczając moduł siatki zdarzeń i ponowiając próbę żądania. Włączenie rejestrowania na dostawę może mieć wpływ na przepływność i opóźnienie, więc po zakończeniu debugowania nasze zalecenie jest, aby włączyć to z powrotem do **brokera:logDeliverySuccess=false** i ponownego rozmieszczenia modułu siatki zdarzeń.

* Włącz metryki, ustawiając **metryki:reportertype=console** i ponownie wdrożyć moduł siatki zdarzeń. Wszelkie operacje po tym spowoduje metryki są rejestrowane na konsoli modułu siatki zdarzeń, które mogą służyć do dalszego debugowania. Naszym zaleceniem jest włączenie metryk tylko do debugowania i po zakończeniu, aby je wyłączyć, ustawiając **metryki:reportertype=none** i ponownie rozmieszczając moduł siatki zdarzeń.

## <a name="next-steps"></a>Następne kroki

Zgłoś wszelkie problemy, sugestie dotyczące korzystania [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)z siatki zdarzeń w uwolniku IoT edge pod adresem .