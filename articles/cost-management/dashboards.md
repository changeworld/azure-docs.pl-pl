---
title: Wyświetlaj kluczowe metryki, za pomocą pulpitów nawigacyjnych Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można wyświetlić kluczowe metryki za pomocą pulpitów nawigacyjnych w rozwiązaniu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7ea7cbf4526528eb481b6125b006d0c29d86deb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577639"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Wyświetl klucz koszt metryki za pomocą pulpitów nawigacyjnych

Pulpity nawigacyjne w rozwiązaniu Cloudyn przedstawia ogólny widok raportów. Pulpity nawigacyjne umożliwiają wyświetlanie kosztów kluczowe metryki w jednym widoku. Zapewniają także, że tendencje biznesowe zaznaczenie, aby pomóc Państwu jak najlepiej istotnych decyzji biznesowych.

Pulpity nawigacyjne są również używane do tworzenia widoków dla osób z wadami różne obowiązki w Twojej organizacji, która może obejmować:

- Kontroler finansowy
- Właściciel aplikacji lub projektu
- Inżynier ds. metodyki DevOps
- Kierownicy

Pulpity nawigacyjne składają się z elementów widget, a każdy element widget jest zasadniczo Miniatura raportu. Kliknij element widget, aby otworzyć raport. Podczas dostosowywania raportów Zapisz moje raporty i są one dodawane do pulpitu nawigacyjnego.

Pulpit nawigacyjny wersji różnią się dla użytkowników, Zarządzanie (MSP), Enterprise i Cloudyn — wersja Premium. Różnice są określane przez jednostki poziomów dostępu. Aby uzyskać więcej informacji na temat poziomów dostępu, zobacz [poziomów dostępu jednostki](tutorial-user-access.md#entity-access-levels).

Pulpit nawigacyjny dostępność zależy od rodzaju konta dostawcy usługi chmury, która jest używana podczas wyświetlania pulpitów nawigacyjnych. Typ danych dostępna i zebrane przez firmę Cloudyn ma wpływ na raporty w pulpitach nawigacyjnych. Na przykład jeśli nie masz konta usługi AWS następnie nie zobaczysz pulpit nawigacyjny śledzenia S3. Podobnie jeśli nie włączysz dostęp usługi Azure Resource Manager do rozwiązania Cloudyn nie można zobaczyć wszelkie informacje specyficzne dla platformy Azure w elementach widget pulpitu nawigacyjnego optymalizatora.

Możesz użyć dowolnej gotowej pulpitów nawigacyjnych lub można utworzyć własny pulpit nawigacyjny zawierający dostosowane raporty. Jeśli znasz raportach usługi Cloudyn, zobacz [raporty użycia Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Tworzenie niestandardowego pulpitu nawigacyjnego

Aby szybko rozpocząć pracę z niestandardowego pulpitu nawigacyjnego można zduplikować istniejącą grupę, aby korzystać z jego właściwości. Następnie można zmodyfikować go do własnych potrzeb. Na pulpicie nawigacyjnym, które chcesz skopiować, kliknij **Zapisz jako**. Tylko można duplikować dostosowanych pulpitów nawigacyjnych — nie można zduplikować pulpity nawigacyjne, które są dołączone do rozwiązania Cloudyn.

Aby utworzyć niestandardowego pulpitu nawigacyjnego:

1. Na stronie głównej kliknij **Dodaj nowe +**. Zostanie wyświetlona strona Mój pulpit nawigacyjny.  
    ![Moje strony pulpitu nawigacyjnego, w którym dodawane są nowe raporty](./media/dashboards/my-dashboard.png)
2. Kliknij przycisk **Dodaj nowy raport**. Zostanie wyświetlone okno dodawania raportu.
3. Wybierz raport, który chcesz dodać do elementu widget pulpitu nawigacyjnego. Widżet zostanie dodana do pulpitu nawigacyjnego.
4. Powtórz te czynności, aż do pulpitu nawigacyjnego.
5. Aby zmienić nazwę pulpitu nawigacyjnego, kliknij nazwę pulpitu nawigacyjnego na stronie głównej pulpitu nawigacyjnego, a następnie wpisz nową nazwę.

## <a name="modify-a-custom-dashboard"></a>Modyfikowanie niestandardowego pulpitu nawigacyjnego

Takie jak utworzenie niestandardowego pulpitu nawigacyjnego, nie można zmodyfikować na pulpitach nawigacyjnych zawartych w rozwiązaniu cloudyn. Aby zmodyfikować raport niestandardowy pulpit nawigacyjny:

1. Na pulpicie nawigacyjnym znaleźć raport, aby zmodyfikować, a następnie kliknij przycisk **Edytuj**. Zostanie wyświetlony raport.
2. Wprowadź wymagane zmiany w raporcie i kliknij przycisk **Zapisz**. Raport jest zaktualizowana i pojawią się wprowadzone zmiany.

## <a name="share-a-custom-dashboard"></a>Udostępnianie niestandardowego pulpitu nawigacyjnego

Niestandardowy pulpit nawigacyjny można udostępnić innym użytkownikom _publicznych_ lub _Moje jednostki_. Po udostępnieniu na wartość publiczne, wszyscy użytkownicy mogą wyświetlać pulpit nawigacyjny. Tylko użytkownicy z dostępem do bieżącej jednostki można wyświetlić pulpit nawigacyjny po udostępnieniu do moich jednostki. Kroki, aby udostępnić niestandardowy pulpit nawigacyjny publicznego i Moje jednostki są podobne.

Aby udostępnić niestandardowy pulpit nawigacyjny do publicznego:

1. Na pulpicie nawigacyjnym kliknij **ustawienia pulpitu nawigacyjnego**. Zostanie wyświetlone okno Ustawienia pulpitu nawigacyjnego.  
    ![ustawienia pulpitu nawigacyjnego dla niestandardowego pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. W oknie Ustawienia pulpitu nawigacyjnego kliknij symbol koła strzałkę, a następnie kliknij przycisk **publicznych**. Zostanie wyświetlone okno dialogowe potwierdzenia publicznych pulpitu nawigacyjnego.
3. Kliknij przycisk **tak**. Pulpit nawigacyjny jest teraz dostępne dla innych użytkowników.

## <a name="delete-a-custom-dashboard-report"></a>Usuwanie raportu niestandardowego pulpitu nawigacyjnego

Na pulpicie nawigacyjnym, możesz usunąć składnik niestandardowy raport. Usuwanie raportu z poziomu pulpitu nawigacyjnego nie powoduje usunięcia raportu na liście raportów. Zamiast tego raportu usunięcie go z poziomu pulpitu nawigacyjnego tylko.

Aby usunąć składnik pulpitu nawigacyjnego w składniku pulpitu nawigacyjnego, kliknij przycisk **Usuń**. Klikając **Usuń** natychmiast usuwa składnik pulpitu nawigacyjnego.

## <a name="share-a-dashboard-enterprise"></a>Udostępnianie pulpitu nawigacyjnego (dla wersji Enterprise)

Możesz udostępnić niestandardowe pulpity nawigacyjne dla wszystkich użytkowników w Twojej organizacji lub użytkownikom bieżącego obiektu. Udostępnianie pulpitu nawigacyjnego umożliwiają osobom szybki przegląd wysokiego poziomu kluczowego wskaźnika wydajności. Po udostępnieniu pulpitu nawigacyjnego, automatycznie replikuje pulpitu nawigacyjnego do wszystkich Cloudyn jednostek/klientów. Zmiany do udostępnionego pulpitu nawigacyjnego są automatycznie aktualizowane.

Aby udostępnić pulpit nawigacyjny wszystkich użytkowników w tym podjednostek:

1. Na stronie głównej pulpitu nawigacyjnego kliknij **Edytuj**.
2. Kliknij przycisk **udziału** , a następnie wybierz **publicznych**.
3. Zostanie wyświetlone okno potwierdzenia globalne publicznych pulpitu nawigacyjnego.
4. Kliknij przycisk **tak** można ustawić pulpit nawigacyjny jako globalne publicznych pulpitu nawigacyjnego.

Aby udostępnić pulpit nawigacyjny wszystkim użytkownikom bieżąca jednostka:

1. Ze strony głównej pulpitu nawigacyjnego, kliknij przycisk **Edytuj**.
2. Kliknij przycisk **udziału** , a następnie wybierz **Moje jednostki**.
3. Kliknij przycisk **tak** można ustawić pulpit nawigacyjny jako publiczny pulpitu nawigacyjnego.

## <a name="duplicate-a-custom-dashboard"></a>Duplikowanie niestandardowego pulpitu nawigacyjnego

Podczas tworzenia nowego pulpitu nawigacyjnego, możesz chcieć użyć podobnych właściwości z istniejącego pulpitu nawigacyjnego. Można zduplikować pulpitu nawigacyjnego, aby utworzyć nową.

Tylko można duplikować niestandardowych pulpitów nawigacyjnych. Nie można duplikować standardowa pulpitów nawigacyjnych.

Duplikowanie pulpitu nawigacyjnego (Klonuj) niestandardowego:

1. Na pulpicie nawigacyjnym, którą chcesz zduplikować kliknij **Zapisz jako**. Nowy pulpit nawigacyjny zostanie otwarty z taką samą nazwę i numer.
2. Zmień nazwę zduplikowanych pulpit nawigacyjny i zmodyfikuj go, jak chcesz.

— Lub —

1. W ustawieniach pulpitu nawigacyjnego kliknij **Zapisz jako** w wierszu, którą chcesz zduplikować pulpitu nawigacyjnego.
2. Zduplikowane pulpit nawigacyjny zostanie otwarty.
3. Zmienianie nazwy pulpitu nawigacyjnego i zmodyfikuj go, jak chcesz.

## <a name="set-a-default-dashboard"></a>Ustaw domyślny pulpit nawigacyjny

Można ustawić dowolny pulpit nawigacyjny jako domyślne. Ustawienie domyślne sprawia, że są wyświetlane jako karty skrajnie po lewej na liście karty Pulpit nawigacyjny. Wyświetla domyślny pulpit nawigacyjny, kiedy otworzyć Cloudyn portal.

- Czy chcesz ustawić jako domyślne, a następnie kliknij kartę pulpitu nawigacyjnego **domyślne** po prawej stronie.

— Lub —

1. Kliknij przycisk **ustawienia pulpitu nawigacyjnego** zapoznać się z listą dostępnych pulpitów nawigacyjnych i wybierz pulpit nawigacyjny, który chcesz ustawić jako domyślny.  
    ![Opcje pulpitu nawigacyjnego dla domyślnego pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. Kliknij przycisk **domyślne** w wierszu pulpitu nawigacyjnego. Zostanie wyświetlone okno potwierdzenia domyślny pulpit nawigacyjny.
3. Kliknij przycisk **Yes** (Tak). Pulpit nawigacyjny jest ustawiona jako domyślna.

## <a name="management-dashboard"></a>Pulpit nawigacyjny zarządzania
Zarządzanie (lub MSP pulpitu nawigacyjnego, aby użytkownicy MSP) pulpit nawigacyjny zawiera najważniejsze typy głównego raportu.  
![Wyświetlanie raportów pulpit nawigacyjny zarządzania](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Podsumowanie jednostki kosztu (tylko wersja Enterprise)
Ten element widget zawiera podsumowanie jednostki kosztu zarządzanych, w tym liczbę jednostek i liczbę kont.
- Kliknij element widget, aby otworzyć raport Enterprise — szczegóły.

### <a name="cost-over-time"></a>Koszt w funkcji czasu
Ten element widget może pomóc wykryć trendy związane z kosztami. Zawiera opis koszt w ciągu ostatniego dnia w oparciu trendów z ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt względem czasu, aby wyświetlić i filtrowania dodatkowych szczegółów.

### <a name="asset-controller"></a>Kontroler aktywów
Ten element widget wyróżnia liczby uruchomionych wystąpień z poprzedniego dnia, powyżej trendu użycia z ostatnich 30 dni.
- Kliknij element widget, aby otworzyć pulpit nawigacyjny kontroler aktywów.

### <a name="unused-ri-detector"></a>Czujnik nieużywanych Wystąpień RI
Ten element widget wyróżnia liczba Amazon EC2 nieużywanych rezerwacji.
- Kliknij element widget można otworzyć raportu obecnie nieużywane rezerwacje, w którym można zobaczyć nieużywane rezerwacje można modyfikować.

### <a name="cost-by-service"></a>Koszt według usługi
Ten element widget wyróżnia zamortyzowany koszt przez usługę w ciągu ostatnich 30 dni. Umieść kursor nad wykresu kołowego, aby zobaczyć koszty dla danej usługi.
- Kliknij element widget, aby otworzyć raport analiza rzeczywistego kosztu.

### <a name="potential-savings"></a>Potencjalne oszczędności
Ten element widget zawiera typ wystąpienia ceny zaleceniami dotyczącymi usług Amazon EC2 i Amazon RDS.
- Kliknij przycisk Otwórz element widget raport analizy oszczędności. Wyświetla listę według typów wystąpień z potencjalne oszczędności kosztów.

### <a name="compute-instances---daily-trend"></a>Wystąpienia obliczeniowe — Trend dzienny
Ten element widget Wyświetla aktywne wystąpienia według typu, w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie, w którym można zobaczyć podział wszystkich wystąpień w ciągu ostatnich 30 dni.

### <a name="storage-by-department"></a>Pamięć masowa według działu
Ten element widget Wyświetla usługi storage używane według działów. Umieść kursor nad wykresu kołowego, aby zobaczyć zużycie miejsca w magazynie według działów.
- Kliknij element widget, aby otworzyć pulpit nawigacyjny śledzenia S3.

## <a name="cost-controller-dashboard"></a>Koszt pulpit nawigacyjny kontrolera
Pulpit nawigacyjny kontroler kosztów zawiera najważniejsze funkcje alokacji kosztów wstępnie ustalony.  
![Wyświetlanie raportów kosztów pulpit nawigacyjny kontrolera](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Koszt w funkcji czasu
Ten element widget pomaga wykryć trendy związane z kosztami. Zawiera opis koszt w ciągu ostatniego dnia w oparciu trendów z ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt względem czasu, aby wyświetlić i filtrowania dodatkowych szczegółów.

### <a name="monthly-cost-trends"></a>Miesięczny koszt trendów
Ten element widget wyróżnia przewidywanych wydatków zamortyzowany i faktycznego wydatków od początku miesiąca.
- Kliknij element widget, aby otworzyć raport przewidywany koszt dla bieżącego miesiąca, który zawiera podsumowanie kosztów od początku miesiąca.

Ten raport przedstawia kosztów od początku miesiąca, koszt poprzedniego miesiąca oraz bieżącego miesiąca, przewidywany koszt. Bieżący miesiąc przewidywany koszt jest obliczany przez dodanie aktualne miesięcznych kosztów i projekcji. Rzutowanie jest na podstawie jej kosztu monitorowane w ciągu ostatnich 30 dni.

### <a name="12-month-planner"></a>Planista 12-miesięcznego
Ten element widget wyróżnia koszty planowane w ciągu najbliższych 12 miesięcy i potencjalne oszczędności.
- Kliknij element widget, aby otworzyć raport przewidywany koszt roczny.

### <a name="cost-by-service"></a>Koszt według usługi
Ten element widget wyróżnia zamortyzowany koszt przez usługę w ciągu ostatnich 30 dni.
- Umieść kursor nad wykresu kołowego, aby zobaczyć koszty dla danej usługi.
- Kliknij element widget, aby otworzyć raport analiza rzeczywistego kosztu.

### <a name="cost-by-account"></a>Koszt według konta
Ten element widget wyróżnia zamortyzowany koszt konta w ciągu ostatnich 30 dni.
- Umieść kursor nad wykresu kołowego, aby zobaczyć koszty na jednym koncie.
- Kliknij element widget, aby otworzyć raport analiza rzeczywistego kosztu.

### <a name="cost-trend-by-day"></a>Trend kosztu wg dnia
Ten element widget najważniejsze funkcje wydatki w ciągu ostatnich 30 dni.
- Umieść kursor nad wykres słupkowy, aby zobaczyć koszty na dzień.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt względem czasu.

### <a name="cost-trend-by-month---last-6-months"></a>Trend kosztu za miesiąc — ostatnie 6 miesięcy

Ten element widget najważniejsze funkcje wydania w ciągu ostatnich sześciu miesięcy.
- Umieść kursor nad wykres słupkowy, aby zobaczyć koszty miesięcznie.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt względem czasu.

## <a name="asset-controller-dashboard"></a>Pulpit nawigacyjny kontrolera zasobów

Ten pulpit nawigacyjny Wyświetla liczbę uruchomionych wystąpień, dostępnych i używanych dysków, dystrybucję typów wystąpień i informacje dotyczące magazynu.  
![Wyświetlanie raportów pulpit nawigacyjny kontrolera zasobów](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Wystąpienia obliczeniowe
Ten element widget Wyświetla liczbę uruchomionych wystąpień, w oparciu o trendu użycia z ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie.

### <a name="disks"></a>Dyski
Ten element widget wyróżnia łączną liczbą i liczby dysków, które są w użyciu i dostępne.
- Kliknij element widget, aby otworzyć raport aktywnych dysków.

### <a name="instance-type-distribution"></a>Rozkład typu wystąpienia
Ten element widget prezentuje typy wystąpienia wykresu kołowego.
- Kliknij element widget, aby otworzyć raport wystąpienia dystrybucji, który zawiera podział aktywnego wystąpienia według wybranej agregacji.

### <a name="compute-instances---daily-trend"></a>Wystąpienia obliczeniowe — Trend dzienny
Ten element widget wyróżnia wystąpień obliczeniowych (narzędzia spot, zastrzeżone i na żądanie) dziennie w ciągu ostatnich 30 dni.
- Umieść kursor nad wykresie, aby wyświetlić liczbę wystąpień obliczeniowych, na typ dziennie.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie.

### <a name="all-buckets-s3"></a>Wszystkie przedziały (S3)
Ten element widget wyróżnia całkowita ilość miejsca S3 i liczba przechowywanych obiektów.
- Kliknij element widget, aby otworzyć pulpit nawigacyjny śledzenia S3. Pulpit nawigacyjny pomaga znaleźć, analizowania i wyświetlić swoje bieżące użycie magazynu oraz trendów.

### <a name="sql-db-instances-rds"></a>Wystąpienia bazy danych SQL (RDS)
Ten element widget wyróżnia liczby uruchomionych wystąpień Amazon usług pulpitu zdalnego w oparciu trendów z ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport usług pulpitu zdalnego wystąpienia względem czasu.

## <a name="optimizer-dashboard"></a>Optymalizator pulpitu nawigacyjnego
Ten pulpit nawigacyjny Wyświetla redukcję zatrudnienia zalecenia, nieużywane zasoby i potencjalne oszczędności.  
![Pulpit nawigacyjny Optymalizator przedstawiający różne raporty](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Kalkulator wystąpień Zarezerwowanych
Ten element widget Wyświetla liczbę zalecenia zakupu wystąpień Zarezerwowanych i wyróżnienie potencjalne oszczędności roczne.
- Kliknij element widget, aby otworzyć Kalkulator wystąpienia zarezerwowane, gdzie można określić, kiedy należy używać na żądanie a zastrzeżone planów cenowych.

### <a name="sizing"></a>Zmiany rozmiaru
Ten element widget wyróżnia zalecanych rozmiarów i potencjalne oszczędności, jeśli zaimplementowana.
- Kliknij element widget, aby otworzyć raport usługi EC2 Cost Effective Sizing zalecenia.

### <a name="unused-ri-detector"></a>Czujnik nieużywanych Wystąpień RI
Ten element widget wyróżnia liczba Amazon EC2 nieużywanych rezerwacji.
- Kliknij element widget, aby otworzyć raport obecnie nieużywane rezerwacje, gdzie można przejrzeć nieużywanych rezerwacji, które można modyfikować.

###  <a name="available-disks"></a>Dostępne dyski
Ten element widget prezentuje liczbę niedołączone dyski w danym wdrożeniu.
- Kliknij element widget, aby otworzyć raport niedołączone dyski.

### <a name="rds-ri-calculator"></a>Kalkulator wystąpień Zarezerwowanych usług pulpitu zdalnego
Ten element widget prezentuje liczbę rezerwacji zaleceń dotyczących wystąpień Amazon usług pulpitu zdalnego i potencjalne oszczędności.
- Kliknij element widget, aby otworzyć raport zalecenia zakupu wystąpień Zarezerwowanych usług pulpitu zdalnego, którym można zobaczyć zalecenia Cloudyn, aby korzystać z wystąpień zarezerwowanych zamiast wystąpienia na żądanie.

### <a name="rds-sizing"></a>Ustalanie rozmiaru usług pulpitu zdalnego
Ten element widget pokazuje liczbę zalecenia wymiarowania efektywnego i potencjalne oszczędności.
- Kliknij element widget, aby otworzyć raport zalecenia wymiarowania efektywnego usług pulpitu zdalnego, którym są wyświetlane szczegółowe Amazon rozmiaru zalecenia dotyczące usług pulpitu zdalnego.

Zalecenia dotyczące optymalizacji są oparte na dane dotyczące użycia i wydajności, monitorowania w ciągu ostatniego miesiąca.

## <a name="s3-tracker-dashboard"></a>Pulpit nawigacyjny śledzenia S3
Pulpit nawigacyjny śledzenia S3 pomaga znaleźć, analizowania i wyświetlić swoje bieżące użycie magazynu oraz trendów.  
![Pulpit nawigacyjny śledzenia S3 przedstawiający różne raporty](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Wszystkie pakiety
Ten element widget wyróżnia łączny rozmiar wszystkich swoje zasobników w GB i całkowita liczba obiektów w swojej zasobników.
- Kliknij przycisk widżet, aby otworzyć raport rozmiar dystrybucji S3. Raport pomaga analizować swoje S3 rozmiar zasobnika, folder najwyższego poziomu, klasę magazynu i stanu przechowywania wersji.

### <a name="bucket-properties"></a>Przedział właściwości
Ten element widget wyróżnia całkowita liczba zasobników magazynu.
- Kliknij element widget, aby wyświetlić raport o właściwościach przedział S3.

### <a name="scan-status"></a>Stan skanowania
Ten element widget wyróżnia podczas ostatniego skanowania pod kątem S3 zostało to zrobione, i gdy rozpocznie się kolejny.
- Kliknij element widget, aby otworzyć raport o stanie skanowania S3.

### <a name="storage-by-bucket"></a>Pamięć masowa według przedziału
Ten element widget prezentuje procent używanej przez klasę magazynu każdego przedziału.
- Kliknij przycisk widżet, aby otworzyć raport rozmiar dystrybucji S3. Raport pomaga analizować swoje S3 rozmiar zasobnika, folder najwyższego poziomu, klasę magazynu i stanu przechowywania wersji.

### <a name="number-of-objects-by-bucket"></a>Liczba obiektów według przedziału
Ten element widget prezentuje liczbę obiektów dla przedziału w rzeczywista liczba i procent. Umieść kursor nad przedziału, aby zobaczyć, całkowita liczba obiektów.
- Kliknij element widget, aby otworzyć raport rozmiar dystrybucji S3 (skanowanie na podstawie).

## <a name="cloud-comparison-dashboard"></a>Pulpit nawigacyjny rozwiązania cloud porównania
Pulpit nawigacyjny porównania chmurze ułatwia porównanie kosztów od różnych dostawców w chmurze na podstawie ceny, typ Procesora i pamięci RAM.  
![Wyświetlanie raportów pulpit nawigacyjny rozwiązania cloud porównania](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>Usługi EC2 Koszt na platformie Azure dzięki typu wystąpienia
Ten element widget wyróżnia ostatnich 30 dni użytkowania kursów na żądanie. Porównuje koszty dzięki bieżącym vs koszt Amazon EC2 potencjalnych kosztów na platformie Azure.
- Umieść kursor nad paski do porównania kosztów na typ wystąpienia.
- Kliknij element widget otworzyć przenoszenie wdrożenia Your — raport analizy kosztów.

### <a name="ec2-cost-in-azure"></a>Usługi EC2 Koszt na platformie Azure
Ten element widget Pokazuje bieżące koszty Amazon EC2 i porównuje je na platformie Azure. Porównanie zależy od ostatnich 30 dni użytkowania kursów na żądanie.
- Kliknij element widget otworzyć przenoszenie wdrożenia Your - raport analizy kosztów.

### <a name="ec2azure-instance-type-mapping"></a>Mapowanie typu wystąpienia usługi EC2/platformy Azure
Ten element widget wyróżnia najlepsze mapowania jednostek obliczeniowych elastycznej między Amazon EC2 i platformą Azure.
- Kliknij element widget, aby otworzyć raport mapowanie typu wystąpienia.

## <a name="next-steps"></a>Kolejne kroki
- Odczyt [raporty użycia Cloudyn](use-reports.md) artykuł, aby dowiedzieć się więcej na temat raportów.
