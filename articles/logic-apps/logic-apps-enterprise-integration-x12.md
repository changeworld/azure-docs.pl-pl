---
title: X12 komunikaty dla integracji dla przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wymiana X12 komunikatów w formacie EDI, B2B integracji przedsiębiorstwa w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: f06e213dbae31c9d7c4e212d605cc962aba71d2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64728747"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymiana X12 komunikatów B2B integracji przedsiębiorstwa w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Przed przystąpieniem do wymiany X12 wiadomości w usłudze Azure Logic Apps, należy utworzyć X12 umowy i zapisanie tej umowy na koncie integracji. Poniżej przedstawiono procedurę tworzenia X12 umowy.

> [!NOTE]
> Ta funkcja obejmuje X12 strony dla usługi Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure
* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) , są zdefiniowane w ramach konta integracji i skonfigurowano X12 identyfikator w obszarze **tożsamości biznesowych**    
* Wymagany [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) przesyłanych do swojego konta integracji

Po zakończeniu [utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [partnerów dodane](logic-apps-enterprise-integration-partners.md)i mieć [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , którego chcesz użyć, możesz utworzyć X12 umowy, wykonując następujące kroki.

## <a name="create-an-x12-agreement"></a>Utwórz X12 umowy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. W głównym menu platformy Azure, wybierz **wszystkich usług**. 
   W polu wyszukiwania wprowadź "integration", a następnie wybierz pozycję **kont integracji**.  

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Jeśli **wszystkich usług** nie jest wyświetlane, trzeba najpierw rozwiń menu. W górnej części menu zwinięty wybierz **Show menu**.

3. W obszarze **kont integracji**, wybierz konto integracji, w której chcesz dodać umowy.

   ![Wybierz miejsce utworzenia umowę konta integracji](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Wybierz **Przegląd**, a następnie wybierz **umów** kafelka. 
   Jeśli nie masz umowy kafelka, dodać Kafelek. 

   ![Wybierz Kafelek "Umowy"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. W obszarze **umów**, wybierz **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. W obszarze **Dodaj**, wprowadź **nazwa** dla umowy. 
   Wybierz typ umowy **X12**. 
   Wybierz **Partner hosta**, **tożsamości hosta**, **Partner gościa**, i **tożsamość gościa** dla umowy. 
   Aby uzyskać więcej szczegółów właściwości Zobacz tabelę w tym kroku.

    ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Właściwość | Opis |
    | --- | --- |
    | Name (Nazwa) |Nazwa umowy |
    | Typ umowy | Powinien być X12 |
    | Partner hosta |Umowa musi mieć partnera hosta i gościa. Partner hosta reprezentuje organizację, która umożliwia skonfigurowanie umowy. |
    | Tożsamość hosta |Identyfikator partnera hosta |
    | Partner gościa |Umowa musi mieć partnera hosta i gościa. Partner gościa reprezentuje organizację, która jest prowadzących działalność we współpracy z partnerem hosta. |
    | Tożsamość gościa |Identyfikator partnera gościa |
    | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez umowę. |
    | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez umowę. |  

   > [!NOTE]
   > Rozdzielczość X12 Umowy zależy od tego, pasujące kwalifikator nadawcy i identyfikator i kwalifikator odbiorcy i identyfikator, który zdefiniowano w przychodzącej wiadomości i partnera. W przypadku zmiany tych wartości dla partnera, zaktualizuj zbyt umowy.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurowanie, jak dojścia Twojej umowy odebranych komunikatów

Teraz, gdy ustawiono właściwości umowy, można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty przychodzące otrzymany od partnera za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz opcję **ustawienia odbierania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Otrzymał ustawienia** jest podzielone na następujące sekcje: Identyfikatory potwierdzenia, schematów, koperty, numery kontrolne, ocen i ustawienia wewnętrzne.

2. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości przychodzących, które są zgodne z wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

![Ustaw właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Właściwość | Opis |
| --- | --- |
| ISA1 (kwalifikator autoryzacji) |Wybierz wartość kwalifikator autoryzacji z listy rozwijanej. |
| ISA2 |Opcjonalny. Wprowadź wartość informacji o autoryzacji. Jeśli wprowadzona dla ISA1 wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |
| ISA3 (kwalifikator bezpieczeństwa) |Wybierz wartość kwalifikatora zabezpieczeń z listy rozwijanej. |
| ISA4 |Opcjonalny. Wprowadź wartość informacji zabezpieczeń. Jeśli wprowadzona dla ISA3 wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |

### <a name="acknowledgment"></a>Po potwierdzeniu

![Ustaw właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Właściwość | Opis |
| --- | --- |
| Oczekiwane TA1 |Zwraca techniczne potwierdzenia do nadawcy wymiany |
| Oczekiwane FA |Zwraca funkcjonalności potwierdzenia do nadawcy wymiany. Następnie określ, czy potwierdzenia 997 lub 999, w zależności od używanej wersji schematu |
| Obejmują pętli pętlę AK2/IK2 |Umożliwia generowanie pętlę AK2 pętli w funkcjonalności potwierdzenia dla zaakceptowanych zestawów transakcji |

### <a name="schemas"></a>Schematy

Wybierz schemat dla każdego typu transakcji (ST1) i aplikację nadawcy (GS2). Potok receive dezasembluje wiadomości przychodzących, dopasowując wartości ST1 i GS2 w przychodzącej wiadomości, za pomocą wartości ustawione w tym miejscu i schematu komunikatu przychodzącego ze schematem, że ustawiona w tym miejscu.

![Wybierz schemat](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Właściwość | Opis |
| --- | --- |
| Version |Wybierz X12 wersję |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| Aplikacja nadawcy (GS02) |Wybierz aplikację nadawcy |
| Schemat |Wybierz plik schematu, który chcesz użyć. Schematy są dodawane do Twojego konta integracji. |

> [!NOTE]
> Skonfiguruj wymagane [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) przekazany do usługi [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kopert

![Określ separatora w zestawie transakcji: standardowy identyfikator lub Separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separator, do użytku w zestawie transakcji: <p>Wybierz **standardowy identyfikator** do notacji dziesiętnej, należy użyć kropki (.), zamiast notacji dziesiętnej przychodzących dokumentu w EDI otrzymywać potoku. <p>Wybierz **Separator powtórzeń** do określenia separator dla powtarzanych wystąpień elementu prostych danych lub strukturą danych wielokrotnego. Na przykład zazwyczaj daszka (^) jest używany jako separator powtórzeń. Schematy HIPAA można używać tylko strzałkę. |

### <a name="control-numbers"></a>Numery kontrolne

![Wybierz sposób obsługiwania duplikaty numerów kontrolnych](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty numeru kontrolnego wymiany |Blokuj wymian duplikatów. Sprawdza, czy numer kontrolny wymiany odebrany numer kontrolny wymiany (ISA13). Jeśli zostanie wykryte dopasowanie potoku odbioru nie przetwarza wymiany. Można określić liczbę dni w przypadku sprawdzania, zapewniając wartość *Sprawdzaj duplikaty ISA13 co (dni)* . |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Blok wymiany przy użyciu numerów kontrolnych grupy. |
| Nie zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Blok wymiany przy użyciu numerów kontrolnych zestawu transakcji duplikatów. |

### <a name="validations"></a>Liczba ocen

![Ustawić właściwości sprawdzania poprawności odebranej wiadomości](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po ukończeniu każdego wiersza sprawdzania poprawności innego jest dodawany automatycznie. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ komunikatu EDI. |
| Walidacja EDI |Walidacja EDI należy wykonać na typach danych, zgodnie z definicją schematu EDI właściwości, ograniczenia długości, elementy z pustymi danymi i separatory kończące. |
| Rozszerzona Walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie element danych, powtórzenia, wyliczenia i danych weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowują wszelkie dodatkowe wiodące lub końcowe zero i miejsca znaków. Nie usuwaj te znaki. |
| Przycinaj zera wiodące/kończące |Usuń wiodących ani końcowych zero i znaków spacji. |
| Zasady dotyczące separatorów kończących |Generowanie separatory kończące. <p>Wybierz **niedozwolone** do Stanów Zjednoczonych zabraniają końcowe ograniczniki i separatorów w wymianie odebrane. Jeśli wymiana zawiera końcowe ograniczniki i separatory, wymiana zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** do akceptowania wymian z lub bez końcowe ograniczniki i separatorów. <p>Wybierz **obowiązkowe** podczas wymiany musi mieć końcowe ograniczniki i separatorów. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

![Wybierz ustawienia wewnętrzne](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Właściwość | Opis |
| --- | --- |
| Konwertuj domyślny format dziesiętny "Nn" do podstawowej wartości liczbowej 10 |Konwertuje liczbę EDI, który jest określony za pomocą formatu "Nn" na wartość liczbową base 10 |
| Utwórz puste tagi XML, jeśli dozwolone są separatory kończące |Zaznacz to pole wyboru, aby mieć nadawcy wymiany zawierają puste tagi XML dla końcowych separatorów. |
| Podziel wymiany na zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu|Analizuje każdej transakcji, ustaw w wymianie do innego dokumentu XML, stosując koperty odpowiedni zestaw transakcji. Wstrzymuje działanie tylko transakcje, gdy sprawdzanie poprawności nie powiedzie się. |
| Rozdziel wymianę na zestawy transakcji — Zawieś wymianę w przypadku błędu|Analizuje każdej transakcji, ustaw w wymianie do innego dokumentu XML, stosując odpowiednie koperty. Wstrzymuje działanie całej wymiany, gdy wystąpi niepowodzenie weryfikacji, jeden lub więcej zestawów transakcji w wymianie. | 
| Zachowaj wymianę — Zawieś zestawy transakcji w przypadku błędu |Pozostawia wymiana bez zmian, tworzy dokument XML dla całego wymiany wsadowej. Wstrzymuje zestawów transakcji, które powodują Niepowodzenie weryfikacji przerywając przetwarzania wszystkich innych zestawów transakcji. |
| Zachowaj wymianę — Zawieś wymianę w przypadku błędu |Pozostawia wymiana bez zmian, tworzy dokument XML dla całego wymiany wsadowej. Wstrzymuje działanie całej wymiany, gdy wystąpi niepowodzenie weryfikacji, jeden lub więcej zestawów transakcji w wymianie. |

## <a name="configure-how-your-agreement-sends-messages"></a>Skonfiguruj sposób umowie wysyłania wiadomości

Można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty wychodzące, które wysyłają do partnera za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz opcję **ustawienia wysyłania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Ustawienia wysyłania** jest podzielone na następujące sekcje: Identyfikatory, potwierdzenia, schematów, koperty, zestawów znaków i separatory, numerów kontrolnych i sprawdzanie poprawności.

2. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości wychodzących, które są zgodne z wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

![Ustaw właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Właściwość | Opis |
| --- | --- |
| Kwalifikator autoryzacji (ISA1) |Wybierz wartość kwalifikator autoryzacji z listy rozwijanej. |
| ISA2 |Wprowadź wartość informacji o autoryzacji. Jeśli ta wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |
| Kwalifikator bezpieczeństwa (ISA3) |Wybierz wartość kwalifikatora zabezpieczeń z listy rozwijanej. |
| ISA4 |Wprowadź wartość informacji zabezpieczeń. Jeśli ta wartość jest różna od 00, w tym polu tekstowym wartość (ISA4) wprowadź co najmniej jednej wartości alfanumeryczne i maksymalnie 10. |

### <a name="acknowledgment"></a>Po potwierdzeniu

![Ustaw właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Oczekiwane TA1 |Zwróć techniczne potwierdzenia (TA1) do nadawcy wymiany. To ustawienie określa, czy partner hosta, który wysyła wiadomość żądania potwierdzenia od partnera gościa w umowie. Te potwierdzenia powinny przez partnera hosta, oparte na ustawieniach odbierania umowy. |
| Oczekiwane FA |Zwróć funkcjonalności potwierdzenia (da) do nadawcy wymiany. Wybierz, czy potwierdzeń 997 lub 999 sprawdzającego wersję schematu, którą pracujesz. Te potwierdzenia powinny przez partnera hosta, oparte na ustawieniach odbierania umowy. |
| Wersja FA |Wybierz wersję FA |

### <a name="schemas"></a>Schematy

![Wybierz opcję schematu do użycia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Version |Wybierz X12 wersję |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| SCHEMA |Wybierz schemat używany. Schematy znajdują się na koncie integracji. Jeśli wybierzesz schematu pierwszy, automatycznie konfiguruje transakcji i wersji typu  |

> [!NOTE]
> Skonfiguruj wymagane [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) przekazany do usługi [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Kopert

![Określ separatora w zestawie transakcji: standardowy identyfikator lub Separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separator, do użytku w zestawie transakcji: <p>Wybierz **standardowy identyfikator** do notacji dziesiętnej, należy użyć kropki (.), zamiast notacji dziesiętnej przychodzących dokumentu w EDI otrzymywać potoku. <p>Wybierz **Separator powtórzeń** do określenia separator dla powtarzanych wystąpień elementu prostych danych lub strukturą danych wielokrotnego. Na przykład zazwyczaj daszka (^) jest używany jako separator powtórzeń. Schematy HIPAA można używać tylko strzałkę. |

### <a name="control-numbers"></a>Numery kontrolne

![Określ właściwości numeru kontrolnego](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Właściwość | Opis |
| --- | --- |
| Numer wersji kontroli (ISA12) |Wybierz wersję standard X12 |
| Wskaźnik użycia (ISA15) |Wybierz kontekst wymiany.  Wartości są informacji i danych produkcyjnych lub danych testowych |
| Schemat |Generuje segmentów GS i ST zakodowane w formacie X12 wymiany, wysyła go do wysyłania potoku |
| GS1 |Opcjonalne, wybierz wartość dla funkcjonalności kod z listy rozwijanej |
| GS2 |Opcjonalnie, aplikacja nadawcy |
| GS3 |Opcjonalnie odbiorca aplikacji |
| GS4 |Opcjonalnie wybierz CCYYMMDD i rrmmdd |
| GS5 |Opcjonalnie, wybierz opcję hh: mm, HHMMSS lub HHMMSSdd |
| GS7 |Opcjonalne, wybierz wartość dla Agencji odpowiada z listy rozwijanej |
| GS8 |Opcjonalnie, wersję dokumentu |
| (ISA13). numer kontrolny wymiany |Wymagane, wprowadź zakres wartości dla numeru kontrolnego wymiany. Podaj wartość liczbową z co najmniej 1 i maksymalnej 999999999 |
| Numer kontrolny grupy (GS06) |Wymagane, wprowadź zakres numerów dla numeru kontrolnego grupy. Podaj wartość liczbową z co najmniej 1 i maksymalnej 999999999 |
| Numer kontrolny zestawu transakcji (ST02) |Wymagane, wprowadź zakres numerów numer kontrolny zestawu transakcji. Wprowadź zakres wartości liczbowych z co najmniej 1 i maksymalnej 999999999 |
| Prefiks |Opcjonalne wyznaczony dla zakresu numerów kontrolnych zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowej dwa pola, a wartością alfanumeryczną (w razie potrzeby) dla pól prefiksem i sufiksem. Środkowy pola są wymagane i zawierają minimalne i maksymalne wartości dla numeru kontrolnego |
| Suffix |Opcjonalne wyznaczony dla zakresu numerów kontrolnych zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowej dwóch pól i wartości alfanumeryczne (w razie potrzeby) dla pól prefiksem i sufiksem. Środkowy pola są wymagane i zawierają minimalne i maksymalne wartości dla numeru kontrolnego |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Inne niż zestaw znaków, dla każdego typu komunikatu można wprowadzić innego zestawu ograniczników. Jeśli zestaw znaków nie jest określona dla schematu podanym komunikatem, jest używany domyślny zestaw znaków.

![Określ ograniczniki typy komunikatów](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Właściwość | Opis |
| --- | --- |
| Zestaw ma być używany znaków |Aby sprawdzić właściwości, wybierz X12 zestaw znaków. Opcje są podstawowy, rozszerzony i UTF8. |
| Schemat |Wybierz schemat z listy rozwijanej. Po wykonaniu każdego wiersza, nowy wiersz jest automatycznie dodawany. Dla wybranego schematu wybierz zestaw separatorów, do którego chcesz użyć, w oparciu o poniższe opisy separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Do oddzielania elementów danych złożonych, wprowadź jeden znak. |
| Separator elementów danych |Do oddzielania elementów prostych danych w obrębie danych złożonych elementów, wprowadź jeden znak. |
| Znak zastępczy |Wprowadź znak zastępczy, używany do zastępowania wszystkie znaki separatora w ładunku danych podczas generowania X12 wychodzących wiadomości. |
| Terminator segmentu |Aby wskazać koniec segmentu EDI, wprowadź jeden znak. |
| Suffix |Wybierz znak, który jest używany z identyfikatora segmentu. Po wyznaczeniu sufiksu, element danych terminator segmentu może być pusta. Terminator segmentu jest puste, należy określić sufiks. |

> [!TIP]
> Aby podać wartości znaków specjalnych, Edytuj Umowę w formacie JSON i podaj wartość ASCII znaki specjalne.

### <a name="validation"></a>Walidacja

![Ustawić właściwości sprawdzania poprawności do wysyłania wiadomości](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po ukończeniu każdego wiersza sprawdzania poprawności innego jest dodawany automatycznie. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ komunikatu EDI. |
| Walidacja EDI |Walidacja EDI należy wykonać na typach danych, zgodnie z definicją schematu EDI właściwości, ograniczenia długości, elementy z pustymi danymi i separatory kończące. |
| Rozszerzona Walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie element danych, powtórzenia, wyliczenia i danych weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowują wszelkie dodatkowe wiodące lub końcowe zero i miejsca znaków. Nie usuwaj te znaki. |
| Przycinaj zera wiodące/kończące |Usuń początkowe lub końcowe zero znaków. |
| Zasady dotyczące separatorów kończących |Generowanie separatory kończące. <p>Wybierz **niedozwolone** do Stanów Zjednoczonych zabraniają końcowe ograniczniki i separatorów w wymianie wysłane. Jeśli wymiana zawiera końcowe ograniczniki i separatory, wymiana zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** wysyłać wymian z lub bez końcowe ograniczniki i separatorów. <p>Wybierz **obowiązkowe** Jeśli wysłane wymiany musi mieć końcowe ograniczniki i separatorów. |

## <a name="find-your-created-agreement"></a>Znajdź utworzony umowy

1.  Po zakończeniu na ustawienie swoje właściwości umowy **Dodaj** wybierz **OK** w stanie zakończyć tworzenia umowy, a następnie wróć do konta integracji.

    Nowo dodane umowy teraz pojawia się w Twojej **umów** listy.

2.  Można również wyświetlić Twojej umowy w przeglądzie konta integracji. W menu konta integracji, wybierz **Przegląd**, a następnie wybierz **umów** kafelka.

    ![Wybierz Kafelek "Umowy"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  

