---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka C | Dokumentacja firmy Microsoft
description: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka C
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
ms.date: 12/4/2017
ms.author: shvija
ms.openlocfilehash: 25da8255af6a23a4f01db5a1ec4f1ddcd2eeb1bb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002461"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka C

## <a name="introduction"></a>Wprowadzenie
Event Hubs to wysoce skalowalny system przyjmowania danych może obsługiwać miliony zdarzeń na sekundę, umożliwiając aplikacji przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Po zebraniu danych do Centrum zdarzeń, można przekształcić i przechowywanie danych przy użyciu dowolnego dostawcy analiz w czasie rzeczywistym lub klastra magazynu.

Aby uzyskać więcej informacji, zobacz [Przegląd usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń za pomocą aplikacji konsoli w C. Aby uzyskać informacje dotyczące odbierania zdarzeń, kliknij odpowiedni język odbierający w spisie treści po lewej stronie.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* To środowisko projektowe języka C. Ten samouczek zakłada stosu kompilatorów GCC dla procesorów na maszynie Wirtualnej systemu Linux platformy Azure z systemem Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Aktywne konto platformy Azure. Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Wysyłanie komunikatów do usługi Event Hubs
W tej sekcji przedstawiono sposób pisania aplikacji w języku C do wysyłania zdarzeń do Centrum zdarzeń. Kod używa biblioteki AMQP protonowego [projektu Apache Qpid](http://qpid.apache.org/). To jest odpowiednikiem pomocą tematów i kolejek usługi Service Bus z obsługą protokołu AMQP z C, co zostało pokazane [w tym przykładzie](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Aby uzyskać więcej informacji, zobacz [dokumentacji protonów Qpid](http://qpid.apache.org/proton/index.html).

1. Z [strony Qpid AMQP Messenger](https://qpid.apache.org/proton/messenger.html), postępuj zgodnie z instrukcjami, aby zainstalować protonów Qpid, w zależności od środowiska.
2. Aby skompilować biblioteki protonów, zainstaluj następujące pakiety:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Pobierz [biblioteki protonowego Qpid](http://qpid.apache.org/proton/index.html)i wyodrębnij go, np.:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
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
5. W katalogu roboczego, Utwórz nowy plik o nazwie **sender.c** następującym kodem. Pamiętaj, aby zastąpić wartości Nazwa/klucza sygnatury dostępu Współdzielonego, nazwa Centrum zdarzeń i przestrzeni nazw. Należy również zastąpić zakodowane w adresie URL wersję klucza **SendRule** utworzone wcześniej. Kodowanie adresu URL można ją [tutaj](http://www.w3schools.com/tags/ref_urlencode.asp).
   
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


## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
