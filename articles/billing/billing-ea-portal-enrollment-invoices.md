---
title: Faktury za rejestracje w usłudze Azure Enterprise
description: W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise i wykonywać związane z nimi działania.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: d6faf56554d666ce29b68ba90bb920b4e6c56c30
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644566"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za rejestracje w usłudze Azure Enterprise

W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise i wykonywać związane z nimi działania. Faktura odzwierciedla naliczone opłaty, dlatego należy sprawdzić, czy jest prawidłowa. Warto również zapoznać się z innymi zadaniami, które mogą być wymagane w związku z fakturami.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Zmiana numeru zamówienia zakupu na fakturze nadwyżkowej

W witrynie Azure EA Portal automatycznie generowany jest domyślny numer zamówienia zakupu, o ile administrator umowy EA nie ustawił tego numeru przed datą wystawienia faktury. Administrator umowy EA może zaktualizować numer zamówienia zakupu w ciągu siedmiu dni od otrzymania automatycznej wiadomości e-mail z powiadomieniem o fakturze.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu usług platformy Azure:

1. W witrynie Azure EA Portal kliknij pozycję **Raport**, a następnie **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Usługi platformy Azure**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat. Numery zamówień zakupu można edytować przez maksymalnie siedem dni po powiadomieniu o fakturze lub do momentu zapłacenia faktury zależnie od tego, co nastąpi wcześniej.
1. Podaj nowy numer zamówienia zakupu w polu  **Numer zamówienia zakupu** .
1. Kliknij pozycję  **Zapisz** , aby przesłać zmianę.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu na platformie handlowej:

1. W witrynie Azure EA Portal kliknij pozycję **Raport**, a następnie **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Platforma handlowa**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat. Numery zamówień zakupu można edytować przez maksymalnie siedem dni po powiadomieniu o fakturze lub do momentu zapłacenia faktury zależnie od tego, co nastąpi wcześniej.
1. Podaj nowy numer zamówienia zakupu w polu  **Numer zamówienia zakupu** .
1. Kliknij pozycję  **Zapisz** , aby przesłać zmianę.

## <a name="cadence-of-azure-ea-billing"></a>Okresowość rozliczeń dla umowy Azure EA

### <a name="billing-intervals"></a>Interwał rozliczeń

Firma Microsoft corocznie w rocznicę wejścia rejestracji w życie dokonuje rozliczeń dotyczących zakupów usług platformy Microsoft Azure w ramach zobowiązań oraz zaległości dla użycia przekraczającego kwoty zobowiązania. Opłaty dla zobowiązania są określane na podstawie stawki miesięcznej i rozliczane rocznie z góry. Opłaty nadwyżkowe są obliczane w każdym miesiącu i rozliczane jako zaległości na końcu okresu rozliczeniowego.

W zależności od wybranego sposobu dokonywania zakupów w ramach zobowiązania, roczne zobowiązanie będzie przypadać na rocznicę rejestracji lub datę aktywacji jednorocznego aneksu subskrypcji.

Fakturę nadwyżkową otrzymasz w zależności od daty rozpoczęcia rejestracji i konfiguracji.

**Rejestracje bezpośrednie z datą początkową przed 1 maja 2018 r.** : Bezpośredni klienci umowy Enterprise Azure (EA) są skonfigurowani pod kątem rocznego cyklu rozliczeniowego dla usług platformy Azure (bez usług z witryny Marketplace). Twój cykl rozliczeniowy jest oparty na dacie rocznicy. Data rocznicy to data wejścia w życie Twojej umowy. Aby otrzymać pierwszą fakturę nadwyżkową platformy Azure, musisz przekroczyć 150% swojego progu zobowiązania pieniężnego.  Gdy łączne użycie usług przekroczy 150% progu zobowiązania pieniężnego, nastąpi automatyczna konwersja na kwartalny cykl rozliczeniowy zgodnie z datą rocznicy.  Jeśli nie przekroczysz 150% progu zobowiązania pieniężnego, rejestracja pozostanie objęta rocznym cyklem rozliczeniowym, a faktura nadwyżkowa zostanie wstawiona na koniec roku zobowiązania.

**Rejestracje bezpośrednie z datą początkową po 1 maja 2018 r.** : Faktury dla użycia platformy Azure i opłat rozliczanych oddzielnie dla klienta będą objęte miesięcznym cyklem rozliczeniowym.  Opłaty, które nie są objęte zobowiązaniem pieniężnym platformy Azure, będą należne jako płatności nadwyżkowe.  

**Rejestracje pośrednie z datą początkową przed 1 maja 2018 r.** : Pośredni klienci umowy Enterprise Azure (EA) są skonfigurowani pod kątem kwartalnego cyklu rozliczeniowego.  Partner handlowy będzie wystawiać faktury bezpośrednio klientowi.  

**Rejestracje pośrednie z datą początkową po 1 maja 2018 r.** : Każda umowa pośrednia z datą początkową 1 maja 2018 r. lub późniejszą będzie rozliczana miesięcznie.  

### <a name="increasing-commitment"></a>Zwiększanie zobowiązania

Zobowiązanie można zwiększyć w dowolnym momencie, a opłaty zostaną naliczone za liczbę miesięcy pozostałą dla okresu zobowiązania w danym roku. Jeśli na przykład zarejestrujesz się w ramach jednorocznego aneksu subskrypcji i zwiększysz swoje zobowiązanie w ciągu szóstego miesiąca, faktura zostanie wystawiona za pozostałe sześć miesięcy tego okresu. Ilości dla Twojego zobowiązania zostaną wtedy zaktualizowane na ostatnie sześć miesięcy okresu zobowiązania na potrzeby obliczenia wszelkich opłat za użycie nadwyżkowe.

### <a name="overage"></a>Nadwyżka

W przypadku nadwyżki opłaty są naliczane za użycie lub rezerwacje, które przekroczyły zobowiązanie w okresie rozliczeniowym. Aby wyświetlić szczegółowe informacje o sposobie obliczenia ilości nadwyżkowych dla poszczególnych pozycji, zapoznaj się z raportem podsumowania użycia lub skontaktuj się z partnerem handlowym.

Dla każdej pozycji na fakturze zobaczysz łączną opłatę (wartość), kwotę zobowiązania użytą do pokrycia opłat (użycie zobowiązania) i kwotę opłat w ramach przekroczenia zobowiązania (wartość netto).  Odpowiednie podatki są obliczane tylko od kwoty netto przekroczenia zobowiązania.

Wystawianie faktur nadwyżkowych jest zautomatyzowane.  Czas wysyłania powiadomień i faktur zależy od daty zakończenia okresu rozliczeniowego klienta.  Powiadomienie o nadwyżce jest wysyłane zwykle siedem dni po dacie zakończenia okresu rozliczeniowego klienta. W tym czasie klienci mogą przejść do portalu, przejrzeć opłaty i zaktualizować numery zamówień zakupu wygenerowane przez system (można to także zrobić w dowolnym momencie przed zwolnieniem nadwyżki).  Faktury nadwyżkowe zostaną wysyłane 7-9 dni później.

### <a name="azure-marketplace"></a>Azure Marketplace

Od cyklu rozliczeniowego przypadającego na kwiecień 2019 r. klienci zaczną otrzymywać pojedynczą fakturę dla platformy Azure, ponieważ połączyliśmy wszystkie opłaty za platformę Azure i witrynę Azure Marketplace w ramach jednej faktury, która zastępuje dwie oddzielne faktury. (Ta zmiana nie ma wpływu na klientów w Australii, Japonii lub Singapurze). Podczas przejścia do faktury skonsolidowanej otrzymasz częściową fakturę za witrynę Marketplace. Końcowa oddzielna faktura będzie zawierać opłaty za witrynę Marketplace sprzed daty aktualizacji rozliczeń. Opłaty za witrynę Marketplace po tej dacie będą widoczne na fakturze za platformę Azure. Po okresie przejściowym zobaczysz wszystkie opłaty za platformę Azure i witrynę Marketplace na skonsolidowanej fakturze.  

### <a name="purchase-order-numbers"></a>Numery zamówień zakupu

Domyślny numer zamówienia zakupu jest generowany przez system. Użytkownicy mogą zaktualizować numer zamówienia zakupu w portalu Enterprise Portal, logując się jako administrator przedsiębiorstwa i przechodząc do sekcji Raporty. W prawym górnym rogu okna znajduje się pole z numerem zamówienia zakupu, które umożliwia administratorowi przedsiębiorstwa edytowanie numeru zamówienia zakupu po kliknięciu ikony ołówka.

## <a name="adjust-billing-frequency"></a>Zmiana częstotliwości fakturowania

Klienci otrzymują faktury w cyklu rocznym, kwartalnym lub miesięcznym. Cykl rozliczeniowy jest określany podczas podpisywania umowy przez klienta. Rozliczanie miesięczne to najkrótszy cykl rozliczeniowy.

Aby zmienić cykl rozliczeniowy w przypadku rejestracji bezpośrednich, wymagane jest zatwierdzenie przez administratora przedsiębiorstwa. W przypadku rejestracji pośrednich wymagane jest zatwierdzenie przez administratora partnera. Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.

Aby zmienić roczny lub kwartalny cykl rozliczeniowy na miesięczny, wymagana jest poprawka do umowy.  Każda zmiana aktualnego cyklu rozliczeniowego dla rejestracji wymaga zatwierdzenia przez administratora przedsiębiorstwa lub przez osobę wskazaną w umowie jako _Kontakt odbiorcy faktury_. Można przesłać zatwierdzenie za pomocą formularza [Azure EA Portal — pomoc techniczna](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c), wybierając kategorię problemu **Rozliczenia i fakturowanie**.  Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.

W przypadku podpisania poprawki M503 można zmienić częstotliwość fakturowania dla dowolnej umowy na miesięczną. 

## <a name="credits-and-adjustments"></a>Środki i korekty

Wszystkie środki i korekty zastosowane do rejestracji są widoczne w portalu [https://ea.azure.com](https://ea.azure.com) w sekcji **Raporty**.

Aby wyświetlić środki, wykonaj następujące kroki:

1. Wybierz sekcję **Raporty**.
1. Kliknij pozycję **Podsumowanie użycia**.
1. W prawym górnym rogu zmień widok _M_ na _C_.
1. Rozwiń pole korekty w tabeli zobowiązań usług platformy Azure.
1. W tym wierszu zobaczysz środki zastosowane dla rejestracji i krótkie wyjaśnienie, na przykład: Środki dla umowy dotyczącej poziomu usług.

## <a name="request-an-invoice-copy"></a>Żądanie kopii faktury

Aby zażądać kopii faktury, skontaktuj się z partnerem.

## <a name="overage-offset-by-customers"></a>Przesunięcie opłat za użycie nadwyżkowe przez klientów

Jeśli klient ma nadwyżki, których koszty chce pokryć w ramach zobowiązania pieniężnego, muszą być spełnione następujące kryteria:

- Klient ma naliczone, ale nieuregulowane opłaty za nadwyżki, a od daty zakończenia rozliczanej usługi nie minął jeszcze jeden rok.
- Dostępna kwota zobowiązania pieniężnego pokrywa w całości naliczone opłaty, w tym wszystkie poprzednie nieuregulowane faktury za platformę Azure.
- Okres rozliczeniowy wybrany do uzupełnienia jest całkowicie zamknięty. Rozliczenia są całkowicie zamykane po 5. dniu każdego miesiąca.
- Okres rozliczeniowy wybrany do przesunięcia jest całkowicie zamknięty.
- Rabaty ACD opierają się na rzeczywistej wysokości nowego zobowiązania pieniężnego, od której odejmuje się wszelkie środki przeznaczone na opłacenie poprzedniego użycia. To wymaganie dotyczy tylko naliczonych opłat za użycie nadwyżkowe. To rozwiązanie można zastosować tylko do usług rozliczanych ze zobowiązania pieniężnego — nie można w ten sposób regulować opłat z platformy Marketplace. Opłaty na platformie Marketplace są rozliczane osobno.
- Jeśli klient chce wykonać przesunięcie opłat za użycie nadwyżkowe, może utworzyć wniosek o pomoc techniczną. Taki wniosek o pomoc techniczną może też utworzyć zespół ds. obsługi klientów. Aby ukończyć ten proces, wymagane jest zatwierdzenie w formie wiadomości e-mail przez administratora umowy EA klienta lub osobę wskazaną jako Kontakt odbiorcy faktury.

## <a name="view-price-sheet-information"></a>Wyświetlanie informacji o cenniku

Administratorzy przedsiębiorstwa mogą wyświetlać cennik usług platformy Azure przypisany do ich rejestracji.

Aby wyświetlić bieżący cennik:

1. W witrynie Azure EA Portal kliknij pozycję **Raporty**, a następnie kliknij pozycję **Arkusz cen**.
2. Wyświetl arkusz cen lub kliknij pozycję **Pobierz**.

![Przykład przedstawiający informacje o cenniku](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Aby pobrać cennik historyczny:

1. W witrynie Azure EA Portal kliknij pozycję **Raporty**, a następnie kliknij pozycję **Pobierz zestawienie użycia**.
2. Pobierz arkusz cen.

![Przykład pokazujący, jak pobrać starszy arkusz cen](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Jeśli masz pytania dotyczące różnic w cenach, zapoznaj się z wymienionymi poniżej potencjalnymi przyczynami:

Cennik mógł ulec zmianie między poprzednią rejestracją a nową rejestracją. Zmiany cen następują, ponieważ ceny zależą od umowy związanej z konkretną rejestracją i obowiązują od daty rozpoczęcia umowy do daty jej zakończenia. W momencie przeniesienia rejestracji do nowej rejestracji zaczyna obowiązywać cennik z nowej umowy. Ceny są określane przez arkusz cen klienta. W związku z tym ceny w nowej rejestracji mogą być wyższe.

Cennik zmienia się też w momencie przedłużenia rejestracji na czas nieokreślony. Zaczyna wówczas obowiązywać cennik płatności zgodnie z rzeczywistym użyciem.

## <a name="request-detailed-usage-information"></a>Żądanie szczegółowych informacji o użyciu

Administratorzy przedsiębiorstwa mogą wyświetlać podsumowanie danych użycia, wykorzystanych zobowiązań pieniężnych i opłat związanych z dodatkowym użyciem w witrynie Azure EA Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

Aby wyświetlić szczegółowe informacje o użyciu na określonych kontach, można pobrać raport Szczegóły użycia, przechodząc do pozycji **Raporty** > **Pobierz zestawienie użycia**. Raport nie zawiera żadnych należnych podatków. Między momentem, w którym wystąpiło użycie, a odzwierciedleniem tego użycia w raporcie może wystąpić opóźnienie do ośmiu godzin.

W przypadku rejestracji pośrednich partner musi włączyć funkcję marży, zanim będzie można wyświetlać informacje związane z kosztami.

## <a name="reports"></a>Raporty

Administratorzy przedsiębiorstwa mogą wyświetlić podsumowanie danych użycia, użytego zobowiązania pieniężnego i opłat związanych z dodatkowym użyciem w witrynie Enterprise Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

**Raporty umowy EA**

- Podsumowanie i wykresy użycia
- Raport użycia usługi
- Raport Saldo i opłaty
- Raport Szczegóły użycia
- Raport opłat za witrynę Marketplace
- Arkusz cen
- Zaawansowane pobieranie raportu
- Formatowanie raportu CSV

**Aby wyświetlić raporty i wykresy podsumowania użycia**

1. W witrynie Azure EA Portal w obszarze nawigacji po lewej kliknij pozycję **Raporty** i wyświetl kartę **Podsumowanie użycia**.
1. Wybierz termin zobowiązania z menu rozwijanego zakresów dat w lewym górnym rogu.
1. Wybierz okres lub miesiąc na wykresie, aby wyświetlić dodatkowe szczegóły.
1. Wyświetl wykres użycia miesiąc do miesiąca z podziałem na wykorzystane użycie, nadpłatę za usługę, opłaty naliczane osobno i opłaty za korzystanie z witryny Marketplace.
1. W wybranym miesiącu filtruj według działów, kont i subskrypcji poniżej wykresu.
1. Przełącz się między podziałami Opłata według usług i Opłata według hierarchii.
1. Wyświetl szczegóły dotyczące usług platformy Azure, opłat naliczonych osobno i opłat za witrynę Azure Marketplace.

## <a name="service-usage-report"></a>Raport użycia usługi

Strona Raport użycia usługi umożliwia administratorowi przedsiębiorstwa wyświetlenie podsumowania danych użycia usługi. Choć użycie będzie przedstawione jako podsumowanie dla wszystkich kont i subskrypcji, możesz także filtrować raport według kont lub subskrypcji, aby wyświetlić szczegółowe użycie.

Należy pamiętać, że może wystąpić maksymalnie pięciodniowe opóźnienie między datą rzeczywistego użycia a odzwierciedleniem tego użycia w raporcie.

### <a name="to-view-the-report"></a>Aby wyświetlić raport:

1. Zaloguj się do witryny Enterprise Portal.
1. Kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. Kliknij kartę **Podsumowanie użycia**.
1. Kliknij żądany zakres dat.
1. Wybierz konta lub subskrypcje do wyświetlenia.
1. Zmień widok z Opłaty według usług lub Opłaty według hierarchii, aby wyświetlić różne podziały.
1. Wyświetl szczegółowe informacje, w tym nazwę usługi, jednostkę miary, użyte jednostki, efektywną stawkę i koszt rozszerzony.

## <a name="download-csv-reports"></a>Pobieranie raportów CSV

Strona Pobieranie raportu miesięcznego umożliwia administratorom przedsiębiorstwa pobranie kilku raportów jako plików CSV, w tym raportów Saldo i opłaty, Szczegóły użycia i Opłaty za witrynę Marketplace oraz cennika.

## <a name="to-download-reports"></a>Aby pobrać raporty:

1. W witrynie Azure EA Portal kliknij pozycję **Raport**.
1. Wybierz pozycję **Pobierz użycie** na górnej wstążce.
1. Wybierz pozycję **Pobierz** obok raportu za odpowiedni miesiąc.



## <a name="csv-report-formatting"></a>Formatowanie raportu CSV

Klienci przeglądający raporty CSV z kwotami w euro w witrynie Azure EA Portal mogą napotkać problemy z formatowaniem w odniesieniu do przecinków i kropek.

Możesz na przykład zobaczyć następujące dane:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Godziny | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Powinien zostać wyświetlony następujący ekran:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Godziny | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Główna przyczyna

Program Excel importuje wszystkie pola jako ogólny tekst i zakłada, że liczby są zapisane za pomocą zapisu matematycznego: „1,000.00”.  Dlatego waluta europejska używająca kropki (.) jako separatora tysięcy i przecinka (,) jako separatora miejsc dziesiętnych — „1.000,00” — będzie wyświetlana nieprawidłowo. Problem może być inny w zależności od ustawień regionalnych dla języka.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Podczas importowania pliku CSV wykonaj następujące kroki:

1.    Otwórz program Excel i przejdź do pozycji Plik > Otwórz.
1.    Zostanie wyświetlony kreator importowania tekstu.
1.    W pozycji Typ danych źródłowych wybierz wartość _Rozdzielany_.  Wartość domyślna to _Stała szerokość_.
1.    Kliknij przycisk **Dalej**.
1.    W obszarze Ograniczniki zaznacz pole wyboru Przecinek. Zaznaczenie domyślne to „Tabulator” (usuń je).
1.    Kliknij przycisk **Dalej**.
1.    Przewiń do kolumn „ResourceRate” i „ExtendedCost”.
1.    Wybierz kolumnę „ResourceRate” (zostanie wyróżniona kolorem czarnym).
9.    W polu Format danych w kolumnie wybierz opcję „Tekst” zamiast „Ogólny”.  Zobaczysz, że nagłówek kolumny zmieni się z „Ogólny” na „Tekst”.
10. Powtórz kroki od 8 do 9 dla kolumny „ExtendedCost”. Wybierz pozycję **Finish** (Zakończ).

Jeśli program Excel jest skojarzony w celu automatycznego otwierania plików \*.csv, musisz zamiast tego użyć funkcji „Otwórz” w programie Excel. Otwórz program Excel i przejdź do pozycji Plik > Otwórz.

## <a name="balance-and-charge-report"></a>Raport Saldo i opłaty

Raport Saldo i opłaty oferuje miesięczne podsumowanie informacji o saldach, nowych zakupach, opłatach za witrynę Azure Marketplace, korektach i opłatach za użycie nadwyżkowe. Wszystkie wiersze w tabeli Zobowiązanie usługi platformy Azure pozostaną bez zmian w ciągu miesiąca, a szczegóły dotyczące wszystkich zakupów i korekt będą wyświetlane w sekcjach Szczegóły nowych zakupów i Szczegóły korekty.

### <a name="download-the-balance-and-charge-report"></a>Pobieranie raportu Saldo i opłaty

1. Zaloguj się do witryny Azure EA Portal jako administrator przedsiębiorstwa.
1. Kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. Kliknij kartę **Pobieranie raportu**.
1. Wybierz odpowiedni miesiąc w kolumnie _Saldo i opłaty_ i kliknij go, aby pobrać raport.

## <a name="usage-detail-report"></a>Raport Szczegóły użycia

Raport Szczegóły użycia zawiera miesięczne podsumowanie dotyczące usług i ilości użytych w ramach rejestracji, w tym informacje o nazwach mierników, typach mierników i użytych ilościach.

### <a name="download-the-usage-detail-report"></a>Pobieranie raportu Szczegóły użycia

1. Zaloguj się do witryny Azure EA Portal jako administrator przedsiębiorstwa.
1. Kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. Kliknij kartę **Pobierz zestawienie użycia**.
1. Wybierz odpowiedni miesiąc w kolumnie _Szczegóły użycia_ i kliknij go, aby pobrać raport.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Raport Opłaty za witrynę Marketplace w witrynie Azure EA Portal

Dodatkowe informacje na temat opłat za witrynę Marketplace można znaleźć [tutaj](https://azure.microsoft.com/marketplace/faq/).

Istnieją dwa typy opłat za platformę handlową:

- **Na podstawie użycia:** Produkty mierzone w jednostkach transakcyjnych.  Na przykład opłaty za maszyny wirtualne są naliczane co godzinę, a opłaty za wyszukiwanie za pomocą interfejsu API usługi Bing są naliczane według liczby wyszukiwań.
- **Opłata miesięczna:** Rozliczana miesięcznie na podstawie użycia lub dostępu.

Aby wyświetlić różne opłaty w witrynie Enterprise Portal:

1. **Raport Podsumowanie użycia —** pokazuje opłaty naliczane na podstawie użycia **oraz** miesięczne opłaty za platformę handlową.
1. **Raport Opłaty za witrynę Marketplace —** zawiera **tylko** opłaty za platformę handlową naliczane na podstawie użycia.  Opłaty jednorazowe nie są wyświetlane.

Zwróć uwagę, że witryna Azure Marketplace nie jest dostępna dla rejestracji w ramach umowy MPSA.

## <a name="advanced-report-download"></a>Zaawansowane pobieranie raportu

W przypadku raportowania dla określonych zakresów dat lub kont można użyć funkcji zaawansowanego pobierania raportu. Od 30 sierpnia 2016 r. format pliku wyjściowego został zmieniony z xlsx na csv w celu przystosowania do większych zestawów rekordów.

1. Wybierz pozycję **Zaawansowane pobieranie raportu**.
1. Wybierz pozycję **Odpowiedni zakres dat**.
1. Wybierz pozycję **Odpowiednie konta**.
1. Wybierz pozycję **Żądaj danych użycia**.
1. Wybieraj przycisk **Odśwież** do momentu aktualizacji stanu raportu na „Pobierz”.
1. Pobierz raport.

## <a name="reporting-for-non-enterprise-administrators"></a>Raportowanie dla użytkowników innych niż administratorzy przedsiębiorstwa

Administratorzy przedsiębiorstwa mogą włączyć dostęp do wyświetlania kosztów dla administratorów działu i właścicieli konta w ramach rejestracji. Po jego włączeniu właściciel konta może pobrać raporty w formacie csv specyficzne dla swojego konta i swoich subskrypcji oraz wyświetlić informacje dostępne w sekcji raportowania witryny Enterprise Portal.

### <a name="to-enable-access"></a>Aby włączyć dostęp:

 1. Zaloguj się jako administrator przedsiębiorstwa.
 1. Kliknij pozycję **Zarządzaj** w obszarze nawigacji po lewej.
 1. Kliknij kartę **Rejestracja**.
 1. W sekcji Szczegóły rejestracji wybierz ikonę ołówka obok pozycji:
    - Administrator oddziału — wyświetl opłaty
    - Właściciel konta — wyświetl opłaty
 1. Wybierz pozycję **Włączone**.
 1. Kliknij pozycję **Zapisz**.

### <a name="to-view-reports"></a>Aby wyświetlić raporty:

1. Zaloguj się w witrynie Azure EA Portal jako administrator działu lub właściciel konta.
1. Kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. Kliknij kartę **Podsumowanie użycia**, aby wyświetlić informacje dotyczące konta i subskrypcji.
1. Kliknij pozycję **Pobieranie użycia**, aby wyświetlić raporty csv.

Administratorzy działu i właściciele konta nie mogą wyświetlić opłat za pomocą funkcji _Zaawansowane pobieranie raportu_. Kwota kosztów będzie wyświetlana jako 0 USD.

Funkcjonalność wyświetlania opłat przez właściciela konta dotyczy właściciela konta i wszystkich użytkowników, którzy mają uprawnienia do skojarzonych subskrypcji. Jeśli jesteś klientem pośrednim, funkcje kosztów muszą zostać włączone przez partnera handlowego.

## <a name="move-usage-data-to-another-enrollment"></a>Przenoszenie danych dotyczących użycia do innej rejestracji

Dane dotyczące użycia są przenoszone tylko wtedy, gdy przeniesienie ma datę wsteczną. Istnieją dwie opcje przenoszenia danych dotyczących użycia z jednej rejestracji do innej:

- Przeniesienie kont między rejestracjami.
- Przeniesienie rejestracji między rejestracjami.

W przypadku każdej z tych opcji należy przesłać [wniosek o pomoc techniczną](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) do zespołu pomocy technicznej EA. 

## <a name="azure-ea-pricing-overview"></a>Omówienie cennika umowy Azure EA

Ten dokument zawiera szczegółowe informacje na temat sposobu obliczania użycia i odpowiedzi na wiele często zadawanych pytań dotyczących cen różnych usług platformy Azure w programach dla przedsiębiorstw, w przypadku których obliczenia są bardziej skomplikowane.

### <a name="price-protection"></a>Ochrona cen

Klienci i partnerzy handlowi (w przypadku kanałów pośrednich) mają gwarancję otrzymania cen nie wyższych niż określone w cenniku dla klienta lub obowiązujące w dniu wejścia w życie zakupu platformy Azure. Cena ta jest określana jako cena bazowa. W przypadku usług wprowadzonych po dokonaniu zakupu jest to cena obowiązująca z rabatem odpowiedniego poziomu w momencie pierwszego wprowadzenia usługi. Ochrona cen ma zastosowanie w okresie obowiązywania zobowiązania, który może trwać od jednego do trzech lat w zależności od programu dla przedsiębiorstw.

### <a name="price-changes"></a>Zmiany cen

Firma Microsoft może obniżyć bieżącą cenę w ramach programu dla przedsiębiorstw dotyczącą poszczególnych usług platformy Azure w okresie rejestracji.  Rozszerzymy te obniżone stawki na istniejących klientów w okresie obowiązywania niższej ceny.  Jeśli stawki zostaną później zwiększone, cena dla rejestracji dotycząca usługi nie zostanie podwyższona ponad górny limit ochrony cen klienta zdefiniowany powyżej, ale może zostać podwyższona względem poprzedniej, obniżonej ceny.  W obu przypadkach firma Microsoft poinformuje klientów i pośrednich partnerów handlowych o nadchodzących zmianach cen za pośrednictwem wiadomości e-mail wysłanych do administratorów przedsiębiorstwa i partnerów skojarzonych z rejestracją.

### <a name="current-effective-pricing"></a>Aktualnie obowiązujące ceny

Klient i partnerzy handlowi mogą wyświetlić bieżące ceny dla rejestracji, logując się do [witryny Azure Enterprise Portal](https://ea.azure.com/) i przechodząc do strony cennika dla rejestracji.  W przypadku zakupu platformy Azure za pośrednictwem jednego z partnerów handlowych należy uzyskać aktualizacje cennika od partnera handlowego, jeśli nie włączył on wyświetlania narzutów cenowych dla rejestracji.

### <a name="introduction-of-new-azure-services"></a>Wprowadzenie nowych usług platformy Azure

Ciągle ulepszamy platformę Azure i okresowo dodajemy nowe usługi, które są wyceniane niezależnie od istniejących usług.  Niektóre usługi w wersji zapoznawczej są dostępne automatycznie, a inne wymagają od klienta wykonania akcji za pośrednictwem witryny [Azure Account Portal](https://account.windowsazure.com/PreviewFeatures). Zwróć uwagę na to, że po przejściu usługi z wersji zapoznawczej do ogólnie dostępnej ceny mogą wzrosnąć ze względu na zastosowanie pełnych umów SLA dotyczących wydajności i niezawodności. I w końcu niektóre usługi są wprowadzane po raz pierwszy z cenami promocyjnymi, które mogą zostać podwyższone w przyszłości. Każda taka podwyżka z ceny za wersję zapoznawczą lub promocyjnej do ceny za wersję ogólnie dostępną nie jest ograniczona przez standardową cenę bazową i będzie stosowana do dalszego korzystania z tych usług. Klienci mogą uniknąć naliczania opłat związanych z nowymi usługami, unikając ich używania.

### <a name="introduction-of-regional-pricing"></a>Wprowadzenie cen regionalnych

Poza wprowadzaniem nowych usług, także model usług okresowo zmienia się z pojedynczego globalnego na bardziej regionalny w miarę wzrostu regionalnego wsparcia dla poszczególnych usług. Przy pierwszym wprowadzeniu regionów dla usługi ochrona cen dotyczy nowych regionów w porównaniu do wcześniejszej ceny globalnej obowiązującej dla rejestracji. Jednak dodatkowe regiony wprowadzone później dla tej samej usługi są traktowane jako nowe usługi i oferowane za własne indywidualne stawki niezależnie od ochrony ceny bazowej.

### <a name="enterprise-msdn-devtest"></a>Enterprise — MSDN: tworzenie i testowanie

Administratorzy przedsiębiorstwa mogą umożliwić właścicielom konta tworzenie subskrypcji opartych na ofercie EA — MSDN: tworzenie i testowanie w ramach umowy EA. Aby ta funkcja działała poprawnie, właściciel konta musi skonfigurować subskrypcje programu EA — MSDN: tworzenie i testowanie wymagane dla bazowych subskrybentów programu MSDN. Umożliwia to aktywnym subskrybentom programu MSDN uruchamianie obciążeń programistycznych i testowych na platformie Azure przy użyciu specjalnych stawek dla opracowywania i testowania. Aby uzyskać więcej informacji, zobacz [Tworzenie i testowanie (przedsiębiorstwo)](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Wskazówki dotyczące obliczeń użycia w ramach programu dla przedsiębiorstw

Zapoznaj się z [usługami platformy Azure](https://azure.microsoft.com/services/) i [cennikiem platformy Azure](https://azure.microsoft.com/pricing/), aby uzyskać podstawowe informacje o cenach publicznych i jednostkach miary oraz poznać często zadawane pytania i wskazówki dotyczące raportowania użycia dla poszczególnych usług. Dodatkowo podczas obliczania użycia usługi należy stosować wskazówki dotyczące programu dla przedsiębiorstw.

### <a name="enterprise-program-units-of-measure"></a>Jednostki miary dotyczące programu dla przedsiębiorstw

Jednostki miary w przypadku programów dla przedsiębiorstw są często inne niż w przypadku innych programów, takich jak Microsoft Online Services Agreement Service (MOSA). Oznacza to, że dla pewnej liczby usług jednostka miary jest agregowana w celu zapewnienia znormalizowanych cen. Jednostka miary wyświetlana w widoku Podsumowanie użycia w witrynie Enterprise Portal jest zawsze miarą dla przedsiębiorstw. Pełna lista bieżących jednostek miary i konwersji dla każdej usługi jest podana w dokumencie [przyjaznych nazw usług](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Reguły zaokrąglania

Zaokrąglenia w witrynie Enterprise Portal są wykonywane przy użyciu standardowej logiki zaokrąglania bankierskiego, czyli gaussowskiego, zgodnej z normą organizacji IEEE. W jej przypadku wartości połówkowe są zaokrąglane do najbliższej cyfry parzystej. Bardziej popularne zaokrąglanie od połowy w górę zawsze zaokrągla wartość połówkową w górę do pełnej cyfry. Ta metoda daje dokładniejszą sumę łączną dla grupy w porównaniu do standardowej logiki programu Excel.

Gdy pierwsza odrzucana cyfra to 5 i nie ma po niej cyfr lub są to zera, zmień poprzedzającą cyfrę na parzystą (tzn. zaokrąglij do najbliższej parzystej cyfry). Na przykład liczby 2,315 i 2,325 to 2,32 po zaokrągleniu do najbliższej setnej.

Dla porównania — w przypadku korzystania z programu Excel do modelowania reguł zaokrąglania i konwersji używanych w witrynie Enterprise Portal należy użyć formuł MROUND jak pokazano poniżej.

| Scenariusz | Wzór dla logiki bankierskiej |
| --- | --- |
| Użycie zaokrąglania | =MROUND({_source_}, 0.0002) |
| Ceny zaokrąglone (2 miejsca dziesiętne) | =MROUND({_source_}, 0.02) |
| Ceny zaokrąglone (0 miejsc dziesiętnych) | =MROUND({_source_}, 2) |

**Tabela**  **2**  **— Usługa w chmurze i konwersja godzin maszyny wirtualnej**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Konwersja między plikiem dostępnym za pomocą polecenia Pobierz zestawienie użycia i widokiem Podsumowanie użycia w portalu

Nieprzetworzone dane użycia zasobów są raportowane z dokładnością do sześciu miejsc dziesiętnych, co można zobaczyć w raporcie danych dostępnym za pomocą polecenia Pobierz zestawienie użycia. Jednak witryna Azure Enterprise Portal zaokrągla wartości użycia do czterech miejsc dziesiętnych dla jednostek zobowiązania i do wartości całkowitych dla jednostek nadwyżki. Nieprzetworzone użycie jest najpierw zaokrąglane do czterech cyfr dziesiętnych przed konwersją na jednostkę miary dla przedsiębiorstw, a wynikowa wartość w jednostkach dla przedsiębiorstw jest zaokrąglana ponownie do czterech cyfr. Rzeczywista liczba użytych godzin przed konwersją jest wyświetlana tylko w raporcie użycia, a nie w samym interfejsie użytkownika.

Przykład: w raporcie użycia zgłoszono 694,533404 godziny rzeczywistego użycia programu SQL Server. Te jednostki są następnie konwertowane na 6,94533404 jednostek 100 godzin obliczeniowych, które są następnie zaokrąglane do wartości 6,9453 na potrzeby wyświetlenia w witrynie Enterprise Portal.

Te jednostki są następnie po prostu mnożone przez cenę jednostkową zobowiązania, a wynik jest obcinany do dwóch miejsc dziesiętnych w celu określenia rozszerzonej kwoty rozliczenia. W przypadku jena japońskiego (JPY) i wona koreańskiego (KRW) rozszerzona kwota jest zaokrąglana do jedności.

Dla tego samego przykładu w przypadku nadwyżki rozliczane jednostki są obcinane do sześciu miejsc dziesiętnych, a następnie mnożone przez cenę jednostki nadwyżki w celu określenia rozszerzonej kwoty rozliczenia. W przypadku rozliczeń dla dostawcy usług zarządzanych całe użycie skojarzone z działem oznaczonym jako dostawca usług zarządzanych jest zaokrąglane do jednostek po konwersji na jednostkę miary umowy EA, tak jak w przypadku raportowania nadwyżki. W wyniku suma tego użycia może być niższa niż łączna suma całego użycia zgłoszonego w interfejsie użytkownika w zależności od tego, czy dostawca usług zarządzanych nadal mieści się w swoim saldzie zobowiązania pieniężnego, czy też jest już objęty nadwyżką.

### <a name="graduated-pricing"></a>Ceny stopniowane

Cennik programu dla przedsiębiorstw nie obejmuje obecnie cen stopniowanych (gdzie cena za jednostkę zmniejsza się w miarę wzrostu wykorzystania). W ramach przechodzenia z programu MOSA z cenami stopniowanymi do programu dla przedsiębiorstw ceny są określanie proporcjonalnie do warstwy podstawowej usługi po uwzględnieniu rabatów programu dla przedsiębiorstw, jeśli mają to zastosowanie.

### <a name="partial-hour-billing"></a>Częściowe naliczanie godzinowe

Całe użycie jest rozliczane według minut przekonwertowanych na częściowe godziny w miejsce przyrostów godzinowych.  Stawki dla przedsiębiorstw wymienione jako godzinowe są po prostu stosowane do łącznej liczby godzin, w tym wszystkich godzin częściowych.

### <a name="average-daily-consumption"></a>Średnie użycie dzienne

Gdy cena dotyczy miesięcznego użycia usługi, ale użycie jest raportowane codziennie, to użycie jest oceniane raz dziennie, dzielone przez 31 i sumowane dla liczby dni w danym miesiącu rozliczeniowym. Powoduje to, że stawki nigdy nie są wyższe niż oczekiwane dla każdego miesiąca i nieco niższe dla miesięcy krótszych niż 31 dni.

### <a name="compute-hours-conversion"></a>Konwersja godzin obliczeniowych

Wcześniej informacje o użytkowaniu maszyn wirtualnych i usług w chmurze opcji A0, A2, A3 i A4 w warstwach Standardowa i Podstawowa były zawsze generowane jako ułamki (dla opcji A0) lub wielokrotności (dla opcji A2, A3 i A4) minut mierników maszyn wirtualnych A1. Było to czasem mylące dla klientów, więc wprowadziliśmy zmianę polegającą na przypisaniu użycia na minutę do dedykowanych mierników dla opcji A0, A2, A3 i A4.

Nowe pomiary zaczęły obowiązywać w okresie między 27 stycznia a 28 stycznia 2016 r. W pliku csv dla wdrożenia pobranym w okresie przejściowym można zauważyć dwie pozycje: jedną dla wygenerowanego użycia wskazanego przez miernik A1 i drugą dla wygenerowanego użycia wskazanego przez nowy, dedykowany miernik odpowiadającym rozmiarowi wdrożenia.

| **Rozmiar wdrożenia** | **Użycie wygenerowane jako wielokrotność opcji A1 do 26 stycznia 2016 r.** | **Wygenerowane użycie wskazane przez dedykowany miernik od 27 stycznia 2016 r.** |
| --- | --- | --- |
| A0 | 0,25 godziny opcji A1 | 1 godzina opcji A0 |
| A2 | 2 godziny opcji A1 | 1 godzina opcji A2 |
| A3 | 4 godziny opcji A1 | 1 godzina opcji A3 |
| A4 | 8 godzin opcji A1 | 1 godzina opcji A4 |

### <a name="regions"></a>Regiony

Dla usług, w przypadku których strefa i region mają wpływ na ceny, poniższa tabela przedstawia mapowanie dla obszarów geograficznych i regionów:

| Obszar geograficzny | Regiony transferu danych | Regiony usługi CDN |
| --- | --- | --- |
| Strefa 1 | Europa Północna <br> Europa Zachodnia <br> Zachodnie stany USA <br> Wschodnie stany USA <br> Północno-środkowe stany USA <br> Południowo-środkowe stany USA <br> Wschodnie stany USA 2 <br> Środkowe stany USA | Ameryka Północna <br> Europa |
| Strefa 2 | Wschodni region Azji i Pacyfiku <br> Południowo-Wschodni region Azji i Pacyfiku <br> Japonia Wschodnia <br> Japonia Zachodnia <br> Australia Wschodnia <br> Australia Południowo-Wschodnia | Azja i Pacyfik <br> Japonia <br> Ameryka Łacińska <br> Bliski Wschód/Afryka <br> Australia Wschodnia <br> Australia Południowo-Wschodnia |
| Strefa 3 | Brazylia Południowa |   |

**Tabela**  **4**  **— Regiony transferu danych**

Nie ma opłat za ruch wychodzący między usługami (np. O365 i Azure) hostowanymi w tym samym centrum danych.

### <a name="monetary-commitment-and-unbilled-usage"></a>Zobowiązanie pieniężne i nierozliczone opłaty za użycie

Zobowiązanie pieniężne platformy Azure to uiszczana z góry opłata za usługi platformy Azure. Zobowiązanie pieniężne jest wykorzystywane w miarę używania usług. Usługi platformy Azure dostarczane przez firmę Microsoft są rozliczane przy użyciu zobowiązania pieniężnego. Istnieją jednak wyjątki, obejmujące opłaty rozliczane oddzielnie oraz usługi platformy Marketplace.

### <a name="charges-billed-separately"></a>Opłaty rozliczane oddzielnie

Niektóre produkty i usługi udostępniane ze źródeł innych firm nie używają zobowiązania pieniężnego platformy Azure. Zamiast tego te pozycje są rozliczane osobno w ramach faktury nadwyżkowej dla standardowego cyklu rozliczeniowego.

Połączyliśmy wszystkie opłaty za platformę Azure i witrynę Marketplace w pojedynczą fakturę, której cykl odpowiada cyklowi rozliczeniowemu rejestracji. (Ta zmiana nie dotyczy klientów w Australii, Japonii lub Singapurze).

W ramach skonsolidowanej faktury najpierw jest wyświetlone użycie platformy Azure, a następnie wszystkie opłaty za witrynę Marketplace. W przypadku klientów w Australii, Japonii lub Singapurze opłaty za witrynę Marketplace będą nadal znajdować się na oddzielnej fakturze.

Jeśli na końcu standardowego cyklu rozliczeniowego nie ma użycia nadwyżkowego, opłaty naliczane osobno będą znajdować się na oddzielnej fakturze. (Dotyczy klientów w Australii, Japonii i Singapurze).

**Usługi rozliczane oddzielnie to:**

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)

## <a name="azure-marketplace-for-ea-customers"></a>Witryna Azure Marketplace dla klientów umowy EA

W przypadku klientów bezpośrednich opłaty za platformę handlową są widoczne w witrynie Azure EA Portal. Zakupy w witrynie Marketplace i użycie będą rozliczane kwartalnie/miesięcznie poza zobowiązaniem pieniężnym jako zaległości.

Pośredni klienci mogą znaleźć swoje subskrypcje witryny Azure Marketplace na stronie „Zarządzanie subskrypcjami” w witrynie Azure EA Portal, lecz ceny będą ukryte. Klienci powinni skontaktować się ze swoim dostawcą LSP, aby uzyskać informacje o opłatach za platformę handlową.

Nowe cykliczne miesięczne lub roczne zakupy na platformie handlowej będą całkowicie rozliczane w okresie, w którym dokonano zakupu na platformie handlowej. Te pozycje zostaną odnowione automatycznie w następnym okresie w dniu oryginalnego zakupu.

Nie wpłynie to na istniejące cykliczne opłaty za platformę handlową. Miesięczne opłaty cykliczne będą w dalszym ciągu odnawiane w pierwszym dniu każdego miesiąca kalendarzowego, a opłaty roczne będą odnawiane w rocznicę daty zakupu.

Niektóre usługi odsprzedawców innych firm dostępne w witrynie Azure Marketplace używają teraz salda zobowiązania pieniężnego w ramach umowy Enterprise Agreement (EA). Wcześniej te usługi były rozliczane poza zobowiązaniem pieniężnym umowy EA i na oddzielnej fakturze. Zobowiązanie pieniężne umowy EA dla tych usług w witrynie Marketplace upraszcza zarządzanie zakupami i płatnościami klientów. Aby uzyskać pełną listę usług, które obecnie używają zobowiązania pieniężnego, zobacz [witrynę platformy Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Partnerzy

Dostawcy LSP mogą pobrać cennik dla określonej platformy handlowej, przechodząc do cennika w witrynie Azure EA Portal i klikając link **Cennik platformy handlowej** w prawym górnym rogu. Cennik platformy handlowej będzie zawierać wszystkie dostępne usługi i ich ceny.

**Aby pobrać cennik, wykonaj następujące kroki:**

1. Przejdź do pozycji Raporty > Cennik.
1. W prawym górnym rogu odszukaj link do cennika witryny Azure Marketplace pod Twoją nazwą użytkownika.
1. Kliknij prawym przyciskiem myszy link i wybierz polecenie **Zapisz element docelowy jako**.
1. W oknie zapisywania zmień tytuł dokumentu na _AzureMarketplacePricelist.zip_, co spowoduje zmianę pliku xlsx w plik zip.
1. Po zakończeniu pobierania będziesz mieć plik zip z cennikiem dla określonego kraju.
1. Dostawcy LSP powinni odwoływać się do pliku konkretnego kraju w celu określenia cen specyficznych dla kraju. Dostawcy LSP powinni użyć karty „Powiadomienia”, aby zobaczyć jednostki SKU, które są całkiem nowe na platformie handlowej, oraz usunięte jednostki SKU.
1. Zmiany cen będą rzadkie, lecz w przypadku ich wystąpienia dostawcy LSP zostaną powiadomieni o nich oraz o zmianach kursów wymiany z 30-dniowym wyprzedzeniem za pomocą wiadomości e-mail.
1. Dostawcy LSP będą dostawać jedna fakturę dla każdej kombinacji rejestracji, dostawcy ISV i kwartału.

### <a name="enabling-marketplace-purchases"></a>Aktywowanie zakupów na platformie handlowej

Administratorzy przedsiębiorstwa mogą „wyłączać” lub „włączać” zakupy na platformie handlowej dla wszystkich subskrypcji platformy Azure w ramach rejestracji. Jeśli administrator przedsiębiorstwa wyłączy zakupy i istnieją subskrypcje platformy Azure, które już mają subskrypcje platforma handlowej, to te subskrypcje nie zostaną anulowane i pozostaną bez zmian.

Mimo że klienci mogą konwertować bezpośrednie subskrypcje platformy Azure do umowy EA, kojarząc je ze swoją rejestracją w witrynie Azure EA Portal, ta akcja nie powoduje automatycznej konwersji podrzędnych subskrypcji platformy handlowej.

**Aby włączyć zakupy na platforma handlowej:**

1. Zaloguj się do witryny Azure EA Portal jako administrator przedsiębiorstwa.
1. Przejdź do pozycji _Zarządzaj_.
1. W obszarze _Szczegóły rejestracji_ kliknij ikonę ołówka obok pozycji _Azure Marketplace_.
1. Przełącz odpowiednio opcję _Włączone/Wyłączone_ lub _Tylko jednostki SKU Bezpłatna/BYOL\*_ .
1. Kliknij pozycję **Zapisz**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Raport Opłaty za witrynę Marketplace w witrynie Azure EA Portal

Dodatkowe informacje na temat opłat za platformę handlową można znaleźć [tutaj](https://azure.microsoft.com/marketplace/faq/).

Istnieją dwa typy opłat za platformę handlową:

- **Na podstawie użycia:** Produkty są mierzone w jednostkach transakcyjnych.  Na przykład opłaty za maszyny wirtualne są naliczane co godzinę, a opłaty za wyszukiwanie za pomocą interfejsu API usługi Bing są naliczane według liczby wyszukiwań.
- **Nie na podstawie użycia:** Opłata jednorazowa lub cykliczna opłata miesięczna, która jest niezależna od użycia.

Opłaty na podstawie użycia i pozostałe są umieszczane w raporcie opłat za platformę handlową.

Zwróć uwagę, że witryna Azure Marketplace nie jest dostępna dla rejestracji umowy MPSA.

\*Opcja Tylko jednostki SKU Bezpłatna/BYOL powoduje ograniczenie zakupów i nabywania jednostek SKU witryny Azure Marketplace do jednostek SKU bezpłatnych i typu BYOL.

### <a name="services-billed-hourly-for-ea"></a>Usługi rozliczane godzinowo dla umowy EA

Zasób Application Delivery Network i zapora aplikacji internetowej są rozliczane godzinowo dla umowy EA w porównaniu do stawki miesięcznej w programie MOSP.

### <a name="remote-app"></a>Aplikacja zdalna

Klienci z umową EA płacą za aplikację zdalną na podstawie poziomu cen umowy EA i nie są obciążani dodatkowo. Standardowa cena bazowa obejmuje początkowe 40 godzin, a nieograniczona cena bazowa obejmuje początkowe 80 godzin. Aplikacja zdalna nie generuje użycia przez 80 godzin.

## <a name="azure-marketplace-faq"></a>Witryna Azure Marketplace — często zadawane pytania

Ten dokument z często zadawanymi pytaniami umożliwia przejrzenie aktualizacji dotyczących stosowania zobowiązania pieniężnego platformy Azure dla niektórych usług publikowanych przez inne firmy w witrynie Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Co się zmienia w związku z usługami platformy handlowej i zobowiązaniem pieniężnym platformy Azure?

Klienci otrzymują osobną fakturę za wszystkie usługi, które kupują w witrynie Azure Marketplace. Nie dotyczy to wystąpień zarezerwowanych maszyn wirtualnych platformy Azure. Rozszerzamy korzystanie ze zobowiązania pieniężnego platformy Azure pod kątem niektórych usług w witrynie Azure Marketplace opublikowanych przez inne firmy i często kupowanych przez naszych klientów.

### <a name="why-are-we-making-this-change"></a>Dlaczego wprowadzamy tę zmianę?

Klienci nieustannie szukają dodatkowych sposobów korzystania z płatności z góry wniesionych w postaci zobowiązania pieniężnego platformy Azure.  Odnieśliśmy się do częstych żądań klientów i wpłynęliśmy na dużą część klientów witryny Azure Marketplace, rozszerzając zobowiązanie pieniężne na te usługi.

### <a name="what-is-the-customer-benefit"></a>Jak skorzystają klienci?

Klienci uzyskują prostsze rozliczenia i możliwość zapewnienia, że wykorzystają swoje zobowiązanie pieniężne platformy Azure.  Dodanie tej korzyści do przedpłaconego zobowiązania pieniężnego i wystąpień zarezerwowanych za pomocą zobowiązania pieniężnego jeszcze bardziej zwiększa wartość zobowiązania pieniężnego platformy Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Opłaty za jakie usługi zostaną potrącone ze zobowiązania pieniężnego platformy Azure i w jaki sposób moi klienci dowiedzą się o nich?

W środowisku zakupów w witrynie Azure Marketplace wyróżniamy każdą usługę, która będzie używać zobowiązania pieniężnego, za pomocą zastrzeżenia. Obecnie obsługiwane usługi to niektóre usługi opublikowane przez firmy Red Hat, SUSE, Autodesk i Oracle. Opłaty za usługi z podobnymi konwencjami nazewnictwa, lecz opublikowane przez inne firmy niewymienione powyżej, nie będą odejmowane ze zobowiązania pieniężnego. Pełna lista jest dostępna na końcu sekcji często zadawanych pytań.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Co stanie się, jeśli zobowiązanie pieniężne mojego klienta zostanie wyczerpane?

W przypadku klientów, którzy wykorzystali swoje całe zobowiązanie pieniężne i są teraz objęci nadwyżką, opłaty związane z tymi usługami będą znajdować się na następnej fakturze nadwyżkowej wraz z pozostałym użyciem usług.  Jest to zmiana, ponieważ wcześniej te opłaty zostałyby umieszczone na osobnej fakturze razem z innymi ofertami z witryny Azure Marketplace.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Dlaczego nie włączamy zobowiązania pieniężnego platformy Azure dla wszystkich zakupów na platformie handlowej?

Często pracujemy nad zapewnieniem dla klientów najlepszego środowiska związanego z zobowiązaniem pieniężnym platformy Azure. Ta zmiana będzie dotyczyć dużej liczby klientów i znaczącej części sum wydawanych w witrynie Azure Marketplace. Inne usługi mogą zostać dodane w przyszłości.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jak wpływa to na rejestracje pośrednie i partnerów?

Nie ma to wpływu na klientów rejestracji pośredniej ani partnerów. Te usługi są objęte tymi samymi możliwościami zastosowania narzutu przez partnera co inne usługi oparte na użyciu. Jedyną zmianą będzie faktura, na której będą się znajdować, i wnoszenie opłat za pośrednictwem zobowiązania pieniężnego.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Lista usług, za które opłaty są odliczane ze zobowiązania pieniężnego platformy Azure

Określone oferty z witryny Azure Marketplace mogą korzystać ze środków zobowiązania pieniężnego. Zobacz [Zobowiązanie pieniężne platformy Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment), aby uzyskać pełną listę produktów biorących udział w tym programie.

## <a name="additional-information"></a>Dodatkowe informacje

Aby uzyskać więcej informacji, zobacz następujące dodatkowe źródła. Te pliki są aktualizowane dwa razy w miesiącu — 6. i 20. dnia każdego miesiąca. Szczegółowe informacje dotyczące każdego pliku:

| Tytuł dodatku | Opis | Konwencja nazewnictwa adresów URL |
| --- | --- | --- |
| [**Przyjazne nazwy usług**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Zawiera listę wszystkich aktywnych usług z kategorią usługi, przyjazną nazwą usługi, nazwą i numerem katalogowym zobowiązania, nazwą i numerem katalogowym użycia, jednostkami miary i współczynnikiem konwersji między zgłoszonym użyciem a użyciem wyświetlanymi w witrynie Enterprise Portal. | Friendly\_Service\_Names.xlsx |
| [**Pola pobierania usługi**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Ten arkusz kalkulacyjny zawiera listę wszystkich możliwych kombinacji pól dotyczących usługi w pobieranym raporcie użycia. | Service\_Download\_Fields.xlsx |

**Tabela**  **5**  **— Dodatkowe źródła informacji**

## <a name="power-bi-reporting"></a>Raportowanie usługi Power BI

### <a name="power-bi-pro"></a>Power BI Pro

Usługa Power BI Pro jest teraz dostępna dla klientów umowy EA. Za pomocą usługi Power BI Pro można generować i udostępniać raporty w celu efektywnego zarządzania danymi kosztów przy użyciu dodatkowych funkcji współpracy i odświeżania danych. Usługa Power BI Pro zapewnia wyższe limity pojemności danych i przesyłania strumieniowego danych. Atrakcyjne nowe funkcje zarządzania kosztami dla klientów umowy Azure Enterprise będą dostępne wkrótce.

Obecni użytkownicy usługi Power BI (wersja bezpłatna) korzystający z pakietu zawartości Microsoft Azure Consumption Insights mogą skorzystać z 60-dniowej bezpłatnej wersji próbnej usługi Power BI Pro. Jeśli chcesz nadal korzystać z usługi Power BI Pro po zakończeniu okresu bezpłatnej wersji próbnej, musisz dodać licencję.

Aby zarejestrować się w celu uzyskania bezpłatnej wersji próbnej, przejdź do ikony koła zębatego i wybierz pozycję **Zarządzaj magazynem osobistym**. Następnie wybierz pozycję **Wypróbuj bezpłatnie wersję Pro** po prawej stronie. Aby uzyskać więcej informacji na temat bezpłatnej wersji próbnej usługi Power BI Pro, zobacz [Samodzielne rejestrowanie się w usłudze Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial).

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Warunki dotyczące wersji próbnej usługi Power BI Pro dla umowy Microsoft Azure EA

- **Ogólne przeznaczenie**: Rozszerzona usługa Power BI Pro dla oferty wersji próbnej pakietu zawartości „Microsoft Azure Enterprise” („Oferty”) jest dostępna dla istniejących uprawnionych użytkowników w okresie obowiązywania oferty, aby umożliwić im dostęp do pewnych szczegółowych informacji związanych z ich użyciem platformy Microsoft Azure za pomocą określonego pakietu zawartości usługi Power BI.
- **Uprawnienie**: Użytkownicy objęci umową Enterprise Agreement (EA) mogą uczestniczyć w Ofercie, jeśli zajmują stanowisko związane z rozliczeniami, usługami i/lub zarządzaniem kosztami platformy Microsoft Azure w swojej organizacji.
- **Wykluczenia**:
  - Użytkownicy już korzystający z rozszerzonej wersji próbnej usługi Power BI Pro będą nadal uprawnieni w ramach wcześniej istniejącej oferty i nie mogą skorzystać z oferty wersji próbnej usługi Power BI Pro dla umowy EA.
  - Użytkownicy uczestniczący w ofercie mogą używać usługi Power BI Pro tylko za pomocą pakietu zawartości Microsoft Azure Enterprise. Wszelkie inne użycie usługi Power BI Pro jest zabronione.
  - Termin: Oferta rozpocznie się w dniu 1 czerwca 2017 r. i zakończy się 31 maja 2018 r.  Zgody można udzielić w dowolnym momencie 12-miesięcznego okresu, jednak oferta zostanie zakończona 31 maja 2018 r. dla wszystkich użytkowników niezależnie od tego, czy zaakceptowali Ofertę.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Aby uzyskać dostęp do pakietu zawartości Microsoft Azure Consumption Insights:

1. Przejdź do pozycji [Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Kliknij pozycję **Pobierz teraz**.
1. Podaj numer rejestracji i liczbę miesięcy. Kliknij przycisk **Dalej**.
1. Podaj klucz dostępu interfejsu API, aby nawiązać połączenie. Klucz rejestracji można znaleźć w [witrynie Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Kliknij pozycję **Zaloguj**.
1. Proces importowania rozpocznie się automatycznie. Po jego zakończeniu nowy pulpit nawigacyjny, raport i model pojawią się w okienku nawigacji. Kliknij pulpit nawigacyjny, aby wyświetlić zaimportowane dane.

Aby uzyskać więcej informacji na temat generowania klucza interfejsu API dla rejestracji, zobacz plik pomocy dotyczący raportów interfejsu API w [witrynie Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Aby uzyskać więcej informacji na temat nowego pakietu zawartości, pobierz dokument [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Aby uzyskać dostęp do starszego pakietu zawartości usługi Power BI w ramach umowy EA:

 1. Przejdź do [witryny internetowej usługi Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Zaloguj się za pomocą ważnego konta służbowego.
    - Konto służbowe może być takie samo jak użyte w celu uzyskania dostępu do rejestracji za pomocą witryny Azure EA Portal lub inne.
 1. Na pulpicie nawigacyjnym usług wybierz pozycję **Microsoft Azure Enterprise**, a następnie kliknij pozycję **Połącz**.
 1. Na ekranie „Nawiązywanie połączenia z platformą Azure Enterprise” wybierz:
    - Adres URL środowiska platformy Azure: [https://ea.azure.com](https://ea.azure.com/).
    - Liczba miesięcy: wybierz z przedziału od 1 do 36.
    - Numer rejestracji: podaj numer rejestracji.
    - Kliknij przycisk **Dalej**.
 1. W polu Klucz uwierzytelniania podaj klucz interfejsu API. Klucz interfejsu API możesz uzyskać tutaj lub w witrynie Azure Enterprise Portal: na karcie „Pobierz użycie” powyżej, kliknij pozycję „Klucz dostępu interfejsu API”.
    - Skopiuj i wklej klucz w polu „Klucz konta”.
 1. Załadowanie danych do usługi Power BI trwa około 5-30 minut w zależności od rozmiaru zestawów danych.

Funkcja raportowania usługi Power BI jest dostępna dla bezpośrednich klientów umowy EA, partnerów i klientów pośrednich, którzy mają dostęp do wyświetlania informacji dotyczących rozliczeń.

## <a name="report-faq"></a>Często zadawane pytania dotyczące raportowania

W tej części artykułu znajdują się odpowiedzi na często zadawane pytania dotyczące interpretowania raportów.

### <a name="why-is-my-cost-showing-as-0"></a>Dlaczego mój koszt jest wyświetlany jako 0 USD?

**Rejestracja bezpośrednia** Jeśli jesteś właścicielem konta lub administratorem działu, skontaktuj się z administratorem umowy EA, aby włączyć funkcję cennika:

1. Kliknij pozycję **Zarządzaj** w obszarze nawigacji po lewej.
1. Kliknij symbol niebieskiego ołówka obok opłat w widoku administratora działu.
1. Wybierz pozycję **Włączono** i zapisz.
1. Kliknij symbol niebieskiego ołówka obok opłat w widoku właściciela konta.
1. Wybierz pozycję **Włączono** i zapisz.

Ta akcja udostępni właścicielom konta i administratorom działu informacje o kosztach/cenach w raportach użycia.

**Rejestracja pośrednia** Sprawdź, czy partner włączył dla Ciebie funkcję cennika. Może to zrobić tylko partner, a po włączeniu tej funkcji można wyświetlić koszt i ceny w ramach rejestracji jako administrator umowy EA.

Jeśli chcesz włączyć funkcję wyświetlania opłat dla właścicieli konta i administratora działu, wykonaj kroki wymienione w sekcji **Rejestracja bezpośrednia** powyżej.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Raport szczegółów użycia nie zawiera informacji o jednostkach SKU.

Raport szczegółów użycia nie zawiera informacji o jednostkach SKU, jednak można wyświetlić informacje o usługach użyte w raporcie. Następnie można pobrać raport z cennikiem, aby uzyskać informacje o jednostkach SKU.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>Łączna kwota dla witryny Marketplace nie jest zgodna z podsumowaniem użycia ani raportem CSV

Raport Opłaty za witrynę Marketplace zawiera tylko opłaty za platformę handlową naliczone na podstawie użycia. Opłaty jednorazowe nie są wyświetlane. Można zapoznać się ze stroną Podsumowanie użycia, aby uzyskać najbardziej aktualne informacje o użyciu dla opłat na podstawie użycia i jednorazowych.

### <a name="there-is-no-information-on-my-api-report"></a>Mój raport interfejsu API nie zawiera informacji

Klucze interfejsu API wygasają co sześć miesięcy. Wygeneruj nowy klucz interfejsu API, jeśli wystąpił problem. Ważne jest także, aby zażądać od administratora umowy EA wygenerowania nowych kluczy interfejsu API i wykonania kroków opisanych w artykule z często zadawanymi pytaniami dotyczącymi raportów interfejsu API.

### <a name="my-power-bi-report-isnt-working"></a>Mój raport usługi Power BI nie działa

W przypadku problemów z usługą Power BI zgłoś bilet pomocy technicznej w zespole usługi Power BI pod adresem [https://support.powerbi.com](https://support.powerbi.com), aby mógł on udzielić Ci pomocy.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>Moje tagi zasobów nie są umieszczane w raportach

Tagami zasobów zarządza się w witrynie Azure Portal. Możesz skontaktować się z zespołem subskrypcji platformy Azure w witrynie [https://portal.azure.com](https://portal.azure.com). Wykonaj kroki opisane pod [tym linkiem](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request), aby dowiedzieć się, jak zgłosić wniosek o pomoc techniczną.

### <a name="why-does-my-resource-rate-change-every-day"></a>Dlaczego moja stawka zasobu zmienia się codziennie?

Stawka zasobu w szczegółowym raporcie użycia to wartość obliczana, która reprezentuje średnią miesięczną stawkę naliczaną za usługę. Ta stawka jest obliczana za pomocą średniej miesięcznego zobowiązania i miesięcznych opłat nadwyżkowych za jednostkę usługi. Część użycia naliczona dla zobowiązania i stawki nadwyżkowe będą się zmieniać do dnia zamknięcia miesiąca. W związku z tym stawka zasobu także będzie się zmieniać w ciągu miesiąca. Stawka zasobu jest blokowana piątego dnia po końcu miesiąca.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Słownik procesów obliczania stawki zasobu

**Łączna liczba NIEPRZETWORZONYCH jednostek:** Użyta ilość w szczegółowym raporcie użycia.
**Zasobów MOCP na jednostkę:** Nadrzędny system użycia generuje użycie dla każdej usługi w innych jednostkach. (Ustawienie wstępne) **Użycie na jednostkę:** Jednostka miary umowy EA. (Ustawienie wstępne) **Cena:** Cena jednostkowa w witrynie Azure EA Portal.
**Łączny koszt:** Rozszerzony koszt z raportu szczegółowego użycia lub użycia zobowiązania i nadwyżki z witryny Azure EA Portal.


### <a name="charges-calculation"></a>Obliczanie opłat

**Konwertowanie na zasoby MOCP na jednostkę** = ROUND (całkowita liczba nieprzetworzonych jednostek * zasoby MOCP na jednostkę, 4)**Konwertowanie na jednostki** = jednostki po skonwertowaniu na zasoby MOCP na jednostkę / Zużycie na jednostkę **Łączny koszt** = Jednostki * Cena

### <a name="download-usage-calculation-logic"></a>Pobieranie logiki obliczeń użycia

**Stawka zasobu** = Łączny koszt / (Łączna liczba NIEPRZETWORZONYCH jednostek / Zasoby MOCP na jednostkę)

Stawka zasobu jest określana na podstawie opłat i często nie jest zgodna z rzeczywistą ceną jednostkową w cenniku.

W przypadku obliczania opłat nadwyżkowych nieprzetworzone dane użycia zasobów są raportowane z dokładnością do sześciu miejsc dziesiętnych, co można zobaczyć w raporcie danych dostępnym za pomocą polecenia Pobierz zestawienie użycia. Jednak witryna Azure EA Portal zaokrągla wartości użycia do czterech miejsc dziesiętnych dla jednostek zobowiązania i do wartości całkowitych dla jednostek nadwyżki. Oznacza to, że w witrynie Azure EA Portal wszystkie opłaty nadwyżkowe są naliczane wyłącznie za pełne jednostki. Wystąpi znaczna różnica między ceną jednostkową a stawką zasobu za użycie naliczaną jako nadwyżka lub w mieszanych miesiącach.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać informacje na temat faktur i opłat, zobacz [Omówienie rachunku za umowę Azure Enterprise Agreement](billing-understand-your-bill-ea.md).
- Aby dowiedzieć się, jak rozpocząć korzystanie z witryny Azure EA Portal, zobacz [Wprowadzenie do witryny Azure EA Portal](billing-ea-portal-get-started.md).
