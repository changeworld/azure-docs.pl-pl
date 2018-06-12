---
title: X12 wiadomości B2B integracji przedsiębiorstwa - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Exchange X12 wiadomości w formacie EDI B2B enterprise integracji z usługą Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: b269a5171dcb8867f7ea40215d6b8a75d5d918bc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299083"
---
# <a name="exchange-x12-messages-for-enterprise-integration-with-logic-apps"></a>Komunikaty programu Exchange X12 enterprise integracji z usługą logic apps

Przed przystąpieniem do wymiany X12 wiadomości dla usługi Azure Logic Apps, należy utworzyć X12 umowy i przechowywać na koncie integracji tej Umowy. Poniżej przedstawiono kroki tworzenia X12 umowy.

> [!NOTE]
> Ta strona obejmuje X12 funkcje usługi Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure
* Co najmniej dwa [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) który są określone w danych konta integracji i skonfigurowano X12 identyfikator w obszarze **tożsamości firm**    
* Wymaganą [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) można przekazać go do swojego konta integracji

Po [Tworzenie konta usługi integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [dodać partnerów](logic-apps-enterprise-integration-partners.md)i mieć [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) czy chcesz użyć, możesz utworzyć X12 umowy, wykonując następujące kroki.

## <a name="create-an-x12-agreement"></a>Utwórz X12 umowy

1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. Wybierz z menu głównego Azure **wszystkie usługi**. W polu wyszukiwania wpisz "integrację", a następnie wybierz **konta integracji**.  

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Jeśli **wszystkie usługi** nie jest wyświetlane, należy najpierw rozwinąć menu. W górnej części menu zwinięte, wybierz **Pokaż menu**.

3. W obszarze **konta integracji**, wybierz konto integracji, w której chcesz dodać umowy.

   ![Wybierz konto integracji, gdzie można utworzyć umowy](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Wybierz **omówienie**, a następnie wybierz pozycję **umowy** kafelka. Jeśli nie masz kafelka umowy, najpierw Dodaj kafelka. 

   ![Wybierz Kafelek "Umów"](./media/logic-apps-enterprise-integration-as2/agreement-1.png)

5. W obszarze **umowy**, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj"](./media/logic-apps-enterprise-integration-as2/agreement-2.png)     

6. W obszarze **Dodaj**, wprowadź **nazwa** dla umowy. Wybierz typ umowy **X12**. Wybierz **partnera hosta**, **tożsamości hosta**, **partnera gościa**, i **tożsamości gościa** dla umowy. Więcej szczegółów właściwości Zobacz tabelę w tym kroku.

    ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Właściwość | Opis |
    | --- | --- |
    | Name (Nazwa) |Nazwa umowy |
    | Typ umowy | Powinien być X12 |
    | Partner hosta |Umowa musi mieć partnera zarówno hosta, jak i gościa. Partner hosta reprezentuje organizację, która konfiguruje umowy. |
    | Tożsamość hosta |Identyfikator partnera hosta |
    | Partner gościa |Umowa musi mieć partnera zarówno hosta, jak i gościa. Partner gościa reprezentuje organizację, która jest działalność z partnerem hosta. |
    | Tożsamość gościa |Identyfikator partnera gościa |
    | Ustawienia odbierania |Te właściwości stosowane do wszystkich komunikatów odebranych przez umowy. |
    | Ustawienia wysyłania |Te właściwości stosowane do wszystkich wiadomości wysłanych przez umowy. |  

  > [!NOTE]
  > Rozdzielczość X12 Umowy zależy od dopasowania kwalifikator nadawcy i identyfikator oraz kwalifikator odbiornik i identyfikator zdefiniowany w wiadomości przychodzącej i partnera. Zmiana tych wartości dla partnera, zaktualizuj zbyt umowy.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Skonfiguruj sposób dojść do Porozumienia odebrane wiadomości

Teraz, gdy ustawiono właściwości umowy, można skonfigurować sposób identyfikuje niniejszej Umowy oraz obsługi przychodzących komunikatów odebranych od swojego partnera, za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz pozycję **ustawienia odbierania**.
Skonfigurować te właściwości, na podstawie Twojej umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Odbierają ustawienia** jest podzielona na następujące sekcje: identyfikatory, potwierdzenia, schematów, koperty, numery kontroli, sprawdzanie poprawności i ustawienia wewnętrzne.

2. Po zakończeniu upewnij się zapisać ustawienia, wybierając **OK**.

Umowie jest teraz gotowy do obsługi wiadomości przychodzących, które odpowiadają wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

![Identyfikator właściwości.](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Właściwość | Opis |
| --- | --- |
| ISA1 (kwalifikator autoryzacji) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Opcjonalny. Wprowadź wartość informacji o autoryzacji. Jeśli wprowadzona dla ISA1 wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |
| ISA3 (kwalifikator bezpieczeństwa) |Wybierz wartość kwalifikatora zabezpieczeń z listy rozwijanej. |
| ISA4 |Opcjonalny. Wprowadź wartość informacji zabezpieczeń. Jeśli wprowadzona dla ISA3 wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |

### <a name="acknowledgment"></a>Potwierdzenia

![Ustaw właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Właściwość | Opis |
| --- | --- |
| Oczekiwano TA1 |Zwraca techniczne potwierdzenia do nadawcy wymiany |
| Oczekiwano FA |Zwraca funkcjonalności potwierdzenia nadawcy wymiany. Następnie wybierz, czy potwierdzenia 997 lub 999, w zależności od używanej wersji schematu |
| Obejmują AK2/IK2 pętli |Włącza generowanie pętle AK2 w funkcjonalności potwierdzenia dla zestawów zaakceptowane transakcji |

### <a name="schemas"></a>Schematy

Wybierz opcję schematu dla każdego typu transakcji (ST1) i aplikację Sender (GS2). Potok receive rozkłada komunikat przychodzący odpowiadającą wartości ST1 i GS2 w komunikat przychodzący z wartości ustawione w tym miejscu, a schemat komunikat przychodzący ze schematem zestawu w tym miejscu.

![Wybierz opcję schematu](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz X12 wersji |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| Aplikacja nadawcy (GS02) |Wybierz aplikację sender |
| Schemat |Wybierz plik schematu, który ma być używany. Schematy są dodawane do swojego konta integracji. |

> [!NOTE]
> Skonfiguruj wymagane [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) przekazaniu do Twojej [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określ separator w zestawie transakcji: Wybierz identyfikator Standard lub Separator powtórzenia](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separatora, aby użyć zestawu transakcji: <p>Wybierz **standardowy identyfikator** można użyć notacji dziesiętnej kropki (.), zamiast notacji dziesiętnej przychodzącego dokumentu w EDI odbierania potoku. <p>Wybierz **separatora powtarzania** określić separatora dla wystąpień powtórzony element proste danych lub strukturą danych powtórzony. Na przykład zwykle karatach (^) jest używany jako separator powtarzania. Schematy HIPAA można używać tylko karatach. |

### <a name="control-numbers"></a>Numery kontrolne

![Wybierz sposób obsługi sterowania duplikaty numerów](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty Interchange numer formantu |Blokowanie wymianę zduplikowane. Sprawdza, czy numer formantu wymiany Odebrano numer formantu interchange (ISA13). Po wykryciu dopasowania potoku receive nie przetworzyć wymiany. Można określić liczbę dni do sprawdzania, zapewniając wartość *Sprawdź, czy zduplikowany ISA13 co (dni)*. |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Blok wymiany z numerami kontroli zduplikowanej grupie. |
| Nie zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Blok wymiany z numerami kontroli zestaw zduplikowaną transakcję. |

### <a name="validations"></a>Walidacje

![Ustaw właściwości sprawdzania poprawności odebranej wiadomości](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po zakończeniu każdego wiersza weryfikacji innego automatycznie jest dodawany. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Domyślny".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Sprawdzają poprawność EDI typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości danych puste elementy i końcowe separatorów. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie elementu danych i dozwolone powtarzania, wyliczenia i dane weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowaj wszystkie dodatkowe początkowe lub końcowe zero i miejsce znaków. Nie, usuń te znaki. |
| Przycinaj zera wiodące/kończące |Usuń wiodących lub końcowych zero i spacje. |
| Zasady dotyczące separatorów kończących |Generowanie końcowe separatorów. <p>Wybierz **niedozwolone** zakazać końcowe ograniczniki i separatory w odebranej wymiany. Jeśli wymiany końcowe ograniczniki i separatorów, wymiany zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** do akceptowania wymianę z lub bez końcowych ograniczniki i separatorów. <p>Wybierz **obowiązkowe** podczas wymiany musi mieć końcowe ograniczniki i separatorów. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

![Wybierz ustawienia wewnętrzne](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Właściwość | Opis |
| --- | --- |
| Konwertuj domniemanych format dziesiętny "Nn" podstawowej wartości liczbowej 10 |Konwertuje liczbę EDI, określonego w formacie "Nn" na wartość liczbową base-10 |
| Utwórz puste tagi XML, jeśli dozwolone są separatory kończące |Zaznacz to pole wyboru, aby nadawca wymiany obejmują puste tagi XML dla końcowe separatorów. |
| Rozdziel wymianę na zestawy transakcji — zawieś zestawy transakcji w przypadku błędu|Analizuje każdą transakcję, ustaw w wymiany do innego dokumentu XML przez zastosowanie odpowiednich koperty do zestawu transakcji. Wstrzymuje tylko transakcje, których sprawdzenie nie powiodło się. |
| Rozdziel wymianę na zestawy transakcji — zawieś wymianę w przypadku błędu|Analizuje każdą transakcję, ustaw w wymiany do innego dokumentu XML przez zastosowanie odpowiednich koperty. Zawiesza całego wymiany, gdy jeden lub więcej zestawów transakcji w wymiany niepowodzenie sprawdzania poprawności. | 
| Zachowaj wymiany — wstrzymanie zestawy transakcji w przypadku błędu |Pozostawia niezmienione wymiany, tworzy dokument XML całego wsadowej operacji wymiany. Wstrzymuje tylko zestawy transakcji Niepowodzenie weryfikacji, pozostawiając przetworzyć wszystkie inne zestawy transakcji. |
| Zachowaj wymianę — zawieś wymianę w przypadku błędu |Pozostawia niezmienione wymiany, tworzy dokument XML całego wsadowej operacji wymiany. Wstrzymuje całego wymiany, gdy jeden lub więcej zestawów transakcji w wymiany niepowodzenie sprawdzania poprawności. |

## <a name="configure-how-your-agreement-sends-messages"></a>Skonfiguruj sposób umowie wysyłania wiadomości

Można skonfigurować sposób identyfikuje niniejszej Umowy oraz obsługuje wysyła komunikaty wychodzące wysyłane do partnera za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz pozycję **ustawienia wysyłania**.
Skonfigurować te właściwości, na podstawie Twojej umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Wyślij ustawienia** jest podzielona na następujące sekcje: identyfikatory, potwierdzenia, schematów, koperty, zestawów znaków i separatorów, numery kontroli i sprawdzania poprawności.

2. Po zakończeniu upewnij się zapisać ustawienia, wybierając **OK**.

Umowie jest teraz gotowy do obsługi komunikatów wychodzących, które odpowiadają wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

![Identyfikator właściwości.](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Właściwość | Opis |
| --- | --- |
| Kwalifikator autoryzacji (ISA1) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Wprowadź wartość informacji o autoryzacji. Jeśli ta wartość jest równa 00, wprowadź co najmniej jeden znak alfanumeryczny oraz maksymalnie 10. |
| Kwalifikator zabezpieczeń (ISA3) |Wybierz wartość kwalifikatora zabezpieczeń z listy rozwijanej. |
| ISA4 |Wprowadź wartość informacji zabezpieczeń. Jeśli ta wartość jest równa 00 (ISA4) wartość pola tekstowego, wprowadź co najmniej jedną wartość alfanumeryczne i maksymalnie 10. |

### <a name="acknowledgment"></a>Potwierdzenia

![Ustaw właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Oczekiwano TA1 |Zwróć techniczne potwierdzenia (TA1) do nadawcy wymiany. To ustawienie określa, czy partner hosta, który wysyła komunikat żądania potwierdzenia od partnera gościa umowy. Oczekiwano te potwierdzenia przez partnera hosta na podstawie ustawień odbierania umowy. |
| Oczekiwano FA |Zwraca funkcjonalności potwierdzenia (FA) do wymiany nadawcy. Wybierz, czy potwierdzeń 997 lub 999 oparte na wersji schematu, które użytkownik pracuje z. Oczekiwano te potwierdzenia przez partnera hosta na podstawie ustawień odbierania umowy. |
| Wersja FA |Wybierz wersję FA |

### <a name="schemas"></a>Schematy

![Wybierz schemat używany](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz X12 wersji |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| SCHEMA |Wybierz schemat używany. Schematy znajdują się na koncie integracji. Jeśli najpierw wybrać schematu automatycznie konfiguruje wersji i transakcji typu  |

> [!NOTE]
> Skonfiguruj wymagane [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) przekazaniu do Twojej [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określ separator w zestawie transakcji: Wybierz identyfikator Standard lub Separator powtórzenia](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Właściwość | Opis |
| --- | --- |
| Użycie ISA11 |Określa separatora, aby użyć zestawu transakcji: <p>Wybierz **standardowy identyfikator** można użyć notacji dziesiętnej kropki (.), zamiast notacji dziesiętnej przychodzącego dokumentu w EDI odbierania potoku. <p>Wybierz **separatora powtarzania** określić separatora dla wystąpień powtórzony element proste danych lub strukturą danych powtórzony. Na przykład zwykle karatach (^) jest używany jako separator powtarzania. Schematy HIPAA można używać tylko karatach. |

### <a name="control-numbers"></a>Numery kontrolne

![Określ właściwości formantu](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Właściwość | Opis |
| --- | --- |
| Numer wersji kontroli (ISA12) |Wybierz wersję standard X12 |
| Użycie wskaźnika (ISA15) |Wybierz kontekst wymiany.  Wartości są informacji i danych produkcyjnych lub dane testowe |
| Schemat |Generuje segmentów GS i ST wymiany kodowany w formacie X12 wysyłanej do potoku wysyłania |
| GS1 |Opcjonalne, wybierz wartość funkcjonalny kod z listy rozwijanej |
| GS2 |Opcjonalne, nadawca aplikacji |
| GS3 |Opcjonalne, odbiornika aplikacji |
| GS4 |Opcjonalne, wybierz opcję CCYYMMDD lub rrmmdd |
| GS5 |Opcjonalnie wybierz hh: mm, HHMMSS albo HHMMSSdd |
| GS7 |Opcjonalne, wybierz wartość dla właściwej agencji z listy rozwijanej |
| GS8 |Opcjonalne, wersji dokumentu |
| Interchange numer formantu (ISA13) |Wymagane, wprowadź zakres wartości dla numeru kontroli wymiany. Wprowadź wartość liczbową z zakresu od 1 i maksymalnie 999999999 |
| Numer grupy kontroli (GS06) |Wymagane, wprowadź numer grupy kontroli zakresu liczb. Wprowadź wartość liczbową z zakresu od 1 i maksymalnie 999999999 |
| Numer kontroli zestawu transakcji (ST02) |Wymagane, wprowadź zakres numerów formantu ustawić transakcji. Wprowadź zakres wartości liczbowych z co najmniej 1 i maksymalnie 999999999 |
| Prefiks |Opcjonalne, które są przeznaczone dla zakresu numerów kontroli zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowej dwóch pól i wartości alfanumeryczne (w razie potrzeby) dla pól prefiksu i sufiksu. Środkowy pola są wymagane i zawierają minimalne i maksymalne wartości formantu |
| Sufiks |Opcjonalne, wyznaczonego dla zakresu numerów kontroli zestawu transakcji używanych w potwierdzeniu. Wprowadź wartość liczbową dla środkowej dwóch pól i wartości alfanumeryczne (w razie potrzeby) dla pól prefiksu i sufiksu. Środkowy pola są wymagane i zawierają minimalne i maksymalne wartości formantu |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Inne niż zestawu znaków, można wprowadzić inny zestaw ograniczniki dla każdego typu komunikatu. Jeśli zestaw znaków nie jest określona dla schematu danej wiadomości, jest używany domyślny zestaw znaków.

![Określ ograniczniki typów wiadomości](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Właściwość | Opis |
| --- | --- |
| Zestaw znaków do użycia |Aby sprawdzić poprawność właściwości, wybierz X12 zestaw znaków. Dostępne opcje to Basic, rozszerzone i UTF8. |
| Schemat |Wybierz schemat z listy rozwijanej. Po wykonaniu każdego wiersza, jest automatycznie dodawany nowy wiersz. Dla wybranego schematu wybierz zestaw separatorów, którego chcesz używać, oparte na poniższe opisy separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Do oddzielania elementów danych, wprowadź jeden znak. |
| Separator elementów danych |Do oddzielania elementów proste danych w ramach elementów danych, wprowadź jeden znak. |
| Znak zastępczy |Wpisz znak zastępczy używany do zastępowania znaków separatora wszystkich danych ładunku podczas generowania X12 wychodzących wiadomości. |
| Element końcowy segmentu |Aby wskazać koniec segmentu EDI, wprowadź jeden znak. |
| Sufiks |Wybierz znak, który jest używany z identyfikatora segmentu. Jeśli określisz sufiksu element segmentu terminatora danych może być pusta. Terminator segmentu jest puste, należy wyznaczyć sufiks. |

> [!TIP]
> Aby podać wartości znaków specjalnych, edytowanie umowy w formacie JSON i podaj wartość ASCII znak specjalny.

### <a name="validation"></a>Walidacja

![Ustaw właściwości sprawdzania poprawności do wysyłania wiadomości](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po zakończeniu każdego wiersza weryfikacji innego automatycznie jest dodawany. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Domyślny".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Sprawdzają poprawność EDI typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości danych puste elementy i końcowe separatorów. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie elementu danych i dozwolone powtarzania, wyliczenia i dane weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowaj wszystkie dodatkowe początkowe lub końcowe zero i miejsce znaków. Nie, usuń te znaki. |
| Przycinaj zera wiodące/kończące |Usuń początkowe lub końcowe zero znaków. |
| Zasady dotyczące separatorów kończących |Generowanie końcowe separatorów. <p>Wybierz **niedozwolone** zakazać końcowe ograniczniki i separatory w wysłanych wymiany. Jeśli wymiany końcowe ograniczniki i separatorów, wymiany zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** wysłać wymianę z lub bez końcowych ograniczniki i separatorów. <p>Wybierz **obowiązkowe** Jeśli wysłane wymiany musi mieć końcowe ograniczniki i separatorów. |

## <a name="find-your-created-agreement"></a>Znajdź utworzone umowy

1.  Po zakończeniu ustawienie na wszystkie właściwości umowy, **Dodaj** wybierz pozycję **OK** aby zakończyć tworzenie umowy i wrócić do konta integracji.

    Nowo dodany umowy teraz pojawia się w sieci **umowy** listy.

2.  Można również wyświetlić umów w przeglądzie konta integracji. W menu konta integracji, wybierz **omówienie**, a następnie wybierz pozycję **umowy** kafelka.

    ![Wybierz Kafelek "Umów"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger
Zobacz [swagger szczegóły](/connectors/x12/). 

## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")  

