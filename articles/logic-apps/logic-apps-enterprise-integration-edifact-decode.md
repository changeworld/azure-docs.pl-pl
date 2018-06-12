---
title: Dekodowanie EDIFACT wiadomości - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie potwierdzeń z dekodera komunikat EDIFACT w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: bfb64b4abfca6e2a113489b79405b5df2b53c049
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299158"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie EDIFACT wiadomości dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw

Łącznik EDIFACT dekodowanie komunikatu można zweryfikować właściwości specyficzne dla partnerów i EDI, podzielić wymianę w zestawy transakcji lub zachować całą wymianę i generowanie potwierdzeń przetworzonych transakcji. Aby użyć tego łącznika, należy dodać łącznika do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji do używania łącznika EDIFACT dekodowanie komunikatu. 
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [Umowy EDIFACT](logic-apps-enterprise-integration-edifact.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-edifact-messages"></a>Dekodowanie EDIFACT wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Dekodowanie EDIFACT łącznika komunikatu nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3. W polu wyszukiwania wprowadź "EDIFACT" jako filtr. Wybierz **zdekodować komunikatu EDIFACT**.
   
    ![Wyszukiwanie EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć.
   
    ![Tworzenie konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

4. Gdy wszystko będzie gotowe, aby zakończyć tworzenie połączenia, wybierz **Utwórz**. Szczegóły połączenia powinien wyglądać podobnie jak w tym przykładzie:

    ![Szczegóły konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, wybierz komunikat pliku prostego EDIFACT zdekodować.

    ![utworzone połączenie konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Na przykład:

    ![Wybierz EDIFACT pliku prostego komunikatu dekodowania](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Szczegóły dekodera EDIFACT

Łącznik dekodowania EDIFACT wykonuje te zadania: 

* Weryfikuje koperty przed handlowymi umowy z partnerem.
* Rozpoznaje umowy przez dopasowanie kwalifikator nadawcy i identyfikator oraz kwalifikator odbiornik i identyfikator.
* Dzieli wymiany na wiele transakcji, gdy wymiany ma więcej niż jednej transakcji na podstawie umowy odbierać konfigurację ustawień.
* Rozkłada wymiany.
* Weryfikuje EDI i właściwości specyficzne dla partnera, w tym:
  * Sprawdzanie poprawności struktury koperty wymiany
  * Sprawdzanie poprawności schematu envelope względem schematu kontroli
  * Sprawdzanie poprawności schematu elementów danych transakcji zestawu względem schematu komunikatów
  * EDI weryfikacji w przypadku elementów dane zestawu transakcji
* Sprawdza, czy numery kontroli zestawu wymiany, grupy i transakcji nie są duplikatami (jeśli jest skonfigurowane) 
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
* Generuje Technical (kontrola) i/lub funkcjonalności potwierdzenia (jeśli jest skonfigurowane).
  * Potwierdzenia techniczne lub potwierdzenia CONTRL zgłasza wyniki syntaktycznych wyboru zakończenie Odebrano wymiany.
  * Potwierdzenie funkcjonalności potwierdza zaakceptować lub odrzucić odebrane wymiany lub grupy

## <a name="view-swagger-file"></a>Plik struktury Swagger widoku
Aby wyświetlić szczegóły Swagger EDIFACT łącznika, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") 

