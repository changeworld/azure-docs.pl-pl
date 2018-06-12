---
title: Kodowanie X12 wiadomości - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i przekonwertować kodowany w formacie XML komunikaty z X12 komunikatu kodera w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: a01e9ca9-816b-479e-ab11-4a984f10f62d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: ec350560f4aca4e9a0b4f00892b690012c9f08dd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299019"
---
# <a name="encode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kodowanie X12 wiadomości dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw

Łącznik komunikat Koduj X12 zweryfikować EDI i właściwości specyficzne dla partnera, przekonwertować wiadomości w formacie XML na EDI zestawów transakcji w wymiany, a żądania potwierdzenia technicznych i funkcjonalności potwierdzenia.
Aby użyć tego łącznika, należy dodać łącznika do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji Koduj X12 łącznika komunikatu.
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-x12-messages"></a>Kodowanie X12 wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Koduj X12 łącznika komunikatu nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3.  W polu wyszukiwania wprowadź "x12" filtru. Wybierz opcję **X12 —, zakodować w X12 komunikatu przez Nazwa umowy** lub **X12 —, zakodować w X12 komunikatu przez tożsamości**.
   
    ![Wyszukaj "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć. 
   
    ![Integracja połączenia konta](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz **Utwórz**.

    ![utworzone połączenie konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Utworzono połączenie.

    ![Szczegóły połączenia konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kodowanie X12 wiadomości przez Nazwa umowy

Jeśli zdecydujesz się na kodowania X12 otworzyć wiadomości przez Nazwa umowy **nazwa X12 umowy** listy, wpisz lub wybierz Twoje istniejące X12 umowy. Wprowadź komunikat XML do kodowania.

![Wprowadź X12 Nazwa umowy i wiadomości XML do kodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kodowanie X12 wiadomości przez tożsamości

Jeśli wybierzesz do kodowania X12 wiadomości przez tożsamości, wprowadź identyfikator nadawcy, Kwalifikator nadawcy, odbiorcy identyfikator i kwalifikator odbiornik, zgodnie z konfiguracją sieci X12 umowy. Wybierz wiadomości XML do kodowania.
   
![Podaj tożsamości dla nadawcy i odbiorcy, wybierz wiadomości XML do kodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 kodowania szczegóły

X12 łącznik Koduj wykonuje te zadania:

* Rozwiązanie umowy przez nadawcę i odbiorcę właściwości kontekstu dopasowywania.
* Serializuje wymiany EDI, konwertowanie wiadomości w formacie XML w EDI zestawy transakcji w wymiany.
* Stosuje zestaw transakcji nagłówka i przyczepy segmentów
* Generuje numer formantu wymiany, numer kontroli grupy i numer kontroli zestawu transakcji, każdy wychodzący wymiany
* Zastępuje separatorów w danych ładunku
* Sprawdza poprawność właściwości specyficzne dla partnerów i EDI
  * Sprawdzanie poprawności schematu elementów dane zestawu transakcji dla komunikatu schematu
  * EDI Weryfikacja w przypadku elementów dane zestawu transakcji.
  * Rozszerzonej weryfikacji w przypadku elementów dane zestawu transakcji
* Żądania potwierdzenia techniczne i/lub funkcjonalne (jeśli jest skonfigurowane).
  * Generuje techniczne potwierdzenia wyniku weryfikacji nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzanie nagłówka wymiany i przyczepy przez odbiornik adresu
  * Generuje funkcjonalności potwierdzenia wyniku weryfikacji treści. Potwierdzenie funkcjonalności raporty każdego wystąpił błąd podczas przetwarzania odebranego dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") 

