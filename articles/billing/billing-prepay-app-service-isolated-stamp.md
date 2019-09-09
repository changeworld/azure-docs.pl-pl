---
title: Oszczędność kosztów Azure App Servicej opłaty za wyizolowaną sygnaturę z zarezerwowaną pojemnością
description: Dowiedz się, w jaki sposób można zaoszczędzić koszty Azure App Servicej opłaty za wydzieloną sygnaturę z zarezerwowaną pojemnością.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 5161f18499c082b7064eec2be612557ba09eec97
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806358"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Oszczędność kosztów Azure App Servicej opłaty za wyizolowaną sygnaturę z zarezerwowaną pojemnością

Możesz zaoszczędzić pieniądze na Azure App Service odizolowanych opłat za sygnaturę, przekazując do rezerwacji za użycie sygnatury przez trzy lata. Aby kupić zastrzeżoną pojemność opłaty za pieczęć, należy wybrać region platformy Azure, w którym zostanie wdrożona sygnatura, oraz liczbę sygnatur do zakupu.

W przypadku zakupienia rezerwacji użycie opłaty za sygnaturę odizolowaną, która jest zgodna z atrybutami rezerwacji, nie jest już naliczana według stawek płatność zgodnie z rzeczywistym użyciem. Rezerwacja jest stosowana automatycznie do liczby wyizolowanych sygnatur, które pasują do zakresu i regionu zarezerwowanej pojemności. Nie musisz przypisywać rezerwacji do wydzielonej sygnatury. Rezerwacja nie dotyczy procesów roboczych, więc opłaty za inne zasoby skojarzone z sygnaturą są naliczane oddzielnie.

Po wygaśnięciu zarezerwowanej pojemności nadal działają odizolowane sygnatury, ale opłaty są naliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są automatycznie odnawiane.

## <a name="determine-the-right-reservation-to-purchase"></a>Określanie odpowiedniej rezerwacji do zakupu

Zakup rezerwacji polega na tym, że w ciągu następnych trzech lat zobowiązuje się do korzystania z zarezerwowanych ilości. Sprawdź dane dotyczące użycia, aby określić, ile izolowana usługa App Service sygnatur, z których korzystasz, i które mogą być używane w przyszłości.

Ponadto upewnij się, że wiesz, jak wydzielona sygnatura emituje system Linux lub Windows.

- Domyślnie pusta sygnatura odizolowana emituje licznik sygnatur systemu Windows. Na przykład bez wdrożonych procesów roboczych. Ten licznik jest nadal emitowany, jeśli procesy robocze systemu Windows są wdrożone w sygnaturze.
- Licznik jest zmieniany na miernik sygnatury systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux.
- W przypadku wdrożenia zarówno procesów roboczych z systemem Linux, jak i Windows, sygnatura emituje licznik systemu Windows.

W związku z tym licznik sygnatur może ulec zmianie między oknami i Linux w czasie trwania sygnatury.

Kup rezerwacje sygnatur systemu Windows, jeśli masz co najmniej jednego pracownika systemu Windows w sygnaturze. Jedyną chwilą zakupu rezerwacji sygnatury systemu Linux jest zaplanowanie _tylko_ procesów roboczych systemu Linux na sygnaturze.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Kupowanie zarezerwowanej sygnatury pojemności

Możesz kupić wyizolowaną pojemność zastrzeżoną dla [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Płatność za rezerwację z [góry lub miesięczna płatność](billing-monthly-payments-reservations.md). Aby kupić zastrzeżoną pojemność, musisz mieć rolę właściciela dla co najmniej jednej subskrypcji przedsiębiorstwa lub pojedynczej subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem.

- W przypadku subskrypcji przedsiębiorstwa w [portalu EA](https://ea.azure.com/)należy włączyć opcję **Dodaj wystąpienia zarezerwowane** . Jeśli to ustawienie jest wyłączone, musisz być administratorem EA.
- W przypadku programu Cloud Solution Provider (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą zakupić SQL Data Warehouse zarezerwowaną pojemność.

**Aby kupić:**

1. Przejdź do [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Wybierz subskrypcję. Użyj listy **subskrypcja** , aby wybrać subskrypcję, która jest używana do regulowania zarezerwowanej pojemności. Metoda płatności za subskrypcję jest naliczana za koszt zarezerwowanej pojemności. Typ subskrypcji musi być umową Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P) lub płatność zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P) lub z subskrypcją dostawcy usług kryptograficznych.
    - W przypadku subskrypcji Enterprise opłaty są odliczane od salda zobowiązania pieniężnego rejestracji lub naliczane jako nadwyżka.
    - W przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem opłaty obciążają kartę kredytową lub metodę płatności faktury powiązaną z subskrypcją.
1. Wybierz **zakres** , aby wybrać zakres subskrypcji.
    - **Zakres pojedynczej grupy zasobów** — stosuje rabat rezerwacji do zasobów pasujących tylko w wybranej grupie zasobów.
    - **Zakres pojedynczej subskrypcji** — stosuje rabat rezerwacji do pasujących zasobów w wybranej subskrypcji.
    - **Zakres udostępniony** — stosuje rabat rezerwacji do pasujących zasobów w uprawnionych subskrypcjach, które znajdują się w kontekście rozliczeń. W przypadku klientów Umowa Enterprise, kontekst rozliczania to rejestracja. W przypadku indywidualnych subskrypcji z płatnością zgodnie z rzeczywistym użyciem zakres rozliczeń to wszystkie kwalifikujące się subskrypcje utworzone przez administratora konta.
1. Wybierz **region** , aby wybrać region platformy Azure objęty zarezerwowaną pojemnością i dodać rezerwację do koszyka.
1. Wybierz typ planu izolowanego, a następnie kliknij przycisk **Wybierz**.  
    ![Przyklad](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Wprowadź liczbę sygnatur izolowana usługa App Service, które mają zostać zarezerwowane. Na przykład, liczba trzech zarezerwowanych oznacza region. Kliknij pozycję **Next: Przejrzyj i Kup**.
1. Przejrzyj i kliknij pozycję **Kup teraz**.

Po zakupie przejdź do pozycji [rezerwacje](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) , aby wyświetlić stan zakupu i monitorować go w dowolnym momencie.

## <a name="cancel-exchange-or-refund-reservations"></a>Anulowanie, wymiana lub zastrzeżenia zwrotne

Można anulować, wymienić lub refundować rezerwacje z pewnymi ograniczeniami. Aby uzyskać więcej informacji, zobacz samoobsługowe [weksle i zwroty dla Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplikacja rabatowa wyświetlana w danych użycia

Dane użycia mają rzeczywistą cenę równą zero za użycie, które pobiera rabat rezerwacji. Dane użycia przedstawiają rabat rezerwacji dla każdego wystąpienia sygnatury w każdej rezerwacji.

Aby uzyskać więcej informacji na temat sposobu, w jaki rabat związany z rezerwacją jest wyświetlany w danych użycia, zobacz [pobierz Umowa Enterprise koszty rezerwacji i użycie](billing-understand-reserved-instance-usage-ea.md) , jeśli jesteś klientem Umowa Enterprise (EA). W przeciwnym razie zobacz temat [użycie usługi Azure Reservation dla indywidualnej subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem](billing-understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:
  - [Co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Dowiedz się, w jaki sposób jest stosowany rabat związany z zarezerwowaną sygnaturą Azure App Service](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](billing-understand-reserved-instance-usage-ea.md)
