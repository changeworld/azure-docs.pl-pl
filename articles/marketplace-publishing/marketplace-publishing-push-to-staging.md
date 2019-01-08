---
title: Przygotuj i przetestować ofertę we wdrożeniach w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowe instrukcje dotyczące dostarczanie marketingowych zawartości, Konfigurowanie planów cenowych i testowania oferty przed wdrożeniem w portalu Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ROBOTS: NOINDEX
ms.openlocfilehash: f1e6e779731564f714cc100d25a53da8732dca74
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075995"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>Dokończ tworzenie oferty z zawartości marketingowej
W tym kroku proces publikowania należy podać niektórych zawartości marketingowej i szczegółowe informacje o ofercie i/lub jednostek SKU w witrynie Azure Marketplace. Na przykład zostanie Podaj opis produktu, logo firmy, plany ceny, szczegółowe informacje o planach i inne informacje wymagane do wypchnięcia oferty i/lub jednostki SKU do wdrażania przejściowego. Te informacje są używane jako marketingowe w witrynie Azure portal. Rozpocznie się ten proces w [portalu wydawców][link-pubportal].

## <a name="step-1-provide-marketplace-marketing-content"></a>Krok 1: Podaj marketingowych zawartości witryny Marketplace
*Język angielski jest domyślnym i jedynym obsługiwanym językiem.*  Sprawdź wszystkie informacje podane w polach w języku angielskim. Wszystkie informacje można edytować w dowolnym momencie przed wypchnięciem do wdrażania przejściowego.

1. Przejdź do portalu wydawców [ https://publish.windowsazure.com ](https://publish.windowsazure.com).
2. W menu po lewej stronie, kliknij polecenie **marketingu** kartę.
3. W okienku głównym kliknij **języka angielskiego (US)** przycisku.
   
   > [!IMPORTANT]
   > Wszystkie pola muszą mieć, włącznie z obrazami, aby można było wykonać Wypychanie do wdrażania przejściowego.
   > 
   > 

### <a name="details-and-plans"></a>Szczegółowe informacje i plany
1. Wprowadź tytuł oferty (maksymalnie 50 znaków), oferują Podsumowanie (maksymalnie 100 znaków), oferują długie Podsumowanie (maksymalnie 256 znaków), opis (maksymalnie 1300 znaków), logo, w ramach oferty **szczegóły** kartę
2. Wprowadź tytuł planu (maksymalnie 50 znaków), podsumowanie planu (maksymalnie 100 znaków), należy zaplanować, opis (maksymalnie 2000 znaków) w obszarze **plany** kartę.
   
   > [!NOTE]
   > Można użyć następujących tagów HTML do formatowania podsumowanie, długie Podsumowanie i opis oferty i plany. Dozwolonych tagów HTML są h1, h2, h3, h4, h5, p, ol, ul, li [target | href], silne, em, b, i.
   > 
   > 
3. Nie należy wprowadzać zduplikowane tekst w polu Opis oferty i plan.
4. Nie należy wprowadzać zduplikowane tekst w polu Tytuł planu i oferty długie podsumowanie.
5. Wprowadź tekst zduplikowane pod tytułem planu i nie oferują podsumowanie.
6. Nie należy wprowadzać tytuły identyczne planu dla oferty z wieloma planami.
7. Przekaż obrazy wymaganych specyfikacji (określonych w portalu wydawców) w formacie PNG, jeden dla każdego rozmiaru.
8. Upewnij się, że logo postępuj zgodnie z wytycznych dotyczących logo portalu Azure Marketplace, które zostały wymienione poniżej.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Wytycznych dotyczących Logo witryny Azure Marketplace**

Wszystkie logo, które są przekazywane w portalu wydawców należy przestrzegać poniższych wytycznych:

* Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.
* Kolory motywu w witrynie Azure Portal są białe i czarne. Dlatego należy unikać te kolory jako kolor tła logo usługi. Użyj niektóre koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe. **Następnie korzystając z przezroczystym tłem, upewnij się, że logo/tekstu nie są białe lub czarne lub niebieski.**
* Nie należy używać gradientu tła na logo.
* Należy unikać wprowadzania tekstu, nawet Twoja firma lub nazwa marki na logo. Wygląd i działanie logo powinno być "płaską" i unikać gradientów.
* Logo nie powinien być rozciągnięty.
* Małe logo powinny mieć rozmiar 40 X 40 piks.
* Średnie logo powinno być o rozmiarze 90 X 90 piks.
* Duże logo powinny mieć rozmiar 115 X 115 piks.
* Szerokie logo powinny mieć rozmiar 255 X 115 piks.
* Hero logo powinny mieć rozmiar 815 X 290 piks.

> [!NOTE]
> Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. Jednak po przekazanych ikony elementów hero, nie można usunąć z publikowanie portalu. W tym czasie partner musi korzystać z portalu Azure Marketplace dotyczących ikony elementów Hero.
> 
> 

  ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Dodatkowe wytyczne dotyczące ikona logo Hero (opcjonalnie)**

* Logo usługi Hero jest opcjonalne. Wydawcy mogą zrezygnować z Przekaż Hero logo. **Jednak po przekazanych ikony elementów hero, nie można usunąć z publikowanie portalu. W tym czasie partner musi korzystać z portalu Azure Marketplace dotyczących ikony Hero inne oferty nie zostanie zatwierdzona do środowiska produkcyjnego.**
* Nazwa wyświetlana wydawcy, tytuł plan i ofertę, długie podsumowanie są wyświetlane w kolorze białym czcionki. Dlatego należy unikać, pamiętając wszelkie jasny kolor tła ikony elementów Hero. Czarny, biały i przezroczyste tło nie jest dozwolone dla elementu Hero ikon.
* Nazwa wyświetlana wydawcy, plan, że tytuł, długie Podsumowanie oferty i przycisk Utwórz są osadzone programowo wewnątrz Hero logo po tej oferty, które będą wyświetlane. Nie należy więc wprowadzić dowolny tekst, podczas projektowania Hero logo. Po prostu pozostaw puste miejsce po prawej stronie ponieważ tekst (oznacza to, że nazwa wyświetlana wydawcy, tytuł plan oferty, długie podsumowanie) zostaną uwzględnione programowo przez firmę Microsoft za pośrednictwem miejsca. Puste miejsce tekst powinien być 415 x 100, po prawej stronie (i jest przesuwane w 370 pikseli od lewej strony).
  
  ![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>Linki
Na **łącza** kartę na pasku po lewej stronie, Podaj wszelkie linki z informacjami, które mogą pomóc klientom. Wpisz nazwę i adres URL dla każdego linku.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>Przykładowe obrazy (opcjonalnie)
> [!NOTE]
> W tym przykładowy obraz jest opcjonalny.
> Mimo że można przekazać wiele przykładowe obrazy w publikowania w portalu, tylko jeden obraz (losowo wybrany system) pobiera wyświetlane w witrynie Azure portal. Z tego powodu zaleca się przekazanie co najwyżej jeden przykładowy obraz.
> 
> 

Na **przykładowe obrazy** kartę w menu po lewej stronie, a następnie Przekaż nowy obraz, klikając **Przekaż nowy obraz**. Jeśli masz istniejący obraz, a chcesz go zastąpić, kliknij przycisk **obrazu Zastąp**.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>Informacje prawne
Na **prawne** kartę, podaj link do Twoich zasad lub warunków użytkowania. Wprowadź lub Wklej warunki w dużych **warunki użytkowania** pole. Limit liczby znaków prawne warunki użytkowania wynosi 1 000 000 znaków.

![Rysowanie](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**Uwaga:** W przypadku ofert maszyny wirtualnej po oferty/jednostki SKU jest umieszczone w witrynie Azure portal, nie można zmienić pola podane poniżej:

* **Identyfikator oferty:** [portal wydawców -> maszyny wirtualne -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [portal wydawców -> maszyny wirtualne -> Wybierz -> Twoja oferta jednostki SKU kartę -> Dodaj jednostkę SKU]
* **Wydawca Namespace:** [portal wydawców -> maszyny wirtualne -> karta -> Poinformuj nas o Twojej firmie (znalezione w obszarze "Krok 2 zarejestrować Twoja firma") -> Przewodnik wydawcy Namespace -> Namespace]

W przypadku ofert maszyny wirtualnej po oferty/jednostki SKU znajduje się w witrynie Azure Marketplace nie można zmienić pola podane poniżej:

* **Identyfikator oferty:** [portal wydawców -> maszyny wirtualne -> Wybierz -> Twoja oferta obrazów maszyn wirtualnych -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [portal wydawców -> maszyny wirtualne -> Wybierz -> Twoja oferta jednostki SKU kartę -> Dodaj jednostkę SKU]
* **Wydawca Namespace:** [portal wydawców -> maszyny wirtualne -> Przewodnik -> karta Namespace wydawcy Poinformuj nas o Twoja firma (znalezione w ramach kroku 2 Zarejestruj) -> Namespace]
* **Porty:** [portal wydawców -> maszyny wirtualne -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Otwórz porty]
* **Zmiany wymienione dla jednostek SKU cennika**
* **Zmiana modelu z wymienionych dla jednostek SKU rozliczeń**
* **Usuwanie rozliczeń regionach wymienionych dla jednostek SKU**
* **Zmiana liczba dysków danych z wymienionych dla jednostek SKU**

## <a name="step-2-set-your-prices"></a>Krok 2: Ustalanie cen
### <a name="pricing-models"></a>Modele cen
| Model cen | Opis |
| --- | --- |
| Podstawowa |Zryczałtowana stawka miesięczna płatne w momencie zakupu; na przykład w wysokości 10 USD/miesiąc. |
| Użycie (zwane) użycie, licznik) |Płatność za użycie, która jest zdefiniowana według wydawcy, oferty. Nadwyżkowe elementy w warstwie nie można zdefiniować na stanowisko, za użytkownika itp., jak nie obowiązuje koncepcja ułamka użytkownika lub możliwości w celu dzielimy. Użycie jest zgłaszany przez partnera w systemie godzinowym. Klient płaci na miesięcznego okresu rozliczeniowego, w przeciwieństwie do góry like plany miesięczne. |
| Bezpłatna wersja próbna |Klient może używać bezpłatnie przez ograniczony czas, a następnie po tej dacie Zapłać normalnych stawek za użycie. |
| Warstwa bezpłatna |Plan jest zawsze bezpłatne. |
| Migracja (zwane) Konwersja lub uaktualnienie/obniżenia poziomu) planu |Koncepcja użytkownika przenoszenie z ich bieżący plan do innego planu dopuszczalne; zdefiniowane przez partnera. |

**Cennik modeli, które są dostępne przez typ oferty**

> [!IMPORTANT]
> Dostępność niektórych modeli cenowych zależą od typu oferty. Zobacz w poniższej tabeli.
> 
> 

|  | Tylko podstawowy | Tylko zużycie | Podstawa + zużycie |
| --- | --- | --- | --- |
| Obraz maszyny wirtualnej |Nie |Yes |Nie |
| Usługi dla deweloperów |Yes |Yes |Yes |

### <a name="21-set-your-vm-prices"></a>2.1. Ustalanie cen maszyn wirtualnych
Obecnie dla maszyn wirtualnych, mamy następujące **trzy rodzaje modelami rozliczeń:**

* **Co godzinę:** Klienci jest naliczana na podstawie za godzinę, oparte na stawkach ustawione przez wydawców na rozmiary maszyn wirtualnych. W przypadku programu **rozliczeń godzinowych** modelu jednostek SKU, całkowita cena będzie sumą kosztów oprogramowania pobieranych przez wydawcę i kosztów infrastruktury pobierane przez firmę Microsoft. To łączny koszt pojawi się do klienta jako godzinowe i miesięczna cena podczas biorąc pod uwagę zakupu (patrz poniższy zrzut ekranu). **Wydawca otrzyma 80% koszty oprogramowania pobieranych przez nich.** Dlatego należy odpowiednio obliczeń przed ustawieniem ceny od jednostek SKU.
  
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **Bezpłatna wersja próbna:** Ta opcja jest inną wersję model godzinowy. W tym miejscu klienta nie jest naliczana za koszty oprogramowania dla pierwsze 30 days(Free) po wdrożeniu maszyny Wirtualnej. Po upływie 30 dni one jest naliczana na podstawie za godzinę, oparte na stawkach ustawione przez wydawców w modelu co godzinę.
* **Bring-Your-Own-License (BYOL):** Wydawców Zarządzanie licencjonowania oprogramowania działającego na maszynie Wirtualnej.

**Ważne:** Gdy oferty/jednostki SKU jest wyświetlany w portalu Azure Marketplace, nie można zmienić pola podane poniżej.

* **Zmiany wymienione dla jednostek SKU cennika**
* **Zmiana modelu z wymienionych dla jednostek SKU rozliczeń**
* **Usuwanie rozliczeń regionach wymienionych dla jednostek SKU**
* **Zmiana liczba dysków danych z wymienionych dla jednostek SKU**
* **Identyfikator oferty:** [portal wydawców -> maszyny wirtualne -> Wybierz -> Twoja oferta obrazów maszyn wirtualnych -> Identyfikator oferty]
* **Identyfikator jednostki SKU:** [portal wydawców -> maszyny wirtualne -> Wybierz -> Twoja oferta jednostki SKU kartę -> Dodaj jednostkę SKU]
* **Wydawca Namespace:** [portal wydawców -> maszyny wirtualne -> Przewodnik -> karta Namespace wydawcy Poinformuj nas o Twoja firma (znalezione w ramach kroku 2 Zarejestruj) -> Namespace]
* **Porty:** [portal wydawców -> maszyny wirtualne -> Twoja oferta -> obrazów maszyn wirtualnych kartę -> Otwórz porty]

### <a name="sell-to-countries-of-the-sku"></a>"" Kraje nabywców jednostki SKU
Należy rozważyć, którym udostępniasz od jednostek SKU. Niektóre kraje są klasyfikowane jako "Microsoft podatkową", a inne są klasyfikowane jako "Podatkową dostawców ISV."

* W krajach "Microsoft podatkową" Firma Microsoft zbiera podatków od klientów i zapłaci podatki (publiczna) dla rządu.
* W krajach "Wpłaca niezależnego dostawcy oprogramowania" partnerzy są odpowiedzialne za zbieranie klientów podatki i wypłaty podatku dla rządu. Jeśli chcesz sprzedawać w krajach "Niezależnego dostawcy oprogramowania wpłaca" musi mieć możliwość obliczania i zapłacić podatki w wybranych krajach.

> [!NOTE]
> Jednostki SKU nie będzie dostępna w krajach, chyba że ich ceny [portal wydawców](https://publish.windowsazure.com). Wskazówki dotyczące Przygotuj cennik co godzinę i jednostki SKU w ramach opcji BYOL są podane poniżej.
> 
> 

### <a name="211-how-to-set-up-hourly-pricing-model-for-a-sku"></a>2.1.1 sposobu konfigurowania co godzinę modelu cen dla jednostek SKU
Wykonaj czynności podane poniżej, aby skonfigurować model cen dla jednostek SKU co godzinę:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** kartę, a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Upewnij się, że jednostka SKU jest oznaczony jako "Co godzinę Model rozliczeń". Jeśli nie, następnie kliknij pozycję **Edytuj** przycisk, aby przywrócić model rozliczeń. Zostanie otwarte okno. Usuń zaznaczenie pola wyboru "Pomoc dotycząca rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (zwane również Bring Your Own License)", a następnie zapisz zmiany.
5. Jeśli chcesz włączyć bezpłatną wersję próbną przez pierwsze 30 dni wdrożenie jednostki SKU, następnie wybierz opcję "Miesiąc" pytania "Jest bezpłatna wersja próbna dostępna?" W przeciwnym razie wybierz opcję "Nie wersja próbna". Teraz wykonać czynności podane poniżej.
6. W menu po lewej stronie kliknij **ceny** kartę.
7. Wybierz region podstawowy.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. Ustaw ceny dla wszystkich rdzeni. *Przewidzieć cenę rdzeni jednostki SKU, nawet jeśli jednostki SKU nie obsługuje.*
   
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. Ręcznie ustawić cen dla innych regionach lub Kreator AUTOPRICE służy do ustawiania ceny innych regionach na podstawie regionu podstawowego. Aby użyć AUTOPRICE kreatora kliknij przycisk **AUTOPRICE inne rynków oparte na ceny w Stany Zjednoczone.** **Uwaga:** Etykieta przycisku mogą się różnić w zależności od regionu, który wybrano. Ponieważ wybrane Stany Zjednoczone podczas tworzenia tego dokumentu, dlatego ten przycisk ma etykietę jako "Price rynkach, na podstawie cen w Stanach Zjednoczonych automatycznie", na poniższym zrzucie ekranu.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. Zostanie otwarty Kreator cena automatycznie. Pierwsza strona wyświetla wybór podstawowy rynku. Wprowadzić sekcji i przejdź do następnej strony, klikając przycisk "->".
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. Opcja wyboru rdzeni i planów będą wyświetlane na stronie 2. Wybierz żądane plany, a następnie kliknij przycisk "->" przycisk. Kliknij przycisk **Przełącz wszystkie** przycisk, aby wybrać wszystkie **usługi planów** i **liczniki** lub można ręcznie zaznacz pola wyboru. **Należy podać cena rdzeni jednostki SKU, nawet jeśli jednostki SKU nie obsługuje.** Dlatego upewnij się, że wybrane są wszystkie rozmiary core.
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. Strona 3 wyświetla rynków/regionach. Kliknij przycisk **Przełącz wszystkie** przycisk, aby wybrać we wszystkich regionach lub ręcznie, zaznacz pola wyboru dla regionu. Kliknij przycisk "->", aby przejść do następnej strony. **Uwaga:** Kraje przekazana podatkowych firmy Microsoft są wskazywane przez dom, takich jak symboli. Aby uzyskać więcej informacji można znaleźć w sekcji "" kraje nabywców jednostki SKU tej strony.
    
    ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. Strona 4 Wyświetla kursy wymiany. Kliknij przycisk Zakończ, aby wykonać kroki.

### <a name="212-how-to-set-up-byol-pricing-model-for-a-sku"></a>2.1.2 sposobu konfigurowania BYOL modelu cen dla jednostek SKU
Wykonaj czynności podane poniżej, aby skonfigurować model cen dla jednostek SKU w ramach opcji BYOL:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** kartę, a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Upewnij się, że jednostka SKU jest oznaczony jako "Bring your own license jednostki SKU". W przeciwnym razie kliknij przycisk Edytuj, aby przywrócić model rozliczeń. Zostanie otwarte okno. Zaznacz pole wyboru "Pomoc dotycząca rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (zwane również Bring Your Own License)", a następnie zapisz zmiany.
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. W menu po lewej stronie kliknij **ceny** kartę.
6. Wybierz region podstawowy i udostępnić jednostki SKU w regionie, zaznaczając pole wyboru dla jednostki SKU w sekcji dostępności jednostki SKU EXTERNALLY-LICENSED (BYOL) (patrz poniższy zrzut ekranu).
   
   ![Rysowanie](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. Udostępnij jednostki SKU w innych regionach ręcznie lub można użyć Kreatora AUTOPRICE, w tym celu. Zapoznaj się punkty #9-#13 (które wyjaśnienia dotyczące korzystania z Kreatora AUTOPRICE) w sekcji **"2.1.1 sposób konfigurowania co godzinę, model cen dla jednostki SKU"** tej strony.

### <a name="22-set-your-developer-service-prices"></a>2.2. Ustalanie cen usługi dla deweloperów
Plany mogą być dowolną kombinacją podstawowej i użycia, gdzie jest miesięczna cena podstawowej, a nadwyżkowe ceną bezpieczeństwa jest płatność za użycie. (Zobacz poniżej szczegółowe).

**Przykład:**  Oferta usługi dla deweloperów firmy Contoso

| Planowanie | Cena | Zawiera | Ścieżki migracji |
| --- | --- | --- | --- |
| Bezpłatna |0 USD/miesiąc |Podstawowe funkcje. |Można poddać migracji do innego planu |
| Brązowy |10 USD/miesiąc |Podstawowe funkcje i limit przydziału 1000 X funkcji. |Można migrować do planów brązowa oraz Silver i Gold |
| Bronze Plus |Okres bezpłatnej wersji próbnej: 0 USD/miesiąc + 0 USD/meter01 |Podstawowe funkcje i przydział 10 000 X funkcji.  Gdy jest używana funkcja X limitu przydziału, klient może płatność za użycie za pośrednictwem meter01. |Można migrować do planów oraz Silver i Gold |
| Bronze Plus |Okres płatne (zwane) Bezpłatna wersja próbna wygasła): 10 USD/miesiąc + $0.05/meter01 |Podstawowe funkcje i przydział 10 000 X funkcji.  Gdy jest używana funkcja X limitu przydziału, klient może płatność za użycie za pośrednictwem meter01. |Można migrować do planów oraz Silver i Gold |
| Srebrny |0,15 USD/meter01 |Klient może płatność za użycie za pośrednictwem meter01, czyli funkcji X. |Można migrować do planów brązowa i Gold |
| Silver Plus |20 USD/miesiąc + 0,15 USD/meter01 + $0.01/meter02 |Podstawowe funkcje i przydział 10 000 X funkcji do 100 funkcji Y.  Po przydziału X funkcji odbiorcy mogą płatność za użycie za pośrednictwem meter01.  Po przydziału funkcji Y klient może płatność za użycie za pośrednictwem meter02. |Można migrować do planów brązowa Plus i Gold |
| Złoty |1000 USD/miesiąc |Limit przydziału wynoszący 10 000 X 1000 funkcji Y, funkcji i nieograniczone funkcji Z. |Można migrować do wszystkich planów, z wyjątkiem bezpłatnie |

## <a name="step-3-provide-support-information"></a>Krok 3: Podaj informacje o pomocy technicznej
Szczegółowe dane kontaktowe są używane na potrzeby wewnętrznej komunikacji między partnerem i firmą Microsoft tylko. Adres URL pomocy technicznej będą dostępne dla klientów końcowych.

1. Przejdź do **pomocy technicznej** nagłówka po lewej stronie portalu wydawców.
2. Wprowadź informacje w obszarze **kontakt techniczny**.
3. Wprowadź informacje w obszarze **techniczną**. Jeśli podasz tylko obsługa poczty e-mail, wprowadź numer telefonu fikcyjnego, a w zamian zostanie użyta z podanego adresu e-mail.
4. Wprowadź adres URL pomocy technicznej.

## <a name="step-4-choose-azure-marketplace-categories"></a>Krok 4: Wybieranie kategorii portalu Azure Marketplace
**Kategorie** karta zawiera szereg opcji. Można wybrać maksymalnie pięć kategorii.

## <a name="how-your-marketing-will-appear"></a>Jak będzie wyglądać marketingowe
Poniżej przedstawiono szczegółowy widok sposobu wykorzystywania oferty informacji marketingowych na [witryny sieci Web portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i [witryny Azure portal](https://portal.azure.com).

### <a name="azure-marketplace-website"></a>Witryny sieci Web Azure Marketplace
![Rysowanie](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Lista ofert w witrynie Azure Marketplace*

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Szczegóły opis oferty w witrynie Azure Marketplace*

![Rysowanie](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Szczegóły cennika w witrynie Azure Marketplace opis oferty*

### <a name="azure-portal"></a>Azure Portal
![Rysowanie](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Lista ofert w witrynie Azure portal*

![Rysowanie](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Szczegóły opis oferty w witrynie Azure portal*

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy trwa ładowanie zawartości witryny Marketplace, przejdźmy do przodu z testowaniem ofertę w środowisku tymczasowym. Jednak musisz wybrać typ oferty odpowiednie z listy poniżej, jak kroki różnią się od typu oferty.

* [Testowanie oferty maszyny Wirtualnej w środowisku tymczasowym](marketplace-publishing-vm-image-test-in-staging.md)
* [Przetestować ofertę szablonu rozwiązania w środowisku tymczasowym](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>Zobacz także
* [Wprowadzenie: Jak opublikować ofertę w portalu Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
