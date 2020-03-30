---
title: Optymalizuj koszty magazynu obiektów Blob z zarezerwowaną pojemnością — Usługa Azure Storage
description: Dowiedz się więcej o zakupie pojemności zarezerwowanej usługi Azure Storage w celu zaoszczędzenia kosztów w zasobach bloku blob i usługi Azure Data Lake Storage Gen2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e73686629de8481f6a37e5bfafc9b723206b4853
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351026"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej

Możesz zaoszczędzić pieniądze na kosztach magazynowania danych obiektów blob za pomocą zarezerwowanej pojemności usługi Azure Storage. Pojemność zarezerwowana usługi Azure Storage oferuje zniżkę na pojemność bloków obiektów blob i danych usługi Azure Data Lake Storage Gen2 na standardowych kontach magazynu, gdy zobowiążesz się do rezerwacji na jeden rok lub trzy lata. Rezerwacja zapewnia stałą ilość pojemności magazynowej na okres rezerwacji.

Pojemność zarezerwowana usługi Azure Storage może znacznie zmniejszyć koszty pojemności bloków obiektów blob i danych usługi Azure Data Lake Storage Gen2. Uzyskane oszczędności zależą od czasu trwania rezerwacji, całkowitej pojemności, którą zdecydujesz się zarezerwować, a także warstwy dostępu i rodzaju nadmiarowości wybranej dla konta magazynu. Zarezerwowana pojemność zapewnia rabat rozliczeniowy i nie wpływa na stan zasobów usługi Azure Storage.

Aby uzyskać informacje na temat cen rezerwacji usługi Azure Storage, zobacz [Ceny blokowych obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/) i [Cennik usługi Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Warunki rezerwacji usługi Azure Storage

W poniższych sekcjach opisano warunki rezerwacji usługi Azure Storage.

### <a name="reservation-capacity"></a>Pojemność rezerwacji

Zarezerwowaną pojemność usługi Azure Storage można kupić w jednostkach o pojemności 100 TB i 1 PB miesięcznie na okres jednego roku lub trzech lat.

### <a name="reservation-scope"></a>Zakres rezerwacji

Pojemność zarezerwowana usługi Azure Storage jest dostępna dla pojedynczej subskrypcji lub dla wielu subskrypcji (zakres udostępniony). W przypadku zakresu do pojedynczej subskrypcji rabat rezerwacji jest stosowany tylko do wybranej subskrypcji. Po objęciu wielu subskrypcji rabat rezerwacji jest dzielony między te subskrypcje w kontekście rozliczeń odbiorcy.

Podczas zakupu pojemności zarezerwowanej usługi Azure Storage można użyć rezerwacji zarówno dla bloku obiektów blob, jak i danych usługi Azure Data Lake Storage Gen2. Rezerwacja jest stosowana do użycia w ramach zakupionego zakresu i nie może być ograniczona do określonego konta magazynu, kontenera lub obiektu w ramach subskrypcji. Rezerwacji nie można podzielić na wiele subskrypcji.

Rezerwacja usługi Azure Storage obejmuje tylko ilość danych przechowywanych w subskrypcji lub udostępnionej grupie zasobów. Wczesne usunięcie, operacje, przepustowość i opłaty za transfer danych nie są uwzględniane w rezerwacji. Zaraz po zakupie rezerwacji opłaty za zdolności produkcyjne, które pasują do atrybutów rezerwacji, są naliczane według stawek rabatowych, a nie według stawek płatności zgodnie z rzeczywistym i wyjazdem. Aby uzyskać więcej informacji na temat rezerwacji platformy Azure, zobacz [Co to są rezerwacje platformy Azure?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Obsługiwane typy kont, warstwy i opcje nadmiarowości

Pojemność zarezerwowana usługi Azure Storage jest dostępna dla zasobów na standardowych kontach magazynu, w tym konta magazynu ogólnego przeznaczenia w wersji 2 (GPv2) i magazynu obiektów Blob.

Wszystkie warstwy dostępu (gorące, chłodne i archiwalne) są obsługiwane dla rezerwacji. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Azure Blob storage: hot, cool i archiwum warstwy dostępu.](storage-blob-storage-tiers.md)

Wszystkie rodzaje nadmiarowości są obsługiwane w przypadku rezerwacji. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [Nadmiarowość usługi Azure Storage](../common/storage-redundancy.md).

> [!NOTE]
> Pojemność zarezerwowana usługi Azure Storage nie jest dostępna dla kont magazynu w warstwie Premium, kont magazynu ogólnego przeznaczenia w wersji 1 (GPv1), usługi Azure Data Lake Storage Gen1, obiektów blob stron, usługi Azure Queue storage, usługi Azure Table storage lub Usługi Azure Files.  

### <a name="security-requirements-for-purchase"></a>Wymagania bezpieczeństwa zakupu

Aby kupić pojemność rezerwową:

- Musisz być w roli **właściciela** dla co najmniej jednej subskrypcji Enterprise lub indywidualnej ze stawkami płatności zgodnie z rzeczywistymu polub.
- W przypadku subskrypcji enterprise należy włączyć funkcję **Dodaj wystąpienia zarezerwowane** w portalu EA. Lub, jeśli to ustawienie jest wyłączone, musisz być administratorem EA w subskrypcji.
- W przypadku programu Dostawcy rozwiązań w chmurze (CSP) tylko agenci administracyjni lub agenci sprzedaży mogą kupować pojemność zarezerwowaną usługi Azure Blob Storage.

## <a name="determine-required-capacity-before-purchase"></a>Określ wymaganą pojemność przed zakupem

Podczas zakupu rezerwacji usługi Azure Storage należy wybrać region, warstwę dostępu i opcję nadmiarowości dla rezerwacji. Rezerwacja jest ważna tylko dla danych przechowywanych w tym regionie, warstwy dostępu i poziomu nadmiarowości. Załóżmy na przykład, że zakupisz rezerwację danych w zachodniej części STANÓW Zjednoczonych dla warstwy gorąca przy użyciu magazynu nadmiarowego strefy (ZRS). Nie można użyć tej samej rezerwacji dla danych w stanach Wschodnich stanów USA, danych w warstwie archiwum lub danych w magazynie geograficznie nadmiarowym (GRS). Możesz jednak zakupić kolejną rezerwację na dodatkowe potrzeby.  

Rezerwacje są dostępne już dziś dla bloków 100 TB lub 1 PB, z wyższymi rabatami dla 1 bloków PB. W przypadku zakupu rezerwacji w witrynie Azure portal firma Microsoft może przedstawić ci zalecenia oparte na poprzednim użyciu, aby określić, którą rezerwację należy kupić.

## <a name="purchase-azure-storage-reserved-capacity"></a>Zakup pojemności zarezerwowanej usługi Azure Storage

Zarezerwowaną pojemność usługi Azure Storage można kupić za pośrednictwem [witryny Azure portal](https://portal.azure.com). Płatność za rezerwację jest wnoszona z góry lub w ratach miesięcznych. Aby uzyskać więcej informacji na temat zakupów z [płatnościami miesięcznymi, zobacz Kupowanie rezerwacji platformy Azure z płatnościami z góry lub miesięcznymi płatnościami.](/azure/billing/billing-monthly-payments-reservations)

Aby uzyskać pomoc dotyczącą identyfikowania warunków rezerwacji, które są odpowiednie dla twojego scenariusza, zobacz [Opis rabatu pojemności zarezerwowanej usługi Azure Storage.](../../cost-management-billing/reservations/understand-storage-charges.md)

Wykonaj następujące kroki, aby kupić pojemność rezerwową:

1. Przejdź do okienka [Rezerwacje zakupu](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) w witrynie Azure portal.  
1. Wybierz **usługę Azure Blob Storage,** aby kupić nową rezerwację.  
1. Wypełnij wymagane pola zgodnie z opisem w poniższej tabeli:

    ![Zrzut ekranu przedstawiający sposób zakupu zarezerwowanej pojemności](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Pole  |Opis  |
   |---------|---------|
   |**Zakres**   |  Wskazuje, ile subskrypcji może korzystać z korzyści rozliczeniowych skojarzonych z rezerwacją. Kontroluje również sposób stosowania rezerwacji do określonych subskrypcji. <br/><br/> Jeśli wybierzesz **udostępnione,** rabat rezerwacji zostanie zastosowany do pojemności usługi Azure Storage w dowolnej subskrypcji w kontekście rozliczeń. Kontekst rozliczeń jest oparty na tym, jak zarejestrowałeś się na platformie Azure. Dla klientów korporacyjnych zakresem udostępnionym jest rejestracja i obejmuje wszystkie subskrypcje w ramach rejestracji. W przypadku klientów korzystających z płatności zgodnie z rzeczywistymi oczekiwaniami zakres współużytkowane obejmuje wszystkie indywidualne subskrypcje ze stawkami płatności zgodnie z rzeczywistymu po drodze utworzonymi przez administratora konta.  <br/><br/>  Jeśli wybierzesz **pojedynczą subskrypcję,** rabat rezerwacji zostanie zastosowany do pojemności usługi Azure Storage w wybranej subskrypcji. <br/><br/> Jeśli wybierzesz **pojedynczą grupę zasobów,** rabat rezerwacji zostanie zastosowany do pojemności usługi Azure Storage w wybranej subskrypcji i wybranej grupy zasobów w ramach tej subskrypcji. <br/><br/> Zakres rezerwacji można zmienić po zakupie rezerwacji.  |
   |**Subskrypcja**  | Subskrypcja używana do płacenia za rezerwację usługi Azure Storage. Metoda płatności w wybranej subskrypcji jest używana do obciążania kosztów. Subskrypcja musi być jednym z następujących typów: <br/><br/>  Enterprise Agreement (numery ofert: MS-AZR-0017P lub MS-AZR-0148P): W przypadku subskrypcji Enterprise opłaty są odejmowane od salda zobowiązań pieniężnych rejestracji lub naliczane jako nadmierne. <br/><br/> Subskrypcja indywidualna ze stawkami płatności zgodnie z rzeczywistym obciążeniem (numery ofert: MS-AZR-0003P lub MS-AZR-0023P): W przypadku indywidualnej subskrypcji ze stawkami płatności zgodnie z rzeczywistym ratą opłaty są naliczane na podstawie metody płatności kartą kredytową lub fakturą w ramach subskrypcji.    |
   | **Region** | Region, w którym obowiązuje rezerwacja. |
   | **Warstwa dostępu** | Warstwa dostępu, dla której obowiązuje rezerwacja. Opcje obejmują *Hot*, *Cool*lub *Archiwum*. Aby uzyskać więcej informacji na temat warstw dostępu, zobacz [Azure Blob storage: hot, cool i archive access tiers](storage-blob-storage-tiers.md). |
   | **Nadmiarowość** | Opcja nadmiarowości dla rezerwacji. Dostępne opcje to *LRS*, *ZRS,* *GRS*i *RA-GZRS*. Aby uzyskać więcej informacji na temat opcji nadmiarowości, zobacz [Nadmiarowość usługi Azure Storage](../common/storage-redundancy.md). |
   | **Częstotliwość rozliczeń** | Wskazuje, jak często konto jest rozliczane za rezerwację. Opcje obejmują *miesięczne* lub *z góry*. |
   | **Rozmiar** | Region, w którym obowiązuje rezerwacja. |
   |**Termin**  | Rok lub trzy lata.   |

1. Po wybraniu parametrów rezerwacji portal Azure wyświetla koszt. Portal pokazuje również procent rabatu w stosunku do rozliczeń zgodnie z rzeczywistymu posuwu.

1. W okienku **Rezerwacje zakupu** przejrzyj całkowity koszt rezerwacji. Można również podać nazwę rezerwacji.

    ![Zrzut ekranu przedstawiający sposób zakupu rezerwacji](media/storage-blob-reserved-capacity/purchase-reservations.png)

Po zakupie rezerwacji jest automatycznie stosowany do wszystkich istniejących zasobów bloku usługi Azure Storage lub usługi Azure Data Lake Gen2, które są zgodne z warunkami rezerwacji. Jeśli nie utworzono jeszcze żadnych zasobów usługi Azure Storage, rezerwacja będzie stosowana przy każdym utworzeniu zasobu, który pasuje do warunków rezerwacji. W obu przypadkach termin rezerwacji rozpoczyna się natychmiast po pomyślnym zakupie.

## <a name="exchange-or-refund-a-reservation"></a>Wymiana lub zwrot rezerwacji

Możesz wymienić lub zwrócić pieniądze na rezerwację, z pewnymi ograniczeniami. Ograniczenia te są opisane w poniższych sekcjach.

Aby wymienić lub zwrócić rezerwację, przejdź do szczegółów rezerwacji w witrynie Azure portal. Wybierz **opcję Wymiana** lub Zwrot **pieniędzy**i postępuj zgodnie z instrukcjami, aby przesłać prośbę o pomoc techniczną. Po przetworzeniu żądania firma Microsoft wyśle wiadomość e-mail z potwierdzeniem wypełnienia żądania.

Aby uzyskać więcej informacji na temat zasad rezerwacji platformy Azure, zobacz [Samoobsługowe wymiany i zwroty pieniędzy za rezerwacje platformy Azure.](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

### <a name="exchange-a-reservation"></a>Wymiana rezerwacji

Wymiana rezerwacji umożliwia otrzymanie proporcjonalnego zwrotu pieniędzy w zależności od niewykorzystanej części rezerwacji. Następnie można zastosować zwrot do ceny zakupu nowej rezerwacji usługi Azure Storage.

Nie ma limitu liczby wymian, które możesz wykonać. Ponadto nie ma opłaty związanej z wymianą. Nowa rezerwacja, którą kupujesz, musi mieć taką samą lub większą wartość niż proporcjonalny kredyt z pierwotnej rezerwacji. Rezerwacja usługi Azure Storage może być wymieniana tylko na inną rezerwację usługi Azure Storage, a nie na rezerwację dla innej usługi platformy Azure.

### <a name="refund-a-reservation"></a>Zwrot rezerwacji

W każdej chwili możesz anulować rezerwację usługi Azure Storage. Po anulowaniu rezerwacji otrzymasz proporcjonalny zwrot na podstawie pozostałego terminu rezerwacji, minus 12 procent opłaty za wcześniejsze rozwiązanie. Maksymalny zwrot rocznie wynosi 50 000 USD.

Anulowanie rezerwacji natychmiast kończy rezerwację i zwraca pozostałe miesiące firmie Microsoft. Pozostałe saldo proporcjonalne, pomniejszone o opłatę, zostanie zwrócone do pierwotnej formy zakupu.

## <a name="expiration-of-a-reservation"></a>Wygaśnięcie rezerwacji

Po wygaśnięciu rezerwacji wszelkie pojemności usługi Azure Storage, które są używane w ramach tej rezerwacji jest rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacje nie są odnawiane automatycznie.

Otrzymasz powiadomienie e-mail 30 dni przed wygaśnięciem rezerwacji i ponownie w dniu wygaśnięcia. Aby kontynuować korzystanie z oszczędności kosztów, które zapewnia rezerwacja, odnowi ją nie później niż w dniu wygaśnięcia.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Co to są rezerwacje platformy Azure?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Omówienie stosowania rabatu za rezerwację do usługi Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)
