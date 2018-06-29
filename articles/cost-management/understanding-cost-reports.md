---
title: Opis raportów zarządzania koszt zarządzania koszt Azure | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga w zrozumieniu Cloudyn kosztów zarządzania raportów podstawowej struktury i funkcji.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 75709e099c6126997d91bf4b679de473fc75a485
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064003"
---
# <a name="understanding-cost-management-reports"></a>Raporty zarządzania koszt opis

Ten artykuł pomaga w zrozumieniu Cloudyn kosztów zarządzania raportów podstawowej struktury i funkcji. Większość raportów Cloudyn są intuicyjne i zapewnia jednolite wyglądu i działania. Po przeczytaniu tego artykułu są gotowe do użycia. wszystkie raporty zarządzania kosztów. Wiele funkcji dostępnych w całej różne raporty, umożliwiając Przejdź raporty z łatwością. Raporty są można dostosowywać i można wybrać kilka opcji do obliczania i wyświetlania wyników.

## <a name="report-fields-and-options"></a>Opcje i pola raportu

Poniżej przedstawiono wygląd, na przykład raport koszt w czasie. Większość Cloudyn raporty mają podobny układ.

![przykładowy raport](./media/understanding-cost-reports/sample-report.png)

Poszczególnych numerowane obszar obrazu poprzedniego jest szczegółowo opisane w poniższych informacji:

1. **Zakres dat**

    Użyj listy zakres dat, aby określić przedział czasu raportu za pomocą wstępnie ustawionych lub niestandardowy.
2. **Zapisany filtr**

    Użyj listy zapisany filtr, aby zapisać bieżące grupy i filtry, które są stosowane do raportu. Zapisane filtry są dostępne przez raportów kosztów i wydajności, w tym:

      - Analiza kosztów
      - Alokacja
      - Zarządzanie zasobami
      - Optymalizacja

  Wpisz nazwę filtru, a następnie kliknij pozycję **zapisać**.

3. **Tagi**

    Użyj obszaru tagi do grupy według kategorii tagu. Tagi wyświetlane w menu są Azure dział lub tagi Centrum kosztów lub tagi koszt Cloudyn przez jednostki i subskrypcji. Wybierz tagi, które mają być filtrowane wyniki. Możesz także wpisać nazwę znacznika (— słowo kluczowe) mają być filtrowane wyniki.

    ![Wybierz opcje](./media/understanding-cost-reports/select-options.png)

    Kliknij przycisk **Dodaj** można dodać nowego filtru.

    ![Dodaj filtr](./media/understanding-cost-reports/add-filter.png)

    Tag, grupowanie i filtrowanie nie odnosi się do zasobów platformy Azure lub tagi grupy zasobów.

    Alokacja kosztu tag grupowanie i filtrowanie są dostępne w **grup** opcji menu.

4. **Grupami w raportach**

    Użyj grup w analizy kosztów raportów do wyświetlenia standard wyszczególnione kategorii z rozliczeniami dane w raporcie.  Jednak grup w Pokaż raporty alokacji kosztów wyświetlanie znaczników kategorii. Na podstawie tag kategorie są definiowane w modelu alokacji kosztów i standardowe kategorii dla pozycji z danych dotyczących rozliczeń.

    ![tagi grup](./media/understanding-cost-reports/groups-tags01.png)

    ![tagi grup](./media/understanding-cost-reports/groups-tags02.png)

    W raportach alokacji kosztów grup w kategorie na podstawie tag grupy mogą być następujące:
      - Tagi
      - tagi grupy zasobów
      - Cloudyn koszt tagów jednostki
      - Kategorie tagu subskrypcji dla celów alokacji kosztów

  Przykładami mogą być następujące:
     - Centrum kosztu
     - Dział
     - Aplikacja
     - Środowisko
     - Kod kosztów

    Poniżej przedstawiono listę wbudowanych grup dostępne w raportach:

    - **Typ kosztu**
      - Wybierz typ kosztu lub wiele typów kosztów, albo wybierz wszystko. Typy kosztu:
        - Jednorazową opłatę
        - Pomoc techniczna
        - Koszt użycia
    - **Klienta**
        - Wybierz odbiorcę wielu klientów, lub wszystkich klientów.
    - **Nazwa konta**
        - Nazwa konta lub subskrypcji. Na platformie Azure jest to nazwa subskrypcji platformy Azure.
    - **Brak konta**
        - Wybierz konto, wiele kont lub wszystkich kont. Na platformie Azure jest identyfikatorem GUID subskrypcji platformy Azure.
    - **Konto nadrzędne**
        - Wybierz konto nadrzędne, wiele kont lub wybierz.
    - **Usługa**
        - Wybierz usługi, wielu usług lub wszystkich usług.
    - **Dostawca**
        - Dostawcy chmury, gdzie są skojarzone zasoby i koszty.
    - **Region**
        - Region, w którym znajduje się zasób.
    - **Dostępność strefy**
        - Usług AWS samodzielnie lokalizacji w obrębie regionu.
    - **Typ zasobu**
        - Typ zasobu w użyciu.
    - **Sub-Type**
        - Wybierz podtyp.
    - **Operacja**
        - Wybierz operację, lub **Pokaż wszystkie**.
    - **Model cen**
        - Wszystkie wyprzedzeniem
        - Nr wyprzedzeniem
        - Partial wyprzedzeniem
        - Na żądanie
        - Rezerwacja
        - Miejsce
    - **Typ opłaty**
        - Wybierz typ opłaty ujemna lub dodatnia lub oba.
    - **Tenancy**
        - Określa, czy maszyna jest uruchomiona jako dedykowane maszyny.
    -   **Typ użycia**
          - Typ użycia może być jednorazowe opłaty lub cykliczne opłat.

5. **Filtry**

    Użyj filtrów pojedynczego lub wielokrotnego wyboru, aby ustawić zakresy wybranych wartości. Aby ustawić filtr, kliknij przycisk **Dodaj** , a następnie wybierz filtr kategorii i wartości.

6. **Model kosztu**

    Koszt modelu umożliwia wybierz wcześniej utworzony za pomocą koszt alokacji 360 model kosztów. Może mieć wielu Cloudyn koszt modeli, w zależności od wymagań alokacji kosztów. Niektóre z zespołów organizacji może kosztów alokacji wymagania, które różnią się od innych użytkowników. Każdy zespół może mieć własne dedykowane koszt modelu.

    Informacji o tworzeniu definicję modelu koszt alokacji, zobacz [użyć znaczniki niestandardowe, aby przydzielić koszty](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortyzacja**

    Użyj amortyzacji w raportach alokacji kosztów, aby zobaczyć użycie z systemem innym niż na podstawie opłaty za usługi lub jednorazowe dostawcami koszty i rozpowszechniania kosztów wraz z upływem czasu równomiernie podczas ich cykl życia. Przykłady jednorazowe opłaty mogą być następujące:
    - Opłaty roczne
    - Opłaty roczne składniki zabezpieczeń
    - Zarezerwowane wystąpień zakupu opłat
    - Niektóre elementy portalu Azure Marketplace.

  W obszarze amortyzacji, wybierz **amortyzowanego koszt** lub **rzeczywisty koszt**.

8. **Rozdzielczość**

    Umożliwia wybór rozwiązania czasu, w ramach zakresu dat zaznaczonego rozpoznawania. Rozdzielczość czasu określa sposób jednostki są wyświetlane w raporcie i może być:
    - Codziennie
    - Cotygodniowo
    - Miesięczne
    - Kwartalnie
    - Roczne

9. **Zasady alokacji**

    Użyj reguł alokacji na stosowanie lub wyłączyć obliczanie kosztu alokacji kosztów. Można włączyć lub wyłączyć Obliczanie alokacji kosztów dla danych dotyczących rozliczeń. Ponowne obliczanie ma zastosowanie do wybranych kategorii w raporcie. Umożliwia ona ocenić wpływ ponowne obliczenie kosztów alokacji względem danych pierwotnych rozliczeń.

10. **Bez kategorii**

    Użyj bez kategorii, aby dołączyć lub wykluczyć bez kategorii kosztów w raporcie.

11. **Pokaż/Ukryj pola**

    Pokaż/Ukryj opcji nie ma żadnego efektu w raportach.

12.   **Format wyświetlania**

    Użyj formatu wyświetlania, aby wybrać różne widoki wykresu lub tabeli.

    ![Format wyświetlania](./media/understanding-cost-reports/display-formats.png)

13. **Kolor wielu**

    Użyj wielu kolorów, aby ustawić kolor wykresy w raporcie.

14. **Akcje**

    Użyj akcje, aby zapisać, wyeksportować lub ustalania harmonogramu raportu.

15. **Zasady**

    Mimo że nie przedstawianych, niektóre raporty zawierają zasady obliczania szacowany koszt. W tych raportach **skonsolidowanych** zasad zawiera zalecenia dotyczące wszystkich kont i subskrypcje w ramach bieżącego obiektu, takie jak Microsoft rejestracji lub usług AWS płatnika. **Autonomiczny** zasad zawiera zalecenia dotyczące jednego konto lub subskrypcja tak, jakby istnieje żadnych innych subskrypcji. Zasady, które należy wybrać zmienia się na strategii optymalizacji używany przez organizację. Projekcje kosztu są oparte na ostatnich 30 dni od użycia.

## <a name="save-and-schedule-reports"></a>Zapisz i planowania raportów

Po utworzeniu raportu można zapisać go do użytku w przyszłości. Zapisane raporty są dostępne w **Moje narzędzia** > **Moje raporty**. Jeśli wprowadzono zmiany do istniejącego raportu i zapisz go, raport jest zapisywany jako nową wersję. Alternatywnie można zapisać go jako nowy raport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Zapisywanie raportu do portalu Cloudyn

Podczas wyświetlania wszystkich raportów, kliknij przycisk **akcje** , a następnie wybierz **Zapisz moje raporty**. Nazwę raportu, a następnie dodaj swój własny adres URL lub użyj automatycznie utworzone adresu URL. Można opcjonalnie **udostępnianie** raport publicznie z innymi osobami w organizacji lub można ją udostępnić, do jednostki. Jeśli nie mają raportu, pozostaje osobiste raportu i można tylko wyświetlić. Zapisz raport.


### <a name="save-a-report-to-cloud-provider-storage"></a>Zapisywanie raportu do dostawcy magazynu w chmurze

Aby zapisać raport w dostawcy usług w chmurze, musi już skonfigurowano konto magazynu. Podczas wyświetlania wszystkich raportów, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Nazwę raportu, a następnie dodaj swój własny adres URL lub użyj automatycznie utworzone adresu URL. Wybierz **Zapisz do magazynu** a następnie wybierz konto magazynu lub dodania nowego. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

### <a name="schedule-a-report"></a>Harmonogram raportu

Można uruchamiać raporty w zaplanowanych odstępach czasu i należy je wysyłane do adresatów listy lub w chmurze dostawcy magazynu konta usługi. Podczas wyświetlania wszystkich raportów, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Można wysłać raport za pośrednictwem poczty e-mail i zapisać na konto magazynu. W obszarze **harmonogram**, wybierz interwał (codziennie, co tydzień lub co miesiąc). Co tydzień i co miesiąc wybierz dzień lub daty do dostarczania i wybierz godzinę. Zapisz zaplanowany raport. Po wybraniu formatu raport programu Excel, raport jest wysyłany jako załącznik. Po wybraniu formatu zawartości wiadomości e-mail, raportu dotyczącego wyników, które są wyświetlane w formacie wykresu są dostarczane jako wykresu.

### <a name="export-a-report-as-a-csv-file"></a>Raport można wyeksportować do pliku CSV

Podczas wyświetlania wszystkich raportów, kliknij przycisk **akcje** , a następnie wybierz **wyeksportować wszystkie dane raportów**. Zostanie wyświetlone okno podręczne i zostanie pobrany plik CSV.

## <a name="next-steps"></a>Kolejne kroki

- Więcej informacji na temat raportów, które znajdują się w Cloudyn w [raporty użycia kosztów zarządzania](use-reports.md).
- Dowiedz się więcej o tym, jak używać raportów do tworzenia [pulpity nawigacyjne](dashboards.md).
