---
title: Tworzenie rozwiązań B2B - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Odbieranie danych w aplikacjach logiki za pomocą funkcji B2B w pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a27a413ba9a0d974cf90fe842d5fc325ab308a56
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298121"
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Odbieranie danych w aplikacjach logiki z funkcjami B2B w pakiet integracyjny dla przedsiębiorstw

Po utworzeniu konta integracji z partnerami i umowy, można przystąpić do tworzenia przepływu pracy (B2B) firma do firma aplikację logiki z [pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć AS2 i X12 akcje, muszą mieć konta integracji przedsiębiorstwa. Dowiedz się [sposobu tworzenia konta integracji przedsiębiorstwa](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Tworzenie aplikacji logiki z łączniki B2B

Wykonaj następujące kroki, aby utworzyć aplikację logiki B2B, który używa AS2 i X12 akcje na odbieranie danych z partnerem handlowym:

1. Tworzenie aplikacji logiki, następnie [połączyć aplikację z kontem integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Dodaj **żądania - zostanie odebrane żądanie HTTP podczas** do aplikacji logiki wyzwalacza.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Aby dodać **dekodowania AS2** akcji wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Aby filtrować wszystkie akcje do tego, który ma, wprowadź słowo **as2** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Wybierz **AS2 - AS2 zdekodować komunikatu** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Dodaj **treści** , który ma być używany jako dane wejściowe. W tym przykładzie wybierz treści żądania HTTP, które wyzwala aplikacji logiki. Lub wprowadź wyrażenie danych wejściowych nagłówków **nagłówki** pola:

    @triggerOutputs() [nagłówki]

7. Dodaj wymagane **nagłówki** dla AS2, który można znaleźć w nagłówkach żądania HTTP. W tym przykładzie wybierz nagłówki żądania HTTP, wyzwalających aplikacji logiki.

8. Teraz Dodaj dekodowania X12 Akcja komunikatu. Wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Aby filtrować wszystkie akcje do tego, który ma, wprowadź słowo **x12** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Wybierz **X12-dekodowania X12 komunikat** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Teraz należy określić w danych wejściowych tej akcji. Tych danych wejściowych jest dane wyjściowe z poprzedniej akcji AS2.

    Zawartość komunikatu rzeczywiste w obiekcie JSON i jest kodowanie base64, dlatego należy określić wyrażenie jako dane wejściowe. 
    Wprowadź poniższe wyrażenie w **X12 PŁASKIM pliku wiadomości do dekodowania** pola wejściowego:
    
    @base64ToString(body('Decode_AS2_message')? ["AS2Message"]? ["Content"])

    Teraz dodać kroki w celu zdekodowania X12 dane odebrane z partnerem handlowym i dane wyjściowe elementów w obiekcie JSON. 
    Powiadomiono partnera otrzymanie danych, możesz wysłać ponownie odpowiedzi zawierające powiadomienia dyspozycji wiadomości (MDN) AS2, w akcji odpowiedzi HTTP.

12. Aby dodać **odpowiedzi** akcji, wybierz **Dodaj akcję**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Aby filtrować wszystkie akcje do tego, który ma, wprowadź słowo **odpowiedzi** w polu wyszukiwania.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Wybierz **odpowiedzi** akcji.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Dostęp MDN z danych wyjściowych do **komunikat dekodowania X12** akcji, ustaw odpowiedzi **treści** wartością tego wyrażenia:

    @base64ToString(body('Decode_AS2_message')? ["OutgoingMdn"]? ["Content"])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Zapisz pracę.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Teraz po zakończeniu konfigurowania aplikacji logiki B2B. W przypadku aplikacji rzeczywistych można przechowywać X12 dekodowane dane w magazynie — biznesowych (LOB) aplikacji lub danych. Aby połączyć aplikacje LOB i użycia tych interfejsów API w aplikacji logiki, można dodać dodatkowe akcje lub zapisać niestandardowych interfejsów API.

## <a name="features-and-use-cases"></a>Funkcje i przypadki użycia

* AS2 i X12 dekodowania i kodowania akcje let wymiany danych między partnerami handlowymi przy użyciu standardowych protokołach branżowych w aplikacjach logiki.
* Wymiana danych z partnerami handlowymi, umożliwia AS2 i X12 z lub bez siebie nawzajem.
* Akcje B2B pomóc łatwe tworzenie partnerów i umów w ramach konta integracji i używać ich w aplikacji logiki.
* Podczas rozszerzania aplikacji logiki z innymi działaniami umożliwia wysyłanie oraz odbieranie danych między innych aplikacji i usług, takich jak SalesForce.

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
