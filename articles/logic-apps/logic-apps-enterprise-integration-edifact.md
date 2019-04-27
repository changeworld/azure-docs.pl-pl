---
title: Komunikatów EDIFACT integracji przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Wymienianie komunikatów EDIFACT w formacie EDI, B2B integracji przedsiębiorstwa w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: bbcdad7c5496cd08994a613b07e1bc7c611e4572
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684509"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymienianie komunikatów EDIFACT integracji przedsiębiorstw B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Przed przystąpieniem do wymiany komunikatów EDIFACT dla usługi Azure Logic Apps, należy utworzyć umowę EDIFACT i przechowywać tej umowy na koncie integracji. Poniżej przedstawiono procedurę tworzenia umowy EDIFACT.

> [!NOTE]
> Ta strona obejmuje funkcje EDIFACT w usłudze Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, które są potrzebne:

* [Konta integracji](logic-apps-enterprise-integration-create-integration-account.md) który został już zdefiniowany i skojarzonych z subskrypcją platformy Azure  
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) które zostały już zdefiniowane w ramach konta integracji

> [!NOTE]
> Podczas tworzenia umowy zawartości w wiadomości, które odbierania lub wysyłania do i od partnera musi być zgodna z typem umowy.

Po zakończeniu [utworzyć konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [partnerów dodane](logic-apps-enterprise-integration-partners.md), umowę EDIFACT można utworzyć, wykonując następujące kroki.

## <a name="create-an-edifact-agreement"></a>Tworzenie umowy EDIFACT 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wprowadź "integration", a następnie wybierz pozycję **kont integracji**.

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Jeśli **wszystkich usług** nie jest wyświetlane, trzeba najpierw rozwiń menu. W górnej części menu zwinięty wybierz **Pokaż etykiety tekstowe**.

3. W obszarze **kont integracji**, wybierz konto integracji, w którym chcesz utworzyć umowy.

   ![Wybierz miejsce utworzenia umowę konta integracji](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Wybierz **umowy**. Jeśli nie masz umowy kafelka, dodać Kafelek.   

   ![Wybierz Kafelek "Umowy"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stronie umowy wybierz **Dodaj**.

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. W obszarze **Dodaj**, wprowadź **nazwa** dla umowy. Aby uzyskać **typ umowy**, wybierz opcję **EDIFACT**. Wybierz **Partner hosta**, **tożsamości hosta**, **Partner gościa**, i **tożsamość gościa** dla umowy.

   ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Właściwość | Opis |
   | --- | --- |
   | Name (Nazwa) |Nazwa umowy |
   | Typ umowy | Powinien być EDIFACT |
   | Partner hosta |Umowa musi mieć partnera hosta i gościa. Partner hosta reprezentuje organizację, która umożliwia skonfigurowanie umowy. |
   | Tożsamość hosta |Identyfikator partnera hosta |
   | Partner gościa |Umowa musi mieć partnera hosta i gościa. Partner gościa reprezentuje organizację, która jest prowadzących działalność we współpracy z partnerem hosta. |
   | Tożsamość gościa |Identyfikator partnera gościa |
   | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez umowę. |
   | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez umowę. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Konfigurowanie, jak dojścia Twojej umowy odebranych komunikatów

Teraz, gdy ustawiono właściwości umowy, można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty przychodzące otrzymany od partnera za pośrednictwem niniejszej Umowy.

1. W obszarze **Dodaj**, wybierz opcję **ustawienia odbierania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

   **Otrzymał ustawienia** jest podzielone na następujące sekcje: Identyfikatory potwierdzenia, schematów, numerów kontrolnych, weryfikacji i ustawienia wewnętrzne.

   ![Skonfiguruj "Otrzymywać ustawienia"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości przychodzących, które są zgodne z wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z zakresu od 1 do 14 znaków. |
| UNB6.2 (kwalifikator odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie dwóch znaków. |

### <a name="acknowledgments"></a>Potwierdzenia

| Właściwość | Opis |
| --- | --- |
| Potwierdzenie odbioru komunikatu (CONTRL) |Zaznacz to pole wyboru do zwrócenia techniczne potwierdzenie (CONTRL) do nadawcy wymiany. Potwierdzenie są wysyłane do nadawcy wymiany oparte na ustawieniach wysyłania umowy. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, aby przywrócić funkcjonalności potwierdzenie (CONTRL) nadawcy wymiany potwierdzenie są wysyłane do nadawcy wymiany oparte na ustawieniach wysyłania umowy. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYPE) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji komunikatu. (Co najmniej, jeden znak; maksymalna, trzy znaki). |
| UNH2.3 (ZWOLNIENIE) |Wprowadź numer wersji komunikatu. (Co najmniej, jeden znak; maksymalna, trzy znaki). |
| UNH2.5 (KOD PRZYPISANY SKOJARZONE) |Wprowadź kod przypisany. (Maksymalnie sześć znaków. Musi być alfanumeryczny). |
| UNG2.1 (IDENTYFIKATOR NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 35 znaków. |
| UNG2.2 (KWALIFIKATOR KODU NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczne, z maksymalnie cztery znaki. |
| SCHEMA |Wybierz wcześniej przekazane schemat, który chcesz korzystać z usługi skojarzonego konta integracji. |

### <a name="control-numbers"></a>Numery kontrolne
| Właściwość | Opis |
| --- | --- |
| Nie zezwalaj na duplikaty numeru kontrolnego wymiany |Aby zablokować wymian duplikatów, wybierz tę właściwość. Zaznaczenie tego pola wyboru oznacza, akcja dekodowania EDIFACT sprawdza, czy numer kontrolny wymiany (UNB5) odebranych wymiany jest niezgodny numer kontrolny wymiany wcześniej przetworzonych. Jeśli zostanie wykryte dopasowanie, wymiana nie został przetworzony. |
| Sprawdź pod kątem zduplikowanych elementów UNB5 co (dni) |Jeśli została wybrana opcja zabrania numerów kontrolnych wymiany duplikatów, można określić liczbę dni, kiedy należy sprawdzić, zapewniając odpowiednią wartość dla tego ustawienia. |
| Nie zezwalaj na duplikaty numerów kontrolnych grupy |Aby zablokować wymian przy użyciu numerów kontrolnych grupy (UNG5), wybierz tę właściwość. |
| Nie zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Aby zablokować wymian przy użyciu numerów kontrolnych zestawu transakcji zduplikowane (UNH1), wybierz tę właściwość. |
| Numer kontrolny potwierdzenia EDIFACT |Aby wyznaczyć numerów odwołanie do zestawu transakcji do użycia w potwierdzenie, wprowadź wartość prefiksu, zakresu numerów odniesienia oraz sufiks. |

### <a name="validations"></a>Walidacje

Po ukończeniu każdego wiersza sprawdzania poprawności innego jest dodawany automatycznie. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Default".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ komunikatu EDI. |
| Walidacja EDI |Walidacja EDI należy wykonać na typach danych, zgodnie z definicją schematu EDI właściwości, ograniczenia długości, elementy z pustymi danymi i separatory kończące. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie element danych, powtórzenia, wyliczenia i danych weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowują wszelkie dodatkowe wiodące lub końcowe zero i miejsca znaków. Nie usuwaj te znaki. |
| Przycinaj zera wiodące/kończące |Usuń wiodących ani końcowych zero i znaków spacji. |
| Zasady dotyczące separatorów kończących |Generowanie separatory kończące. <p>Wybierz **niedozwolone** do Stanów Zjednoczonych zabraniają końcowe ograniczniki i separatorów w wymianie odebrane. Jeśli wymiana zawiera końcowe ograniczniki i separatory, wymiana zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** do akceptowania wymian z lub bez końcowe ograniczniki i separatorów. <p>Wybierz **obowiązkowe** podczas wymiany odebranych musi mieć końcowe ograniczniki i separatorów. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

| Właściwość | Opis |
| --- | --- |
| Utwórz puste tagi XML, jeśli dozwolone są separatory kończące |Zaznacz to pole wyboru, aby mieć nadawcy wymiany zawierają puste tagi XML dla końcowych separatorów. |
| Rozdziel wymianę na zestawy transakcji — zawieś zestawy transakcji w przypadku błędu|Analizuje każdej transakcji, ustaw w wymianie do innego dokumentu XML, stosując koperty odpowiedni zestaw transakcji. Zawieś zestawy transakcji, które powodują Niepowodzenie weryfikacji. |
| Rozdziel wymianę na zestawy transakcji — zawieś wymianę w przypadku błędu|Analizuje każdej transakcji, ustaw w wymianie do innego dokumentu XML, stosując odpowiednie koperty. Wstrzymaj wymiana całego, gdy wystąpi niepowodzenie weryfikacji, jeden lub więcej zestawów transakcji w wymianie. | 
| Zachowaj wymianę — Zawieś zestawy transakcji w przypadku błędu |Pozostawia wymiana bez zmian, tworzy dokument XML dla całego wymiany wsadowej. Zawieś zestawy transakcji, które powodują Niepowodzenie weryfikacji przerywając przetwarzania wszystkich innych zestawów transakcji. |
| Zachowaj wymianę — zawieś wymianę w przypadku błędu |Pozostawia wymiana bez zmian, tworzy dokument XML dla całego wymiany wsadowej. Wstrzymaj wymiana całego, gdy wystąpi niepowodzenie weryfikacji, jeden lub więcej zestawów transakcji w wymianie. |

## <a name="configure-how-your-agreement-sends-messages"></a>Skonfiguruj sposób umowie wysyłania wiadomości

Można skonfigurować, jak niniejsza Umowa identyfikuje i obsługuje komunikaty wychodzące wysyłane do partnerów za pośrednictwem niniejszej Umowy.

1.  W obszarze **Dodaj**, wybierz opcję **ustawienia wysyłania**.
Należy skonfigurować te właściwości, w oparciu o umowy z partnerem, który wymienia wiadomości z Tobą. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Ustawienia wysyłania** jest podzielone na następujące sekcje: Identyfikatory, potwierdzenia, schematów, koperty, zestawów znaków i separatory, numerów kontrolnych i sprawdzanie poprawności.

    ![Skonfiguruj "Wyślij ustawienia"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Po wykonaniu tych czynności upewnij się zapisać ustawienia, wybierając **OK**.

Teraz umowy jest gotowy do obsługi wiadomości wychodzących, które są zgodne z wybrane ustawienia.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB1.2 (wersja składni) |Wybierz wartość z zakresu od **1** i **4**. |
| UNB2.3 (adres routingu odwrotnego nadawcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 14 znaków. |
| UNB3.3 (adres routingu odwrotnego odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 14 znaków. |
| UNB6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jedną i maksymalnie 14 znaków. |
| UNB6.2 (kwalifikator odwołania do odbiorcy) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie dwóch znaków. |
| UNB7 (identyfikator odwołania do aplikacji) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 14 znaków |

### <a name="acknowledgment"></a>Po potwierdzeniu
| Właściwość | Opis |
| --- | --- |
| Potwierdzenie odbioru komunikatu (CONTRL) |Zaznacz to pole wyboru, jeśli partner hostowanej oczekuje otrzymać pomoc potwierdzenie (CONTRL). To ustawienie określa, że hostowanej partnera, który wysyła wiadomość, zażąda potwierdzenia od partnera gościa. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, jeśli partner hostowanej oczekuje otrzymać funkcjonalności potwierdzenie (CONTRL). To ustawienie określa, że hostowanej partnera, który wysyła wiadomość, zażąda potwierdzenia od partnera gościa. |
| Generuj pętlę SG1/SG4 dla zaakceptowanych zestawów transakcji |Jeśli została wybrana opcja żądania potwierdzenia funkcjonalności, zaznacz to pole wyboru, aby wymusić Generowanie SG1/BL4 pętli w funkcjonalności potwierdzenia CONTRL dla zaakceptowanych zestawów transakcji. |

### <a name="schemas"></a>Schematy
| Właściwość | Opis |
| --- | --- |
| UNH2.1 (TYPE) |Wybierz typ zestawu transakcji. |
| UNH2.2 (WERSJA) |Wprowadź numer wersji komunikatu. |
| UNH2.3 (ZWOLNIENIE) |Wprowadź numer wersji komunikatu. |
| SCHEMA |Wybierz schemat używany. Schematy znajdują się na koncie integracji. Aby uzyskać dostęp do Twojego schematów, najpierw połączyć konta integracji aplikacji logiki. |

### <a name="envelopes"></a>Koperty
| Właściwość | Opis |
| --- | --- |
| UNB8 (kod priorytetu przetwarzania) |Wprowadź wartość alfabetycznym, który nie jest więcej niż jeden znak. |
| UNB10 (uzgodnienie komunikacji) |Wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 40 znaków. |
| UNB11 (wskaźnik testu) |Zaznacz to pole wyboru, aby wskazać, że wymiany generowane dane testowe |
| Zastosuj segment UNA (porada ciągu usługi) |Zaznacz to pole wyboru, aby wygenerować segment UNA wymiany do wysłania. |
| Zastosuj segmenty UNG (nagłówek grupy funkcji) |Zaznacz to pole wyboru, aby utworzyć grupowanie segmenty w nagłówku grupy funkcjonalnej komunikaty wysyłane do partnera gościa. Aby utworzyć segmenty UNG używane są następujące wartości: <p>Aby uzyskać **UNG1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie sześciu znaków. <p>Aby uzyskać **UNG2.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 35 znaków. <p>Aby uzyskać **UNG2.2**, wprowadź wartość alfanumeryczne z maksymalnie cztery znaki. <p>Aby uzyskać **UNG3.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 35 znaków. <p>Aby uzyskać **UNG3.2**, wprowadź wartość alfanumeryczne z maksymalnie cztery znaki. <p>Aby uzyskać **UNG6**, wprowadź wartość alfanumeryczne z co najmniej jedną i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.1**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.2**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie trzy znaki. <p>Aby uzyskać **UNG7.3**, wprowadź wartość alfanumeryczne z co najmniej 1 znak i maksymalnie 6 znaków. <p>Aby uzyskać **UNG8**, wprowadź wartość alfanumeryczne z co najmniej jeden znak i maksymalnie 14 znaków. |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Inne niż zestaw znaków, możesz wprowadzić innego zestawu ograniczników ma być używany dla każdego typu komunikatu. Jeśli nie określono zestaw znaków dla schematu podanym komunikatem, jest używany domyślny zestaw znaków.

| Właściwość | Opis |
| --- | --- |
| UNB1.1 (identyfikator systemu) |Wybierz zestaw ma zostać zastosowany wychodzących wymiany znaków EDIFACT. |
| Schemat |Wybierz schemat z listy rozwijanej. Po wykonaniu każdego wiersza, nowy wiersz jest automatycznie dodawany. Dla wybranego schematu wybierz zestaw separatorów, do którego chcesz użyć, w oparciu o poniższe opisy separatora. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Do oddzielania elementów danych złożonych, wprowadź jeden znak. |
| Separator elementów danych |Do oddzielania elementów prostych danych w obrębie danych złożonych elementów, wprowadź jeden znak. |
| Element końcowy segmentu |Aby wskazać koniec segmentu EDI, wprowadź jeden znak. |
| Sufiks |Wybierz znak, który jest używany z identyfikatora segmentu. Po wyznaczeniu sufiksu, element danych terminator segmentu może być pusta. Terminator segmentu jest puste, należy określić sufiks. |

### <a name="control-numbers"></a>Numery kontrolne
| Właściwość | Opis |
| --- | --- |
| UNB5 (numer kontrolny wymiany) |Wprowadź prefiks zakresu wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania wychodzących wymiany. Prefiksem i sufiksem są opcjonalne, gdy numer kontrolny jest wymagana. Numer kontrolny są zwiększane dla każdej nowej wiadomości; Prefiks i sufiks pozostają takie same. |
| UNG5 (numer kontrolny grupy) |Wprowadź prefiks zakresu wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania numer kontrolny grupy. Prefiksem i sufiksem są opcjonalne, gdy numer kontrolny jest wymagana. Numer kontrolny są zwiększane dla każdej nowej wiadomości, aż do osiągnięcia maksymalnej wartości; Prefiks i sufiks pozostają takie same. |
| UNH1 (numer odwołania do nagłówka komunikatu) |Wprowadź prefiks zakresu wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania numer odwołania do nagłówka komunikatu. Prefiks i sufiks są opcjonalne, gdy wymagany jest numer odwołania. Numer referencyjny są zwiększane dla każdej nowej wiadomości; Prefiks i sufiks pozostają takie same. |

### <a name="validations"></a>Walidacje

Po ukończeniu każdego wiersza sprawdzania poprawności innego jest dodawany automatycznie. Jeśli nie określisz żadnych reguł sprawdzania poprawności używa wiersza "Default".

| Właściwość | Opis |
| --- | --- |
| Typ wiadomości |Wybierz typ komunikatu EDI. |
| Walidacja EDI |Walidacja EDI należy wykonać na typach danych, zgodnie z definicją schematu, ograniczenia długości, elementy z pustymi danymi i separatory kończące właściwości EDI. |
| Rozszerzona walidacja |Jeśli nie jest typem danych EDI, sprawdzanie poprawności jest na wymaganie element danych, powtórzenia, wyliczenia i danych weryfikacji długości elementu (min/max). |
| Zezwalaj na zera wiodące/kończące |Zachowują wszelkie dodatkowe wiodące lub końcowe zero i miejsca znaków. Nie usuwaj te znaki. |
| Przycinaj zera wiodące/kończące |Usuń początkowe lub końcowe zero znaków. |
| Zasady dotyczące separatorów kończących |Generowanie separatory kończące. <p>Wybierz **niedozwolone** do Stanów Zjednoczonych zabraniają końcowe ograniczniki i separatorów w wymianie wysłane. Jeśli wymiana zawiera końcowe ograniczniki i separatory, wymiana zadeklarowano nie prawidłowy. <p>Wybierz **opcjonalnie** wysyłać wymian z lub bez końcowe ograniczniki i separatorów. <p>Wybierz **obowiązkowe** Jeśli wysłane wymiany musi mieć końcowe ograniczniki i separatorów. |

## <a name="find-your-created-agreement"></a>Znajdź utworzony umowy

1.  Po zakończeniu na ustawienie swoje właściwości umowy **Dodaj** wybierz **OK** w stanie zakończyć tworzenia umowy, a następnie wróć do konta integracji.

    Nowo dodane umowy teraz pojawia się w Twojej **umów** listy.

2.  Można również wyświetlić Twojej umowy w przeglądzie konta integracji. W menu konta integracji, wybierz **Przegląd**, a następnie wybierz **umów** kafelka. 

    ![Wybierz Kafelek "Umowy"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Wyświetl plik struktury Swagger
Aby wyświetlić szczegóły struktury Swagger łącznika EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Dowiedz się więcej
* [Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")  

