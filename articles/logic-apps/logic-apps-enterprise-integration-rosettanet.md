---
title: Komunikaty RosettaNet dla integracji B2B
description: Wymiana komunikatów RosettaNet w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 2cc2ac08b9624c1d1d9bee9ce91a7c91189d7f2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792422"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Wymiana komunikatów RosettaNet dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps

[RosettaNet](https://resources.gs1us.org) to konsorcjum non-profit, które ustanowiło standardowe procesy wymiany informacji biznesowych. Normy te są powszechnie stosowane w procesach łańcucha dostaw i są szeroko rozpowszechnione w branży półprzewodników, elektroniki i logistyki. Konsorcjum RosettaNet tworzy i utrzymuje procesy interfejsu partnera (PIPs), które zapewniają typowe definicje procesów biznesowych dla wszystkich wymian komunikatów RosettaNet. RosettaNet opiera się na XML i definiuje wytyczne dotyczące komunikatów, interfejsy dla procesów biznesowych i struktury implementacji komunikacji między firmami.

W [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md)łącznik RosettaNet ułatwia tworzenie rozwiązań integracyjnych obsługujących standardy RosettaNet. Łącznik jest oparty na RosettaNet Implementation Framework (RNIF) w wersji 2.0.01. RNIF to otwarta siećowa struktura aplikacji, która umożliwia partnerom biznesowym wspólne uruchamianie pips RosettaNet. Ta struktura definiuje strukturę wiadomości, potrzebę potwierdzenia, kodowanie wielozadaniowe rozszerzenia poczty internetowej (MIME) i podpis cyfrowy.

W szczególności złącze zapewnia następujące możliwości:

* Kodowanie lub odbieranie wiadomości RosettaNet.
* Dekodowanie lub wysyłanie wiadomości RosettaNet.
* Poczekaj na odpowiedź i generowanie powiadomienia o awarii.

W przypadku tych możliwości łącznik obsługuje wszystkie pipsy zdefiniowane przez RNIF 2.0.01. Komunikacja z partnerem może być synchroniczowa lub asynchroniza.

## <a name="rosettanet-concepts"></a>Koncepcje RosettaNet

Oto kilka pojęć i terminów, które są unikatowe dla specyfikacji RosettaNet i są ważne podczas tworzenia integracji opartych na RosettaNet:

* **Pip**

  Organizacja RosettaNet tworzy i utrzymuje procesy interfejsu partnera (PIPs), które zapewniają typowe definicje procesów biznesowych dla wszystkich wymian komunikatów RosettaNet. Każda specyfikacja PIP zawiera plik definicji typu dokumentu (DTD) i dokument zawierający wytyczne dotyczące wiadomości. Plik DTD definiuje strukturę komunikatów zawartości usługi. Dokument zawierający wytyczne dotyczące komunikatów, który jest czytelnym dla człowieka plikiem HTML, określa ograniczenia na poziomie elementu. Razem pliki te zapewniają pełną definicję procesu biznesowego.

   Pipsy są podzielone na kategorie według funkcji biznesowej wysokiego poziomu lub klastra, podfunkcji lub segmentu. Na przykład "3A4" jest PIP dla zamówienia zakupu, podczas gdy "3" jest funkcją zarządzania zamówieniami, a "3A" jest podfunkcjam zapisu zamówienia &. Aby uzyskać więcej informacji, zobacz [witrynę RosettaNet](https://resources.gs1us.org).

* **Akcja**

  Część PIP, wiadomości akcji są wiadomości biznesowe, które są wymieniane między partnerami.

* **Sygnału**

   Część PIP, wiadomości sygnału są potwierdzenia, które są wysyłane w odpowiedzi na komunikaty akcji.

* **Pojedyncze działanie i podwójne działanie**

  W przypadku PIP o pojedynczej akcji jedyną odpowiedzią jest komunikat sygnału potwierdzenia. W przypadku PIP o podwójnej akcji inicjator odbiera komunikat odpowiedzi i odpowiada z potwierdzeniem oprócz przepływu wiadomości z pojedynczą akcją.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) zdefiniowanych na koncie integracyjnym i skonfigurowanych za pomocą kwalifikatora "DUNS" w obszarze **Tożsamości biznesowe**

* Konfiguracja procesu PIP, która jest wymagana do wysyłania lub odbierania wiadomości RosettaNet, na koncie integracji. Konfiguracja procesu przechowuje wszystkie charakterystyki konfiguracji PIP. Następnie można odwołać się do tej konfiguracji podczas tworzenia umowy z partnerem. Aby utworzyć konfigurację procesu PIP na koncie integracji, zobacz [Dodawanie konfiguracji procesu PIP](#add-pip).

* Opcjonalne [certyfikaty](../logic-apps/logic-apps-enterprise-integration-certificates.md) do szyfrowania, odszyfrowywania lub podpisywania wiadomości przekazanych na konto integracji. Certyfikaty są wymagane tylko w przypadku używania podpisywania lub szyfrowania.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>Dodawanie konfiguracji procesu PIP

Aby dodać konfigurację procesu PIP do konta integracji, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz swoje konto integracji.

1. W **okienku Przegląd** wybierz kafelek **RosettaNet PIP.**

   ![Wybierz płytkę RosettaNet](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. W obszarze **RosettaNet PIP**wybierz pozycję **Dodaj**. Podaj dane PIP.

   ![Dodaj szczegóły RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Twoja nazwa PIP |
   | **Kod PIP** | Tak | Trzycyfrowy kod PIP. Aby uzyskać więcej informacji, zobacz [RosettaNet PIP](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **Wersja PIP** | Tak | Numer wersji PIP, który jest dostępny na podstawie wybranego kodu PIP |
   ||||

   Aby uzyskać więcej informacji na temat tych właściwości PIP, odwiedź [witrynę rosettanet](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Po zakończeniu wybierz przycisk **OK**, który tworzy konfigurację PIP.

1. Aby wyświetlić lub edytować konfigurację procesu, wybierz PIP i wybierz polecenie **Edytuj jako JSON**.

   Wszystkie ustawienia konfiguracji procesu pochodzą ze specyfikacji PIP. Logic Apps wypełnia większość ustawień wartościami domyślnymi, które są najczęściej używanymi wartościami dla tych właściwości.

   ![Edytuj konfigurację RosettaNet PIP](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Upewnij się, że ustawienia odpowiadają wartościom w odpowiedniej specyfikacji PIP i spełniają Twoje potrzeby biznesowe. W razie potrzeby zaktualizuj wartości w JSON i zapisz te zmiany.

## <a name="create-rosettanet-agreement"></a>Tworzenie umowy RosettaNet

1. W [witrynie Azure portal](https://portal.azure.com)znajdź i otwórz swoje konto integracji, jeśli nie jest jeszcze otwarty.

1. W okienku **Przegląd** wybierz kafelek **Umowy.**

   ![Kafelek Wybieranie opcji Umowy](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. W obszarze **Umowy**wybierz pozycję **Dodaj**. Podaj szczegóły umowy.

   ![Dodawanie szczegółów umowy](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Nazwa** | Tak | Nazwa umowy |
   | **Typ umowy** | Tak | Wybierz **RosettaNet**. |
   | **Partner gospodarza** | Tak | Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
   | **Tożsamość hosta** | Tak | Identyfikator partnera-hosta |
   | **Partner gości** | Tak | Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner-gość reprezentuje organizację, która prowadzi interesy z partnerem hostingowym. |
   | **Tożsamość gościa** | Tak | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różna | Te właściwości dotyczą wszystkich wiadomości odebranych przez partnera hosta |
   | **Ustawienia wysyłania** | Różna | Te właściwości dotyczą wszystkich wiadomości wysyłanych przez partnera hosta |  
   | **Referencje RosettaNet PIP** | Tak | Pip odwołuje się do umowy. Wszystkie komunikaty RosettaNet wymagają konfiguracji PIP. |
   ||||

1. Aby skonfigurować umowę na odbieranie wiadomości przychodzących od partnera gościa, wybierz **pozycję Ustawienia odbierania**.

   ![Odbieranie ustawień](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie wiadomości przychodzących, w obszarze **Wiadomości**wybierz **pozycję Wiadomość powinna być podpisana** lub Wiadomość powinna być odpowiednio **zaszyfrowana.**

      | Właściwość | Wymagany | Opis |
      |----------|----------|-------------|
      | **Wiadomość powinna być podpisana** | Nie | Podpisz przychodzące wiadomości za pomocą wybranego certyfikatu. |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat używany do podpisywania |
      | **Włączanie szyfrowania wiadomości** | Nie | Szyfruj przychodzące wiadomości za pomocą wybranego certyfikatu. |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat używany do szyfrowania |
      ||||

   1. W każdym zaznaczeniu wybierz odpowiedni [certyfikat](./logic-apps-enterprise-integration-certificates.md), który został wcześniej dodany do konta integracji, aby użyć go do podpisywania lub szyfrowania.

1. Aby skonfigurować umowę wysyłania wiadomości do partnera gościa, wybierz pozycję **Wyślij ustawienia**.

   ![Ustawienia wysyłania](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Aby włączyć podpisywanie lub szyfrowanie wiadomości wychodzących, w obszarze **Wiadomości**wybierz odpowiednio **pozycję Włącz podpisywanie wiadomości** lub Włącz **szyfrowanie wiadomości.** W każdym zaznaczeniu wybierz odpowiedni algorytm i [certyfikat](./logic-apps-enterprise-integration-certificates.md), który został wcześniej dodany do konta integracji, aby użyć go do podpisywania lub szyfrowania.

      | Właściwość | Wymagany | Opis |
      |----------|----------|-------------|
      | **Włączanie podpisywania wiadomości** | Nie | Podpisywanie wiadomości wychodzących za pomocą wybranego algorytmu podpisywania i certyfikatu. |
      | **Algorytm podpisywania** | Tak, jeśli podpisywanie jest włączone | Algorytm podpisywania do użycia na podstawie wybranego certyfikatu |
      | **Certyfikat** | Tak, jeśli podpisywanie jest włączone | Certyfikat używany do podpisywania |
      | **Włączanie szyfrowania wiadomości** | Nie | Szyfruj wychodzące za pomocą wybranego algorytmu szyfrowania i certyfikatu. |
      | **Algorytm szyfrowania** | Tak, jeśli szyfrowanie jest włączone | Algorytm szyfrowania do użycia na podstawie wybranego certyfikatu |
      | **Certyfikat** | Tak, jeśli szyfrowanie jest włączone | Certyfikat używany do szyfrowania |
      ||||

   1. W **obszarze Punkty końcowe**określ wymagane adresy URL używane do wysyłania wiadomości akcji i potwierdzeń.

      | Właściwość | Wymagany | Opis |
      |----------|----------|-------------|
      | **Adres URL akcji** |  Tak | Adres URL używany do wysyłania wiadomości akcji. Adres URL jest polem wymaganym zarówno dla komunikatów synchronicznych, jak i asynchronicznych. |
      | **Adres URL potwierdzenia** | Tak | Adres URL do wysyłania wiadomości potwierdzających. Adres URL jest polem wymaganym dla wiadomości asynchronicznych. |
      ||||

1. Aby skonfigurować umowę z referencyjncami RosettaNet PIP dla partnerów, wybierz **pozycję RosettaNet PIP references**. W obszarze **Nazwa PIP**wybierz nazwę poprzednio utworzonego PIP.

   ![Odwołania do PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Wybór wypełnia pozostałe właściwości, które są oparte na PIP skonfigurowany na koncie integracji. W razie potrzeby można zmienić **rolę PIP**.

   ![Wybrany PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Po wykonaniu tych kroków możesz przystąpić do wysyłania lub odbierania wiadomości RosettaNet.

## <a name="rosettanet-templates"></a>Szablony RosettaNet

Aby przyspieszyć tworzenie i zalecać wzorce integracji, można użyć szablonów aplikacji logiki do dekodowania i kodowania komunikatów RosettaNet. Podczas tworzenia aplikacji logiki, można wybrać z galerii szablonów w logic app designer. Można również znaleźć te szablony w [repozytorium GitHub dla usługi Azure Logic Apps](https://github.com/Azure/logicapps).

![Szablony RosettaNet](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>Odbieranie lub dekodowanie wiadomości RosettaNet

1. [Tworzenie pustej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) z aplikacją logiki.

1. Przed dodaniem akcji do dekodowania komunikatu RosettaNet, należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania.

1. Po dodaniu wyzwalacza wybierz pozycję **Nowy krok**.

   ![Dodaj wyzwalacz żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wpisz "rosettanet" i wybierz tę akcję: **RosettaNet Decode**

   ![Znajdź i wybierz akcję "RosettaNet Decode"](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Podaj informacje dotyczące właściwości akcji:

   ![Podaj szczegóły akcji](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Komunikat** | Tak | Komunikat RosettaNet do dekodowania  |
   | **Nagłówki** | Tak | Nagłówki HTTP, które zawierają wartości dla wersji, która jest wersją RNIF, oraz typ odpowiedzi, który wskazuje typ komunikacji między partnerami i może być synchroniczne lub asynchroniczne |
   | **Roli** | Tak | Rola partnera przyjmującego w PIP |
   ||||

   Z akcji RosettaNet Decode dane wyjściowe wraz z innymi właściwościami zawierają **sygnał wychodzący,** który można wybrać do kodowania i powrotu do partnera lub podjąć inne działania na tym wyjściu.

## <a name="send-or-encode-rosettanet-messages"></a>Wysyłanie lub kodowanie wiadomości RosettaNet

1. [Tworzenie pustej aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

1. [Połącz swoje konto integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account) z aplikacją logiki.

1. Przed dodaniem akcji do kodowania komunikatu RosettaNet, należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania.

1. Po dodaniu wyzwalacza wybierz pozycję **Nowy krok**.

   ![Dodaj wyzwalacz żądania](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. W polu wyszukiwania wpisz "rosettanet" i wybierz tę akcję: **RosettaNet Encode**

   ![Znajdź i wybierz akcję "RosettaNet Encode"](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Podaj informacje dotyczące właściwości akcji:

   ![Podaj szczegóły akcji](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Komunikat** | Tak | Komunikat RosettaNet do kodowania  |
   | **Partner gospodarza** | Tak | Nazwa partnera hosta |
   | **Partner gości** | Tak | Nazwa partnera gościa |
   | **Kod PIP** | Tak | Kod PIP |
   | **Wersja PIP** | Tak | Wersja PIP |  
   | **Tożsamość wystąpienia PIP** | Tak | Unikatowy identyfikator tej wiadomości PIP |  
   | **Typ wiadomości** | Tak | Typ wiadomości do zakodowania |  
   | **Roli** | Tak | Rola partnera przyjmującego |
   ||||

   Zakodowana wiadomość jest teraz gotowa do wysłania do partnera.

1. Aby wysłać zakodowaną wiadomość, w tym przykładzie użyto akcji **HTTP,** której nazwa została zmieniona na "HTTP - Send encoded message to partner".

   ![Akcja HTTP do wysyłania wiadomości RosettaNet](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   Zgodnie ze standardami RosettaNet transakcje biznesowe są uznawane za zakończone tylko wtedy, gdy wszystkie kroki zdefiniowane przez PIP zostaną zakończone.

1. Po wysłaniu przez hosta zakodowanej wiadomości do partnera host czeka na sygnał i potwierdzenie. Aby wykonać to zadanie, dodaj **akcję RosettaNet oczekiwania na odpowiedź.**

   ![Dodaj akcję "RosettaNet wait for response"](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Czas trwania do użycia do oczekiwania i liczba ponownych prób są oparte na konfiguracji PIP na koncie integracji. Jeśli odpowiedź nie zostanie odebrana, ta akcja generuje powiadomienie o niepowodzeniu. Aby obsłużyć ponownych prób, zawsze umieścić **Encode** i **czekać na** odpowiedzi akcji w **Until** pętli.

   ![Do pętli z akcjami RosettaNet](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak sprawdzać poprawność, przekształcać i inne operacje związane z wiadomościami za pomocą [pakietu Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
