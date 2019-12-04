---
title: Tworzenie integracji B2B Enterprise
description: Odbieraj dane B2B w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793098"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Odbieraj dane B2B przy użyciu Azure Logic Apps i Pakiet integracyjny dla przedsiębiorstw

Po utworzeniu konta integracji z partnerami i umowami można przystąpić do tworzenia przepływu pracy Business-Business (B2B) dla aplikacji logiki przy użyciu [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z akcji AS2 i X12, musisz mieć konto Integracja dla przedsiębiorstw. Dowiedz się, [jak utworzyć konto integracja dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Tworzenie aplikacji logiki za pomocą łączników B2B

Wykonaj następujące kroki, aby utworzyć aplikację logiki B2B, która używa akcji AS2 i X12 do odbierania danych od partnera handlowego:

1. Utwórz aplikację logiki, a następnie [Połącz aplikację z kontem integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Dodaj **żądanie — po odebraniu żądania HTTP** do aplikacji logiki.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Aby dodać akcję **DEKODUJ AS2** , wybierz pozycję **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Aby odfiltrować wszystkie akcje do tego, które chcesz, wpisz słowo **AS2** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Wybierz akcję **AS2-Decode AS2** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Dodaj **treść** , która ma być używana jako dane wejściowe. 
   W tym przykładzie wybierz treść żądania HTTP, które wyzwala aplikację logiki. Lub wprowadź wyrażenie, które wprowadzi nagłówki w polu **nagłówki** :

    @triggerOutputs() ["nagłówki"]

7. Dodaj wymagane **nagłówki** dla AS2, które można znaleźć w nagłówkach żądania HTTP. 
   W tym przykładzie wybierz nagłówki żądania HTTP, które wyzwalają aplikację logiki.

8. Teraz Dodaj akcję Dekoduj X12 komunikat. Wybierz pozycję **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Aby odfiltrować wszystkie akcje do tego, które chcesz, wpisz słowo **X12** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Wybierz akcję **X12-Decode X12** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Teraz należy określić dane wejściowe tej akcji. 
    Dane wejściowe są danymi wyjściowymi poprzedniej akcji AS2.

    Rzeczywista zawartość wiadomości znajduje się w obiekcie JSON i jest zakodowana w formacie Base64, dlatego należy określić wyrażenie jako dane wejściowe. 
    Wprowadź następujące wyrażenie w **komunikacie prostego pliku X12, aby zdekodować** pole wejściowe:
    
    @base64ToString(treść ("Decode_AS2_message")? [" AS2Message']? ["Content"])

    Teraz Dodaj kroki, aby zdekodować dane X12 otrzymane z partnera handlowego i elementów wyjściowych w obiekcie JSON. 
    Aby powiadomić partnera o otrzymaniu danych, można wysłać odpowiedź zawierającą powiadomienie o AS2 komunikatu (powiadomienia MDN) w akcji odpowiedzi HTTP.

12. Aby dodać akcję **odpowiedź** , wybierz pozycję **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Aby odfiltrować wszystkie akcje do tego, które chcesz, w polu wyszukiwania wprowadź tekst **odpowiedź** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Wybierz akcję **odpowiedzi** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Aby uzyskać dostęp do powiadomienia MDN z danych wyjściowych akcji **dekodowanie X12 komunikatu** , Ustaw pole **treści** odpowiedzi na następujące wyrażenie:

    @base64ToString(treść ("Decode_AS2_message")? [" OutgoingMdn']? ["Content"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Zapisz pracę.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Teraz wszystko jest gotowe do skonfigurowania aplikacji logiki B2B. W świecie rzeczywistym można przechowywać zdekodowane X12 dane w aplikacji LOB lub magazynu danych. jest to wymagane w przypadku aplikacji biznesowych. Aby połączyć własne aplikacje LOB i użyć tych interfejsów API w aplikacji logiki, możesz dodać dalsze akcje lub napisać niestandardowe interfejsy API.

## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia

* AS2 i X12 akcje kodowania i kodowania umożliwiają wymianę danych między partnerami handlowymi przy użyciu standardowych protokołów branżowych w usłudze Logic Apps.
* Aby wymienić dane z partnerami handlowymi, możesz użyć AS2 i X12 z lub bez siebie.
* Akcje B2B umożliwiają łatwe tworzenie partnerów i umów w ramach konta integracji i korzystanie z nich w aplikacji logiki.
* Rozszerzając aplikację logiki na inne akcje, możesz wysyłać i odbierać dane między innymi aplikacjami i usługami, takimi jak SalesForce.

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
