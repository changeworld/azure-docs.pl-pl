---
title: Komunikaty EDIFACT dla integracji B2B
description: Wymiana komunikatów EDIFACT w formacie EDI dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/26/2016
ms.openlocfilehash: 3ada12a0cde122fb78815a1d3241d8acb9da2580
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651461"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymiana komunikatów EDIFACT dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji dla przedsiębiorstw

Przed rozpoczęciem wymiany komunikatów EDIFACT dla usługi Azure Logic Apps należy utworzyć umowę EDIFACT i przechowywać tę umowę na koncie integracji. Oto kroki, jak utworzyć umowę EDIFACT.

> [!NOTE]
> Ta strona obejmuje funkcje EDIFACT dla usługi Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure  
* Co najmniej dwóch [partnerów,](logic-apps-enterprise-integration-partners.md) którzy są już zdefiniowani na koncie integracji

> [!NOTE]
> Podczas tworzenia umowy zawartość wiadomości, które otrzymujesz lub wysyłasz do i od partnera, musi być zgodna z typem umowy.

Po [utworzeniu konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [dodaniu partnerów](logic-apps-enterprise-integration-partners.md)można utworzyć umowę EDIFACT, wykonując następujące kroki.

## <a name="create-an-edifact-agreement"></a>Tworzenie umowy EDIFACT 

1. Zaloguj się do [Portalu Azure](https://portal.azure.com "Portal Azure"). 

2. W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź "integracja", a następnie wybierz **pozycję Konta integracji**.

   ![Znajdź swoje konto integracyjne](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Jeśli **wszystkie usługi** nie są wyświetlane, może być najpierw rozwiń menu. U góry zwiniętego menu wybierz pozycję **Pokaż etykiety tekstowe**.

3. W obszarze **Konta integracji**wybierz konto integracji, na którym chcesz utworzyć umowę.

   ![Wybierz konto integracji, gdzie ma utworzyć umowę](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Wybierz **opcję Umowy**. Jeśli nie masz kafelka Umowy, najpierw dodaj kafelek.   

   ![Wybieranie kafelka "Umowy"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stronie Umowy wybierz pozycję **Dodaj**.

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. W obszarze **Dodaj**wprowadź **nazwę** umowy. W przypadku **typu Umowy**wybierz pozycję **EDIFACT**. Wybierz **partnera hosta,** **tożsamość hosta,** **partnera gościa**i **tożsamość gościa** dla swojej umowy.

   ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Właściwość | Opis |
   | --- | --- |
   | Nazwa |Nazwa umowy |
   | Typ umowy | Powinien być EDIFACT |
   | Partner gospodarza |Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
   | Tożsamość hosta |Identyfikator partnera-hosta |
   | Partner gości |Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner-gość reprezentuje organizację, która prowadzi interesy z partnerem hostingowym. |
   | Tożsamość gościa |Identyfikator partnera gościa |
   | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich wiadomości otrzymanych przez umowę. |
   | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich wiadomości wysyłanych przez umowę. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurowanie sposobu obsługi odebranych wiadomości w umowie

Po ustawieniu właściwości umowy można skonfigurować sposób identyfikowania i obsługi przychodzących wiadomości otrzymanych od partnera za pośrednictwem tej umowy.

1. W obszarze **Dodaj**wybierz pozycję **Odbierz ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości można znaleźć w tabelach w tej sekcji.

   **Ustawienia odbierania** są podzielone na następujące sekcje: Identyfikatory, Potwierdzenie, Schematy, Numery kontroli, Sprawdzanie poprawności i Ustawienia wewnętrzne.

   ![Konfigurowanie "Ustawienia odbierania"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Po zakończeniu upewnij się, że ustawienia są zapisywane, wybierając przycisk **OK**.

Teraz umowa jest gotowa do obsługi przychodzących wiadomości zgodnych z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB6.1 (hasło referencyjne odbiorcy) |Wprowadź wartość alfanumeryczną w zakresie od 1 do 14 znaków. |
| UNB6.2 (kwalifikator referencyjny odbiorcy) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie dwoma znakami. |

### <a name="acknowledgments"></a>Podziękowania

| Właściwość | Opis |
| --- | --- |
| Odbiór wiadomości (CONTRL) |Zaznacz to pole wyboru, aby zwrócić potwierdzenie techniczne (CONTRL) do nadawcy wymiany. Potwierdzenie jest wysyłane do nadawcy wymiany na podstawie ustawień wysyłania dla umowy. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, aby zwrócić potwierdzenie funkcjonalne (CONTRL) nadawcy wymiany Potwierdzenie jest wysyłane do nadawcy wymiany na podstawie ustawień wysyłania dla umowy. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYP) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji wiadomości. (Minimum, jeden znak; maksimum, trzy znaki). |
| UNH2.3 (WYDANIE) |Wprowadź numer wydania wiadomości. (Minimum, jeden znak; maksimum, trzy znaki). |
| UNH2.5 (SKOJARZONY KOD) |Wprowadź przypisany kod. (Maksymalnie sześć znaków. Musi być alfanumeryczny). |
| UNG2.1 (Identyfikator nadawcy aplikacji) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 35 znakami. |
| UNG2.2 (KWALIFIKATOR KODU NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. |
| Schematu |Wybierz wcześniej przekazany schemat, którego chcesz użyć, ze skojarzonego konta integracji. |

### <a name="control-numbers"></a>Numery kontrolne

| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty numeru kontroli wymiany |Aby zablokować zduplikowane wymiany, wybierz tę właściwość. Jeśli ta opcja jest zaznaczona, akcja dekodowania EDIFACT sprawdza, czy numer kontroli wymiany (UNB5) dla odebranej wymiany nie jest zgodny z wcześniej przetworzonym numerem kontroli wymiany. Jeśli zostanie wykryte dopasowanie, wymiana nie jest przetwarzana. |
| Sprawdzanie duplikatów UNB5 co (dni) |Jeśli wybrano opcję niezezwolenia na zduplikowane numery kontroli wymiany, można określić liczbę dni, przez które należy wykonać kontrolę, podając odpowiednią wartość dla tego ustawienia. |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Aby zablokować wymiany z zduplikowanymi numerami kontrolnymi grupy (UNG5), wybierz tę właściwość. |
| Nie zezwalaj na duplikaty numerów kontroli zestawu transakcji |Aby zablokować wymiany z zduplikowanymi numerami kontroli zestawu transakcji (UNH1), wybierz tę właściwość. |
| Numer kontroli potwierdzenia EDIFACT |Aby wyznaczyć numery referencyjne zestawu transakcji do użycia w potwierdzeniu, wprowadź wartość prefiksu, zakres numerów referencyjnych i sufiks. |

### <a name="validation"></a>Sprawdzanie poprawności

Po zakończeniu każdego wiersza sprawdzania poprawności, inny jest automatycznie dodawany. Jeśli nie określisz żadnych reguł, sprawdzanie poprawności używa wiersza "Domyślne".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Wykonaj sprawdzanie poprawności EDI na typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości, puste elementy danych i separatory końcowe. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, sprawdzanie poprawności jest na wymagania elementu danych i dozwolone powtórzenia, wyliczenia i sprawdzanie poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj dodatkowe znaki wiodące lub końcowe zero i spacji. Nie usuwaj tych znaków. |
| Przycinanie zer wiodących/końcowych |Usuń znaki zerem i spacji. |
| Zasady separatora końcowego |Generowanie separatorów końcowych. <p>Wybierz **opcję Nie wolno** zakazać końcowych ograniczników i separatorów w odebranej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz **opcjonalnie,** aby akceptować wymiany z ogranicznikami i separatorami końcowymi lub bez. <p>Wybierz **opcję Obowiązkowe,** gdy odebrana wymiana musi mieć końcowe ograniczniki i separatory. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

| Właściwość | Opis |
| --- | --- |
| Tworzenie pustych znaczników XML, jeśli separatory końcowe są dozwolone |To pole wyboru należy zaznaczyć, aby nadawca wymiany zawierał puste znaczniki XML dla separatorów końcowych. |
| Split Interchange jako zestawy transakcji - wstrzymanie transakcji ustawia się na błąd|Analizuje każdą transakcję ustawioną w wymianie w oddzielnym dokumencie XML, stosując odpowiednią pulę do zestawu transakcji. Wstrzymaj tylko zestawy transakcji, które nie powiodą się. |
| Split Interchange jako zestawy transakcji - wstrzymanie wymiany w sprawie błędu|Analizuje każdą transakcję ustawioną w wymianie w oddzielnym dokumencie XML, stosując odpowiednią kopertę. Wstrzymaj całą wymianę, gdy co najmniej jedna transakcja ustawia się w weryfikacji awarii wymiany. | 
| Zachowaj wymianę - wstrzymanie zestawów transakcji na błąd |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Wstrzymaj tylko zestawy transakcji, które nie powiodą się, kontynuując przetwarzanie wszystkich innych zestawów transakcji. |
| Zachowaj wymianę - wstrzymanie wymiany w sprawie błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Wstrzymaj całą wymianę, gdy co najmniej jedna transakcja ustawia się w weryfikacji awarii wymiany. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurowanie sposobu, w jaki umowa wysyła wiadomości

Można skonfigurować sposób, w jaki ta umowa identyfikuje i obsługuje wychodzące wiadomości wysyłane do partnerów za pośrednictwem tej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Wyślij ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z Tobą wiadomości. Opisy właściwości można znaleźć w tabelach w tej sekcji.

    **Ustawienia wysyłania** są zorganizowane w następujące sekcje: Identyfikatory, Potwierdzenie, Schematy, Koperty, Zestawy znaków i separatory, Numery kontrolne i Weryfikacje.

    ![Konfigurowanie "Ustawienia wysyłania"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Po zakończeniu upewnij się, że ustawienia są zapisywane, wybierając przycisk **OK**.

Teraz umowa jest gotowa do obsługi wiadomości wychodzących zgodnych z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB1.2 (wersja składnia) |Wybierz wartość z **1** do **4**. |
| UNB2.3 (Adres odwrotnego routingu nadawcy) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami. |
| UNB3.3 (adres routingu wstecznego odbiorcy) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami. |
| UNB6.1 (hasło referencyjne odbiorcy) |Wprowadź wartość alfanumeryczną z co najmniej jednym i maksymalnie 14 znakami. |
| UNB6.2 (kwalifikator referencyjny odbiorcy) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie dwoma znakami. |
| UNB7 (identyfikator odwołania do aplikacji) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami |

### <a name="acknowledgment"></a>Potwierdzenia

| Właściwość | Opis |
| --- | --- |
| Odbiór wiadomości (CONTRL) |Zaznacz to pole wyboru, jeśli hostowany partner oczekuje potwierdzenia technicznego (CONTRL). To ustawienie określa, że hostowany partner, który wysyła wiadomość, żąda potwierdzenia od partnera gościa. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, jeśli hostowany partner oczekuje potwierdzenia funkcjonalności (CONTRL). To ustawienie określa, że hostowany partner, który wysyła wiadomość, żąda potwierdzenia od partnera gościa. |
| Generowanie pętli SG1/SG4 dla akceptowanych zestawów transakcji |Jeśli zdecydujesz się zażądać potwierdzenia funkcjonalnego, zaznacz to pole wyboru, aby wymusić generowanie pętli SG1/SG4 w funkcjonalnych potwierdzeniach CONTRL dla zaakceptowanych zestawów transakcji. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYP) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji wiadomości. |
| UNH2.3 (WYDANIE) |Wprowadź numer wydania wiadomości. |
| Schematu |Wybierz schemat do użycia. Schematy znajdują się na koncie integracji. Aby uzyskać dostęp do schematów, najpierw połącz konto integracji z aplikacją logiki. |

### <a name="envelopes"></a>Koperty

| Właściwość | Opis |
| --- | --- |
| UNB8 (kod priorytetu przetwarzania) |Wprowadź wartość alfabetyczną, która nie ma więcej niż jednego znaku. |
| UNB10 (Umowa komunikacyjna) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 40 znakami. |
| UNB11 (wskaźnik testu) |Zaznacz to pole wyboru, aby wskazać, że wygenerowana wymiana jest danymi testowymi |
| Zastosuj segment UNA (porada dotycząca ciągu serwisowego) |Zaznacz to pole wyboru, aby wygenerować segment UNA dla wymiany do wysłania. |
| Zastosuj segmenty UNG (nagłówek grupy funkcyjnej) |Zaznacz to pole wyboru, aby utworzyć segmenty grupowania w nagłówku grupy funkcjonalnej w wiadomościach wysyłanych do partnera gościa. Do tworzenia segmentów UNG używane są następujące wartości: <p>W przypadku **UNG1**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie sześcioma znakami. <p>W przypadku **UNG2.1**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 35 znakami. <p>W przypadku **UNG2.2**wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. <p>W przypadku **UNG3.1**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 35 znakami. <p>W przypadku **UNG3.2**wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. <p>W przypadku **UNG6**wprowadź wartość alfanumeryczną z co najmniej jednym i maksymalnie trzema znakami. <p>W przypadku **ung7.1**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie trzema znakami. <p>W przypadku **UNG7.2**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie trzema znakami. <p>W przypadku **UNG7.3**wprowadź wartość alfanumeryczną z co najmniej 1 znakiem i maksymalnie 6 znakami. <p>W przypadku **UNG8**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami. |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Poza zestawem znaków można wprowadzić inny zestaw ograniczników, które mają być używane dla każdego typu wiadomości. Jeśli zestaw znaków nie jest określony dla danego schematu wiadomości, używany jest domyślny zestaw znaków.

| Właściwość | Opis |
| --- | --- |
| UNB1.1 (identyfikator systemu) |Wybierz zestaw znaków EDIFACT, który ma być stosowany w zamian za wyjęcie. |
| Schemat |Wybierz schemat z listy rozwijanej. Po zakończeniu każdego wiersza nowy wiersz jest automatycznie dodawany. Dla wybranego schematu wybierz zestaw separatorów, którego chcesz użyć, na podstawie poniższych opisów separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator komponentów |Aby oddzielić złożone elementy danych, wprowadź pojedynczy znak. |
| Separator elementu danych |Aby oddzielić proste elementy danych w złożonych elementach danych, wprowadź pojedynczy znak. |
| Terminator segmentu |Aby wskazać koniec segmentu EDI, wprowadź pojedynczy znak. |
| Sufiks |Wybierz znak, który jest używany z identyfikatorem segmentu. Jeśli wyznaczysz sufiks, element danych terminatora segmentu może być pusty. Jeśli terminator segmentu pozostaje pusty, należy wyznaczyć sufiks. |

### <a name="control-numbers"></a>Numery kontrolne

| Właściwość | Opis |
| --- | --- |
| UNB5 (numer kontrolny wymiany) |Wprowadź prefiks, zakres wartości numeru formantu wymiany i sufiks. Wartości te są używane do generowania wymiany wychodzącej. Prefiks i sufiks są opcjonalne, podczas gdy numer kontrolny jest wymagany. Numer kontrolny jest zwiększany dla każdej nowej wiadomości; przedrostek i sufiks pozostają takie same. |
| UNG5 (numer kontrolny grupy) |Wprowadź prefiks, zakres wartości numeru formantu wymiany i sufiks. Wartości te są używane do generowania numeru kontroli grupy. Prefiks i sufiks są opcjonalne, podczas gdy numer kontrolny jest wymagany. Numer kontrolny jest zwiększany dla każdej nowej wiadomości, dopóki nie zostanie osiągnięta maksymalna wartość; przedrostek i sufiks pozostają takie same. |
| UNH1 (numer referencyjny nagłówka wiadomości) |Wprowadź prefiks, zakres wartości numeru formantu wymiany i sufiks. Wartości te są używane do generowania numeru referencyjnego nagłówka wiadomości. Prefiks i sufiks są opcjonalne, podczas gdy numer referencyjny jest wymagany. Numer referencyjny jest zwiększany dla każdej nowej wiadomości; przedrostek i sufiks pozostają takie same. |

### <a name="validation"></a>Sprawdzanie poprawności

Po zakończeniu każdego wiersza sprawdzania poprawności, inny jest automatycznie dodawany. Jeśli nie określisz żadnych reguł, sprawdzanie poprawności używa wiersza "Domyślne".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Wykonaj sprawdzanie poprawności EDI na typach danych zdefiniowanych przez właściwości EDI schematu, ograniczenia długości, puste elementy danych i separatory końcowe. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, sprawdzanie poprawności jest na wymagania elementu danych i dozwolone powtórzenia, wyliczenia i sprawdzanie poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj dodatkowe znaki wiodące lub końcowe zero i spacji. Nie usuwaj tych znaków. |
| Przycinanie zer wiodących/końcowych |Usuń znaki początkowe lub końcowe zero. |
| Zasady separatora końcowego |Generowanie separatorów końcowych. <p>Wybierz **opcję Nie wolno** zakazać końcowych ograniczników i separatorów w wysłanej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz **opcjonalne,** aby wysyłać wymiany z ogranicznikami i separatorami końcowymi lub bez. <p>Wybierz **opcję Obowiązkowe,** jeśli wysłana wymiana musi mieć końcowe ograniczniki i separatory. |

## <a name="find-your-created-agreement"></a>Znajdź utworzoną umowę

1.  Po zakończeniu ustawiania wszystkich właściwości umowy na stronie **Dodaj** wybierz przycisk **OK,** aby zakończyć tworzenie umowy i powrócić do konta integracyjnego.

    Nowo dodana umowa pojawi się teraz na liście **umów.**

2.  Umowy można również wyświetlić w omówienie konta integracji. W menu konta integracyjnego wybierz pozycję **Przegląd**, a następnie wybierz kafelek **Umowy.** 

    ![Wybieranie kafelka "Umowy"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/edifact/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)