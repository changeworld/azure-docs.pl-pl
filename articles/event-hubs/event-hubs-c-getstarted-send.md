---
title: Wysyłanie zdarzeń za pomocą języka C - usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji języka C, która wysyła zdarzenia do usługi Azure Event Hubs.
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
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a8f647018ba6ed3c9e951db2054036b60c7d4ab5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822474"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka C

## <a name="introduction"></a>Wprowadzenie
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń za pomocą aplikacji konsoli w C. 

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* To środowisko projektowe języka C. Ten samouczek zakłada stosu kompilatorów GCC dla procesorów na maszynie Wirtualnej systemu Linux platformy Azure z systemem Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń**. Użyj [witryny Azure portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z Centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Pobierz wartość klucza dostępu Centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w artykule: [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu został użyty w kodzie, napisany w dalszej części tego samouczka. Jest domyślna nazwa klucza: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Pisanie kodu, aby wysyłać komunikaty do usługi Event Hubs
W tej sekcji przedstawiono sposób pisania aplikacji w języku C do wysyłania zdarzeń do Centrum zdarzeń. Kod używa biblioteki AMQP protonowego [projektu Apache Qpid](https://qpid.apache.org/). To jest odpowiednikiem pomocą tematów i kolejek usługi Service Bus z obsługą protokołu AMQP z C, co zostało pokazane [w tym przykładzie](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Aby uzyskać więcej informacji, zobacz [dokumentacji protonów Qpid](https://qpid.apache.org/proton/index.html).

1. Z [strony Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), postępuj zgodnie z instrukcjami, aby zainstalować protonów Qpid, w zależności od środowiska.
2. Aby skompilować biblioteki protonów, zainstaluj następujące pakiety:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Pobierz [biblioteki protonowego Qpid](https://qpid.apache.org/proton/index.html)i wyodrębnij go, np.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Utwórz katalog kompilacji, kompilacji i instalacji:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. W katalogu roboczego, Utwórz nowy plik o nazwie **sender.c** następującym kodem. Pamiętaj, aby zastąpić wartości Nazwa/klucza sygnatury dostępu Współdzielonego, nazwa Centrum zdarzeń i przestrzeni nazw. Należy również zastąpić zakodowane w adresie URL wersję klucza **SendRule** utworzone wcześniej. Kodowanie adresu URL można ją [tutaj](https://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. Skompiluj plik, zakładając, że **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ten kod używa wychodzących okna 1, aby wymusić wiadomości się jak najszybciej. Zalecane jest, spróbuj aplikacji do komunikatów usługi batch w celu zwiększenia przepływności. Zobacz [strony Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html) informacji o sposobie używania biblioteki protonów Qpid, w tym i innych środowisk, a także na platformach, w których znajdują się powiązania (obecnie Perl, PHP, Python i Ruby).

Uruchamianie aplikacji w celu wysyłania komunikatów do Centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologią dotyczącą usługi Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
