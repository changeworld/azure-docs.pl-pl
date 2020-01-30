---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847136"
---
Oszczędzaj na dyskach półprzewodnikowych w warstwie Premium (SSD) z zarezerwowaną pojemnością, w połączeniu z zarezerwowanymi wystąpieniami maszyn wirtualnych, możesz zmniejszyć łączne koszty maszyny wirtualnej. Rabat rezerwacji jest automatycznie stosowany do zgodnych dysków w wybranym zakresie rezerwacji, więc nie trzeba przypisywać rezerwacji do dysku zarządzanego w celu uzyskania rabatów. Rabaty są stosowane co godzinę przy użyciu dysku, a wszystkie niewykorzystane pojemności nie są przenoszone. Rabat związany z rezerwacją dysku zarządzanego nie dotyczy dysków niezarządzanych, Ultra Disk ani zużycia obiektów BLOB stron.

## <a name="determine-your-storage-needs"></a>Określanie potrzeb związanych z magazynem

Przed zakupem rezerwacji należy określić, jakie są potrzeby związane z magazynem. Obecnie rezerwacja dysku jest dostępna tylko w przypadku jednostek SKU z opcjami SELECT SSD w warstwie Premium. Jednostka SKU warstwy SSD w warstwie Premium określa rozmiar i wydajność dysku. W przypadku określania potrzeb związanych z magazynem nie zaleca się używania dysków jako całkowitej pojemności. nie można użyć rezerwacji dla większego dysku (takiego jak P40) i użyć go do zapłacenia za dwa mniejsze dyski (P30). Zakup rezerwacji polega wyłącznie na zakupie łącznej liczby dysków w jednostce SKU.

Rezerwacja dysku jest wykonywana dla jednostki SKU dysku, dlatego użycie rezerwacji jest oparte na jednostce jednostek SKU dysku zamiast podanego rozmiaru. Jeśli na przykład zarezerwowano 1 P40 z 2 TiB zdolności produkcyjnych, ale tylko przydzielono 2 dyski P30, użycie tych samych P30 nie zostanie uwzględnione dla rezerwacji P40 i zostanie zapłacona stawka płatność zgodnie z rzeczywistym użyciem.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Uwagi dotyczące zakupu

Podczas rozważania zakupu rezerwacji dysku zalecamy stosowanie następujących najlepszych rozwiązań:

- Analizuj informacje o użyciu, aby pomóc w ustaleniu, które rezerwacje należy zakupić. Upewnij się, że śledzenie użycia jest śledzone w jednostkach SKU dysku zamiast zainicjowanej lub zajętej pojemności dysku. 
- Sprawdź rezerwację dysku wraz z rezerwacją maszyny wirtualnej. Zdecydowanie zalecamy utworzenie rezerwacji dla użycia maszyn wirtualnych i dysków w celu maksymalnego zapisywania. Możesz rozpocząć od określenia odpowiedniej rezerwacji maszyny wirtualnej, a następnie odpowiednio oszacować rezerwację dysku. Ogólnie rzecz biorąc, będziesz mieć standardową konfigurację dla każdego z obciążeń, na przykład program SQL Server może mieć dwa dyski danych P40 i jeden dysk systemu operacyjnego P30. Ten rodzaj wzorca może pomóc w ustaleniu ilości rezerwacji, które mogą zostać naliczone. Takie podejście może uprościć proces oceny i upewnić się, że masz wyrównany plan dla maszyn wirtualnych i dysków w zakresie subskrypcji, regionów i innych. 

## <a name="purchase-restrictions"></a>Ograniczenia zakupu

Rabaty zarezerwowane nie są obecnie dostępne dla następujących dysków:
- Dyski niezarządzane/stronicowe obiekty blob
- SSD w warstwie Standardowa lub standardowy dysk twardy
- SSD w warstwie Premium jednostki SKU mniejsze niż P30 — rezerwacje są niedostępne w przypadku jednostek SKU/P2/P3/P4/P6/P10/P15/P20 SSD w warstwie Premium.
- Chmury — rezerwacje nie są dostępne do zakupu w regionach Azure gov, Niemcy i Chiny.
- Ograniczenia pojemności — w rzadkich przypadkach platforma Azure ogranicza zakup nowych rezerwacji dla podzbioru jednostek SKU dysku z powodu niskiej wydajności w regionie.

## <a name="buy-a-disk-reservation"></a>Kupowanie rezerwacji dysku

Rezerwacje dysku platformy Azure można zakupić za pomocą [Azure Portal](https://portal.azure.com/). Możesz uiścić płatność za rezerwację z góry lub miesięczną płatnością. Aby uzyskać więcej informacji na temat kupowania miesięcznych płatności, zobacz [kupowanie rezerwacji z płatnościami miesięcznymi](../articles/cost-management-billing/reservations/monthly-payments-reservations.md).

Wykonaj następujące kroki, aby zakupić zastrzeżoną pojemność:

1. Przejdź do bloku [rezerwacje zakupów](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w Azure Portal.
1. Wybierz pozycję **Azure Managed disks** , aby kupić rezerwację.

    ![disks-Reserved-Purchase-Reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Wypełnij pola wymagane zgodnie z opisem w poniższej tabeli:

   |Pole  |Opis  |
   |---------|---------|
   |**Zakres**   |  Wskazuje, ile subskrypcji może korzystać z zalet rozliczeń skojarzonych z rezerwacją. Kontroluje również sposób zastosowania rezerwacji do określonych subskrypcji. <br/><br/> W przypadku wybrania opcji **udostępnione**rabat zostanie zastosowany do pojemności usługi Azure Storage w dowolnej subskrypcji w kontekście rozliczania. Kontekst rozliczania jest oparty na sposobie rejestracji w usłudze Azure. W przypadku klientów korporacyjnych zakresem udostępnionym jest rejestracja i uwzględnianie wszystkich subskrypcji w ramach rejestracji. W przypadku klientów z systemem płatność zgodnie z rzeczywistym użyciem zakres udostępniony obejmuje wszystkie poszczególne subskrypcje z opłatami z rabatem zgodnie z rzeczywistym użyciem utworzonymi przez administratora konta.  <br/><br/>  W przypadku wybrania opcji **Pojedyncza subskrypcja**rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji. <br/><br/> W przypadku wybrania opcji **pojedynczy zasób**rabat zostanie zastosowany do pojemności usługi Azure Storage w ramach wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Można zmienić zakres rezerwacji po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja, która jest używana do płacenia za rezerwację usługi Azure Storage. Metoda płatności w ramach wybranej subskrypcji jest używana w celu naliczania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Umowa Enterprise (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): w przypadku subskrypcji przedsiębiorstwa opłaty są naliczane od salda zobowiązania pieniężnego rejestracji lub opłaty są naliczane jako nadwyżkowe. <br/><br/> Indywidualna subskrypcja z stawką płatności zgodnie z rzeczywistym użyciem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): w przypadku pojedynczej subskrypcji z stawką płatności zgodnie z rzeczywistym użyciem opłaty są naliczane za kartę kredytową lub formę płatności faktury dla subskrypcji.    |
   | **Dyski** | Jednostka SKU, którą chcesz utworzyć. |
   | **Region** | Region, w którym rezerwacja jest obowiązująca. |
   | **Częstotliwość rozliczeń** | Wskazuje, jak często konto jest rozliczane dla rezerwacji. Opcje obejmują *co miesiąc* lub z *góry*. |

    ![Premium-SSD-Reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Po wybraniu parametrów dla rezerwacji Azure Portal zostanie wyświetlony koszt. W portalu jest również pokazywana wartość procentowa rabatu w porównaniu z płatnościami zgodnie z rzeczywistym użyciem. Wybierz pozycję **dalej** , aby przejdź do bloku **rezerwacje zakupów** .

1. W okienku **rezerwacje zakupu** możesz nadać nazwę rezerwacji i wybrać łączną liczbę rezerwacji, które chcesz wprowadzić. Liczba rezerwacji jest mapowana na liczbę dysków. Jeśli na przykład chcesz zarezerwować setki dysków, Zmień **liczbę** na 100.
1. Sprawdź łączny koszt rezerwacji.

    ![Premium-SSD-Reserved-Selecting-SKU-Total-Purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Po zakupieniu rezerwacji zostanie ona automatycznie zastosowana do wszystkich istniejących zasobów usługi Azure Disk Storage, które pasują do warunków rezerwacji. Jeśli nie utworzono jeszcze żadnych zasobów usługi Azure Disk Storage, rezerwacja zostanie zastosowana po każdym utworzeniu zasobu odpowiadającego postanowieniom rezerwacji. W obu przypadkach okres rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="exchange-or-refund-a-reservation"></a>Program Exchange lub zwrot rezerwacji

Z pewnymi ograniczeniami można wymienić lub refundować rezerwację.

Aby wymienić lub zwrócić zastrzeżenie, przejdź do szczegółów rezerwacji w Azure Portal. Wybierz pozycję **Exchange lub refunda**i postępuj zgodnie z instrukcjami, aby przesłać żądanie pomocy technicznej. Gdy żądanie zostało przetworzone, firma Microsoft wyśle do Ciebie wiadomość e-mail w celu potwierdzenia zakończenia żądania.

Aby uzyskać więcej informacji na temat zasad Azure Reservations, zobacz samoobsługowe [wymianę i zwroty za Azure Reservations](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Wymiana rezerwacji

Wymiana rezerwacji pozwala uzyskać proporcjonalną refundację w oparciu o nieużywaną część rezerwacji. Następnie można zastosować zwrot do ceny zakupu nowej rezerwacji dysku platformy Azure.
Liczba wymian, których możesz dokonać, nie jest niczym ograniczona. Ponadto z wymianą nie jest związana żadna opłata. Nowe rezerwacji, które należy zakupić, muszą mieć wartość równą lub większą niż proporcjonalny kredyt z oryginalnej rezerwacji. Rezerwacja dysku platformy Azure może być wymieniana tylko dla innej rezerwacji dysku platformy Azure, a nie dla rezerwacji dla żadnej innej usługi platformy Azure.

### <a name="refund-a-reservation"></a>Zwrot rezerwacji

W dowolnym momencie możesz anulować rezerwację dysku platformy Azure. Jeśli anulujesz, otrzymasz proporcjonalną refundację na podstawie pozostałego okresu rezerwacji, pomniejszoną o 12% opłatę za wczesne zakończenie. Maksymalna zwrot za rok to $50 000.
Anulowanie rezerwacji powoduje natychmiastowe zakończenie rezerwacji i zwrócenie pozostałych miesięcy do firmy Microsoft. Pozostałe saldo (proporcjonalnie do wartości) pomniejszone o opłatę zostanie zwrócone do oryginalnej formy zakupu.

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji

Po wygaśnięciu rezerwacji każda pojemność dysku platformy Azure, która jest używana w ramach tej rezerwacji, jest rozliczana według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.
Otrzymasz powiadomienie e-mail za 30 dni przed wygaśnięciem rezerwacji, a następnie datę wygaśnięcia. Aby nadal korzystać z zalet oszczędności kosztów rezerwacji, Odnów ją nie później niż data wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Co to są rezerwacje platformy Azure?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Zapoznaj się ze sposobem stosowania rabatu rezerwacji do Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)