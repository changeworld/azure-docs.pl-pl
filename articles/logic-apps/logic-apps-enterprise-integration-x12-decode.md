---
title: Dekodowanie X12 komunikaty — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie potwierdzeń z X12 dekodera wiadomość w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701447"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekodowanie X12 wiadomości w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Za pośrednictwem łącznika komunikat dekodowania X12 można zweryfikować koperty dla umowy z partnerem handlowym, weryfikowanie integracji EDI i właściwości specyficzne dla partnerów, Podziel wymianę na zestawy transakcji lub zachować całą wymian i generowanie potwierdzeń dla przetwarzania transakcji. Aby użyć tego łącznika, łącznik należy dodać do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikat dekodowania X12.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-x12-messages"></a>Dekodowanie X12 wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Dekodowanie X12 łącznika komunikat nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź "x12" jako filtr. Wybierz **X12 — dekodowanie X12 komunikat**.
   
    ![Wyszukaj "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć. 

    ![Podaj szczegóły połączenia dla konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**.
   
    ![Szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po połączenie utworzenie, jak pokazano w tym przykładzie wybierz X12 komunikat pliku prostego do dekodowania.

    ![utworzone połączenie konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Na przykład:

    ![Komunikat pliku dekodowania prostego wybierz X12](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Zawartość komunikatu rzeczywiste lub ładunku dla tablicy komunikatów, dobre i złe, jest zakodowany w formacie base64. Dlatego należy określić wyrażenie, które przetwarza tę zawartość.
   > Oto przykład, który przetwarza zawartość jako plik XML, który można wprowadzić w widoku Kod lub przy użyciu Kreatora wyrażeń w projektancie.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Przykład zawartości](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 dekodowania szczegóły

X12 łącznika dekodowania wykonuje te zadania:

* Sprawdza poprawność koperty względem handlowymi umowy z partnerem
* Weryfikuje EDI i właściwości specyficzne dla partnerów
  * Strukturalne Walidacja EDI i sprawdzanie poprawności schematu rozszerzonego
  * Sprawdzanie poprawności strukturę koperty wymiany.
  * Weryfikacja schematu koperty względem schematu kontroli.
  * Weryfikacja schematu elementów danych zestawu transakcji względem schematu komunikatów.
  * Walidacja EDI wykonywane na elementach danych zestawu transakcji 
* Sprawdza, czy numerów kontrolnych zestawu wymiany, grupy i transakcji nie są duplikatami
  * Sprawdza, czy numer kontrolny wymiany względem wymian odebrane wcześniej.
  * Sprawdza, czy numer kontrolny grupy względem innych numery kontrolne grupy w wymianie.
  * Sprawdza, czy numer kontrolny zestawu transakcji przed innymi numerów kontrolnych zestawu transakcji w tej grupie.
* Dzieli wymianę na zestawy transakcji lub zachowuje całego wymiany:
  * Podziel wymiany na zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu: Wymiana dzieli dane do transakcji ustawia i analizuje każdy zestaw transakcji. 
  X12 akcji dekodowania danych wyjściowych tylko tych transakcji zestawów, które się nie powieść sprawdzania poprawności, aby `badMessages`i dane wyjściowe, ustawia dla pozostałego transakcji `goodMessages`.
  * Rozdziel wymianę na zestawy transakcji — Zawieś wymianę w przypadku błędu: Wymiana dzieli dane do transakcji ustawia i analizuje każdy zestaw transakcji. 
  Jeśli jeden lub więcej transakcji ustawia się w wymianie wystąpi niepowodzenie weryfikacji, X12 akcji dekodowania danych wyjściowych, wszystkie transakcje ustawia się w tej wymiany do `badMessages`.
  * Zachowaj wymianę — Zawieś zestawy transakcji w przypadku błędu: Zachowaj wymiany i przetwarzanie całego wymiany wsadowej. 
  X12 akcji dekodowania danych wyjściowych tylko tych transakcji zestawów, które się nie powieść sprawdzania poprawności, aby `badMessages`i dane wyjściowe, ustawia dla pozostałego transakcji `goodMessages`.
  * Zachowaj wymianę — Zawieś wymianę w przypadku błędu: Zachowaj wymiany i przetwarzanie całego wymiany wsadowej. 
  Jeśli jeden lub więcej transakcji ustawia się w wymianie wystąpi niepowodzenie weryfikacji, X12 akcji dekodowania danych wyjściowych, wszystkie transakcje ustawia się w tej wymiany do `badMessages`. 
* Generuje potwierdzenie technicznych i/lub funkcjonalnej (jeśli jest skonfigurowane).
  * Potwierdzenie techniczne generuje w wyniku weryfikacji nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzania nagłówka wymiany i przyczepy przez odbiorcę adresu.
  * Potwierdzenie funkcjonalności generuje w wyniku weryfikacji treści. Potwierdzenie funkcjonalności raporty każdy wystąpił błąd podczas przetwarzania odebranych dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") 

