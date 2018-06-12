---
title: Dekodowanie AS2 wiadomości - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak używać dekodera AS2 w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a5ca615d984d07513b12399b6f7e7901490f2e41
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298835"
---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie AS2 wiadomości dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw 

Aby ustalić, bezpieczeństwa i niezawodności podczas przesyłania wiadomości, korzystania z łącznika komunikat dekodowania AS2. Ten łącznik umożliwia cyfrowe podpisywanie, odszyfrowywania i potwierdzeń za pośrednictwem komunikatu powiadomienia dyspozycji (MDN).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji do używania łącznika AS2 dekodowanie komunikatu.
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [Umowy AS2](logic-apps-enterprise-integration-as2.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-as2-messages"></a>Dekodowanie AS2 wiadomości

1. [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Dekodowanie AS2 łącznika komunikatu nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3.  W polu wyszukiwania wprowadź "AS2" filtru. Wybierz **AS2 - AS2 zdekodować komunikatu**.
   
    ![Wyszukaj "AS2"](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć.
   
    ![Utwórz połączenie integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz **Utwórz**.

    ![Szczegóły połączenia integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, wybierz **treści** i **nagłówki** z danych wyjściowych żądania.
   
    ![utworzone połączenie integracji](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    Na przykład:

    ![Wybierz treści i nagłówków z danych wyjściowych żądań](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>Szczegóły dekodera AS2

Łącznik AS2 dekodowania wykonuje te zadania: 

* Przetwarza nagłówków AS2/HTTP
* Weryfikuje podpis (jeśli jest skonfigurowane)
* Odszyfrowuje wiadomości (jeśli jest skonfigurowane)
* Dekompresuje komunikatu (jeśli jest skonfigurowane)
* Uzgadnia odebrane MDN z oryginalnej wiadomości wychodzących
* Aktualizacje i są powiązane bez odrzucania bazy danych
* Zapisuje rekordy AS2 raportowania stanu
* Zawartość ładunek danych wyjściowych jest kodowany w standardzie base64
* Określa, czy MDN jest wymagana i czy MDN powinno odbywać się synchronicznie czy asynchroniczne na podstawie konfiguracji umowy AS2
* Generuje MDN synchroniczna lub asynchroniczna, (na podstawie konfiguracji umów)
* Ustawia właściwości i tokenów korelacji w MDN

## <a name="try-this-sample"></a>Spróbuj w tym przykładzie

Aby wypróbować logiki pełnej funkcjonalności aplikacji oraz przykładowy AS2 scenariusza wdrażania, zobacz [AS2 szablon aplikacji logiki oraz scenariusz](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/as2/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md) 

