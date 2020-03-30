---
title: Dekodowanie wiadomości X12
description: Sprawdzanie poprawności edi i generowanie potwierdzeń za pomocą dekodera wiadomości X12 w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792377"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekodowanie wiadomości X12 w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack

Dzięki złączu wiadomości Dekodowanie X12 można zweryfikować kopertę względem umowy z partnerem handlowym, zweryfikować edi i właściwości specyficzne dla partnera, podzielić wymiany na zestawy transakcji lub zachować całe węzły przesiadkowe oraz wygenerować potwierdzenia w przypadku transakcji przetworzonych. Aby użyć tego łącznika, należy dodać łącznik do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* Konto platformy Azure; można utworzyć [darmowe konto](https://azure.microsoft.com/free)
* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Aby użyć łącznika wiadomości Dekoduj X12, musisz mieć konto integracji.
* Co najmniej dwóch [partnerów,](logic-apps-enterprise-integration-partners.md) którzy są już zdefiniowani na koncie integracji
* [Umowa X12,](logic-apps-enterprise-integration-x12.md) która jest już zdefiniowana na koncie integracyjnym

## <a name="decode-x12-messages"></a>Dekodowanie wiadomości X12

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik wiadomości Dekoduj X12 nie ma wyzwalaczy, więc należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W projektancie aplikacji logiki dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wpisz "x12" dla filtru. Wybierz **X12 - Dekodowanie wiadomości X12**.
   
    ![Wyszukaj "x12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Jeśli wcześniej nie utworzyłeś żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć. 

    ![Podaj szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Wymagane są właściwości z gwiazdką.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracyjnego. Upewnij się, że konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Po zakończeniu szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.
   
    ![szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po utworzeniu połączenia, jak pokazano w tym przykładzie, wybierz komunikat pliku płaskiego X12 do dekodowania.

    ![utworzone połączenie z kontem integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Przykład:

    ![Wybierz wiadomość o pliku płaskim X12 do dekodowania](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Rzeczywista zawartość wiadomości lub ładunek dla tablicy wiadomości, dobre lub złe, jest zakodowany base64. Dlatego należy określić wyrażenie, które przetwarza tę zawartość.
   > Oto przykład, który przetwarza zawartość jako XML, który można wprowadzić w widoku kodu lub za pomocą konstruktora wyrażeń w projektancie.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Przykład zawartości](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Szczegóły dotyczące dekodowania X12

Łącznik Dekodowania X12 wykonuje następujące zadania:

* Weryfikuje kopertę przed umową z partnerem handlowym
* Sprawdza poprawność właściwości EDI i specyficzne dla partnera
  * Walidacja strukturalna EDI i rozszerzona walidacja schematu
  * Walidacja struktury koperty interchange.
  * Sprawdzanie poprawności schematu koperty względem schematu formantu.
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji względem schematu wiadomości.
  * Sprawdzanie poprawności EDI wykonywane na elementach danych zestawu transakcji 
* Sprawdza, czy numery kontroli wymiany, grupy i zestawu transakcji nie są duplikatami
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
* Generuje potwierdzenie techniczne i/lub funkcjonalne (jeśli jest skonfigurowane).
  * Potwierdzenie techniczne generuje w wyniku sprawdzania poprawności nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzania nagłówka wymiany i przyczepy przez odbiornik adresowy.
  * Potwierdzenie funkcjonalności generuje w wyniku sprawdzania poprawności treści. Potwierdzenie funkcjonalne zgłasza każdy błąd napotkany podczas przetwarzania odebranego dokumentu

## <a name="view-the-swagger"></a>Zobacz łup
Zobacz [szczegóły swagger](/connectors/x12/). 

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie") 

