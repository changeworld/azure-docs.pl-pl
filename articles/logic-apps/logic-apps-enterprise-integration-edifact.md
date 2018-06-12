---
title: EDIFACT wiadomości B2B integracji przedsiębiorstwa - Azure Logic Apps | Dokumentacja firmy Microsoft
description: EDIFACT wymiany wiadomości w formacie EDI B2B enterprise integracji z usługą Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/26/2016
ms.author: LADocs; jonfan
ms.openlocfilehash: d85db6f5be487578ceab261d2779589439bfad31
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300100"
---
# <a name="exchange-edifact-messages-for-enterprise-integration-with-logic-apps"></a>Komunikaty Exchange EDIFACT enterprise integracji z usługą logic apps

Przed przystąpieniem do wymiany wiadomości EDIFACT dla usługi Azure Logic Apps, należy utworzyć umowy EDIFACT i przechowywać na koncie integracji tej Umowy. Poniżej przedstawiono procedurę tworzenia umowy EDIFACT.

> [!NOTE]
> Ta strona zawiera funkcje EDIFACT na potrzeby usługi Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzone z subskrypcją platformy Azure  
* Co najmniej dwa [partnerów](logic-apps-enterprise-integration-partners.md) które już zostały zdefiniowane w ramach konta integracji

> [!NOTE]
> Podczas tworzenia umowy zawartości w wiadomości, które odbierać lub wysyłać do i od partnera musi odpowiadać typowi umowy.

Po [Tworzenie konta usługi integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [dodać partnerów](logic-apps-enterprise-integration-partners.md), można utworzyć umowy EDIFACT, wykonując następujące kroki.

## <a name="create-an-edifact-agreement"></a>Tworzenie umów EDIFACT 

1. Zaloguj się w witrynie [Azure Portal](http://portal.azure.com "Azure Portal"). 

2. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wpisz "integrację", a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Jeśli **wszystkie usługi** nie jest wyświetlane, należy najpierw rozwinąć menu. W górnej części menu zwinięte, wybierz **Pokaż tekst etykiety**.

3. W obszarze **konta integracji**, wybierz konto integracji, w której chcesz utworzyć umowy.

   ![Wybierz konto integracji, gdzie można utworzyć umowy](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Wybierz **umowy**. Jeśli nie masz kafelka umowy, najpierw Dodaj kafelka.   

   ![Wybierz Kafelek "Umów"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stronie umowy, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. W obszarze **Dodaj**, wprowadź **nazwa** dla umowy. Aby uzyskać **typ umowy**, wybierz pozycję **EDIFACT**. Wybierz **partnera hosta**, **tożsamości hosta**, **partnera gościa**, i **tożsamości gościa** dla umowy.

   ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Właściwość | Opis |
   | --- | --- |
   | Name (Nazwa) |Nazwa umowy |
   | Typ umowy | Powinien być EDIFACT |
   | Partner hosta |Umowa musi mieć partnera zarówno hosta, jak i gościa. Partner hosta reprezentuje organizację, która konfiguruje umowy. |
   | Tożsamość hosta |Identyfikator partnera hosta |
   | Partner gościa |Umowa musi mieć partnera zarówno hosta, jak i gościa. Partner gościa reprezentuje organizację, która jest działalność z partnerem hosta. |
   | Tożsamość gościa |Identyfikator partnera gościa |
   | Ustawienia odbierania |Te właściwości stosowane do wszystkich komunikatów odebranych przez umowy. |
   | Ustawienia wysyłania |Te właściwości stosowane do wszystkich wiadomości wysłanych przez umowy. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Skonfiguruj sposób dojść do Porozumienia odebrane wiadomości

Teraz, gdy ustawiono właściwości umowy, można skonfigurować sposób identyfikuje niniejszej Umowy oraz obsługi przychodzących komunikatów odebranych od swojego partnera, za pośrednictwem niniejszej Umowy.

1. W obszarze **Dodaj**, wybierz pozycję **ustawienia odbierania**.
Skonfigurować te właściwości, na podstawie Twojej umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

   **Odbierają ustawienia** jest podzielona na następujące sekcje: identyfikatory, potwierdzenia, schematów, numery kontroli, weryfikacji i ustawienia wewnętrzne.

   ![Skonfiguruj "Otrzymywać ustawienia"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Po zakończeniu upewnij się zapisać ustawienia, wybierając **OK**.

Umowie jest teraz gotowy do obsługi wiadomości przychodzących, które odpowiadają wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z zakresu od 1 do 14 znaków. |
| UNB6.2 (kwalifikator odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie dwóch znaków. |

### <a name="acknowledgments"></a>Potwierdzenia

| Właściwość | Opis |
| --- | --- |
| Potwierdzenie odbioru komunikatu (CONTRL) |Zaznacz to pole wyboru, aby przywrócić techniczne potwierdzenia (CONTRL) nadawcy wymiany. Potwierdzenie są wysyłane do nadawcy wymiany na podstawie ustawień wysyłania umowy. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, aby przywrócić funkcjonalności potwierdzenia (CONTRL) nadawcy wymiany potwierdzenie są wysyłane do nadawcy wymiany na podstawie ustawień wysyłania umowy. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYPE) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji komunikatu. (Minimalna, jeden znak; maksymalna, trzy znaki). |
| UNH2.3 (WERSJA) |Wprowadź numer wersji komunikatu. (Minimalna, jeden znak; maksymalna, trzy znaki). |
| UNH2.5 (SKOJARZONY KOD PRZYPISANEJ) |Wprowadź kod przypisanej. (Maksymalnie sześć znaków. Musi być alfanumeryczny). |
| UNG2.1 (IDENTYFIKATOR NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i 35 znaków. |
| UNG2.2 (KWALIFIKATOR KODU NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczne z maksymalnie cztery znaki. |
| SCHEMA |Wybierz wcześniej przekazane schemat, który ma być używany z Twojego konta skojarzone integracji. |

### <a name="control-numbers"></a>Numery kontrolne
| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty Interchange numer formantu |Aby zablokować wymianę duplikatów, wybierz tę właściwość. Zaznaczenie tego pola wyboru oznacza, akcja dekodowania EDIFACT sprawdza, czy numer kontroli wymiany (UNB5) odebrane wymiany nie odpowiada numer formantu wcześniej przetworzonych wymiany. W przypadku wykrycia dopasowanie wymiany nie został przetworzony. |
| Sprawdź pod kątem zduplikowanych elementów UNB5 co (dni) |Jeśli wybrano opcję Nie zezwalaj na wymianę zduplikowane numery kontroli, można określić liczbę dni, kiedy należy sprawdzić, zapewniając odpowiednią wartość dla tego ustawienia. |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Aby zablokować wymianę z grupy zduplikowane numery kontroli (UNG5), wybierz tę właściwość. |
| Nie zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Aby zablokować wymianę numerami kontroli zestaw zduplikowaną transakcję (UNH1), wybierz tę właściwość. |
| Numer kontrolny potwierdzenia EDIFACT |Do wyznaczania liczby transakcji zestawu odwołania do użycia w potwierdzenia, wprowadź wartość sufiksu prefiks oraz zakres numerów odwołania. |

### <a name="validations"></a>Walidacje

Po zakończeniu każdego wiersza weryfikacji innego automatycznie jest dodawany. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Domyślny".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Sprawdzają poprawność EDI typy danych zdefiniowane przez schemat EDI właściwości, ograniczenia długości danych puste elementy i końcowe separatorów. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie elementu danych i dozwolone powtarzania, wyliczenia i dane weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowaj wszystkie dodatkowe początkowe lub końcowe zero i miejsce znaków. Nie, usuń te znaki. |
| Przycinaj zera wiodące/kończące |Usuń wiodących lub końcowych zero i spacje. |
| Zasady dotyczące separatorów kończących |Generowanie końcowe separatorów. <p>Wybierz **niedozwolone** zakazać końcowe ograniczniki i separatory w odebranej wymiany. Jeśli wymiany końcowe ograniczniki i separatorów, wymiany zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** do akceptowania wymianę z lub bez końcowych ograniczniki i separatorów. <p>Wybierz **obowiązkowe** odebrane wymiany musi mieć, gdy końcowy ogranicznik i separatorów. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

| Właściwość | Opis |
| --- | --- |
| Utwórz puste tagi XML, jeśli dozwolone są separatory kończące |Zaznacz to pole wyboru, aby nadawca wymiany obejmują puste tagi XML dla końcowe separatorów. |
| Rozdziel wymianę na zestawy transakcji — zawieś zestawy transakcji w przypadku błędu|Analizuje każdą transakcję, ustaw w wymiany do innego dokumentu XML przez zastosowanie odpowiednich koperty do zestawu transakcji. Wstrzymaj tylko zestawy transakcji, które niepowodzenie sprawdzania poprawności. |
| Rozdziel wymianę na zestawy transakcji — zawieś wymianę w przypadku błędu|Analizuje każdą transakcję, ustaw w wymiany do innego dokumentu XML przez zastosowanie odpowiednich koperty. Zawiesić całego wymiany, gdy jeden lub więcej zestawów transakcji w wymiany weryfikacji nie powiedzie się. | 
| Zachowaj wymiany — wstrzymanie zestawy transakcji w przypadku błędu |Pozostawia niezmienione wymiany, tworzy dokument XML całego wsadowej operacji wymiany. Wstrzymaj tylko zestawy transakcji Niepowodzenie weryfikacji, pozostawiając przetworzyć wszystkie inne zestawy transakcji. |
| Zachowaj wymianę — zawieś wymianę w przypadku błędu |Pozostawia niezmienione wymiany, tworzy dokument XML całego wsadowej operacji wymiany. Zawiesić całego wymiany, gdy jeden lub więcej zestawów transakcji w wymiany weryfikacji nie powiedzie się. |

## <a name="configure-how-your-agreement-sends-messages"></a>Skonfiguruj sposób umowie wysyłania wiadomości

Można skonfigurować sposób identyfikuje niniejszej Umowy oraz obsługi komunikatów wychodzących, które wysyłają do partnerów za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz pozycję **ustawienia wysyłania**.
Skonfigurować te właściwości, na podstawie Twojej umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Wyślij ustawienia** jest podzielona na następujące sekcje: identyfikatory, potwierdzenia, schematów, koperty, zestawów znaków i separatorów, numery kontroli i sprawdzanie poprawności.

    ![Skonfiguruj "Wyślij ustawienia"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Po zakończeniu upewnij się zapisać ustawienia, wybierając **OK**.

Umowie jest teraz gotowy do obsługi komunikatów wychodzących, które odpowiadają wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB1.2 (składnia wersja) |Wybierz wartość z zakresu od **1** i **4**. |
| UNB2.3 (adres routingu odwrotnego nadawcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i 14 znaków. |
| UNB3.3 (adres routingu odwrotnego odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i 14 znaków. |
| UNB6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jedną i maksymalnie 14 znaków. |
| UNB6.2 (kwalifikator odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie dwóch znaków. |
| UNB7 (identyfikator odwołania do aplikacji) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i 14 znaków |

### <a name="acknowledgment"></a>Potwierdzenia
| Właściwość | Opis |
| --- | --- |
| Potwierdzenie odbioru komunikatu (CONTRL) |Zaznacz to pole wyboru, jeśli hostowanej partnera oczekuje na odbieranie techniczne potwierdzenia (CONTRL). To ustawienie określa, że hostowanej partnera, który jest wysyłany komunikat, zażąda potwierdzenia od partnera gościa. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, jeśli hostowanej partnera oczekuje na odbieranie funkcjonalności potwierdzenia (CONTRL). To ustawienie określa, że hostowanej partnera, który jest wysyłany komunikat, zażąda potwierdzenia od partnera gościa. |
| Generuj pętlę SG1/SG4 dla zaakceptowanych zestawów transakcji |Jeśli wybrano opcję żądania potwierdzenia funkcjonalności, zaznacz to pole wyboru, aby wymusić generowania pętle grupę SG1/BL4 w funkcjonalności potwierdzenia CONTRL dla zestawów zaakceptowane transakcji. |

### <a name="schemas"></a>Schematy
| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYPE) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji komunikatu. |
| UNH2.3 (WERSJA) |Wprowadź numer wersji komunikatu. |
| SCHEMA |Wybierz schemat używany. Schematy znajdują się na koncie integracji. Aby uzyskać dostęp do Twojego schematów, należy najpierw połączyć konta integracji do aplikacji logiki. |

### <a name="envelopes"></a>Koperty
| Właściwość | Opis |
| --- | --- |
| UNB8 (kod priorytetu przetwarzania) |Wprowadź wartość alfabetycznej, który nie jest więcej niż jeden znak. |
| UNB10 (uzgodnienie komunikacji) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 40 znaków. |
| UNB11 (wskaźnik testu) |Zaznacz to pole wyboru do wskazywania wymiany wygenerowane dane testowe |
| Zastosuj segment UNA (porada ciągu usługi) |Zaznacz to pole wyboru, aby wygenerować segment UNA wymiany do wysłania. |
| Zastosuj segmenty UNG (nagłówek grupy funkcji) |Zaznacz to pole wyboru, aby tworzyć segmenty grupowania w nagłówku grupy funkcjonalnej w wiadomości wysłane na serwer partnerski gościa. Aby utworzyć segmenty UNG używane są następujące wartości: <p>Aby uzyskać **UNG1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie sześć znaków. <p>Aby uzyskać **UNG2.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i 35 znaków. <p>Aby uzyskać **UNG2.2**, wprowadź wartość alfanumeryczne z maksymalnie cztery znaki. <p>Aby uzyskać **UNG3.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i 35 znaków. <p>Aby uzyskać **UNG3.2**, wprowadź wartość alfanumeryczne z maksymalnie cztery znaki. <p>Aby uzyskać **UNG6**, wprowadź wartość alfanumeryczne z co najmniej jedną i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.2**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.3**, wprowadź wartość alfanumeryczne z co najmniej 1 znak i 6 znaków. <p>Aby uzyskać **UNG8**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i 14 znaków. |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Inne niż zestawu znaków, można wprowadzić inny zestaw ograniczników do zastosowania w przypadku każdego typu komunikatu. Jeśli nie określono zestaw znaków dla schematu danej wiadomości, jest używany domyślny zestaw znaków.

| Właściwość | Opis |
| --- | --- |
| UNB1.1 (identyfikator systemu) |Wybierz zestaw powinny zostać zastosowane w wychodzących wymiany znaków EDIFACT. |
| Schemat |Wybierz schemat z listy rozwijanej. Po wykonaniu każdego wiersza, jest automatycznie dodawany nowy wiersz. Dla wybranego schematu wybierz zestaw separatorów, którego chcesz używać, oparte na poniższe opisy separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Do oddzielania elementów danych, wprowadź jeden znak. |
| Separator elementów danych |Do oddzielania elementów proste danych w ramach elementów danych, wprowadź jeden znak. |
| Element końcowy segmentu |Aby wskazać koniec segmentu EDI, wprowadź jeden znak. |
| Sufiks |Wybierz znak, który jest używany z identyfikatora segmentu. Jeśli określisz sufiksu element segmentu terminatora danych może być pusta. Terminator segmentu jest puste, należy wyznaczyć sufiks. |

### <a name="control-numbers"></a>Numery kontrolne
| Właściwość | Opis |
| --- | --- |
| UNB5 (numer kontrolny wymiany) |Wprowadź prefiks zakresu wartości numery kontroli wymiany i sufiks. Te wartości są używane do generowania wychodzących wymiany. Prefiksu i sufiksu są opcjonalne, gdy wymagany jest numer formantu. Formant numer jest zwiększany dla każdego nowego komunikatu; prefiksu i sufiksu pozostają takie same. |
| UNG5 (numer kontrolny grupy) |Wprowadź prefiks zakresu wartości numery kontroli wymiany i sufiks. Te wartości są używane do generowania numer grupy formantu. Prefiksu i sufiksu są opcjonalne, gdy wymagany jest numer formantu. Numer formantu jest zwiększany dla każdej nowej wiadomości, aż do osiągnięcia maksymalnej wartości; prefiksu i sufiksu pozostają takie same. |
| UNH1 (numer odwołania do nagłówka komunikatu) |Wprowadź prefiks zakresu wartości numery kontroli wymiany i sufiks. Te wartości są używane do generowania numer odwołania nagłówka wiadomości. Prefiksu i sufiksu są opcjonalne, gdy wymagany jest numer odwołania. Numer jest zwiększany dla każdego nowego komunikatu; prefiksu i sufiksu pozostają takie same. |

### <a name="validations"></a>Walidacje

Po zakończeniu każdego wiersza weryfikacji innego automatycznie jest dodawany. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Domyślny".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ wiadomości EDI. |
| Walidacja EDI |Sprawdzają poprawność EDI typy danych zdefiniowane przez właściwości EDI schematu, ograniczenia długości danych puste elementy i końcowe separatorów. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie elementu danych i dozwolone powtarzania, wyliczenia i dane weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowaj wszystkie dodatkowe początkowe lub końcowe zero i miejsce znaków. Nie, usuń te znaki. |
| Przycinaj zera wiodące/kończące |Usuń początkowe lub końcowe zero znaków. |
| Zasady dotyczące separatorów kończących |Generowanie końcowe separatorów. <p>Wybierz **niedozwolone** zakazać końcowe ograniczniki i separatory w wysłanych wymiany. Jeśli wymiany końcowe ograniczniki i separatorów, wymiany zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** wysłać wymianę z lub bez końcowych ograniczniki i separatorów. <p>Wybierz **obowiązkowe** Jeśli wysłane wymiany musi mieć końcowe ograniczniki i separatorów. |

## <a name="find-your-created-agreement"></a>Znajdź utworzone umowy

1.  Po zakończeniu ustawienie na wszystkie właściwości umowy, **Dodaj** wybierz pozycję **OK** aby zakończyć tworzenie umowy i wrócić do konta integracji.

    Nowo dodany umowy teraz pojawia się w sieci **umowy** listy.

2.  Można również wyświetlić umów w przeglądzie konta integracji. W menu konta integracji, wybierz **omówienie**, a następnie wybierz pozycję **umowy** kafelka. 

    ![Wybierz Kafelek "Umów"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Plik struktury Swagger widoku
Aby wyświetlić szczegóły Swagger EDIFACT łącznika, zobacz [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")  

