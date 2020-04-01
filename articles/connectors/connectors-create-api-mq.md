---
title: Łączenie się z serwerem IBM MQ
description: Wysyłanie i pobieranie wiadomości za pomocą platformy Azure lub lokalnego serwera IBM MQ i aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410240"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Łączenie się z serwerem IBM MQ z usługi Azure Logic Apps

Łącznik IBM MQ wysyła i pobiera wiadomości przechowywane na serwerze IBM MQ lokalnie lub na platformie Azure. Ten łącznik zawiera klienta MQ firmy Microsoft, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. W tym artykule przedstawiono przewodnik startowy dotyczący używania złącza MQ. Możesz rozpocząć od przeglądania pojedynczej wiadomości w kolejce, a następnie wypróbować inne akcje.

Złącze IBM MQ zawiera następujące działania, ale nie zapewnia wyzwalaczy:

- Przeglądaj pojedynczą wiadomość bez usuwania wiadomości z serwera IBM MQ.
- Przeglądaj partię wiadomości bez usuwania wiadomości z serwera IBM MQ.
- Odbierz pojedynczą wiadomość i usuń wiadomość z serwera IBM MQ.
- Odbierz partię wiadomości i usuń wiadomości z serwera IBM MQ.
- Wyślij pojedynczą wiadomość do serwera IBM MQ.

Oto oficjalnie obsługiwane wersje IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli używasz lokalnego serwera MQ, [zainstaluj lokalną bramę danych na](../logic-apps/logic-apps-gateway-install.md) serwerze w sieci. Serwer, na którym zainstalowana jest lokalna brama danych, musi również mieć zainstalowany program .NET Framework 4.6, aby łącznik MQ działał.

  Po zakończeniu instalowania bramy należy również utworzyć zasób na platformie Azure dla lokalnej bramy danych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia bramy danych](../logic-apps/logic-apps-gateway-connection.md).

  Jeśli serwer MQ jest publicznie dostępny lub dostępny na platformie Azure, nie trzeba używać bramy danych.

* Aplikacja logiki, w której chcesz dodać akcję MQ. Ta aplikacja logiki musi używać tej samej lokalizacji co lokalne połączenie bramy danych i musi już mieć wyzwalacz, który uruchamia przepływ pracy.

  Łącznik MQ nie ma żadnych wyzwalaczy, więc należy najpierw dodać wyzwalacz do aplikacji logiki. Na przykład można użyć wyzwalacza cyklu. Jeśli jesteś nowy w aplikacjach logiki, spróbuj tego [przewodnika Szybki start, aby utworzyć pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Tworzenie połączenia MQ

Jeśli podczas dodawania akcji MQ nie masz jeszcze połączenia MQ, zostanie wyświetlony monit o utworzenie połączenia, na przykład:

![Podaj informacje o połączeniu](media/connectors-create-api-mq/connection-properties.png)

1. Jeśli łączysz się z lokalnym serwerem MQ, wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych.**

1. Podaj informacje o połączeniu serwera MQ.

   * W przypadku **programu Server**można wprowadzić nazwę serwera MQ lub wprowadzić adres IP, po którym następuje dwukropek i numer portu.

   * Aby użyć warstwy SSL (Secure Sockets Layer), wybierz **opcję Włącz SSL?**.

     Łącznik MQ obsługuje obecnie tylko uwierzytelnianie serwera, a nie uwierzytelnianie klienta. Aby uzyskać więcej informacji, zobacz [Problemy z połączeniem i uwierzytelnianiem](#connection-problems).

1. W sekcji **bramy** wykonaj następujące kroki:

   1. Z listy **Subskrypcja** wybierz subskrypcję platformy Azure skojarzoną z zasobem bramy platformy Azure.

   1. Z listy **Brama połączenia** wybierz zasób bramy platformy Azure, którego chcesz użyć.

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemy z połączeniem i uwierzytelnianiem

Gdy aplikacja logiki próbuje połączyć się z lokalnym serwerem MQ, może pojawić się ten błąd:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Jeśli używasz łącznika MQ bezpośrednio na platformie Azure, serwer MQ musi użyć certyfikatu wystawionego przez zaufany [urząd certyfikacji.](https://www.ssl.com/faqs/what-is-a-certificate-authority/)

* Jeśli używasz lokalnej bramy danych, spróbuj użyć certyfikatu wystawionego przez zaufany [urząd certyfikacji,](https://www.ssl.com/faqs/what-is-a-certificate-authority/) jeśli to możliwe. Jeśli jednak ta opcja nie jest możliwa, można użyć certyfikatu z podpisem własnym, który nie jest wystawiany przez zaufany [urząd certyfikacji](https://www.ssl.com/faqs/what-is-a-certificate-authority/) i jest uważany za mniej bezpieczny.

  Aby zainstalować certyfikat z podpisem własnym serwera, można użyć narzędzia **Menedżer certyfikacji systemu Windows** (certmgr.msc). W tym scenariuszu na komputerze lokalnym, na którym jest uruchomiona lokalna usługa bramy danych, należy zainstalować certyfikat w magazynie certyfikatów **komputera lokalnego** na poziomie **zaufanych głównych urzędów certyfikacji.**

  1. Na komputerze, na którym jest uruchomiona usługa bramy danych lokalnych, otwórz menu Start, znajdź i wybierz pozycję **Zarządzaj certyfikatami użytkowników**.

  1. Po otwarciu narzędzia Menedżer certyfikacji systemu Windows przejdź do folderu **Certyfikaty — Zaufane** >  **główne urzędy certyfikacji** komputera lokalnego i zainstaluj certyfikat.

     > [!IMPORTANT]
     > Upewnij się, że certyfikat został zainstalowany w magazynie Certyfikaty — > **Zaufane główne urzędy certyfikacji** **komputera lokalnego.**

* Serwer MQ wymaga zdefiniowania specyfikacji szyfrowania, której chcesz używać dla połączeń SSL. Jednak SsLStream w .NET nie pozwala określić kolejność szyfrowania specyfikacji. Aby obejść to ograniczenie, można zmienić konfigurację serwera MQ, aby dopasować się do pierwszej specyfikacji szyfrowania w pakiecie, który łącznik wysyła w negocjacjach SSL.

  Podczas próby połączenia serwer MQ rejestruje komunikat o zdarzeniu, który wskazuje, że połączenie nie powiodło się, ponieważ drugi koniec użył niepoprawnej specyfikacji szyfrowania. Komunikat o zdarzeniu zawiera specyfikację szyfrowania, która pojawia się jako pierwsza na liście. Zaktualizuj specyfikację szyfrowania w konfiguracji kanału, aby dopasować go do specyfikacji szyfrowania w komunikacie o zdarzeniu.

## <a name="browse-single-message"></a>Przeglądanie pojedynczej wiadomości

1. W aplikacji logiki w obszarze wyzwalacza lub innej akcji wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania `mq`wprowadź i wybierz akcję **Przeglądaj wiadomość.**

   ![Wybierz akcję "Przeglądaj wiadomość"](media/connectors-create-api-mq/browse-message.png)

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection).

1. Po utworzeniu połączenia skonfiguruj właściwości akcji **Przeglądaj wiadomość:**

   | Właściwość | Opis |
   |----------|-------------|
   | **Kolejka** | Jeśli różni się od kolejki określonej w połączeniu, określ tę kolejkę. |
   | **Identyfikator wiadomości**, **identyfikator korelacji,** **identyfikator grupy**i inne właściwości | Przeglądaj w poszukiwaniu wiadomości opartej na różnych właściwościach wiadomości MQ |
   | **IncludeInfo (Uwzględnij)** | Aby dołączyć dodatkowe informacje o wiadomości do danych wyjściowych, **wybierz**true . Aby pominąć dodatkowe informacje o wiadomościach w danych wyjściowych, wybierz **false**. |
   | **Limit czasu** | Wprowadź wartość, aby określić, jak długo czekać na wiadomość do dotrzeć do pustej kolejki. Jeśli nic nie zostanie wprowadzone, pierwsza wiadomość w kolejce jest pobierana i nie ma czasu spędzonego na oczekiwanie na wyświetlenie wiadomości. |
   |||

   Przykład:

   ![Właściwości akcji "Przeglądaj wiadomość"](media/connectors-create-api-mq/browse-message-properties.png)

1. Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby przetestować aplikację, wybierz pozycję **Uruchom**.

   Po zakończeniu uruchamiania projektant pokazuje kroki przepływu pracy i ich stan, dzięki czemu można przejrzeć dane wyjściowe.

1. Aby wyświetlić szczegóły dotyczące każdego kroku, kliknij pasek tytułu kroku. Aby przejrzeć więcej informacji o danych wyjściowych kroku, wybierz opcję **Pokaż surowe dane wyjściowe**.

   ![Przeglądanie danych wyjściowych wiadomości](media/connectors-create-api-mq/browse-message-output.png)

   Oto kilka przykładowych surowych danych wyjściowych:

   ![Przeglądanie nieprzetworzonego wydruku wiadomości](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Jeśli zostanie **true** **ustawiona wartość TrueInfo,** wyświetlane są dodatkowe dane wyjściowe:

   ![Przeglądaj wiadomość zawiera informacje](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Przeglądanie wielu wiadomości

Akcja **Przeglądaj wiadomości** zawiera opcję **BatchSize,** aby wskazać, ile wiadomości ma powrócić z kolejki. Jeśli **BatchSize** nie ma wartości, wszystkie wiadomości są zwracane. Zwrócone dane wyjściowe to tablica komunikatów.

1. Wykonaj poprzednie kroki, ale zamiast tego dodaj akcję **Przeglądaj wiadomości.**

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection). W przeciwnym razie domyślnie używane jest pierwsze wcześniej skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Możesz też wybrać inne połączenie.

1. Podaj informacje dotyczące akcji.

1. Zapisz i uruchom aplikację logiki.

   Po zakończeniu działania aplikacji logiki, oto kilka przykładowych danych wyjściowych z akcji **Przeglądaj wiadomości:**

   ![Przykładowe dane wyjściowe "Przeglądaj wiadomości"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Odbieranie pojedynczej wiadomości

Akcja **Odbieraj komunikat** ma takie same dane wejściowe i wyjściowe jak akcja **Przeglądaj wiadomość.** Podczas korzystania z **receive wiadomości,** wiadomość jest usuwana z kolejki.

## <a name="receive-multiple-messages"></a>Odbieranie wielu wiadomości

Akcja **Odbieraj wiadomości** ma takie same dane wejściowe i wyjściowe jak akcja **Przeglądaj wiadomości.** Podczas korzystania z **receive wiadomości**, wiadomości są usuwane z kolejki.

> [!NOTE]
> Podczas uruchamiania akcji przeglądania lub odbierania w kolejce, która nie ma żadnych komunikatów, akcja kończy się niepowodzeniem z tym wyjściem:
>
> ![MQ "brak wiadomości" błąd](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Wyślij wiadomość

1. Wykonaj poprzednie kroki, ale zamiast tego dodaj akcję **Wyślij wiadomość.**

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection). W przeciwnym razie domyślnie używane jest pierwsze wcześniej skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Możesz też wybrać inne połączenie.

1. Podaj informacje dotyczące akcji. W przypadku **typu wiadomości**wybierz prawidłowy typ wiadomości: **Datagram,** **Odpowiedz**lub **Poproś**

   ![Właściwości "Wyślij akcję wiadomości"](media/connectors-create-api-mq/send-message-properties.png)

1. Zapisz i uruchom aplikację logiki.

   Po zakończeniu działania aplikacji logiki, oto kilka przykładowych danych wyjściowych z akcji **Wyślij wiadomość:**

   ![Przykładowe wyjście "Wyślij wiadomość"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat działań i limitów, które są opisane w opisie Swagger łącznika, przejrzyj [stronę referencyjną](/connectors/mq/)łącznika .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
