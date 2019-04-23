---
title: Skorzystaj z możliwości przedpłaty na opłaty za magazyn danych SQL za pomocą platformy Azure rezerwowanie pojemności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przedpłaty na opłaty za magazyn danych SQL za pomocą rezerwowanie pojemności, aby zaoszczędzić pieniądze.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013810"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Skorzystaj z możliwości przedpłaty na opłaty za magazyn danych SQL za pomocą rezerwowanie pojemności

Możesz oszczędzić pieniądze dzięki usłudze Azure SQL Data Warehouse, prepaying zrozumiała dla Ciebie cDWU czas trwania rok lub trzy lata. Aby kupić pojemność SQL Data Warehouse zarezerwowane, musisz wybrać Azure region i czas trwania. Następnie Dodaj jednostkę SKU magazynu danych SQL do koszyka i wybierz ilość jednostek cDWU, które chcesz kupić.

Po zakupie rezerwacji, SQL Data Warehouse użycia zgodną atrybuty rezerwacji jest już naliczana płatności — jako — można przejść stawki.

Rezerwacja nie obejmuje pamięci masowej lub sieci opłaty skojarzone z użyciem SQL Data Warehouse.

Po wygaśnięciu rezerwowanie pojemności wystąpienia SQL Data Warehouse będą nadal działać, ale są rozliczane według stawki płatności — jako — możesz Przejdź. Rezerwacji nie odnowiony automatycznie.

Aby uzyskać informacje o cenach, zobacz [SQL Data Warehouse zastrzeżone oferty pojemności](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Możesz kupić pojemność usługi Azure SQL Data Warehouse zarezerwowane [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Aby kupić rezerwowanie pojemności:

- Konieczne jest posiadanie roli właściciela dla co najmniej jeden enterprise lub subskrypcję płatność za rzeczywiste użycie.
- W przypadku subskrypcji Enterprise **Dodawanie wystąpień zarezerwowanych** należy włączyć opcję [portalu EA portal](https://ea.azure.com/). Jeśli ustawienie jest wyłączone, musi być administratorem umowy EA.
- Program Cloud Solution Provider (CSP) tylko agentów administratora lub agentom sprzedaży można kupić pojemność SQL Data Warehouse zastrzeżone.

Aby uzyskać więcej informacji na temat jak rozliczane są klientów korporacyjnych i klientów płatności za zakupy rezerwacji, zobacz [użycia platformy Azure rezerwacji dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md) i [zrozumienie platformy Azure użycie rezerwacji dla subskrypcji z płatnością za rzeczywiste użycie](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Wybierz odpowiednie rozmiar przed dokonaniem zakupu

SQL Data Warehouse, w których wielkość rezerwacji powinna być oparta na łączną jednostek obliczeniowych magazynu danych (cDWU), które zostaną zużyte. Możliwe są zakupy cDWU przyrostem 100.

Na przykład przyjęto założenie, że Twoje całkowite użycie usługi SQL Data Warehouse jest DW3000c. Umożliwia rezerwowanie pojemności dla wszystkich jej zakup. Tak musisz kupić 30 jednostek pojemności cDWU zastrzeżone.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Kup pojemność magazynu danych SQL zastrzeżone

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **wszystkich usług** > **rezerwacje**.
3. Wybierz subskrypcję. Użyj listy subskrypcji, aby wybrać subskrypcję, która jest używana do zapłacenia za rezerwowanie pojemności. Formy płatności dla subskrypcji jest naliczana opłata z góry kosztów rezerwowanie pojemności. Typ subskrypcji musi być umowy enterprise agreement (oferują liczb: MS-AZR-0017P lub MS-AZR - 0148 P) lub płatność za rzeczywiste użycie (oferują liczb: MS-AZR-0003P lub MS-AZR - 0023 P).
  - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
  - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
4. Wybierz zakres. Użyj listy zakresów, aby wybrać zakres subskrypcji.
  - Za pomocą **pojedynczego** opcja, rabat związany z rezerwacją jest stosowana w usłudze SQL Data Warehouse wdrożone w ramach wybranej subskrypcji.
  - Za pomocą **Shared** opcji Rabat związany z rezerwacją jest stosowany do wystąpień działających w ramach dowolnej subskrypcji w ramach kontekstu rozliczeń.
    - Dla klientów korporacyjnych kontekstu rozliczeń jest rejestracji umowy EA.
    - Dla klientów rozliczana według bieżącego użycia udostępnionych zakres jest wszystkie subskrypcje płatność za rzeczywiste użycie utworzonego przez administratora konta.
5. Wybierz region, aby wybrać region platformy Azure, który pasuje do żadnego rezerwowanie pojemności.
6. Wybierz ilość. Wprowadź liczbę 100 jednostek magazynu danych (cDWU), które chcesz kupić.    
  Na przykład ilość 30 pozwoli uzyskać 3000 cDWU zarezerwowanych pojemności co godzinę.
7. Przegląd SQL Data Warehouse zastrzeżone kosztów rezerwacji pojemności w **koszty** sekcji.
8. Wybierz pozycję **Kup**.
9. Wybierz **Wyświetl tę rezerwację** wyświetlić Twój status zakupu.

## <a name="cancellations-and-exchanges"></a>Anulowanie i wymiany

Jeśli potrzebujesz wydajności rezerwowej anulować usługi SQL Data Warehouse, może być 12% zakończenia opłatę za wcześniejsze zakończenie. Zwroty zależą od niższej kwoty z dwóch: cena zakupu i bieżąca cena rezerwacji. Zwroty są ograniczone do 50,000.00 $ rocznie. Zwrot kosztów, które otrzymujesz jest pozostałe saldo proporcjonalnie pomniejszona o 12% zakończenia opłatę za wcześniejsze zakończenie. Aby zażądać anulowania, przejdź do rezerwacji w witrynie Azure portal i wybierz pozycję **zwrot** do utworzenia żądania obsługi.

Jeśli musisz zmienić pojemności usługi SQL Data Warehouse zarezerwowane do innego regionu lub termin mogą wymieniać go do innej rezerwacji, która jest większa lub równa wartości. Data rozpoczęcia okresu nowej rezerwacji nie jest przenoszona z wymienionej rezerwacji. Jeden lub trzy lata termin rozpoczyna się, gdy utworzysz nową rezerwację. Aby zażądać wymiany, otwórz rezerwacji w witrynie Azure portal i wybierz **Exchange** do utworzenia żądania obsługi.

Aby uzyskać więcej informacji na temat rezerwacji exchange lub refundacji zobacz [wymiany rezerwacji i zwroty](billing-azure-reservations-self-service-exchange-and-refund.md).

Rabat związany z rezerwacją jest automatycznie stosowany do liczby wystąpień SQL Data Warehouse, zgodnych zakres pojemności SQL Data Warehouse, zastrzeżone i regionu. Można zaktualizować zakresu pojemnością SQL Data Warehouse zarezerwowane, przy użyciu [witryny Azure portal](https://portal.azure.com/), PowerShell, interfejsu wiersza polecenia lub przy użyciu interfejsu API.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://portal.azure.com/).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat sposobu rezerwacji rabaty dotyczą usługi Azure SQL Data Warehouse, zobacz [jak rezerwacji rabaty dotyczą usługi Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Aby dowiedzieć się więcej na temat rezerwacji Azure, zobacz następujące artykuły:
  - [Co to jest Azure rezerwacje?](billing-save-compute-costs-reservations.md)
  - [Zarządzanie usługą Azure Reservations](billing-manage-reserved-vm-instance.md)
  - [Świadomość, że rezerwacje platformy Azure z rabatami](billing-understand-reservation-charges.md)
  - [Opis zastrzeżenia dla Twojej subskrypcji zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md)
  - [Opis zastrzeżenia dla Twojej rejestracji Enterprise](billing-understand-reserved-instance-usage-ea.md)
