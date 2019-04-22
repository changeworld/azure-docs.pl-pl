---
title: Opis Cloudyn koszt raporty zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia zrozumienie struktury podstawowe raporty zarządzania kosztów Cloudyn i funkcji.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 6f856aeae74ea285cd6a0326fd225e454a1cbe43
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265835"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Cloudyn Understanding management raportów kosztów

Ten artykuł ułatwia zrozumienie struktury podstawowe raporty zarządzania kosztów Cloudyn i funkcji. Większość raportów Cloudyn są intuicyjne i jednolity wygląd i sposób działania. Po przeczytaniu tego artykułu, są gotowe do użycia wszystkich raportów rozwiązania cost management. Wiele standardowych funkcji są dostępne w całej różne raporty, umożliwiające przejście raporty z łatwością. Raporty są możliwe do dostosowania i możesz wybrać kilka opcji obliczają i wyświetlają wyniki.

## <a name="report-fields-and-options"></a>Pola raportu i opcje

Poniżej przedstawiono przykładowy raport kosztu w czasie. Większość raportów Cloudyn mają podobny układ.

![Przykładowy raport kosztu w czasie z obszarami numerowane odpowiadający opisów](./media/understanding-cost-reports/sample-report.png)

Każdy ponumerowany obszar na wcześniejszej ilustracji są szczegółowo opisane w następujących informacji:

1. **Zakres dat**

    Użyj listy zakres dat, aby zdefiniować przedział czasu raportu za pomocą wstępnie ustawionych lub niestandardowy.
2. **Zapisany filtr**

    Użyj listy zapisany filtr, aby zapisać bieżące grupy i filtry, które są stosowane do raportu. Zapisane filtry są dostępne przez raportów kosztów i wydajności, w tym:

      - Analiza kosztów
      - Alokacja
      - Zarządzanie zasobami
      - Optymalizacja

   Wpisz nazwę filtru, a następnie kliknij **Zapisz**.

3. **Tagi**

    Według tagów kategorii, należy użyć obszaru tagów do grupy. Znaczniki wyświetlane w menu są działu na platformie Azure lub tagów Centrum kosztu lub są kosztu firmy Cloudyn tagi jednostki i subskrypcji. Wybierz tagi do filtrowania wyników. Możesz również wpisać nazwę tagu (słowo kluczowe), aby filtrować wyniki.

    ![Przykład listy tagów, aby przefiltrować wyniki według](./media/understanding-cost-reports/select-options.png)

    Kliknij przycisk **Dodaj** można dodać nowego filtru.

    ![Dodaj pole filtru przedstawiający opcje i warunki, aby filtrować według](./media/understanding-cost-reports/add-filter.png)

    Tag, grupowanie i filtrowanie nie odnosi się do zasobów platformy Azure lub tagi z grupy zasobów.

    Alokacja kosztów tag grupowania i filtrowania są dostępne w **grup** opcji menu.

4. **Grupami w raportach**

    Używanie grup w analizy kosztów raporty muszą pokazywać w warstwie standardowa podzielonych na kategorie z danych w raporcie rozliczeń.  Jednak grupy alokacji kosztów raporty pokazu wyświetlić kategorie na podstawie tagów. Kategorie na podstawie tagów są zdefiniowane w model przydzielania kosztów oraz standardowe kategorie dla pozycji z danych dotyczących rozliczeń.

    ![Pierwszy przykład listę znaczników, które można pogrupować wg](./media/understanding-cost-reports/groups-tags01.png)

    ![Drugi przykład listę znaczników, które można pogrupować wg](./media/understanding-cost-reports/groups-tags02.png)

    W raportach alokacji kosztów grup w kategorie na podstawie tagu grupy mogą być następujące:
      - Tagi
      - Pozycja znaczniki grupy zasobów
      - Koszt tagi jednostek Cloudyn
      - Kategorie tag subskrypcji na potrzeby przydzielania kosztów

   Przykładami mogą być następujące:
   - Centrum kosztu
   - Dział
   - Aplikacja
   - Środowisko
   - Kod kosztów

     Oto lista wbudowanej grupy dostępne w raportach:

     - **Typ kosztu**
     - Wybierz typ kosztu lub wiele typów kosztów, albo wybierz wszystko. Typy kosztów:
       - Jednorazowa opłata
       - Pomoc techniczna
       - Koszt użycia
     - **Odbiorcy**
       - Wybierz określonego odbiorcy, wielu klientów lub wszystkich klientów.
     - **Nazwa konta**
       - Nazwa konta lub subskrypcji. Na platformie Azure jest nazwa subskrypcji platformy Azure.
     - **Brak konta**
       - Wybierz konto, wiele kont lub wszystkich kont. Na platformie Azure jest identyfikator GUID subskrypcji platformy Azure.
     - **Konta nadrzędne**
       - Wybierz konto nadrzędne, wiele kont lub wybierz opcję.
     - **Usługa**
       - Wybierz usługę, wiele usług lub wszystkich usług.
     - **Dostawca**
       - Dostawca chmury, gdzie są skojarzone zasoby i wydatki związane z.
     - **Region**
       - Region, w którym znajduje się zasób.
     - **Strefa dostępności**
       - Usługi AWS samodzielnie lokalizacje w regionie.
     - **Typ zasobu**
       - Typ zasobu w użyciu.
     - **Sub-Type**
       - Wybierz typ podrzędnych.
     - **Operacja**
       - Wybierz operację lub **Pokaż wszystko**.
     - **Model cen**
       - Wszystkie z wyprzedzeniem
       - Brak ponoszonych z góry kosztów
       - Partial ponoszonych z góry kosztów
       - Na żądanie
       - Rezerwacja
       - Miejsce
     - **Typ opłaty**
       - Wybierz typ opłaty ujemne lub dodatni, lub obu.
     - **Tenancy**
       - Czy maszyna jest uruchomiona jako dedykowane maszyny.
     - **Typ użycia**
       - Typ użycia może być opłat jednorazowych lub opłat cyklicznych.

5. **Filtry**

    Użyj filtrów pojedynczego lub wielokrotnego wyboru, aby ustawić zakresy adresów wybrane wartości. Aby ustawić filtr, kliknij **Dodaj** a następnie wybierz filtr kategorie i wartości.

6. **Model kosztów**

    Użyj modelu kosztów, aby wybrać model kosztów, która została wcześniej utworzona za pomocą Cost Allocation 360. Możesz mieć wiele modeli kosztów Cloudyn, w zależności od wymagań alokacji kosztów. Niektóre zespołom w organizacji mogą kosztów alokacji wymagania, które różnią się od innych użytkowników. Każdy zespół może mieć własne modelu kosztów dedykowanych.

    Aby uzyskać informacji na temat tworzenia definicji model alokacji kosztów, zobacz [przydzielanie kosztów za pomocą tagów niestandardowych](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortyzacja**

    Użyj amortyzacji w raportach alokacji kosztów, aby wyświetlić dane bez użycia na podstawie opłat za usługę lub koszty płatne jednorazowe i rozłożona równomiernie podczas ich czas na ich kosztów. Przykłady jednorazowe opłaty mogą być następujące:
    - Opłaty roczne pomocy technicznej
    - Opłaty roczne składniki zabezpieczeń
    - Opłaty za zakupu wystąpień zarezerwowanych
    - Niektóre elementy w portalu Azure Marketplace.

   W obszarze amortyzacji wybierz **amortyzowanego kosztu** lub **rzeczywistego kosztu**.

8. **Rozdzielczość**

    Użyj rozwiązania, aby wybrać czas rozdzielczość w ramach wybranego zakresu dat. Rozdzielczość czasu określa, jak jednostki są wyświetlane w raporcie i można:
    - Codziennie
    - Cotygodniowo
    - Miesięczne
    - Kwartalnie
    - Roczne

9. **Zasady alokacji**

    Użyj reguł alokacji zastosować lub wyłączyć obliczanie kosztu alokacji kosztów. Można włączyć lub wyłączyć Obliczanie alokacji kosztów dla danych dotyczących rozliczeń. Ponowne obliczanie mają zastosowanie do kategorii wybranych w raporcie. Umożliwia ocenę wpływu ponowne obliczenie alokacji kosztów względem nieprzetworzonych danych dotyczących rozliczeń.

10. **Bez kategorii**

    Użyj kategorii do dołączania lub wykluczania bez kategorii kosztów w raporcie.

11. **Pokaż/Ukryj pola**

    Opcja Pokaż/Ukryj nie ma żadnego efektu w raportach.

12. **Formaty wyświetlania**

    Użyj formatów wyświetlania, aby wybrać różne widoki wykres lub tabelę.

    ![Symbole formaty wyświetlania, które można wybierać](./media/understanding-cost-reports/display-formats.png)

13. **Wielu kolorów**

    Użycie wielu kolor, aby ustawić kolor wykresów w raporcie.

14. **Akcje**

    Aby zapisać, eksportowanie lub ustalania harmonogramu raportu za pomocą akcji.

15. **Zasady**

    Mimo że nie na zdjęciu niektóre raporty obejmują zasady przewidywany koszt obliczeń. W tych raportach **skonsolidowanych** zasad zawiera zalecenia dotyczące wszystkich kont i subskrypcji w ramach bieżącej jednostki, takich jak rejestracja w usłudze Microsoft lub usług AWS płatnika. **Autonomiczny** zasad zawiera zalecenia dotyczące jednego konta lub subskrypcji, tak, jakby nie istnieją żadne inne subskrypcje. Zasady, które możesz wybrać zmienia się od strategii optymalizacji używany przez Twoją organizację. Projekcji kosztów są oparte na ostatnich 30 dni użytkowania.

## <a name="save-and-schedule-reports"></a>Zapisz i planowania raportów

Po utworzeniu raportu można zapisać go do użytku w przyszłości. Zapisane raporty są dostępne w **Moje narzędzia** > **Moje raporty**. Jeśli wprowadzasz zmiany do istniejącego raportu i zapisz go, raport jest zapisywany jako nową wersję. Lub zapisz go jako nowy raport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Zapisywanie raportu w portalu Cloudyn

Podczas wyświetlania dowolnego raportu, kliknij przycisk **akcje** , a następnie wybierz **Zapisz moje raporty**. Nazywanie raportu, a następnie dodaj własny adres URL lub użyj automatycznie utworzone adresu URL. Można opcjonalnie **udostępnianie** raportu publicznie innym osobom w organizacji lub udostępnić go do jednostki. Nie udostępniono raport, pozostaje osobistych raportów i można tylko wyświetlić. Zapisz raport.


### <a name="save-a-report-to-cloud-provider-storage"></a>Zapisywanie raportu do dostawcy magazynu w chmurze

Aby zapisać raport do dostawcy usług w chmurze, możesz muszą być już skonfigurowane na koncie magazynu. Podczas wyświetlania dowolnego raportu, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Nazywanie raportu, a następnie dodaj własny adres URL lub użyj automatycznie utworzone adresu URL. Wybierz **zapisywanie w magazynie** a następnie wybierz konto magazynu lub dodać nowy. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

### <a name="schedule-a-report"></a>Harmonogram raportu

Możesz uruchomić raporty w zaplanowanych odstępach czasu i wysłała je do adresatów listy lub w chmurze usługi dostawcy konta magazynu. Podczas wyświetlania dowolnego raportu, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Można wysłać raport za pośrednictwem poczty e-mail i zapisać na koncie magazynu. W obszarze **harmonogram**, wybierz interwał (codziennie, co tydzień lub co miesiąc). Co tydzień i co miesiąc wybierz dzień lub daty do dostarczania i wybierz godzinę. Zapisz zaplanowany raport. Jeśli wybierzesz format raportu programu Excel, raport jest wysyłany jako załącznik. Po wybraniu format zawartości wiadomości e-mail, raportu dotyczącego wyników, które są wyświetlane w formacie wykresu są dostarczane jako wykres.

### <a name="export-a-report-as-a-csv-file"></a>Raport można wyeksportować jako plik CSV

Podczas wyświetlania dowolnego raportu, kliknij przycisk **akcje** , a następnie wybierz **wyeksportować wszystkie dane raportów**. Pojawi się okno podręczne i zostanie pobrany plik CSV.

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat raportów, które znajdują się w rozwiązaniu Cloudyn w [raporty użycia Cloudyn](use-reports.md).
- Dowiedz się więcej o tym, jak używać raportów do tworzenia [pulpity nawigacyjne](dashboards.md).
