---
title: Przygotowanie i testowania ofertę do wdrożenia w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowe instrukcje na udostępnienie marketingu zawartości, skonfigurowanie planów cenowych i testowania ofertę przed wdrożeniem w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: mbaldwin
ms.openlocfilehash: 5bec9ac89487a824b9de9209547f0b96fa5fe678
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
ms.locfileid: "29947274"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Dokończ tworzenie oferty z zawartością marketing
W tym kroku procesu publikowania należy podać niektórych zawartości marketing i szczegółowe informacje o ofercie i/lub jednostki SKU w portalu Azure Marketplace. Na przykład zapewni opis produktu, logo firmy, plany cen, szczegółowe informacje o planach i inne informacje niezbędne do dystrybuowania oferty i/lub jednostki SKU do tymczasowego. Te informacje są używane jako marketing zawartość w portalu Azure. Rozpocznie się tym procesem w [portal publikowania][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Krok 1: Podaj marketingu zawartości witryny Marketplace
**Język angielski jest ustawieniem domyślnym i tylko obsługiwanego języka.** Upewnij się, że wszystkie informacje w polach jest w języku angielskim. Wszystkie informacje można edytować w dowolnym momencie przed wypchnięciem do wdrażania przejściowego.

1. Przejdź do portalu publikowania [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. W menu po lewej stronie kliknij **Marketing** kartę.
3. W okienku głównym kliknij **języka angielskiego (US)** przycisku.
   
   > [!IMPORTANT]
   > Wszystkie pola muszą mieć wpisów, w tym obrazów, należy mieć możliwość wypychania do tymczasowego.
   > 
   > 

### <a name="details-and-plans"></a>Szczegółowe informacje i planów
1. Wprowadź tytuł oferty (maksymalnie 50 znaków), oferują Podsumowanie (maksymalnie 100 znaków), oferują długie Podsumowanie (maks. 256 znaków), opis (maksymalnie 1300 znaków), logo, w obszarze oferty **szczegóły** kartę
2. Wprowadź tytuł planu (maksymalnie 50 znaków), podsumowanie planu (maksymalnie 100 znaków), należy zaplanować opis (maksymalnie 2000 znaków) w obszarze **plany** kartę.
   
   > [!NOTE]
   > Można użyć następujących tagów HTML do formatowania podsumowania długie Podsumowanie i opis oferty i planów. Dozwolonych tagów HTML są h1, h2, h3, h4, h5, p, ol, ul, li, [docelowy | href] silnego, em, b i.
   > 
   > 
3. Nie należy wprowadzać zduplikowane tekst w polu Opis oferty i planu.
4. Nie należy wprowadzać zduplikowane tekst w polu Tytuł i oferty, które długie podsumowanie tego planu.
5. Wprowadź tekst zduplikowane tytułem planu i nie oferują podsumowanie.
6. Nie należy wprowadzać tytułów identyczne planu ofertę z wieloma planami.
7. Przekazywanie obrazów z wymaganymi specyfikacjami (wymienione w portalu publikowania) w formacie PNG, po jednej dla każdego rozmiaru.
8. Upewnij się, że logo wykonaj wytycznych dotyczących logo portalu Azure Marketplace, które zostały wymienione poniżej.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Wytycznych dotyczących Logo witrynę Azure Marketplace**

Należy wykonać wszystkie logo, które są przekazywane w portalu publikowania poniższych wytycznych:

* Projekt platformy Azure ma paletę kolorów proste. Zachowaj numer podstawowy i pomocniczy kolory na logo niski.
* Motyw kolorów portalu Azure są białe i czarne. Dlatego należy unikać kolorów te jako kolor tła swoje logo. Użyj niektórych koloru, która swoje logo widoczne w portalu Azure. Zaleca się proste kolorów podstawowych. **Jeśli używasz przezroczyste tło, a następnie upewnij się, że logo/tekstu nie są białe lub czarne lub niebieski.**
* Nie należy używać gradientu tła na logo.
* Należy unikać wprowadzania tekstu, nawet Twoja firma lub marką, na logo. Wygląd i działanie logo powinno być "prosty" i unikać gradienty.
* Logo nie powinny być skalowane.
* Małego logo powinny mieć rozmiar 40 X 40 pikseli
* Średnia logo powinny mieć rozmiar 90 X 90 pikseli
* Duże logo powinno być rozmiaru 115 X 115 pikseli
* Szerokie logo powinny mieć rozmiar 255 X 115 pikseli
* Logo bohater powinny mieć rozmiar 815 X 290 pikseli

> [!NOTE]
> Logo bohater jest opcjonalna. Wydawcy można zrezygnować z przekazać logo bohater. Jednak po przekazane ikona bohater nie można usunąć z publikowania portalu. W tym czasie partnera musi postępuj zgodnie z wytycznymi portalu Azure Marketplace, bohater ikon.
> 
> 

  ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Dodatkowe zalecenia dla ikony logo bohater (opcjonalnie)**

* Logo bohater jest opcjonalna. Wydawcy można zrezygnować z przekazać logo bohater. **Jednak po przekazane ikona bohater nie można usunąć z publikowania portalu. W tym czasie partnera musi postępuj zgodnie z wytycznymi portalu Azure Marketplace ikon bohater else oferta nie zostanie zatwierdzona do środowiska produkcyjnego.**
* Nazwa wyświetlana wydawca, tytuł planu i długie Podsumowanie oferty są wyświetlane w kolorze białym znakiem. Dlatego należy unikać zachowuje żadnych jasny kolor w tle ikona bohater. Czarne, białe i przezroczystego tła ikon bohater jest niedozwolone.
* Nazwa wyświetlana wydawcy, wymienione planu tytuł, długie Podsumowanie oferty i przycisk Utwórz są osadzonego programowo wewnątrz logo bohater po przejdzie do oferty. Nie należy więc wprowadzić dowolny tekst, podczas projektowania bohater logo. Tylko pozostaw puste miejsce po prawej stronie, ponieważ tekst (np. wyświetlania nazwę wydawcy, Nazwa planu długie Podsumowanie oferty) zostanie uwzględniona programowo przez nas nad nim. Puste miejsce na tekst powinien być 415 x 100 po prawej stronie (i przesunięty 370px z lewej strony).
  
  ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Linki
Na **łącza** na pasku po lewej stronie, wprowadź wszystkie łącza o informacje, które mogą pomóc klientom. Wpisz nazwę i adres URL dla każdego linku.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Przykładowe obrazy (opcjonalnie)
> [!NOTE]
> Łącznie z obrazem próbki jest opcjonalny.
> Mimo że możesz przekazać wiele przykładowych obrazów w publikacji portalu, tylko jeden obraz (losowo w systemie) pobiera wyświetlane w portalu Azure. Z tego powodu zalecamy przekazanie co najwyżej jeden obraz przykładowej.
> 
> 

Na **przykładowe obrazy** karcie w menu po lewej stronie, Przekaż nowy obraz, klikając **Przekaż nowy obraz**. Jeśli masz istniejący obraz i chcesz go zastąpić, kliknij przycisk **Zamień obraz**.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Informacje prawne
Na **prawne** karcie, podaj łącze zasad/warunków użytkowania. Wpisz lub Wklej warunki w dużych **warunki użytkowania** pole. Limit liczby znaków prawne warunki użytkowania wynosi 1 000 000 znaków.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Uwaga:** w przypadku ofert maszyny wirtualnej po oferta/jednostka SKU jest umieszczane w portalu Azure będzie można zmienić pola podanych poniżej:

* **Identyfikator oferty:** [publikowania portalu -> maszyn wirtualnych -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [publikowania portalu -> maszyn wirtualnych -> Wybierz ofertę -> jednostki SKU kartę -> Dodaj jednostki SKU]
* **Wydawca Namespace:** [publikowania portalu -> maszyn wirtualnych -> wskazówki Poinformuj nas o firmy (znalezione w obszarze "Krok 2 zarejestrować Twoja firma") -> -> kartę wydawcy Namespace -> Namespace]

W przypadku ofert maszyny wirtualnej gdy oferta/jednostka SKU jest wyświetlany w portalu Azure Marketplace nie można zmienić pola podanych poniżej:

* **Identyfikator oferty:** [publikowania portalu -> maszyn wirtualnych -> Wybierz ofertę -> obrazów maszyn wirtualnych -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [publikowania portalu -> maszyn wirtualnych -> Wybierz ofertę -> jednostki SKU kartę -> Dodaj jednostki SKU]
* **Wydawca Namespace:** [publikowania portalu -> maszyn wirtualnych -> -> kartę wskazówki Namespace wydawcy Powiedz nam o Twoja firma (znalezione w kroku 2 zarejestrować) -> Namespace]
* **Porty:** [publikowania portalu -> maszyn wirtualnych -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Otwórz porty]
* **Zmiany listy SKU(s) cennika**
* **Zmiana modelu wymienionych SKU(s) rozliczeń**
* **Usuwanie rozliczeń regionów wymienionych SKU(s)**
* **Zmiana liczba dysków danych z listy SKU(s)**

## <a name="step-2-set-your-prices"></a>Krok 2: Ustaw cenach
### <a name="pricing-models"></a>Modelami cenowymi
| Model cen | Opis |
| --- | --- |
| Podstawowa |Płaskie miesięczne szybkość płatnej w momencie zakupu; np. $10 miesięcznie. |
| Zużycie () użycie, miernika) |Należy zwrócić na użycia, który jest zdefiniowany przez wydawcę oferty. Nadwyżkowe elementy w warstwie nie można zdefiniować na stanowisko na użytkownika itd., ponieważ nie ma żadnych koncepcji ułamek użytkownika lub możliwość czy proration. Użycie został zgłoszony przez partnera co godzinę. Odbiorca płaci na miesięczne cyklu rozliczeniowego, w przeciwieństwie do góry podobnego planów miesięczne. |
| Bezpłatna wersja próbna |Klient może używać bezpłatnie, przez ograniczony czas, a następnie później Zapłać normalnych stawek. |
| Warstwa bezpłatna |Plan zawsze jest bezpłatna. |
| Migracja () Konwersja lub uaktualnienie/obniżania) planu |Koncepcja użytkownika przenoszenie z ich bieżący plan do innego planu dopuszczalne; zdefiniowane przez partnera. |

**Cennik modeli dostępnych przez typ oferty**

> [!IMPORTANT]
> Dostępność określonych modeli cenową zależy od typu oferty. Znajdują się w tabeli poniżej.
> 
> 

|  | Tylko podstawowy | Tylko zużycie | Podstawa + zużycie |
| --- | --- | --- | --- |
| Obraz maszyny wirtualnej |Nie |Yes |Nie |
| Usługa Developer |Yes |Yes |Yes |

### <a name="21-set-your-vm-prices"></a>2.1. Ustaw cenach maszyny Wirtualnej
Obecnie maszyny wirtualne zostały następujące **3 typów modeli rozliczeń:**

* **Co godzinę:** klientów zostały naliczone opłaty na podstawie na godzinę według stawki ustawione przez wydawców na rozmiarów maszyn wirtualnych. W przypadku liczby **rozliczenia godzinowe** modelu jednostki SKU, cena razem będzie sumą kosztów oprogramowania pobieranych przez wydawcę i koszty infrastruktury pobierane przez firmę Microsoft. Ten całkowity koszt będzie widoczny dla klienta jako cenę godzinową i miesięczne podczas biorąc pod uwagę zakupu (zobacz poniższy zrzut ekranu). **Wydawca odbiera 80% kosztów oprogramowania pobieranych przez nich.** Dlatego skontaktuj się z upewnij obliczeń przed ustawieniem odpowiednio ceny dla Twojej wersji produktu.
  
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Bezpłatna wersja próbna:** jest innej wersji modelu co godzinę. W tym miejscu klienta nie zostały naliczone opłaty dla oprogramowania koszt pierwsze 30 days(Free) po wdrożeniu maszyny Wirtualnej. Po 30days uzyskać naliczane na podstawie na godzinę według stawki ustawione przez wydawców w modelu co godzinę.
* **Przełącz-Your-właścicielem-licencji (BYOL):** wydawców Zarządzanie licencjonowania oprogramowania działającego na maszynie Wirtualnej.

**Ważne:** po oferta/jednostka SKU jest wyświetlana w portalu Azure Marketplace, nie można zmienić pola podane poniżej.

* **Zmiany listy SKU(s) cennika**
* **Zmiana modelu wymienionych SKU(s) rozliczeń**
* **Usuwanie rozliczeń regionów wymienionych SKU(s)**
* **Zmiana liczba dysków danych z listy SKU(s)**
* **Identyfikator oferty:** [publikowania portalu -> maszyn wirtualnych -> Wybierz ofertę -> obrazów maszyn wirtualnych -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [publikowania portalu -> maszyn wirtualnych -> Wybierz ofertę -> jednostki SKU kartę -> Dodaj jednostki SKU]
* **Wydawca Namespace:** [publikowania portalu -> maszyn wirtualnych -> -> kartę wskazówki Namespace wydawcy Powiedz nam o Twoja firma (znalezione w kroku 2 zarejestrować) -> Namespace]
* **Porty:** [publikowania portalu -> maszyn wirtualnych -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Otwórz porty]

### <a name="sell-to-countries-of-the-sku"></a>"Sprzedaje to" krajów jednostki SKU
Należy rozważyć, którym udostępniasz Twojej wersji produktu. Niektórych krajach są klasyfikowane "Publicznej Microsoft", a inne są sklasyfikowane jako "Publicznej niezależnego dostawcy oprogramowania."

* W krajach "Publicznej Microsoft" Firma Microsoft gromadzi podatki od klientów i płaci podatki (publiczna) Rząd.
* Krajów "Przekaż niezależnego dostawcy oprogramowania" partnerów są zobowiązani do zbierania klientów podatki i płatności podatków Rząd. Jeśli wybierzesz sprzedaży w krajach "Przekaż niezależnego dostawcy oprogramowania", musi mieć możliwość obliczania i zapłacić podatki w krajach, którą wybierzesz.

> [!NOTE]
> Twoje jednostka SKU nie będą dostępne w krajach ustawienia ich ceny [publikowania portal](https://publish.windowsazure.com). Poniżej podano wskazówki do cennika co godzinę i BYOL jednostki SKU.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 jak można skonfigurować godzinowe modelu cenowego dla jednostki SKU
Wykonaj kroki podane poniżej co godzinę model cenowy dla jednostki SKU Instalatora:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** i wybierz ofertę.
3. W menu po lewej stronie po stronie, kliknij przycisk **jednostki SKU** kartę.
4. Upewnij się, że jednostka SKU jest oznaczona jako "Co godzinę modelu rozliczeń". Jeśli nie, należy kliknąć opcję **Edytuj** przycisk, aby przywrócić modelu rozliczeń. Zostanie otwarte okno. Usuń zaznaczenie pola wyboru "rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (alias Bring Your Own License)" i Zapisz zmiany.
5. Jeśli chcesz włączyć bezpłatnej wersji próbnej dla pierwszego 30days wdrożenia jednostka SKU, następnie wybierz opcję "Miesiąc" pytania "Jest bezpłatna wersja próbna dostępne?" W przeciwnym razie wybierz opcję "Brak okres próbny". Teraz wykonać kroki podane poniżej.
6. W menu po lewej stronie po stronie, kliknij przycisk **CENNIK** kartę.
7. Wybierz region podstawowy.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Wartość ceny dla wszystkich rdzeni. **Należy podać cen dla wszystkich rdzeni dla jednostki SKU nawet wtedy, gdy Twoje jednostka SKU nie jest ona obsługiwana.**
   
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Ręcznie Ustaw ceny dla innych regionów lub Kreator AUTOPRICE można ustawić ceny innych regionów, na podstawie regionu podstawowego. Aby użyć AUTOPRICE kreatora kliknij przycisk **AUTOPRICE inne rynkach oparte na cen w UNITED STATES.** **Uwaga:** Etykieta przycisku może się różnić w zależności od regionu, które zostały wybrane. Ponieważ Wybraliśmy Stanów Zjednoczonych podczas tworzenia tego dokumentu, to przycisk jest oznaczona jako "Auto cena innymi rynkach na podstawie cen w Stanach Zjednoczonych" na poniższym zrzucie ekranu.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Zostanie otwarty Kreator cen automatycznie. Pierwsza strona wyświetla wybór podstawowej rynku. Wprowadź sekcji i przejdź do następnej strony, klikając przycisk "->".
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Opcja wyboru rdzeni i planów będą wyświetlane na stronie 2. Wybierz żądaną planów, a następnie kliknij przycisk "->" przycisku. Kliknij przycisk **Przełącz wszystkie** przycisk, aby wybrać wszystkie **usługi planów** i **liczników** lub pozwala na ręczne sprawdzenie pól wyboru. **Należy podać cen dla wszystkich rdzeni dla jednostki SKU nawet wtedy, gdy Twoje jednostka SKU nie jest ona obsługiwana.** Dlatego upewnij się, że wybrane są wszystkie rozmiary core.
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Strona 3 przedstawia rynkach/regionach. Kliknij przycisk **Przełącz wszystkie** przycisk, aby wybrać wszystkie regiony lub ręczne sprawdzenie pól dla regionu. Kliknij przycisk "->", aby przejść do następnej strony. **Uwaga:** Microsoft podatku przekazana krajów są wskazywane przez dom, takich jak symbolu. Aby uzyskać więcej informacji można znaleźć w sekcji "Sprzedaje to" krajów SKU tej strony.
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Strona 4 przedstawia kursy wymiany. Kliknij przycisk Zakończ, aby wykonać kroki.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 jak Instalator BYOL modelu cenowego dla jednostki SKU
Wykonaj kroki podane poniżej można skonfigurować BYOL model cenowy dla jednostki SKU:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** i wybierz ofertę.
3. W menu po lewej stronie po stronie, kliknij przycisk **jednostki SKU** kartę.
4. Upewnij się, że jednostka SKU jest oznaczony jako "Bring your own license jednostki SKU". W przeciwnym razie kliknij przycisk Edytuj, aby przywrócić modelu rozliczeń. Zostanie otwarte okno. Zaznacz pole wyboru "rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (alias Bring Your Own License)" i Zapisz zmiany.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. W menu po lewej stronie po stronie, kliknij przycisk **CENNIK** kartę.
6. Wybierz region podstawowy i udostępnić jednostki SKU w regionie, zaznaczając pole wyboru dla jednostki SKU w sekcji dostępności jednostki SKU EXTERNALLY-LICENSED (BYOL) (zobacz poniższy zrzut ekranu).
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Udostępnij jednostki SKU w różnych regionach ręcznie lub w tym celu można użyć Kreatora AUTOPRICE. Zapoznaj się z punktami #9-#13 (które wyjaśnienia dotyczące korzystania z Kreatora AUTOPRICE) w sekcji **"2.1.1 konfiguracji model cenowy dla jednostki SKU co godzinę"** tej strony.

### <a name="22-set-your-developer-service-prices"></a>2.2. Ustaw cenach usługi Developer
Plany może być dowolną kombinacją base + użycia, gdzie podstawowy jest miesięcznej cenie, a nadwyżkowe cen płatności użycia. (Zobacz poniżej szczegółowe).

**Przykład:** oferty usługi developer firmy Contoso

| Planowanie | Cena | Zawiera | Ścieżki migracji |
| --- | --- | --- | --- |
| Bezpłatna |$0/ miesiąc |Podstawowe funkcje. |Można migrować do innego planu |
| Brązowy |$10 miesięcznie |Podstawowe funkcje i limit 1000 funkcji X. |Można migrować do planów brązowa Plus, srebrna i Gold |
| Brązową Plus |Bezpłatny okres próbny: miesięcznie $0 + $0/meter01 |Podstawowe funkcje i limit przydziału wynoszący 10 000 funkcji X.  Gdy jest używana funkcja X przydziału, klient może Zapłać za Użyj za pośrednictwem meter01. |Można migrować do planów Silver oraz i Gold |
| Brązową Plus |Okres płatną () Bezpłatna wersja próbna wygasła): miesięcznie 10 $ + $ 0,05/meter01 |Podstawowe funkcje i limit przydziału wynoszący 10 000 funkcji X.  Gdy jest używana funkcja X przydziału, klient może Zapłać za Użyj za pośrednictwem meter01. |Można migrować do planów Silver oraz i Gold |
| Srebrny |0,15 USD/meter01 |Klienta można zwrócić na użycie za pomocą meter01, czyli funkcji X. |Można migrować do planów brązowa i Gold |
| Srebrny Plus |20 USD miesięcznie + 0,15 USD/meter01 0,01 $/ meter02 |Podstawowe funkcje i limit przydziału wynoszący 10 000 funkcji X i 100 funkcji Y.  Po przydziału funkcji X klienta można zwrócić na użycie za pośrednictwem meter01.  Gdy jest używana funkcja Y limit przydziału, klienta można należy zwrócić na użycie za pośrednictwem meter02. |Można migrować do planów brązowa oraz i Gold |
| Złoty |1000 USD miesięcznie |Limit przydziału wynoszący 10 000 funkcji 1000 funkcji Y, X i nieograniczone funkcji Z. |Można migrować do wszystkich planów, z wyjątkiem w warstwie bezpłatna |

## <a name="step-3-provide-support-information"></a>Krok 3: Zapewniają obsługę informacji
Szczegóły dotyczące kontaktu służą do wewnętrznej komunikacji między partnerami i Microsoft tylko. Adres URL pomocy technicznej będzie dostępne dla klientów końcowych.

1. Przejdź do **Obsługa** nagłówek po lewej stronie portalu publikowania.
2. Wprowadź informacje w obszarze **Engineering skontaktuj się z**.
3. Wprowadź informacje w obszarze **techniczną**. Jeśli podasz tylko do obsługi poczty e-mail, wprowadź numer telefonu fikcyjny i zostanie użyty adres e-mail podany.
4. Wprowadź adres URL pomocy technicznej.

## <a name="step-4-choose-azure-marketplace-categories"></a>Krok 4: Wybierz kategorie portalu Azure Marketplace
**Kategorii** karta zawiera tablicę wybrane opcje. Ofertę mogą być objęte te i można wybrać maksymalnie pięć kategorii.

## <a name="how-your-marketing-will-appear"></a>Wygląd marketingu
Poniżej znajduje się szczegółowy widok wykorzystania zapewniają informacje o [witryny sieci Web portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i w [portalu Azure](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Azure Marketplace witryny sieci Web
![Rysowanie](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista oferty w witrynie Azure Marketplace*

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Szczegóły opis oferty w witrynie Azure Marketplace*

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Szczegóły cennika w witrynie Azure Marketplace opis oferty*

### <a name="azure-portal"></a>Azure Portal
![Rysowanie](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista oferty w portalu Azure*

![Rysowanie](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Szczegóły opis oferty w portalu Azure*

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy jest ładowany zawartości witryny Marketplace, umożliwia przejście z testowaniem ofertę tymczasowych. Jednak należy wybrać typ oferty odpowiednie z listy poniżej, jak kroki zależą od typu oferty.

* [Testowanie ofertę maszyny Wirtualnej w przemieszczania](marketplace-publishing-vm-image-test-in-staging.md)
* [Testowanie ofertę szablon rozwiązania tymczasowych](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: jak publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
