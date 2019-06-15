---
title: Tworzenie integracji przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Odbieranie danych B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: 05368f627c5e9482a43d5e30b0e16b1d47f6217c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60999180"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Odbieranie danych B2B przy użyciu usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw

Po utworzeniu konta integracji, który ma partnerów oraz umowy, można przystąpić do tworzenia firmami (B2B) przepływu pracy aplikacji logiki przy użyciu [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć AS2 i X12 akcji, konieczne jest posiadanie konta integracji przedsiębiorstwa. Dowiedz się, [jak utworzyć konto integracji przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Utwórz aplikację logiki z łączniki B2B

Wykonaj następujące kroki, aby utworzyć aplikację logiki B2B, która używa AS2 i X12 akcje na odbieranie danych z partnerem handlowym:

1. Następnie utwórz aplikację logiki, [połączyć aplikację z kontem integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Dodaj **żądania — zostanie odebrane żądanie HTTP podczas** wyzwalacz aplikacji logiki.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Aby dodać **dekodowania AS2** akcji wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Aby odfiltrować wszystkie akcje, które chcesz, wprowadź słowo **as2** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Wybierz **AS2 - komunikat dekodowania AS2** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Dodaj **treści** , którą chcesz używać jako danych wejściowych. 
   W tym przykładzie wybierz treści żądania HTTP, która powoduje uruchomienie aplikacji logiki. Lub wprowadź wyrażenie, które danych wejściowych nagłówków **nagłówki** pola:

    @triggerOutputs() ["headers"]

7. Dodaj wymagane **nagłówki** dla pola AS2, który można znaleźć w nagłówkach żądań HTTP. 
   W tym przykładzie wybierz nagłówki żądania HTTP, które mogą powodować aplikacji logiki.

8. Teraz Dodaj akcję komunikat dekodowania X12. Wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Aby odfiltrować wszystkie akcje, które chcesz, wprowadź słowo **x12** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Wybierz **X12 — dekodowanie X12 komunikat** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Teraz należy określić dane wejściowe tej akcji. 
    Te dane wejściowe znajdują się dane wyjściowe z poprzednich akcji AS2.

    Zawartość komunikatu rzeczywiste znajduje się w obiekcie JSON i jest zakodowany w formacie, base64, dlatego należy określić wyrażenie jako dane wejściowe. 
    Wprowadź następujące wyrażenie w **X12 PROSTEGO pliku wiadomości do dekodowania** pole wejściowe:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Teraz dodaj kroki w celu zdekodowania X12 danych odebranych z partnerem handlowym i danych wyjściowych elementów w obiekcie JSON. 
    Aby powiadomić partnera Odebrano dane, możesz wysłać ponownie odpowiedź zawierającą komunikat dyspozycji powiadomień (komunikatu MDN AS2) w celu wykonania akcji odpowiedzi HTTP.

12. Aby dodać **odpowiedzi** akcji, wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Aby odfiltrować wszystkie akcje, które chcesz, wprowadź słowo **odpowiedzi** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Wybierz **odpowiedzi** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Dostęp do powiadomienia MDN z danych wyjściowych **komunikat dekodowania X12** akcji set response ustawić **treści** pole to wyrażenie:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Zapisz swoją pracę.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Teraz po zakończeniu konfigurowania aplikacji logiki B2B. W rzeczywistej aplikacji, możesz chcieć przechowywać X12 dekodowane dane w magazynie line-of-business (LOB) aplikacji lub danych. Aby połączyć aplikacje BIZNESOWE i użyj tych interfejsów API w aplikacji logiki, możesz dodać kolejne akcje lub pisania niestandardowych interfejsów API.

## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia

* AS2 X12 dekodowanie i kodowanie akcje Pozwól, wymianę danych między partnerami handlowymi przy użyciu standardowych w branży protokołów w usłudze logic apps.
* Wymiana danych z partnerami handlowymi, umożliwia AS2 i X12 z lub bez siebie nawzajem.
* Akcji B2B pomogą Ci łatwo utworzyć partnerów oraz umowy na koncie integracji i korzystać z nich w aplikacji logiki.
* Podczas rozszerzania aplikacji logiki z innymi akcjami można wysyłać i odbierać dane między innymi aplikacjami i usługami, takimi jak SalesForce.

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
