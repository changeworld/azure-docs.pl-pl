---
title: Dekodowanie komunikatów EDIFACT
description: Sprawdzanie poprawności edi i generowanie potwierdzeń za pomocą dekodera komunikatów EDIFACT dla aplikacji Logika Azure z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 78c0d20c0f32a6d63d134e958b30d38fe11fcc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790671"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie komunikatów EDIFACT dla aplikacji logiki azure za pomocą pakietu Enterprise Integration Pack

Za pomocą łącznika komunikatu Dekodowanie EDIFACT można sprawdzać poprawność właściwości EDI i właściwości specyficzne dla partnera, dzielić wymiany na zestawy transakcji lub zachowywać całe węzły przesiadkowe oraz generować potwierdzenia dla przetworzonych transakcji. Aby użyć tego łącznika, należy dodać łącznik do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* Konto platformy Azure; można utworzyć [darmowe konto](https://azure.microsoft.com/free)
* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Aby użyć łącznika komunikatu Dekodowanie EDIFACT, musisz mieć konto integracji. 
* Co najmniej dwóch [partnerów,](logic-apps-enterprise-integration-partners.md) którzy są już zdefiniowani na koncie integracji
* [Umowa EDIFACT,](logic-apps-enterprise-integration-edifact.md) która jest już zdefiniowana na koncie integracyjnym

## <a name="decode-edifact-messages"></a>Dekodowanie komunikatów EDIFACT

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikatu Dekodowanie EDIFACT nie ma wyzwalaczy, więc należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W projektancie aplikacji logiki dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3. W polu wyszukiwania wpisz "EDIFACT" jako filtr. Wybierz **opcję Dekoduj komunikat EDIFACT**.
   
    ![szukaj EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Jeśli wcześniej nie utworzyłeś żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć.
   
    ![tworzenie konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Wymagane są właściwości z gwiazdką.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracyjnego. Upewnij się, że konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

4. Po zakończeniu tworzenia połączenia wybierz pozycję **Utwórz**. Szczegóły połączenia powinny wyglądać podobnie do tego przykładu:

    ![szczegóły konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po utworzeniu połączenia, jak pokazano w tym przykładzie, wybierz płaski komunikat pliku EDIFACT do dekodowania.

    ![utworzone połączenie z kontem integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Przykład:

    ![Wybierz płaski komunikat pliku EDIFACT do dekodowania](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Detale dekodera EDIFACT

Łącznik Dekodowania EDIFACT wykonuje następujące zadania: 

* Weryfikuje kopertę przed umową z partnerem handlowym.
* Rozwiązuje umowę, dopasowując kwalifikator nadawcy & identyfikator i kwalifikator odbiorcy & identyfikator.
* Dzieli wymianę na wiele transakcji, gdy wymiana ma więcej niż jedną transakcję na podstawie konfiguracji ustawień odbioru umowy.
* Dezasembles wymiany.
* Sprawdza poprawność właściwości EDI i specyficzne dla partnera, w tym:
  * Walidacja struktury kopert wymiany
  * Sprawdzanie poprawności schematu koperty względem schematu formantu
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji względem schematu wiadomości
  * Sprawdzanie poprawności EDI wykonywane na elementach danych zestawu transakcji
* Sprawdza, czy numery kontroli wymiany, grupy i zestawu transakcji nie są duplikatami (jeśli są skonfigurowane) 
  * Sprawdza numer kontroli wymiany na stosunku do wcześniej odebranych węzłów przesiadkowych. 
  * Sprawdza numer kontrolny grupy pod względem innych numerów kontrolnych grupy w wymianie. 
  * Sprawdza numer kontroli zestawu transakcji względem innych numerów kontroli zestawu transakcji w tej grupie.
* Dzieli wymianę na zestawy transakcji lub zachowuje całą wymianę:
  * Podziel interchange jako zestawy transakcji - wstrzymanie transakcji ustawia się na błąd: Dzieli wymiany na zestawy transakcji i analizuje każdy zestaw transakcji. 
  Akcja Dekodowania X12 wyprowadza tylko te `badMessages`zestawy transakcji, które nie uwzględniają poprawności, i wyprowadza pozostałe transakcje na `goodMessages`.
  * Split Interchange jako zestawy transakcji - zawiesić wymianę na błąd: Dzieli wymiany na zestawy transakcji i analizuje każdy zestaw transakcji. 
  Jeśli co najmniej jeden zestaw transakcji w weryfikacji wymiany zakończy się niepowodzeniem, akcja Dekodowania X12 wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages`.
  * Zachowaj interchange - zawiesić zestawy transakcji na błąd: Zachowaj wymiany i przetwarzania całej wymiany partii. 
  Akcja Dekodowania X12 wyprowadza tylko te `badMessages`zestawy transakcji, które nie uwzględniają poprawności, i wyprowadza pozostałe transakcje na `goodMessages`.
  * Zachowaj wymianę - wstrzymaj wymianę na błąd: Zachowaj wymianę i przetwórz całą wymianę wsadową. 
  Jeśli co najmniej jeden zestaw transakcji w weryfikacji wymiany zakończy się niepowodzeniem, akcja Dekodowania X12 wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages`.
* Generuje potwierdzenie techniczne (formantu) i/lub funkcjonalne (jeśli jest skonfigurowane).
  * Potwierdzenie techniczne lub CONTRL ACK raportuje wyniki syntaktycznej kontroli pełnej otrzymanej wymiany.
  * Potwierdzenie funkcjonalne potwierdza akceptację lub odrzucenie odebranej wymiany lub grupy

## <a name="view-swagger-file"></a>Wyświetl plik Swagger
Aby wyświetlić szczegóły Swagger dla złącza EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie") 

