---
title: Kodowanie wiadomości X12
description: Sprawdzanie poprawności edi i konwertowanie wiadomości zakodowanych w formacie XML za pomocą kodera wiadomości X12 w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792351"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kodowanie wiadomości X12 w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack

Za pomocą łącznika wiadomości Encode X12 można sprawdzić poprawność właściwości EDI i właściwości specyficzne dla partnera, konwertować wiadomości zakodowane w formacie XML na zestawy transakcji EDI w wymianie i zażądać potwierdzenia technicznego, potwierdzenia funkcjonalnego lub obu tych.
Aby użyć tego łącznika, należy dodać łącznik do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* Konto platformy Azure; można utworzyć [darmowe konto](https://azure.microsoft.com/free)
* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Aby użyć łącznika wiadomości Encode X12, musisz mieć konto integracji.
* Co najmniej dwóch [partnerów,](logic-apps-enterprise-integration-partners.md) którzy są już zdefiniowani na koncie integracji
* [Umowa X12,](logic-apps-enterprise-integration-x12.md) która jest już zdefiniowana na koncie integracyjnym

## <a name="encode-x12-messages"></a>Kodowanie wiadomości X12

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik wiadomości Encode X12 nie ma wyzwalaczy, więc należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W projektancie aplikacji logiki dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wpisz "x12" dla filtru. Wybierz **x12 - Kodowanie do wiadomości X12 według nazwy umowy** lub **X12 - Kodowanie do wiadomości X12 przez tożsamości**.
   
    ![Wyszukaj "x12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Jeśli wcześniej nie utworzyłeś żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć. 
   
    ![połączenie z kontem integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Wymagane są właściwości z gwiazdką.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracyjnego. Upewnij się, że konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Po zakończeniu szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.

    ![utworzone połączenie z kontem integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Połączenie zostanie utworzone.

    ![szczegóły połączenia konta integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Kodowanie wiadomości X12 według nazwy umowy

Jeśli wybrano kodowanie wiadomości X12 według nazwy umowy, otwórz listę **Umowy Nazwa X12,** wprowadź lub wybierz istniejącą umowę X12. Wprowadź komunikat XML do zakodowania.

![Wprowadź nazwę umowy X12 i komunikat XML do kodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Kodowanie wiadomości X12 według tożsamości

Jeśli zdecydujesz się zakodować wiadomości X12 według tożsamości, wprowadź identyfikator nadawcy, kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy skonfigurowany w umowie X12. Wybierz komunikat XML do zakodowania.
   
![Podaj tożsamości nadawcy i odbiorcy, wybierz komunikat XML do zakodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 Szczegóły kodowania

Łącznik X12 Encode wykonuje następujące zadania:

* Rozwiązanie umowy przez dopasowanie właściwości kontekstu nadawcy i odbiorcy.
* Serializuje wymianę EDI, konwertując wiadomości zakodowane w formacie XML na zestawy transakcji EDI w wymianie.
* Stosuje segmenty nagłówka i zwiastuna zestawu transakcji
* Generuje numer kontroli wymiany, numer kontroli grupy i numer kontroli zestawu transakcji dla każdej wychodzącej wymiany
* Zastępuje separatory w danych ładunku
* Sprawdza poprawność właściwości EDI i specyficzne dla partnera
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji względem komunikatu Schemat
  * Sprawdzanie poprawności EDI wykonywane na elementach danych zestawu transakcji.
  * Rozszerzona sprawdzanie poprawności wykonywane na elementach danych zestawu transakcji
* Żąda potwierdzenia technicznego i/lub funkcjonalnego (jeśli jest skonfigurowany).
  * Potwierdzenie techniczne generuje w wyniku sprawdzania poprawności nagłówka. Potwierdzenie techniczne informuje o stanie przetwarzania nagłówka wymiany i przyczepy przez odbiornik adresowy
  * Potwierdzenie funkcjonalności generuje w wyniku sprawdzania poprawności treści. Potwierdzenie funkcjonalne zgłasza każdy błąd napotkany podczas przetwarzania odebranego dokumentu

## <a name="view-the-swagger"></a>Zobacz łup
Zobacz [szczegóły swagger](/connectors/x12/). 

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie") 

