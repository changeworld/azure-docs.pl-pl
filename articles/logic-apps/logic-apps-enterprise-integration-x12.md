---
title: Komunikaty X12 dla integracji B2B
description: Komunikaty Programu Exchange X12 w formacie EDI dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: cbf0a1f033ddafc68debab8de26dff29d73cc98e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651478"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Komunikaty programu Exchange X12 dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji dla przedsiębiorstw

Aby można było wymienić komunikaty X12 dla usługi Azure Logic Apps, należy utworzyć umowę X12 i przechowywać tę umowę na koncie integracji. Oto kroki dotyczące tworzenia umowy X12.

> [!NOTE]
> Ta strona obejmuje funkcje X12 dla usługi Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto potrzebne przedmioty:

* [Konto integracji,](logic-apps-enterprise-integration-create-integration-account.md) które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure
* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) zdefiniowanych na koncie integracji i skonfigurowanych przy identyfikowatach X12 w obszarze **Tożsamości biznesowe**    
* Wymagany [schemat,](../logic-apps/logic-apps-enterprise-integration-schemas.md) który można przesłać na konto integracji

Po [utworzeniu konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [dodać partnerów](logic-apps-enterprise-integration-partners.md)i mieć [schemat,](../logic-apps/logic-apps-enterprise-integration-schemas.md) który chcesz użyć, można utworzyć umowę X12, wykonując następujące kroki.

## <a name="create-an-x12-agreement"></a>Tworzenie umowy X12

1. Zaloguj się do [Portalu Azure](https://portal.azure.com "Portal Azure"). 

2. Z głównego menu platformy Azure wybierz pozycję **Wszystkie usługi**. 
   W polu wyszukiwania wprowadź "integracja", a następnie wybierz **pozycję Konta integracji**.  

   ![Znajdź swoje konto integracyjne](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Jeśli **wszystkie usługi** nie są wyświetlane, może być najpierw rozwiń menu. U góry zwiniętego menu wybierz polecenie **Pokaż menu**.

3. W obszarze **Konta integracji**wybierz konto integracji, na którym chcesz dodać umowę.

   ![Wybierz konto integracji, gdzie ma utworzyć umowę](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Wybierz **opcję Przegląd**, a następnie wybierz kafelek **Umowy.** 
   Jeśli nie masz kafelka Umowy, najpierw dodaj kafelek. 

   ![Wybieranie kafelka "Umowy"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. W obszarze **Umowy**wybierz pozycję **Dodaj**.

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. W obszarze **Dodaj**wprowadź **nazwę** umowy. 
   Dla typu umowy wybierz **X12**. 
   Wybierz **partnera hosta,** **tożsamość hosta,** **partnera gościa**i **tożsamość gościa** dla swojej umowy. 
   Aby uzyskać więcej szczegółów właściwości, zobacz tabelę w tym kroku.

    ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Właściwość | Opis |
    | --- | --- |
    | Nazwa |Nazwa umowy |
    | Typ umowy | Powinien być X12 |
    | Partner gospodarza |Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
    | Tożsamość hosta |Identyfikator partnera-hosta |
    | Partner gości |Umowa wymaga zarówno partnera gospodarza, jak i gościa. Partner-gość reprezentuje organizację, która prowadzi interesy z partnerem hostingowym. |
    | Tożsamość gościa |Identyfikator partnera gościa |
    | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich wiadomości otrzymanych przez umowę. |
    | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich wiadomości wysyłanych przez umowę. |  

   > [!NOTE]
   > Rozwiązanie umowy X12 zależy od dopasowania kwalifikator nadawcy i identyfikator i kwalifikator odbiorcy i identyfikator zdefiniowany w wiadomości partnera i przychodzącej. Jeśli te wartości zmienią się dla partnera, zaktualizuj również umowę.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurowanie sposobu obsługi odebranych wiadomości w umowie

Po ustawieniu właściwości umowy można skonfigurować sposób identyfikowania i obsługi przychodzących wiadomości otrzymanych od partnera za pośrednictwem tej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Odbierz ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości można znaleźć w tabelach w tej sekcji.

    **Ustawienia odbierania** są podzielone na następujące sekcje: Identyfikatory, Potwierdzenie, Schematy, Koperty, Numery kontrolne, Weryfikacje i Ustawienia wewnętrzne.

2. Po zakończeniu upewnij się, że ustawienia są zapisywane, wybierając przycisk **OK**.

Teraz umowa jest gotowa do obsługi przychodzących wiadomości zgodnych z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

![Ustawianie właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Właściwość | Opis |
| --- | --- |
| ISA1 (Kwalifikator autoryzacji) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Element opcjonalny. Wprowadź wartość informacji o autoryzacji. Jeśli wartość wprowadzona dla ISA1 jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| ISA3 (kwalifikator zabezpieczeń) |Wybierz wartość kwalifikator zabezpieczeń z listy rozwijanej. |
| ISA4 |Element opcjonalny. Wprowadź wartość informacje o zabezpieczeniach. Jeśli wartość wprowadzona dla ISA3 jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |

### <a name="acknowledgment"></a>Potwierdzenia

![Ustawianie właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Właściwość | Opis |
| --- | --- |
| OCZEKUJE SIĘ TA1 |Zwraca potwierdzenie techniczne nadawcy wymiany |
| ŚT oczekiwano |Zwraca potwierdzenie funkcjonalne do nadawcy wymiany. Następnie wybierz, czy chcesz potwierdzenia 997 czy 999 na podstawie wersji schematu |
| Dołącz pętlę AK2/IK2 |Umożliwia generowanie pętli AK2 w potwierdzeń funkcjonalnych dla akceptowanych zestawów transakcji |

### <a name="schemas"></a>Schematy

Wybierz schemat dla każdego typu transakcji (ST1) i aplikacji nadawcy (GS2). Potok odbierania dezażembles wiadomości przychodzącej przez dopasowanie wartości dla ST1 i GS2 w wiadomości przychodzącej z wartościami, które można ustawić w tym miejscu i schemat wiadomości przychodzącej ze schematem, który można ustawić w tym miejscu.

![Wybierz schemat](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz wersję X12 |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| Aplikacja nadawcy (GS02) |Wybierz aplikację nadawcy |
| Schemat |Wybierz plik schematu, którego chcesz użyć. Schematy są dodawane do konta integracji. |

> [!NOTE]
> Skonfiguruj wymagany [schemat,](../logic-apps/logic-apps-enterprise-integration-schemas.md) który jest przekazytywał do [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określanie separatora w zestawie transakcji: wybierz pozycję Standardowy identyfikator lub separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separator do użycia w zestawie transakcji: <p>Wybierz **standardowy identyfikator,** aby użyć kropki (.) dla notacji dziesiętnej, a nie dziesiętnej notacji dokumentu przychodzącego w potoku odbioru EDI. <p>Wybierz **separator powtórzeń,** aby określić separator dla powtarzających się wystąpień prostego elementu danych lub powtarzanej struktury danych. Na przykład zazwyczaj karat (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatu. |

### <a name="control-numbers"></a>Numery kontrolne

![Wybieranie sposobu obsługi duplikatów numerów sterujących](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty numeru kontroli wymiany |Blokowanie zduplikowanych węzłów przesiadkowych. Sprawdza numer kontroli wymiany (ISA13) dla odebranego numeru kontroli wymiany. Jeśli zostanie wykryte dopasowanie, potok odbierania nie przetwarza wymiany. Można określić liczbę dni na wykonanie czeku, podając wartość *sprawdź dla duplikatów ISA13 co (dni)*. |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Blokuj wymiany ze zduplikowanymi numerami kontrolnymi grupy. |
| Nie zezwalaj na duplikaty numerów kontroli zestawu transakcji |Blokuj wymiany z zduplikowanymi numerami kontroli zestawu transakcji. |

### <a name="validation"></a>Sprawdzanie poprawności

![Ustawianie właściwości sprawdzania poprawności odebranych komunikatów](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po zakończeniu każdego wiersza sprawdzania poprawności, inny jest automatycznie dodawany. Jeśli nie określisz żadnych reguł, sprawdzanie poprawności używa wiersza "Domyślne".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Wykonaj sprawdzanie poprawności EDI na typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości, puste elementy danych i separatory końcowe. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, sprawdzanie poprawności jest na wymagania elementu danych i dozwolone powtórzenia, wyliczenia i sprawdzanie poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj dodatkowe znaki wiodące lub końcowe zero i spacji. Nie usuwaj tych znaków. |
| Przycinanie zer wiodących/końcowych |Usuń znaki zerem i spacji. |
| Zasady separatora końcowego |Generowanie separatorów końcowych. <p>Wybierz **opcję Nie wolno** zakazać końcowych ograniczników i separatorów w odebranej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz **opcjonalnie,** aby akceptować wymiany z ogranicznikami i separatorami końcowymi lub bez. <p>Wybierz **obowiązkowe,** gdy wymiana musi mieć końcowe ograniczniki i separatory. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

![Wybieranie ustawień wewnętrznych](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Właściwość | Opis |
| --- | --- |
| Konwertowanie dorozumianego formatu dziesiętnego "Nn" na podstawową wartość liczbową 10 |Konwertuje numer EDI określony w formacie "Nn" na wartość liczbową base-10 |
| Tworzenie pustych znaczników XML, jeśli separatory końcowe są dozwolone |To pole wyboru należy zaznaczyć, aby nadawca wymiany zawierał puste znaczniki XML dla separatorów końcowych. |
| Split Interchange jako zestawy transakcji - wstrzymanie transakcji ustawia się na błąd|Analizuje każdą transakcję ustawioną w wymianie w oddzielnym dokumencie XML, stosując odpowiednią pulę do zestawu transakcji. Zawiesza tylko transakcje, w których sprawdzanie poprawności zakończy się niepowodzeniem. |
| Split Interchange jako zestawy transakcji - wstrzymanie wymiany w sprawie błędu|Analizuje każdą transakcję ustawioną w wymianie w oddzielnym dokumencie XML, stosując odpowiednią kopertę. Zawiesza całą wymianę, gdy co najmniej jeden zestaw transakcji w weryfikacji awarii wymiany. | 
| Zachowaj wymianę - wstrzymanie zestawów transakcji na błąd |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Zawiesza tylko zestawy transakcji, które nie powiodą się, kontynuując przetwarzanie wszystkich innych zestawów transakcji. |
| Zachowaj wymianę - wstrzymanie wymiany w sprawie błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Zawiesza całą wymianę, gdy co najmniej jeden zestaw transakcji w weryfikacji awarii wymiany. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurowanie sposobu, w jaki umowa wysyła wiadomości

Można skonfigurować sposób, w jaki ta umowa identyfikuje i obsługuje wychodzące wiadomości wysyłane do partnera za pośrednictwem tej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Wyślij ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z Tobą wiadomości. Opisy właściwości można znaleźć w tabelach w tej sekcji.

    **Ustawienia wysyłania** są zorganizowane w następujące sekcje: Identyfikatory, Potwierdzenie, Schematy, Koperty, Zestawy znaków i separatory, Numery kontrolne i Sprawdzanie poprawności.

2. Po zakończeniu upewnij się, że ustawienia są zapisywane, wybierając przycisk **OK**.

Teraz umowa jest gotowa do obsługi wiadomości wychodzących zgodnych z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

![Ustawianie właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Właściwość | Opis |
| --- | --- |
| Kwalifikator autoryzacji (ISA1) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Wprowadź wartość informacji o autoryzacji. Jeśli ta wartość jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| Kwalifikator zabezpieczeń (ISA3) |Wybierz wartość kwalifikator zabezpieczeń z listy rozwijanej. |
| ISA4 |Wprowadź wartość informacje o zabezpieczeniach. Jeśli ta wartość jest inna niż 00 dla pola tekstowego Wartość (ISA4), wprowadź co najmniej jedną wartość alfanumeryczną i maksymalnie 10. |

### <a name="acknowledgment"></a>Potwierdzenia

![Ustawianie właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| OCZEKUJE SIĘ TA1 |Zwróć potwierdzenie techniczne (TA1) do nadawcy wymiany. To ustawienie określa, że partner hosta, który wysyła wiadomość żąda potwierdzenia od partnera gościa w umowie. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
| ŚT oczekiwano |Zwróć potwierdzenie funkcjonalne (FA) do nadawcy wymiany. Wybierz, czy chcesz potwierdzenia 997 czy 999, na podstawie wersji schematu, z którymi pracujesz. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
| Wersja ŚT |Wybierz wersję ŚT |

### <a name="schemas"></a>Schematy

![Wybierz schemat do użycia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz wersję X12 |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| Schematu |Wybierz schemat do użycia. Schematy znajdują się na koncie integracji. Jeśli najpierw wybierzesz schemat, automatycznie skonfiguruje on wersję i typ transakcji  |

> [!NOTE]
> Skonfiguruj wymagany [schemat,](../logic-apps/logic-apps-enterprise-integration-schemas.md) który jest przekazytywał do [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określanie separatora w zestawie transakcji: wybierz pozycję Standardowy identyfikator lub separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separator do użycia w zestawie transakcji: <p>Wybierz **standardowy identyfikator,** aby użyć kropki (.) dla notacji dziesiętnej, a nie dziesiętnej notacji dokumentu przychodzącego w potoku odbioru EDI. <p>Wybierz **separator powtórzeń,** aby określić separator dla powtarzających się wystąpień prostego elementu danych lub powtarzanej struktury danych. Na przykład zazwyczaj karat (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatu. |

### <a name="control-numbers"></a>Numery kontrolne

![Określanie właściwości numeru formantu](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Właściwość | Opis |
| --- | --- |
| Numer wersji sterowania (ISA12) |Wybierz wersję standardu X12 |
| Wskaźnik użycia (ISA15) |Wybierz kontekst wymiany.  Wartości są informacjami, danymi produkcyjnymi lub danymi testowymi |
| Schemat |Generuje segmenty GS i ST dla węzła wymiany zakodowanego w systemie X12, który wysyła do potoku wysyłania |
| GS1 |Opcjonalnie wybierz wartość kodu funkcjonalnego z listy rozwijanej |
| GS2 (WS2) |Opcjonalnie, nadawca aplikacji |
| GS3 |Opcjonalnie, odbiornik aplikacji |
| GS4 (WS4) |Opcjonalnie wybierz CCYYMMDD lub YYMMDD |
| GS5 |Opcjonalnie wybierz HHMM, HHMMSS lub HHMMSSdd |
| GS7 (WS7) |Opcjonalnie wybierz wartość odpowiedzialnej agencji z listy rozwijanej |
| GS8 (WS8) |Opcjonalna wersja dokumentu |
| Numer kontroli wymiany (ISA13) |Wymagane wprowadź zakres wartości dla numeru kontroli wymiany. Wprowadź wartość liczbową o wartości co najmniej 1 i maksymalnej 9999999999 |
| Numer kontrolny grupy (GS06) |Wymagane, wprowadź zakres liczb dla numeru kontrolnego grupy. Wprowadź wartość liczbową o wartości co najmniej 1 i maksymalnej 9999999999 |
| Numer kontrolny zestawu transakcji (ST02) |Wymagane, wprowadź zakres liczb dla numeru kontroli zestawu transakcji. Wprowadź zakres wartości liczbowych o wartościach co najmniej 1 i maksymalnej 9999999999 |
| Prefiks |Opcjonalnie, wyznaczony dla zakresu numerów kontroli zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowych dwóch pól oraz wartość alfanumeryczną (w razie potrzeby) dla pól prefiksu i sufiksu. Pola środkowe są wymagane i zawierają minimalne i maksymalne wartości numeru kontrolnego |
| Sufiks |Opcjonalnie, wyznaczony dla zakresu numerów kontroli zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowych dwóch pól i wartość alfanumeryczną (w razie potrzeby) dla pól prefiksu i sufiksu. Pola środkowe są wymagane i zawierają minimalne i maksymalne wartości numeru kontrolnego |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Poza zestawem znaków można wprowadzić inny zestaw ograniczników dla każdego typu wiadomości. Jeśli zestaw znaków nie jest określony dla danego schematu wiadomości, używany jest domyślny zestaw znaków.

![Określanie ograniczników dla typów wiadomości](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Właściwość | Opis |
| --- | --- |
| Zestaw znaków do użycia |Aby sprawdzić poprawność właściwości, wybierz zestaw znaków X12. Dostępne opcje to Basic, Extended i UTF8. |
| Schemat |Wybierz schemat z listy rozwijanej. Po zakończeniu każdego wiersza nowy wiersz jest automatycznie dodawany. Dla wybranego schematu wybierz zestaw separatorów, którego chcesz użyć, na podstawie poniższych opisów separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator komponentów |Aby oddzielić złożone elementy danych, wprowadź pojedynczy znak. |
| Separator elementu danych |Aby oddzielić proste elementy danych w złożonych elementach danych, wprowadź pojedynczy znak. |
| Znak zastępczy |Wprowadź znak zastępczy używany do zastępowania wszystkich znaków separatora w danych ładunku podczas generowania wychodzącego komunikatu X12. |
| Terminator segmentu |Aby wskazać koniec segmentu EDI, wprowadź pojedynczy znak. |
| Sufiks |Wybierz znak, który jest używany z identyfikatorem segmentu. Jeśli wyznaczysz sufiks, element danych terminatora segmentu może być pusty. Jeśli terminator segmentu pozostaje pusty, należy wyznaczyć sufiks. |

> [!TIP]
> Aby podać wartości znaków specjalnych, edytuj umowę jako JSON i podaj wartość ASCII dla znaku specjalnego.

### <a name="validation"></a>Sprawdzanie poprawności

![Ustawianie właściwości sprawdzania poprawności do wysyłania wiadomości](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po zakończeniu każdego wiersza sprawdzania poprawności, inny jest automatycznie dodawany. Jeśli nie określisz żadnych reguł, sprawdzanie poprawności używa wiersza "Domyślne".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Wykonaj sprawdzanie poprawności EDI na typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości, puste elementy danych i separatory końcowe. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, sprawdzanie poprawności jest na wymagania elementu danych i dozwolone powtórzenia, wyliczenia i sprawdzanie poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj dodatkowe znaki wiodące lub końcowe zero i spacji. Nie usuwaj tych znaków. |
| Przycinanie zer wiodących/końcowych |Usuń znaki początkowe lub końcowe zero. |
| Zasady separatora końcowego |Generowanie separatorów końcowych. <p>Wybierz **opcję Nie wolno** zakazać końcowych ograniczników i separatorów w wysłanej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz **opcjonalne,** aby wysyłać wymiany z ogranicznikami i separatorami końcowymi lub bez. <p>Wybierz **opcję Obowiązkowe,** jeśli wysłana wymiana musi mieć końcowe ograniczniki i separatory. |

## <a name="find-your-created-agreement"></a>Znajdź utworzoną umowę

1.  Po zakończeniu ustawiania wszystkich właściwości umowy na stronie **Dodaj** wybierz przycisk **OK,** aby zakończyć tworzenie umowy i powrócić do konta integracyjnego.

    Nowo dodana umowa pojawi się teraz na liście **umów.**

2.  Umowy można również wyświetlić w omówienie konta integracji. W menu konta integracyjnego wybierz pozycję **Przegląd**, a następnie wybierz kafelek **Umowy.**

    ![Wybieranie kafelka "Umowy"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)