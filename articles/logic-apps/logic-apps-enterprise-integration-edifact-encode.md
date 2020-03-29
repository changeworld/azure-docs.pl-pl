---
title: Kodowanie komunikatów EDIFACT
description: Sprawdzanie poprawności edi i generowanie kodu XML za pomocą kodera komunikatów EDIFACT dla aplikacji Logika Azure z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 257cbd0b1a68ddd2b16235e6f8dec5d5b0eb10e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790647"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Kodowanie komunikatów EDIFACT dla aplikacji logiki azure za pomocą pakietu Enterprise Integration Pack

Za pomocą łącznika komunikatu Encode EDIFACT można sprawdzić poprawność właściwości EDI i właściwości specyficzne dla partnera, wygenerować dokument XML dla każdego zestawu transakcji i zażądać potwierdzenia technicznego, potwierdzenia funkcjonalnego lub obu tych właściwości.
Aby użyć tego łącznika, należy dodać łącznik do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* Konto platformy Azure; można utworzyć [darmowe konto](https://azure.microsoft.com/free)
* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Aby użyć łącznika komunikatu Encode EDIFACT, musisz mieć konto integracji. 
* Co najmniej dwóch [partnerów,](logic-apps-enterprise-integration-partners.md) którzy są już zdefiniowani na koncie integracji
* [Umowa EDIFACT,](logic-apps-enterprise-integration-edifact.md) która jest już zdefiniowana na koncie integracyjnym

## <a name="encode-edifact-messages"></a>Kodowanie komunikatów EDIFACT

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikatu Encode EDIFACT nie ma wyzwalaczy, więc należy dodać wyzwalacz do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W projektancie aplikacji logiki dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wpisz "EDIFACT" jako filtr. Wybierz opcję **Zakoduj wiadomość EDIFACT według nazwy umowy** lub **zakoduj do wiadomości EDIFACT według tożsamości**.
   
    ![szukaj EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Jeśli wcześniej nie utworzyłeś żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć.

    ![tworzenie połączenia z kontem integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Wymagane są właściwości z gwiazdką.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracyjnego. Upewnij się, że konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Po zakończeniu szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.

    ![szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Połączenie zostanie utworzone.

    ![utworzone połączenie z kontem integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kodowanie komunikatu EDIFACT według nazwy umowy

Jeśli zdecydujesz się zakodować komunikaty EDIFACT według nazwy umowy, otwórz listę **umowy Nazwa EDIFACT,** wprowadź lub wybierz nazwę umowy EDIFACT. Wprowadź komunikat XML do zakodowania.

![Wprowadź nazwę umowy EDIFACT i komunikat XML do kodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kodowanie wiadomości EDIFACT według tożsamości

Jeśli zdecydujesz się zakodować komunikaty EDIFACT według tożsamości, wprowadź identyfikator nadawcy, kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy skonfigurowany w umowie EDIFACT. Wybierz komunikat XML do zakodowania.

![Podaj tożsamości nadawcy i odbiorcy, wybierz komunikat XML do zakodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT Szczegóły kodowania

Łącznik Encode EDIFACT wykonuje następujące zadania: 

* Rozwiąż umowę, dopasowując kwalifikator nadawcy & identyfikator i kwalifikator odbiorcy oraz identyfikator
* Serializuje wymianę EDI, konwertując wiadomości zakodowane w formacie XML na zestawy transakcji EDI w wymianie.
* Stosuje segmenty nagłówka i zwiastuna zestawu transakcji
* Generuje numer kontroli wymiany, numer kontroli grupy i numer kontroli zestawu transakcji dla każdej wychodzącej wymiany
* Zastępuje separatory w danych ładunku
* Sprawdza poprawność właściwości EDI i specyficzne dla partnera
  * Sprawdzanie poprawności schematu elementów danych zestawu transakcji względem schematu wiadomości.
  * Sprawdzanie poprawności EDI wykonywane na elementach danych zestawu transakcji.
  * Rozszerzona sprawdzanie poprawności wykonywane na elementach danych zestawu transakcji
* Generuje dokument XML dla każdego zestawu transakcji.
* Żąda potwierdzenia technicznego i/lub funkcjonalnego (jeśli jest skonfigurowany).
  * Jako potwierdzenie techniczne komunikat CONTRL wskazuje odbiór wymiany.
  * Jako potwierdzenie funkcjonalne komunikat CONTRL wskazuje akceptację lub odrzucenie odebranej wymiany, grupy lub wiadomości, z listą błędów lub nieobsługiwałem funkcji

## <a name="view-swagger-file"></a>Wyświetl plik Swagger
Aby wyświetlić szczegóły Swagger dla złącza EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o pakiecie integracji dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o pakiecie integracji w przedsiębiorstwie") 

