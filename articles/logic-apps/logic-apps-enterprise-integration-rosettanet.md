---
title: Komunikaty RosettaNet dla integracji dla przedsiębiorstw B2B — Azure Logic Apps
description: Wymiana komunikatów RosettaNet w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333065"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>RosettaNet komunikatów B2B integracji dla przedsiębiorstw w usłudze Azure Logic Apps 

[RosettaNet](https://resources.gs1us.org) to niedochodowa konsorcjum, która została ustanowiona standardowych procesów na potrzeby udostępniania informacji biznesowych. Standardy są często używane do procesów łańcuch dostaw i są powszechne w branżach półprzewodnikowych, electronics i logistycznych. Konsorcjum RosettaNet tworzy i obsługuje procesy interfejsu partnera (pestek), która zawiera typowe definicje procesów biznesowych, dla wszystkich wymianę komunikatów RosettaNet. RosettaNet jest oparte na języku XML i definiuje komunikat wytycznych, interfejsy dla procesów biznesowych i implementacja struktury do komunikacji między firmami.

W [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), łącznik RosettaNet pomaga w tworzeniu rozwiązań do integracji, które obsługują RosettaNet standardy. Łącznik jest oparty na RosettaNet Framework implementacji (RNIF) w wersji 2.0.01. RNIF to platforma aplikacji otwórz sieci, która umożliwia partnerom biznesowym wspólnie Uruchom pestek RosettaNet. Ta struktura definiuje strukturę wiadomości, konieczność potwierdzenia, kodowanie protokołu dynamicznej konfiguracji hosta (MIME, Multipurpose Internet Mail Extensions) i podpis cyfrowy.

W szczególności łącznik spełnia te wymagania:

* Zakoduj lub odbieranie komunikatów RosettaNet.
* Dekodowanie komunikatów lub wysyłać komunikaty RosettaNet.
* Oczekiwanie na odpowiedź i generowania powiadomień awarii.

Tych funkcji łącznik obsługuje wszystkie pestek, które są definiowane przez RNIF 2.0.01. Komunikacja z partnerem, może być synchroniczna lub asynchroniczna.

## <a name="rosettanet-concepts"></a>Pojęcia dotyczące RosettaNet

Poniżej przedstawiono kilka pojęć i terminów, są unikatowe dla specyfikacji RosettaNet, które są ważne podczas kompilowania na podstawie RosettaNet integracji:

* **PIP**

  Organizacja RosettaNet tworzy i obsługuje procesy interfejsu partnera (pestek), która zawiera typowe definicje procesów biznesowych, dla wszystkich wymianę komunikatów RosettaNet. Każda specyfikacja PIP zawiera plik definicji (DTD) typu dokumentu i dokument wytyczne wiadomości. Plik definicji DTD definiuje strukturę zawartości usługi wiadomości. Dokument wskazówek dotyczących komunikatów, czyli czytelny dla człowieka plik HTML, określa ograniczenia na poziomie elementu. Razem te pliki zawierają kompletną definicję procesu biznesowego.

   Pestek są pogrupowane według funkcji biznesowych wysokiego poziomu, lub klastra i podfunkcji lub segmentu. Na przykład "3A4" jest publiczny adres IP dla zamówienia zakupu, podczas "3" jest funkcją, Zarządzanie zamówieniami i "3A" jest podfunkcji oferty & wpisu zamówienia. Aby uzyskać więcej informacji, zobacz [witryny RosettaNet](https://resources.gs1us.org).

* **Akcja**

  Część PIP, komunikaty akcji są komunikaty biznesowych, które są wymieniane między partnerami.

* **Sygnał**

   Część PIP, komunikaty sygnału są potwierdzenia, które są wysyłane w odpowiedzi na komunikaty akcji.

* **Podwójne akcji i jedną akcję**

  Dla PIP jedną akcję tylko odpowiedź jest wiadomości potwierdzającej sygnału. W przypadku PIP akcji podwójnej precyzji inicjator odbiera komunikat odpowiedzi i odpowiada za pomocą potwierdzenia oprócz przepływ komunikatów pojedynczej akcji.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji muszą być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) , są zdefiniowane w ramach konta integracji i skonfigurowano kwalifikator "DUNS" w obszarze **tożsamości biznesowych**

* Narzędzie PIP konfiguracji procesu, który jest wymagany do wysyłania i odbierania wiadomości RosettaNet na koncie integracji. Konfiguracja procesu przechowuje wszystkie właściwości konfiguracji narzędzia PIP. Ta konfiguracja może się wtedy odwoływać podczas tworzenia umowy z partnerem. Aby utworzyć konfigurację procesu PIP na koncie integracji, zobacz [konfiguracji procesu dodawania PIP](#add-pip).

* Opcjonalnie [certyfikaty](../logic-apps/logic-apps-enterprise-integration-certificates.md) szyfrowania, odszyfrowywania i podpisywania wiadomości, które można przekazać do konta integracji. Certyfikaty są wymagane tylko wtedy, gdy jesteś, użyj podpisywania lub szyfrowania.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Dodaj konfigurację procesu PIP

Aby dodać konfigurację procesu PIP z kontem integracji, wykonaj następujące kroki:

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i Otwórz swoje konto integracji.

1. Na **Przegląd** okienku wybierz **RosettaNet PIP** kafelka.

   ![Wybierz Kafelek RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. W obszarze **RosettaNet PIP**, wybierz **Dodaj**. Podaj informacje dotyczące narzędzia PIP.

   ![Dodaj szczegóły RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Twoja nazwa adresu PIP |
   | **Kod PIP** | Tak | Trzy cyfrowy kod PIP. Aby uzyskać więcej informacji, zobacz [pestek RosettaNet](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Wersja narzędzia PIP** | Yes | Numer wersji narzędzia PIP, który jest dostępny w oparciu o wybrany kod PIP |
   ||||

   Aby uzyskać więcej informacji o tych właściwościach narzędzia PIP, odwiedź stronę [RosettaNet witryny sieci Web](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**, która umożliwia utworzenie konfiguracji narzędzia PIP.

1. Aby wyświetlić lub edytować konfigurację procesu, wybierz publiczny adres IP, a następnie wybierz **Edytuj jako dane JSON**.

   Przetwarzanie wszystkich konfiguracji ustawienia pochodzą z specyfikacje PIP. Usługa Logic Apps umożliwia wypełnienie większość ustawień z wartościami domyślnymi, które są najczęściej używane wartości tych właściwości.

   ![Edytuj konfigurację RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Upewnij się, że ustawienia odpowiadają wartościom w specyfikacji odpowiednie narzędzia PIP i potrzeb Twojej firmy. W razie potrzeby zaktualizuj wartości w formacie JSON i Zapisz zmiany.

## <a name="create-rosettanet-agreement"></a>Tworzenie umowy z RosettaNet

1. W [witryny Azure portal](https://portal.azure.com), Znajdź i Otwórz swoje konto integracji, jeśli nie już otwarty.

1. Na **Przegląd** okienku wybierz **umów** kafelka.

   ![Wybierz Kafelek umowy](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. W obszarze **umów**, wybierz **Dodaj**. Podaj szczegóły umowy.

   ![Dodaj szczegóły umowy](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa umowy |
   | **Typ umowy** | Tak | Wybierz **RosettaNet**. |
   | **Host Partner** | Yes | Umowa wymaga partnera hosta i gościa. Partner hosta reprezentuje organizację, która umożliwia skonfigurowanie umowy. |
   | **Tożsamość hosta** | Tak | Identyfikator partnera hosta |
   | **Partner gościa** | Tak | Umowa wymaga partnera hosta i gościa. Partner gościa reprezentuje organizację, która jest prowadzących działalność we współpracy z partnerem hosta. |
   | **Tożsamość gościa** | Yes | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różna | Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez partnera hosta |
   | **Ustawienia wysyłania** | Różna | Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez partnera hosta |  
   | **Odwołania RosettaNet PIP** | Tak | Odwołania narzędzia PIP umowy. Wszystkie komunikaty RosettaNet wymagają zastosowania takiej konfiguracji narzędzia PIP. |
   ||||

1. Aby skonfigurować umowy do odbierania komunikatów przychodzących od partnera gościa, należy wybrać **ustawienia odbierania**.

   ![Ustawienia odbierania](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie dla komunikatów przychodzących w obszarze **wiadomości**, wybierz opcję **komunikat powinien być podpisany** lub **komunikat powinien być zaszyfrowany** odpowiednio.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Komunikat powinien być podpisany** | Nie | Zaloguj się komunikaty przychodzące z wybranego certyfikatu. |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat podpisywania |
      | **Włącz szyfrowanie komunikatów** | Nie | Szyfrowanie wiadomości przychodzących za pomocą wybranego certyfikatu. |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat szyfrowania |
      ||||

   1. W obszarze każdego wyboru, wybierz odpowiednie [certyfikatu](./logic-apps-enterprise-integration-certificates.md), które zostały wcześniej dodane do Twojego konta integracji na potrzeby podpisywania lub szyfrowania.

1. Aby skonfigurować umowy do wysyłania wiadomości do partnera gościa, należy wybrać **ustawienia wysyłania**.

   ![Ustawienia wysyłania](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie dla wiadomości wychodzących, w obszarze **wiadomości**, wybierz opcję **Włącz podpisywanie komunikatów** lub **Włącz szyfrowanie komunikatów** odpowiednio. W obszarze każdego wyboru, wybierz odpowiedniego algorytmu i [certyfikatu](./logic-apps-enterprise-integration-certificates.md), które zostały wcześniej dodane do Twojego konta integracji na potrzeby podpisywania lub szyfrowania.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Włącz podpisywanie komunikatów** | Nie | Zaloguj się komunikaty wychodzące z wybranego algorytm podpisywania i certyfikatem. |
      | **Algorytm podpisywania** | Tak, jeśli podpisywanie jest włączone | Algorytm podpisywania, aby użyć, oparte na wybrany certyfikat |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat podpisywania |
      | **Włącz szyfrowanie komunikatów** | Nie | Szyfrowanie wychodzących za pomocą algorytmu szyfrowania wybranych i certyfikatu. |
      | **Algorytm szyfrowania** | Tak, jeśli szyfrowanie jest włączone | Algorytm szyfrowania używany, oparte na wybrany certyfikat |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat szyfrowania |
      ||||

   1. W obszarze **punktów końcowych**, określ wymagany adres URL używany do wysyłania wiadomości działania i potwierdzenia.

      | Właściwość | Wymagane | Opis |
      |----------|----------|-------------|
      | **Adres URL akcji** |  Yes | Adres URL używany do wysyłania komunikatów akcji. Adres URL jest polem wymaganym dla komunikatów synchroniczne i asynchroniczne. |
      | **Potwierdzenie adresu URL** | Tak | Adres URL używany do wysyłania wiadomości z potwierdzeniem. Adres URL jest polem wymaganym dla wiadomości asynchronicznych. |
      ||||

1. Aby skonfigurować umowy z odwołaniami RosettaNet PIP dla partnerów, wybierz **RosettaNet PIP odwołuje się do**. W obszarze **nazwa PIP**, wybierz nazwę usługi utworzonej wcześniej PIP.

   ![Odwołania narzędzia PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Wybór wypełnia pozostałe właściwości, które są oparte na publiczny adres IP, który został ustawiony na koncie integracji. Jeśli to konieczne, możesz zmienić **roli PIP**.

   ![Wybrane PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po wykonaniu tych kroków, możesz przystąpić do wysyłania i odbierania wiadomości RosettaNet.

## <a name="rosettanet-templates"></a>Szablony RosettaNet

Aby przyspieszyć opracowywanie i zaleca wzorce integracji, można użyć szablony aplikacji logiki dekodowanie i kodowanie RosettaNet wiadomości. Podczas tworzenia aplikacji logiki można wybrać w galerii szablonów w Projektancie aplikacji logiki. Możesz również znaleźć tych szablonów w [repozytorium GitHub dla usługi Azure Logic Apps](https://github.com/Azure/logicapps).

![Szablony RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Odbieranie i dekodowania RosettaNet wiadomości

1. [Tworzenie pustej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) do aplikacji logiki.

1. Zanim będzie można dodać akcję do dekodowania komunikatu RosettaNet, należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania.

1. Po dodaniu wyzwalacza, wybierz **nowy krok**.

   ![Dodawanie wyzwalacza żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wprowadź "rosettanet", a następnie wybierz następującą akcję: **Dekodowanie RosettaNet**

   ![Znajdowanie i wybieranie akcji "RosettaNet dekodowania"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Podaj informacje dotyczące właściwości akcji:

   ![Podaj szczegóły akcji](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Komunikat** | Tak | Dekoduj komunikat RosettaNet  |
   | **Nagłówki** | Tak | Nagłówki HTTP, które dostarczają wartości dla wersji, która jest wersją RNIF, a typ odpowiedzi, który wskazuje typ komunikacji między partnerami i może być synchroniczna lub asynchroniczna |
   | **Rola** | Tak | Roli partnera hosta na publiczny adres IP |
   ||||

   W ramach akcji dekodowania RosettaNet zawiera dane wyjściowe, oraz inne właściwości **sygnału ruchu wychodzącego**, której można wybrać kodowanie i wrócić do partnera lub wykonać żadnych innych działań na te dane wyjściowe.

## <a name="send-or-encode-rosettanet-messages"></a>Wyślij lub zakodować RosettaNet wiadomości

1. [Tworzenie pustej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) do aplikacji logiki.

1. Zanim będzie można dodać akcję do zakodowania komunikatu RosettaNet, należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania.

1. Po dodaniu wyzwalacza, wybierz **nowy krok**.

   ![Dodawanie wyzwalacza żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wprowadź "rosettanet", a następnie wybierz następującą akcję: **Kodowanie RosettaNet**

   ![Znajdowanie i wybieranie akcji "RosettaNet kodowanie"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Podaj informacje dotyczące właściwości akcji:

   ![Podaj szczegóły akcji](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Komunikat** | Yes | Komunikat RosettaNet do kodowania  |
   | **Partner hosta** | Yes | Nazwa partnera hosta |
   | **Partner gościa** | Tak | Nazwa partnera gościa |
   | **Kod PIP** | Tak | Kod PIP |
   | **Wersja narzędzia PIP** | Yes | Wersja narzędzia PIP |  
   | **Tożsamość wystąpienia PIP** | Tak | Unikatowy identyfikator dla tego komunikatu PIP |  
   | **Typ komunikatu** | Yes | Typ komunikatu do kodowania |  
   | **Rola** | Tak | Rola partner hosta |
   ||||

   Zakodowany komunikat jest teraz gotowe do wysłania do partnera.

1. Aby wysłać zakodowanego komunikatu, w tym przykładzie użyto **HTTP** akcji, która jest zmieniona "HTTP - wysyłania zakodowany komunikat do partnera".

   ![Akcja HTTP do wysyłania wiadomości RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Na standardach RosettaNet transakcje biznesowe są uważane za ukończone, tylko wtedy, gdy spełniono wszystkie kroki, które są definiowane przez publiczny adres IP.

1. Po host wysyła zakodowany komunikat do partnera, host oczekuje na sygnał i potwierdzenia. Aby wykonać to zadanie, należy dodać **RosettaNet oczekiwania na odpowiedź** akcji.

   ![Dodawanie akcji "RosettaNet oczekiwania na odpowiedź"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Czas oczekiwania i liczby ponownych prób zależą od konfiguracji narzędzia PIP na koncie integracji. Jeśli nie zostanie odebrana odpowiedź, ta akcja generuje zgłoszenia błędu. Aby obsłużyć liczbę ponownych prób, zawsze umieszczaj **Koduj** i **oczekiwanie na odpowiedź** akcji w **aż** pętli.

   ![Do momentu pętli z akcjami RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak sprawdzanie poprawności, przekształcania i inne operacje dotyczące wiadomości z [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
