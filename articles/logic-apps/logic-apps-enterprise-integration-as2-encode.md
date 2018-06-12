---
title: Kodowanie wiadomości AS2 - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak używać kodera AS2 w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: fe8a2b00f15fa737c8ed343a47e1cab1c260346b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297934"
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kodowanie wiadomości AS2 dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw

Aby ustalić, bezpieczeństwa i niezawodności podczas przesyłania wiadomości, korzystania z łącznika komunikat AS2 kodowania. Ten łącznik umożliwia cyfrowe podpisywanie, szyfrowanie i potwierdzeń za pośrednictwem wiadomości dyspozycji powiadomienia (MDN), które również prowadzi do obsługę Niemożność wyparcia się.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji do używania łącznika AS2 kodowania komunikatu.
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [Umowy AS2](logic-apps-enterprise-integration-as2.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-as2-messages"></a>Kodowanie wiadomości AS2

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik AS2 kodowania wiadomości nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3.  W polu wyszukiwania wprowadź "AS2" filtru. Wybierz **AS2 - AS2 kodowania komunikatu**.
   
    ![Wyszukaj "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć. 
   
    ![Tworzenie połączenia konta integracji](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz **Utwórz**.
   
    ![Szczegóły połączenia integracji](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, podaj szczegóły **AS2 — od**, **AS2 — do identyfikatorów** zgodnie z konfiguracją w umowie, i **treści**, czyli ładunek komunikatu.
   
    ![Podaj wymagane pola](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Szczegóły kodera AS2

Łącznik AS2 kodowania wykonuje te zadania: 

* Stosuje nagłówków AS2/HTTP
* Znaki wychodzących wiadomości (jeśli jest skonfigurowane)
* Szyfruje komunikaty wychodzące (jeśli jest skonfigurowane)
* Kompresuje komunikatu (jeśli jest skonfigurowane)

## <a name="try-this-sample"></a>Spróbuj w tym przykładzie

Aby wypróbować logiki pełnej funkcjonalności aplikacji oraz przykładowy AS2 scenariusza wdrażania, zobacz [AS2 szablon aplikacji logiki oraz scenariusz](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/as2/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") 

