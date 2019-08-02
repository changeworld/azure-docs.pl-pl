---
title: Automatyczne odnawianie rezerwacji platformy Azure
description: Dowiedz się, jak można automatycznie odnawiać rezerwacje platformy Azure, aby kontynuować pobieranie rabatów rezerwacji.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679460"
---
# <a name="automatically-renew-reservations"></a>Automatyczne odnawianie rezerwacji

Możesz odnowić rezerwacje, aby automatycznie zakupić zastąpienie w przypadku wygaśnięcia istniejącej rezerwacji. Automatyczne odnawianie umożliwia łatwe uzyskiwanie rabatów rezerwacji. Pozwala również na dokładne monitorowanie wygaśnięcia rezerwacji. Dzięki automatycznemu odnawianiu nie ma konieczności ręcznego odnawiania oszczędności. Ustawienie odnowienia jest domyślnie wyłączone. Włącz lub wyłącz ustawienie odnawiania w dowolnym momencie, aż do wygaśnięcia istniejącej rezerwacji.

Odnowienie rezerwacji powoduje utworzenie nowej rezerwacji po wygaśnięciu istniejącej rezerwacji. Nie powoduje to poszerzenia okresu istniejącej rezerwacji.

Zezwól na automatyczne odnawianie w dowolnym momencie. Cena odnowienia jest dostępna 30 dni przed wygaśnięciem istniejącej rezerwacji. Po włączeniu odnowienia więcej niż 30 dni przed wygaśnięciem rezerwacji zostanie wysłana wiadomość e-mail z informacjami o kosztach odnowienia przez 30 dni przed wygaśnięciem. Cena rezerwacji może ulec zmianie między czasem zablokowania ceny odnowienia i czasem odnowienia. W takim przypadku koszt odnowienia będzie niższy z dwóch kosztów. Można wprowadzać zmiany w ilości rezerwacji. W takim przypadku odnowienie zostanie zaktualizowane do korzystania z zestawu cen na rynku w momencie zmiany ilości.

Nie ma obowiązku odnowienia i można zrezygnować z odnowienia w dowolnym momencie przed wygaśnięciem istniejącej rezerwacji.

## <a name="set-up-renewal"></a>Konfigurowanie odnawiania

Przejdź do Azure Portal > **rezerwacje**.

1. Wybierz rezerwację.
2. Kliknijpozycję odnowienie.
3. Wybierz pozycję **automatycznie Kup nową rezerwację po wygaśnięciu**.  
  ![Przykład przedstawiający odnowienie rezerwacji](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Jeśli nie odnowisz

Usługi nadal działają normalnie. Po wygaśnięciu rezerwacji opłaty są naliczone według stawek płatnych zgodnie z rzeczywistym użyciem.

## <a name="required-renewal-permissions"></a>Wymagane uprawnienia do odnawiania

Do odnowienia rezerwacji wymagane są następujące warunki:

- Musisz być właścicielem istniejącej rezerwacji.
- Jeśli rezerwacja obejmuje zakres pojedynczej subskrypcji lub grupy zasobów, musisz być właścicielem subskrypcji.
- Musisz być właścicielem subskrypcji, jeśli ma ona zakres udostępniony.

## <a name="default-renewal-settings"></a>Domyślne ustawienia odnawiania

Domyślnie odnowienie dziedziczy wszystkie właściwości z zastrzeżenia, które wygasa. Zakup odnowienia rezerwacji ma taką samą jednostkę SKU, region, zakres, subskrypcję rozliczeń, termin i ilość.

Można jednak zaktualizować ilość zakupu rezerwacji odnowienia, aby zoptymalizować oszczędności.

## <a name="when-the-new-reservation-is-purchased"></a>Po zakupie nowej rezerwacji

Nowe rezerwacje są kupowane, gdy istniejąca rezerwacja wygaśnie. Staramy się zapobiec wszelkim opóźnieniem między obiema rezerwacjami. Ciągłość zapewnia przewidywalność kosztów i nadal uzyskuje rabaty.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Zmiana rezerwacji nadrzędnej po ustawieniu odnowienia

Jeśli wprowadzisz dowolną z następujących zmian w rezerwacji wygasania, odnowienie rezerwacji zostanie anulowane:

- Podziel
- Scal
- Przenoszenie rezerwacji z jednego konta do innego
- Przenoszenie rezerwacji z subskrypcji usługi WebDirect do subskrypcji umowy Enterprise Agreement (EA) lub dowolnej innej metody zakupu
- Odnów rejestrację

Nowa rezerwacja dziedziczy ustawienie elastyczność rozmiaru zakresu i wystąpienia z rezerwy, która wygasa podczas odnawiania.

## <a name="new-reservation-permissions"></a>Nowe uprawnienia do rezerwacji

Na platformie Azure są kopiowane uprawnienia z ważnością zastrzeżeń do nowej rezerwacji. Ponadto administrator konta subskrypcji w ramach zakupu rezerwacji ma dostęp do nowej rezerwacji.

## <a name="potential-renewal-problems"></a>Potencjalne problemy z odnowieniem

Platforma Azure może nie przetwarzać odnowienia, jeśli:

- Nie można zebrać płatności
- Wystąpił błąd systemu podczas odnawiania
- Wygasające jednostki SKU nie są aktywne podczas odnawiania
- Umowa EA jest odnawiana na inną EA

Otrzymasz powiadomienie e-mail, jeśli wystąpi którykolwiek z powyższych warunków, a odnowienie zostanie zdezaktywowane.

## <a name="renewal-notification"></a>Powiadomienie o odnowieniu

Wiadomości e-mail są wysyłane do różnych osób w zależności od metody zakupu:

- Klienci z umowami EA — wiadomości E-mail są wysyłane do kontaktów powiadomień ustawionych w portalu EA.
- Klienci indywidualnych subskrypcji z stawką płatność zgodnie z rzeczywistym użyciem — wiadomości E-mail są wysyłane do użytkowników, którzy są konfigurowani jako administratorzy konta.
- Klienci dostawcy rozwiązań w chmurze — wiadomości E-mail są wysyłane do kontaktu z powiadomieniem partnera.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz [co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
