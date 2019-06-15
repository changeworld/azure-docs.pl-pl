---
title: Kodowanie X12 komunikaty — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i przekonwertować zakodowane w formacie XML komunikatów za pomocą X12 komunikatu kodera w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.date: 01/27/2017
ms.openlocfilehash: 871d6d2b2019372bd258f8909ed0feeeddac4af7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705697"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kodowanie X12 wiadomości w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Za pośrednictwem łącznika komunikat Koduj X12 weryfikowanie integracji EDI i właściwości specyficzne dla partnerów, przekonwertować zakodowane w formacie XML komunikatów EDI zestawów transakcji w wymianie, a żądania potwierdzenia technicznych i/lub funkcjonalności potwierdzenia.
Aby użyć tego łącznika, łącznik należy dodać do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikat Koduj X12.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-x12-messages"></a>Kodowanie X12 wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Koduj X12 łącznika komunikat nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź "x12" jako filtr. Wybierz opcję **X12 — kodowanie X12 wiadomości według nazwy umowy** lub **X12 — kodowanie X12 wiadomości według tożsamości**.
   
    ![Wyszukaj "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć. 
   
    ![połączenie konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**.

    ![utworzone połączenie konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Połączenie jest gotowy.

    ![Szczegóły połączenia konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kodowanie X12 wiadomości według nazwy umowy

Jeśli zdecydujesz się na kodowanie X12 Otwórz wiadomości według nazwy umowy **nazwa X12 umowy** listy wprowadź lub wybierz z istniejących X12 umowy. Wprowadź komunikat XML do zakodowania.

![Wprowadź X12 Nazwa umowy i komunikat XML do zakodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kodowanie X12 wiadomości według tożsamości

Jeśli zdecydujesz się kodować X12 wiadomości według tożsamości, wprowadź identyfikator nadawcy, Kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy, zgodnie z konfiguracją usługi X12 umowy. Wybierz komunikat XML do zakodowania.
   
![Podaj tożsamości nadawcy i odbiorcy, wybierz komunikat XML do zakodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 kodowanie szczegóły

X12 łącznika Koduj wykonuje te zadania:

* Rozpoznanie umowy, dopasowując nadawcą i odbiorcą właściwości kontekstu.
* Serializuje wymiany EDI, konwersji wiadomości w formacie XML do zestawów transakcji EDI w wymianie.
* Stosuje segmentów nagłówka i elementu końcowego zestawu transakcji
* Generuje numer kontrolny wymiany, numer kontrolny grupy i numer kontrolny zestawu transakcji, dla każdego wychodzących wymiany
* Zastępuje separatory w danych ładunku
* Weryfikuje EDI i właściwości specyficzne dla partnerów
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji dla komunikatu schematu
  * Walidacja EDI wykonywane na elementach danych zestawu transakcji.
  * Rozszerzona Walidacja wykonywane na elementach danych zestawu transakcji
* Żądania potwierdzenia technicznych i/lub funkcjonalnej (jeśli jest skonfigurowane).
  * Potwierdzenie techniczne generuje w wyniku weryfikacji nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzania nagłówka wymiany i przyczepy przez odbiorcę adresu
  * Potwierdzenie funkcjonalności generuje w wyniku weryfikacji treści. Potwierdzenie funkcjonalności raporty każdy wystąpił błąd podczas przetwarzania odebranych dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") 

