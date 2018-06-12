---
title: Dekodowanie X12 wiadomości - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie potwierdzeń z X12 dekodera komunikat w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 14e9f0eefb705eea59a00390494871f5668b0a01
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298665"
---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie X12 wiadomości dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw

Łącznik komunikat dekodowania X12 można zweryfikować koperty przed umowy z partnerem handlowym, zweryfikować EDI i właściwości specyficzne dla partnera, podzielić wymianę w zestawy transakcji lub zachować całą wymianę i generowanie potwierdzeń dla przetworzonych transakcji. Aby użyć tego łącznika, należy dodać łącznika do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji dekodowania X12 łącznika komunikatu.
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [X12 umowy](logic-apps-enterprise-integration-x12.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-x12-messages"></a>Dekodowanie X12 wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Dekoduj X12 łącznika komunikatu nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3.  W polu wyszukiwania wprowadź "x12" filtru. Wybierz **X12-dekodowania X12 komunikat**.
   
    ![Wyszukaj "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć. 

    ![Podaj szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz **Utwórz**.
   
    ![Szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po połączeniu z utworzenie, jak pokazano w tym przykładzie wybierz X12 pliku prostego komunikatu zdekodować.

    ![utworzone połączenie konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Na przykład:

    ![Wybierz X12 płaskim komunikat pliku dekodowania](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Zawartość komunikatu rzeczywiste lub ładunek dla tablicy wiadomości, dobrym lub uszkodzone, jest kodowany w standardzie base64. Dlatego należy określić wyrażenia, który przetwarza tej zawartości.
   > Oto przykład, który przetwarza zawartości w formacie XML, które można wprowadzić w widoku kodu lub za pomocą Kreatora wyrażeń w projektancie.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Przykład zawartości](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 dekodowania szczegóły

X12 łącznik dekodowania wykonuje te zadania:

* Weryfikuje koperty przed handlowymi umowy z partnerem
* Sprawdza poprawność właściwości specyficzne dla partnerów i EDI
  * Sprawdzanie poprawności strukturalnych EDI i rozszerzony schemat sprawdzania poprawności
  * Sprawdzanie poprawności struktury koperty wymiany.
  * Sprawdzanie poprawności schematu envelope względem schematu kontroli.
  * Sprawdzanie poprawności schematu elementów dane zestawu transakcji względem schematu wiadomości.
  * EDI weryfikacji w przypadku elementów dane zestawu transakcji 
* Sprawdza, czy numery kontroli zestawu wymiany, grupy i transakcji nie są duplikatami
  * Sprawdza numer kontroli wymiany przed wymianę wcześniej odebrane.
  * Sprawdza, czy numer grupy kontroli względem innych numery kontroli grupy w wymiany.
  * Sprawdza, czy transakcja Ustaw numer kontroli względem innych numery kontroli zestawu transakcji w tej grupie.
* Dzieli wymiany w zestawy transakcji lub zachowuje całego wymiany:
  * Wymiany podziału jako zestawy transakcji - zawiesić zestawy transakcji o błędzie: wymiany podziałów do transakcji ustawia i analizuje każdego zestawu transakcji. 
  X12 akcji dekodowania danych wyjściowych zestawów tylko tych transakcji niepowodzenie sprawdzania poprawności do `badMessages`i ustawia transakcji pozostałe dane wyjściowe `goodMessages`.
  * Podziel wymiany jako zestawy transakcji - zawiesić wymiany na błąd: wymiany podziałów do transakcji ustawia i analizuje każdego zestawu transakcji. 
  Jeśli jeden lub więcej transakcji ustawia w wymiany wystąpi niepowodzenie weryfikacji, X12 akcji dekodowania danych wyjściowych ustawia wszystkich transakcji w tym wymiany do `badMessages`.
  * Zachowaj wymiany — zawiesza zestawy transakcji na błąd: Zachowaj wymiany i przetwarzanie całego wsadowej operacji wymiany. 
  X12 akcji dekodowania danych wyjściowych zestawów tylko tych transakcji niepowodzenie sprawdzania poprawności do `badMessages`i ustawia transakcji pozostałe dane wyjściowe `goodMessages`.
  * Zachowaj wymiany — zawiesza wymiany na błąd: Zachowaj wymiany i przetwarzanie całego wsadowej operacji wymiany. 
  Jeśli jeden lub więcej transakcji ustawia w wymiany wystąpi niepowodzenie weryfikacji, X12 akcji dekodowania danych wyjściowych ustawia wszystkich transakcji w tym wymiany do `badMessages`. 
* Generuje potwierdzenia techniczne i/lub funkcjonalne (jeśli jest skonfigurowane).
  * Generuje techniczne potwierdzenia wyniku weryfikacji nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzania nagłówka wymiany i przyczepy przez odbiornik. adres.
  * Generuje funkcjonalności potwierdzenia wyniku weryfikacji treści. Potwierdzenie funkcjonalności raporty każdego wystąpił błąd podczas przetwarzania odebranego dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") 

