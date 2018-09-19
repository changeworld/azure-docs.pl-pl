---
title: Zapłać z góry za rdzeni wirtualnych usługi Azure SQL Database w celu zaoszczędzenia pieniędzy | Dokumentacja firmy Microsoft
description: Dowiedz się, jak kupić pojemność usługi Azure SQL Database, zarezerwowane, aby zaoszczędzić na kosztach infrastruktury obliczeniowej.
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.devlang: na
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: carlrab
ms.openlocfilehash: e14f65c16706f8285b6ce390e75f95ea1a8bede6
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295513"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Zapłać z góry za zasoby obliczeniowe bazy danych SQL Database o pojemności usługi Azure SQL Database, zarezerwowane

Oszczędzaj pieniądze dzięki usłudze Azure SQL Database przez prepaying za zasoby obliczeniowe usługi Azure SQL Database, w porównaniu z cenami zgodnie z rzeczywistym użyciem. Pojemność usługi Azure SQL Database, zarezerwowana możesz składają zobowiązanie w bazie danych SQL w danym okresie rok lub trzy lata na uzyskanie znacznych rabatów na kosztach obliczeń. Aby kupić pojemność zastrzeżone bazy danych SQL, należy określić region platformy Azure, typ zasobu, warstwy usług i termin. 

Nie trzeba przypisać rezerwacji wystąpień bazy danych SQL. Dopasowywanie wystąpienia bazy danych SQL, które zostały już uruchomione lub tych, które są nowo wdrożone, będą automatycznie korzystać. Po zakupie rezerwacji, jesteś góry za koszty obliczeniowe dla wystąpień baz danych SQL w danym okresie rok lub trzy lata. Tak szybko, jak można kupić rezerwację, bazy danych SQL, opłaty za zasoby obliczeniowe, które odpowiadają atrybuty rezerwacji nie jest już wynoszą płatności — jako — można przejść stawki. Rezerwacja nie obejmuje opłaty za oprogramowanie, sieci lub magazynu skojarzonego z wystąpieniem bazy danych SQL. Na koniec okresu rezerwacji korzyściami dotyczącymi rozliczeń wygasa, a bazy danych SQL są naliczane płatności — jako — możesz Przejdź opłaty. Rezerwacji nie automatycznego odnawiania. Aby uzyskać informacje o cenach, zobacz [bazy danych SQL zastrzeżone oferty pojemności](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Możesz kupić pojemność usługi Azure SQL Database, zarezerwowane [witryny Azure portal](https://portal.azure.com). Aby kupić pojemność zastrzeżone bazy danych SQL:
- Musisz być w roli właściciela dla co najmniej jeden Enterprise lub subskrypcję z płatnością za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise, zakup rezerwacji platformy Azure musi być włączona w [portalu EA portal](https://ea.azure.com).
-  Dla programu Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży można kupić pojemność bazy danych SQL zastrzeżone.

Szczegółowe informacje na jak rozliczane są klientów korporacyjnych i klientów płatności za zakupy rezerwacji został omówiony w [— często zadawane pytania](#frequently-asked-questions).

## <a name="determine-the-right-sql-size-before-purchase"></a>Rozmiar jest określany na prawo SQL przed dokonaniem zakupu

Rozmiar rezerwacji powinna być oparta na całkowitej ilości zasobów obliczeniowych, używane przez istniejące lub wkrótce do--wdrażanych SQL pojedynczy baz danych i/lub pul elastycznych w określonym regionie i przy użyciu tej samej generacji sprzętu i warstwy usługi. 

Na przykład załóżmy, że używasz jednego ogólnego przeznaczenia 5. generacji — 16 rdzeniach wirtualnych elastycznej puli oraz dwóch krytyczne dla działania, 5. generacji — 4 pojedynczych baz danych (rdzeń wirtualny). Dodatkowo załóżmy powinien planujesz wdrożenie w ramach następnego miesiąca, dodatkowe ogólnego przeznaczenia, 5. generacji — 16 rdzeniach wirtualnych elastycznej puli i jedną krytyczne dla działania, 5. generacji — 32 puli elastycznej (rdzeń wirtualny). Ponadto Załóżmy, że wiesz, że te zasoby będą potrzebne do co najmniej 1 rok. W takim przypadku należy kupić 32 rdzenie wirtualne (2 x 16 pikseli), 1 rok Rezerwacja SQL bazy danych jedną/elastycznej puli ogólnego przeznaczenia — obliczenia 5. generacji i 40 (2 x 4 + 32) rdzeń wirtualny 1 rok Rezerwacja SQL bazy danych jedną/elastycznej puli krytyczne dla działania firmy — obliczenia 5. generacji.

## <a name="buy-sql-database-reserved-capacity"></a>Kup pojemność zastrzeżone bazy danych SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz **Dodaj** a następnie w okienku wybierz typ produktu wybierz **bazy danych SQL** kupić nowe zastrzeżenie dla bazy danych SQL.
4. Wypełnij wymagane pola. Istniejącym lub nowym pojedynczych baz danych lub elastycznej puli pasujący atrybuty, które możesz wybrać kwalifikują się uzyskać rabat w wysokości rezerwowanie pojemności. Rzeczywista liczba wystąpień bazy danych SQL, które Rabat zależy od tego, zakresu i ilość wybrana.

   ![Zrzut ekranu, przed przesłaniem bazy danych SQL zastrzeżone zakupu pojemności](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Pole      | Opis|
    |:------------|:--------------|
    |Name (Nazwa)        |Nazwa tego zastrzeżenia.| 
    |Subskrypcja|Subskrypcja użytej do zapłacenia za rezerwację pojemności bazy danych SQL zastrzeżone. Metodę płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwacji wydajności bazy danych SQL zastrzeżone. Wymagany typ subskrypcji to Enterprise Agreement (numer oferty MS-AZR-0017P) lub Płatność zgodnie z rzeczywistym użyciem (numer oferty MS-AZR-0003P). W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka. W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.|    
    |Zakres       |Zakres rezerwacji pamięci rdzeń wirtualny może obejmować subskrypcji jednej lub wielu subskrypcji (zakres udostępniony). Jeśli wybierzesz: <ul><li>Subskrypcja pojedyncza — rabat związany z rezerwacją pamięci rdzeń wirtualny jest stosowany do wystąpienia bazy danych SQL w ramach tej subskrypcji. </li><li>Czy rabat związany z rezerwacją pamięci rdzeń wirtualny w warstwie współdzielona — są stosowane do wystąpień bazy danych SQL w ramach dowolnej subskrypcji w ramach kontekstu rozliczeń. Dla klientów korporacyjnych zakres udostępniony jest rejestracji i zawiera wszystkie subskrypcje (z wyjątkiem tworzenia i testowania subskrypcje) w ramach rejestracji. Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.</li></ul>|
    |Region      |Region platformy Azure, który pasuje do żadnego z bazą danych SQL zastrzeżone rezerwacji wydajności.|    
    |Typ zasobu|Typ zasobu SQL, który chcesz kupić rezerwację dla.|
    |Warstwy usług|Warstwy usług dla wystąpień baz danych SQL.
    |Termin        |Jeden rok lub trzy lata.|
    |Ilość    |Liczba wystąpień, które zostały zakupione w ramach bazy danych SQL zastrzeżone rezerwacji wydajności. Ilość jest liczby uruchomionych wystąpień bazy danych SQL, które mogą uzyskać rabat rozliczeń. Na przykład jeśli używasz 10 wystąpień bazy danych SQL w regionie wschodnie stany USA, następnie należy określić ilość jako 10, aby zmaksymalizować korzyści dla wszystkich działających maszyn. |

5. Przegląd koszt bazy danych SQL zastrzeżone rezerwacji pojemności **koszty** sekcji.
6. Wybierz pozycję **Kup**.
7. Wybierz **Wyświetl tę rezerwację** Aby wyświetlić stan zakupu.

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Jeśli potrzebujesz anulować bazy danych SQL zastrzeżone rezerwację zdolności produkcyjnych, może być 12% zakończenia opłatę za wcześniejsze zakończenie. Zwroty zależą od najniższej ceny zakupu cen lub bieżąca cena zastrzeżenia. Zwroty są ograniczone do 50 000 USD rocznie. Zwrot kosztów, które otrzymujesz jest pozostałe saldo proporcjonalnie pomniejszona o 12% zakończenia opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **zwrot** do utworzenia żądania obsługi.

Jeśli musisz zmienić rezerwacji wydajności bazy danych SQL zastrzeżone do innego regionu, typu zasobu, warstwę usług lub termin mogą wymieniać go do innej rezerwacji, która jest większa lub równa wartości. Data rozpoczęcia okresu dla nowej rezerwacji nie jest przenoszone z wymiana rezerwacji. Wartość 1 lub 3 lat. rozpoczyna się podczas tworzenia nowej rezerwacji. Aby zażądać programu exchange, przejdź do rezerwacji w witrynie Azure portal i wybierz **Exchange** do utworzenia żądania obsługi.

## <a name="vcore-size-flexibility"></a>elastyczność rozmiaru (rdzeń wirtualny)

elastyczność rozmiaru (rdzeń wirtualny) ułatwia skalowanie w górę lub w dół w ramach warstwy wydajności i region, bez utraty korzyści rezerwowanie pojemności. Wydajności bazy danych SQL zastrzeżone również zapewnia elastyczność tymczasowo przenieść gorąca bazy danych między pulami i pojedynczych baz danych w ramach normalnych operacji (w ramach tego samego regionu i wydajność warstwy) bez utraty rezerwowanie pojemności korzyści. Przechowując buforu bez zastosowanych w rezerwacji, umożliwiają wydajne zarządzanie wzrostów wydajności, nie przekraczając Twojego budżetu.

## <a name="next-steps"></a>Kolejne kroki

Rabat związany z rezerwacją pamięci rdzeń wirtualny jest automatycznie stosowany do wystąpień bazy danych SQL, które odpowiadają zakresem rezerwacji wydajności bazy danych SQL zastrzeżone i atrybutów. Można zaktualizować zakresu rezerwacji wydajności bazy danych SQL zastrzeżone za pośrednictwem [witryny Azure portal](https://portal.azure.com), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API. 

Aby dowiedzieć się, jak zarządzać bazą danych SQL zastrzeżone rezerwacji pojemności, zobacz [wydajności rezerwowej Zarządzanie usługą SQL Database](../billing/billing-manage-reserved-vm-instance.md).

Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:

- [Co to jest Azure rezerwacje?](../billing/billing-save-compute-costs-reservations.md)
- [Zarządzanie usługą Azure Reservations](../billing/billing-manage-reserved-vm-instance.md)
- [Świadomość, że rezerwacje platformy Azure z rabatami](../billing/billing-understand-reservation-charges.md)
- [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](../billing/billing-understand-reserved-instance-usage.md)
- [Opis zastrzeżenia dla Twojej rejestracji Enterprise](../billing/billing-understand-reserved-instance-usage-ea.md)
- [Rezerwacje platformy Azure w programie partnera Centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal masz dodatkowe pytania, [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) można szybko rozwiązać swój problem.

