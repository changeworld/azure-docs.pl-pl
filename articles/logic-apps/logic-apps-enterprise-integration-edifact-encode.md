---
title: Kodowanie komunikatów EDIFACT — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie kodu XML za pomocą kodera komunikatów EDIFACT w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.date: 01/27/2017
ms.openlocfilehash: 7396aee56acdf2476ed1bb3cc5a9909349662dc7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097814"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Kodowanie komunikatów EDIFACT w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Za pomocą łącznika komunikatu kodowanie EDIFACT weryfikowanie integracji EDI i właściwości specyficzne dla partnerów, generowania dokumentu XML dla każdego zestawu transakcji, a żądania potwierdzenia technicznych i/lub funkcjonalności potwierdzenia.
Aby użyć tego łącznika, łącznik należy dodać do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure. Konieczne jest posiadanie konta integracji do korzystania z łącznika komunikatu kodowanie EDIFACT. 
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji
* [Umowy EDIFACT](logic-apps-enterprise-integration-edifact.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-edifact-messages"></a>Kodowanie komunikatów EDIFACT

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik komunikatu kodowanie EDIFACT nie ma wyzwalacze, więc należy dodać wyzwalacza uruchamiającego twoją aplikację logiki, takich jak wyzwalacza żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź "EDIFACT" jako filtr. Wybierz opcję **Koduj jako komunikat EDIFACT według nazwy umowy** lub **Koduj jako komunikat EDIFACT według tożsamości**.
   
    ![Wyszukaj EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Jeśli wcześniej nie utworzono żadnych połączeń z kontem integracji, zostanie wyświetlony monit Utwórz teraz tego połączenia. Nazwij połączenie, a następnie wybierz konto integracji, w którym chcesz się połączyć.

    ![Utwórz połączenie konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konto integracji * |Wprowadź nazwę dla swojego konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby ukończyć tworzenie połączenia, wybierz opcję **Utwórz**.

    ![Szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Połączenie jest gotowy.

    ![utworzone połączenie konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koduj jako komunikat EDIFACT według nazwy umowy

Jeśli została wybrana opcja kodowanie komunikatów EDIFACT według nazwy umowy, otwórz **umowy nazw EDIFACT** listy, wprowadź lub wybierz nazwę umowy EDIFACT. Wprowadź komunikat XML do zakodowania.

![Wprowadź nazwę umowy EDIFACT i komunikat XML do zakodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koduj jako komunikat EDIFACT według tożsamości

Jeśli zdecydujesz się kodowanie komunikatów EDIFACT według tożsamości, wprowadź identyfikator nadawcy, Kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy zgodnie z konfiguracją umowy EDIFACT. Wybierz komunikat XML do zakodowania.

![Podaj tożsamości nadawcy i odbiorcy, wybierz komunikat XML do zakodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Szczegóły kodowanie komunikatów EDIFACT.

Łącznik kodowanie EDIFACT wykonuje następujące zadania: 

* Rozwiązanie umowy, dopasowując kwalifikator nadawcy & identyfikator i kwalifikator odbiorcy i identyfikator
* Serializuje wymiany EDI, konwersji wiadomości w formacie XML do zestawów transakcji EDI w wymianie.
* Stosuje segmentów nagłówka i elementu końcowego zestawu transakcji
* Generuje numer kontrolny wymiany, numer kontrolny grupy i numer kontrolny zestawu transakcji, dla każdego wychodzących wymiany
* Zastępuje separatory w danych ładunku
* Weryfikuje EDI i właściwości specyficzne dla partnerów
  * Weryfikacja schematu elementów danych zestawu transakcji względem schematu komunikatów.
  * Walidacja EDI wykonywane na elementach danych zestawu transakcji.
  * Rozszerzona Walidacja wykonywane na elementach danych zestawu transakcji
* Generuje dokument XML dla każdego zestawu transakcji.
* Żądania potwierdzenia technicznych i/lub funkcjonalnej (jeśli jest skonfigurowane).
  * Jako potwierdzenie technicznych komunikat CONTRL oznacza otrzymanie wymiany.
  * Jako potwierdzenie funkcjonalności komunikat CONTRL oznacza akceptacji lub odrzucenia odebranych wymiany, grupy lub komunikatu z listą błędów lub nieobsługiwanych funkcji

## <a name="view-swagger-file"></a>Wyświetl plik struktury Swagger
Aby wyświetlić szczegóły struktury Swagger łącznika EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw") 

