---
title: Kodowanie wiadomości EDIFACT - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Sprawdź poprawność EDI i generowanie kodu XML za pomocą kodera wiadomości EDIFACT w pakiet integracyjny dla przedsiębiorstw dla usługi Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: eaad2561254d858af99f06e576d67c05838e1220
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299896"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Kodowanie wiadomości EDIFACT dla usługi Azure Logic Apps z pakiet integracyjny dla przedsiębiorstw

Łącznik EDIFACT kodowania komunikatu weryfikacji EDI i właściwości specyficzne dla partnera, generowanie dokumentu XML dla każdego zestawu transakcji, a żądania potwierdzenia technicznych i funkcjonalności potwierdzenia.
Aby użyć tego łącznika, należy dodać łącznika do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* Konto platformy Azure; można utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure. Musi mieć konto integracji do używania łącznika EDIFACT kodowania komunikatu. 
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji
* [Umowy EDIFACT](logic-apps-enterprise-integration-edifact.md) jest już zdefiniowany w ramach konta integracji

## <a name="encode-edifact-messages"></a>Kodowanie EDIFACT wiadomości

1. [Tworzenie aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Kodowanie EDIFACT łącznika komunikatu nie ma wyzwalaczy, dlatego należy dodać wyzwalacza do uruchamiania aplikacji logiki, takich jak wyzwalacz żądania. W Projektancie aplikacji logiki dodać wyzwalacza, a następnie dodać do aplikacji logiki akcję.

3.  W polu wyszukiwania wprowadź "EDIFACT" jako filtr. Wybierz opcję **kodowania komunikatu EDIFACT przez Nazwa umowy** lub **Koduj EDIFACT komunikat przez tożsamości**.
   
    ![Wyszukiwanie EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Jeśli wcześniej nie utworzono wszystkie połączenia z kontem integracji, zostanie wyświetlony monit o utworzyć teraz tego połączenia. Nazwa połączenia, a następnie wybierz konta integracji, na którym chcesz się połączyć.

    ![Tworzenie połączenia konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Właściwości oznaczone gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę połączenia. |
    | Konta integracji * |Wprowadź nazwę konta integracji. Upewnij się, że integracja aplikacji logiki i konta znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinien wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz **Utwórz**.

    ![Szczegóły połączenia konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Utworzono połączenie.

    ![utworzone połączenie konta integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Kodowanie komunikatu EDIFACT przez Nazwa umowy

Jeśli wybrano opcję kodowanie wiadomości EDIFACT według nazwy umowy, otwórz **umowy nazwa EDIFACT** listy, wprowadź lub wybierz nazwę umowy EDIFACT. Wprowadź komunikat XML do kodowania.

![Wprowadź nazwę umowy EDIFACT i wiadomości XML do kodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Kodowanie komunikatu EDIFACT przez tożsamości

Jeśli wybierzesz kodowanie wiadomości EDIFACT przez tożsamości, wprowadź identyfikator nadawcy, Kwalifikator nadawcy, odbiorcy identyfikator i kwalifikator odbiornika zgodnie z konfiguracją umowy EDIFACT. Wybierz wiadomości XML do kodowania.

![Podaj tożsamości dla nadawcy i odbiorcy, wybierz wiadomości XML do kodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Szczegóły EDIFACT kodowania

Łącznik kodowania EDIFACT wykonuje te zadania: 

* Rozwiązać przez dopasowanie kwalifikator nadawcy & identyfikator i odbiornika kwalifikator identyfikator umowy
* Serializuje wymiany EDI, konwertowanie wiadomości w formacie XML w EDI zestawy transakcji w wymiany.
* Stosuje zestaw transakcji nagłówka i przyczepy segmentów
* Generuje numer formantu wymiany, numer kontroli grupy i numer kontroli zestawu transakcji, każdy wychodzący wymiany
* Zastępuje separatorów w danych ładunku
* Sprawdza poprawność właściwości specyficzne dla partnerów i EDI
  * Sprawdzanie poprawności schematu elementów dane zestawu transakcji względem schematu wiadomości.
  * EDI Weryfikacja w przypadku elementów dane zestawu transakcji.
  * Rozszerzonej weryfikacji w przypadku elementów dane zestawu transakcji
* Generuje dokument XML dla każdego zestawu transakcji.
* Żądania potwierdzenia techniczne i/lub funkcjonalne (jeśli jest skonfigurowane).
  * Jako potwierdzenie technicznych komunikat CONTRL oznacza otrzymania wymiany.
  * Jako potwierdzenie funkcjonalności komunikat CONTRL oznacza akceptacji lub odrzucenia wymiany stwierdzono, grupy lub wiadomości z listą błędów lub nieobsługiwanych funkcji

## <a name="view-swagger-file"></a>Plik struktury Swagger widoku
Aby wyświetlić szczegóły Swagger EDIFACT łącznika, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw") 

