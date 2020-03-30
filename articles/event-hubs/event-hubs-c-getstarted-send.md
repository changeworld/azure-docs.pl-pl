---
title: 'Szybki start: wysyłanie zdarzeń przy użyciu c - Usługi Azure Event Hubs'
description: 'Szybki start: Ten artykuł zawiera przewodnik dotyczący tworzenia aplikacji języka C, która wysyła zdarzenia do usługi Azure Event Hubs.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720664"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Szybki start: wysyłanie zdarzeń do centrów zdarzeń platformy Azure przy użyciu języka C

## <a name="introduction"></a>Wprowadzenie
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do centrum zdarzeń przy użyciu aplikacji konsoli w języku C. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko programistyczne C. W tym samouczku przyjęto założenie stosu gcc na maszynie wirtualnej systemu Azure Linux z Ubuntu 14.04.
* [Program Microsoft Visual Studio](https://www.visualstudio.com/).
* **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Użyj [witryny Azure Portal,](https://portal.azure.com) aby utworzyć obszar nazw typu Usługi zdarzeń i uzyskać poświadczenia zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Pobierz wartość klucza dostępu dla Centrum zdarzeń, postępując zgodnie z instrukcjami z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu w kodzie, który piszesz w dalszej części tego samouczka. Domyślna nazwa klucza to: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Pisanie kodu do wysyłania wiadomości do Centrów zdarzeń
W tej sekcji pokazano, jak napisać aplikację C, aby wysłać zdarzenia do centrum zdarzeń. Kod używa biblioteki Proton AMQP z [projektu Apache Qpid](https://qpid.apache.org/). Jest to analogiczne do używania kolejek i tematów usługi Service Bus z amqp z C, jak pokazano [w tym przykładzie](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Aby uzyskać więcej informacji, zobacz [dokumentację Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Na [stronie Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html)postępuj zgodnie z instrukcjami, aby zainstalować Qpid Proton, w zależności od środowiska.
2. Aby skompilować bibliotekę Proton, zainstaluj następujące pakiety:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Pobierz [bibliotekę Qpid Proton](https://qpid.apache.org/proton/index.html)i wypakuj ją, np.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Tworzenie katalogu kompilacji, kompilowanie i instalowanie:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. W katalogu roboczym utwórz nowy plik o nazwie **sender.c** z następującym kodem. Pamiętaj, aby zastąpić wartości klucza/nazwy sygnatury dostępu Współdzielonego, nazwy centrum zdarzeń i obszaru nazw. Należy również zastąpić zakodowaną w adresie URL wersję klucza dla **SendRule** utworzonej wcześniej. Możesz zakodować go w tym [miejscu.](https://www.w3schools.com/tags/ref_urlencode.asp)
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Skompiluj plik, zakładając **gcc:**
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ten kod używa wychodzącego okna 1, aby wymusić wiadomości tak szybko, jak to możliwe. Zaleca się, że aplikacja spróbuj wsadowych komunikatów w celu zwiększenia przepływności. Zobacz [stronę Qpid AMQP Messenger,](https://qpid.apache.org/proton/messenger.html) aby uzyskać informacje o tym, jak korzystać z biblioteki Qpid Proton w tym i innych środowiskach oraz na platformach, dla których są dostarczane powiązania (obecnie Perl, PHP, Python i Ruby).

Uruchom aplikację, aby wysyłać wiadomości do centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
