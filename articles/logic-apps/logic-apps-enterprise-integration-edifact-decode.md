---
title: Dekodowanie komunikatów EDIFACT — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie potwierdzeń przy użyciu dekodera komunikatu EDIFACT do usługi Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 0e61501d-21a2-4419-8c6c-88724d346e81
ms.date: 01/27/2017
ms.openlocfilehash: ccad6eab68fff0891ba287a076692f9437495a4c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097831"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie komunikatów EDIFACT w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Za pośrednictwem łącznika komunikatu EDIFACT do zdekodowania można zweryfikować EDI i właściwości specyficzne dla partnerów, Podziel wymianę na zestawy transakcji lub zachować całą wymian i generowanie potwierdzeń dla przetwarzania transakcji. Aby użyć tego łącznika, łącznik należy dodać do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikatu EDIFACT do zdekodowania. 
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [Umowy EDIFACT](logic-apps-enterprise-integration-edifact.md) jest już zdefiniowany w ramach konta integracji

## <a name="decode-edifact-messages"></a>Dekodowanie komunikatów EDIFACT

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikatu EDIFACT do zdekodowania nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3. W polu wyszukiwania wprowadź "EDIFACT" jako filtr. Wybierz **Dekoduj komunikat EDIFACT**.
   
    ![Wyszukaj EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć.
   
    ![Tworzenie konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

4. Gdy skończysz, aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**. Szczegóły połączenia powinna wyglądać następująco:

    ![Szczegóły konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po utworzeniu połączenia, jak pokazano w poniższym przykładzie, wybierz komunikat pliku prostego EDIFACT do zdekodowania.

    ![utworzone połączenie konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Na przykład:

    ![Wybierz komunikat pliku prostego EDIFACT do zdekodowania](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Szczegóły dekodera EDIFACT

Łącznik dekodowania EDIFACT wykonuje następujące zadania: 

* Sprawdza poprawność koperty względem handlowymi umowy z partnerem.
* Rozstrzyga umowę, dopasowując kwalifikator nadawcy i identyfikator oraz kwalifikator odbiorcy i identyfikator.
* Dzieli wymiany na wiele transakcji, gdy wymiana ma więcej niż jedna transakcja oparte na umowie odbierania ustawień konfiguracji.
* Dezasembluje wymiany.
* Sprawdza poprawność EDI i właściwości specyficzne dla partnerów, w tym:
  * Sprawdzanie poprawności struktury koperty wymiany
  * Sprawdzanie poprawności schematu koperty względem schematu kontroli
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji względem schematu komunikatów
  * Walidacja EDI wykonywane na elementach danych zestawu transakcji
* Sprawdza, czy numerów kontrolnych zestawu wymiany, grupy i transakcji nie są duplikatami (jeśli jest skonfigurowane) 
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
* Generuje technicznych (kontrola) i/lub funkcjonalności potwierdzenia (jeśli jest skonfigurowane).
  * Potwierdzenie techniczne lub potwierdzenia CONTRL zgłasza wyniki syntaktycznych sprawdzanie pełną wymiany odebrane.
  * Potwierdzenie funkcjonalności potwierdza zaakceptować lub odrzucić odebrane wymiany lub grupy

## <a name="view-swagger-file"></a>Wyświetl plik struktury Swagger
Aby wyświetlić szczegóły struktury Swagger łącznika EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") 

