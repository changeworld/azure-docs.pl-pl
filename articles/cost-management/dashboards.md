---
title: Wyświetlić kluczowe metryki w pulpitach nawigacyjnych Azure kosztów zarządzania | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wyświetlania kluczowych metryk za pomocą pulpitów nawigacyjnych w Azure kosztów zarządzania.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b1dc2e2eca900ca0ae72329c3c373b2d24f1b2e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304098"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Klucz widoku koszt metryki za pomocą pulpitów nawigacyjnych

Pulpity nawigacyjne w Cloudyn zawierają ogólny widok raportów. Pulpity nawigacyjne umożliwiają wyświetlanie koszt kluczowe metryki w ramach jednego widoku. Zapewniają także, że trend firm wyróżnia podejmowanie decyzji biznesowych ważne.

Pulpity nawigacyjne są również używane do tworzenia widoków dla osób z innej obowiązki w organizacji, która może obejmować:

- Kontroler finansowy
- Właściciel aplikacji lub projektu
- DevOps odtwarzania
- Członkowie kadry kierowniczej

Pulpity nawigacyjne składają się z elementów widget i każdego widgetu jest zasadniczo miniatur raportu. Kliknij element widget, aby otworzyć raport. Podczas dostosowywania raporty, Zapisz moje raporty i dodawanych do pulpitu nawigacyjnego.

Pulpit nawigacyjny wersji różnią się dla użytkowników, zarządzania (MSP), Enterprise i Premium Cloudyn. Różnice są określane przez jednostki poziomy dostępu. Aby uzyskać więcej informacji na temat poziomów dostępu, zobacz [poziomy dostępu jednostki](tutorial-user-access.md#entity-access-levels).

Dostępność pulpitu nawigacyjnego, zależy od typu konta dostawcy usługi chmury, który jest używany podczas wyświetlania pulpitów nawigacyjnych. Typ danych, dostępna i zebranych przez Cloudyn ma wpływ na raporty w pulpitach nawigacyjnych. Na przykład jeśli nie masz konta usług AWS nie można zobaczyć śledzenie S3 pulpitu nawigacyjnego. Podobnie jeśli nie zostało włączone usługi Azure Resource Manager dostęp do Cloudyn nie można zobaczyć informacje specyficzne dla platformy Azure w Optymalizator elementów widget pulpitu nawigacyjnego.

Można użyć dowolnego z gotowego pulpity nawigacyjne, lub możesz utworzyć własne pulpit nawigacyjny z niestandardowych raportów. Jeśli znasz Cloudyn raportów, zobacz [raporty użycia kosztów zarządzania](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Utwórz niestandardowy pulpit nawigacyjny

Aby szybko rozpocząć pracę z niestandardowym pulpicie nawigacyjnym, można duplikować istniejącą za pomocą jego właściwości. Następnie możesz ją zmodyfikować, w zależności od potrzeb. Na pulpicie nawigacyjnym, którego chcesz skopiować, kliknij przycisk **Zapisz jako**. Można tylko zduplikować dostosowanych pulpitów nawigacyjnych — nie można zduplikować pulpity nawigacyjne, które są dołączone do Cloudyn.

Aby utworzyć niestandardowy pulpit nawigacyjny:

1. Na stronie głównej kliknij **Dodaj nowy +**. Zostanie wyświetlona strona Mój pulpit nawigacyjny.  
    ![Mój pulpit nawigacyjny](./media/dashboards/my-dashboard.png)
2. Kliknij przycisk **Dodaj nowy raport**. Zostanie wyświetlone okno Dodawanie raportu.
3. Wybierz raport, który chcesz dodać do elementu widget pulpitu nawigacyjnego. Widżet jest dodawany do pulpitu nawigacyjnego.
4. Powtórz poprzednie kroki, aż do pulpitu nawigacyjnego.
5. Aby zmienić nazwę pulpitu nawigacyjnego, kliknij nazwę pulpitu nawigacyjnego strony głównej pulpitu nawigacyjnego i wpisz nową nazwę.

## <a name="modify-a-custom-dashboard"></a>Modyfikowanie niestandardowy pulpit nawigacyjny

Podobnie jak utworzenie niestandardowego pulpitu nawigacyjnego, nie można zmodyfikować dołączonego Cloudyn pulpitów nawigacyjnych. Aby zmodyfikować raport niestandardowy pulpit nawigacyjny:

1. Na pulpicie nawigacyjnym znaleźć raportu chcesz zmodyfikować, a następnie kliknij przycisk **Edytuj**. Zostanie wyświetlony raport.
2. Wprowadź wymagane zmiany do raportu i kliknij przycisk **zapisać**. Raport zostanie zaktualizowana i zawiera zmiany.

## <a name="share-a-custom-dashboard"></a>Udostępnianie niestandardowy pulpit nawigacyjny

Niestandardowy pulpit nawigacyjny można udostępniać innym użytkownikom _publicznego_ lub _Moje jednostki_. Po udostępnieniu na wartość publiczne, wszyscy użytkownicy mogą wyświetlać pulpit nawigacyjny. Tylko użytkownicy z dostępem do bieżącego obiektu można wyświetlić pulpit nawigacyjny po udostępnieniu Moje jednostki. Te kroki, aby udostępnić niestandardowy pulpit nawigacyjny publicznego i Moje jednostki są podobne.

Aby udostępnić niestandardowy pulpit nawigacyjny do publicznego:

1. Na pulpicie nawigacyjnym kliknij **ustawienia pulpitu nawigacyjnego**. Zostanie wyświetlone okno Ustawienia pulpitu nawigacyjnego.  
    ![Opcje pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. W oknie dialogowym Ustawienia pulpitu nawigacyjnego kliknij symbol strzałki, a następnie kliknij przycisk **publicznego**. Zostanie wyświetlone okno dialogowe potwierdzenia publicznego pulpitu nawigacyjnego.
3. Kliknij przycisk **tak**. Pulpit nawigacyjny jest teraz dostępne dla innych użytkowników.

## <a name="delete-a-custom-dashboard-report"></a>Usunąć raport niestandardowy pulpit nawigacyjny

Możesz usunąć składnika raport niestandardowy z poziomu pulpitu nawigacyjnego. Usunięcie raportu z poziomu pulpitu nawigacyjnego nie powoduje usunięcia raportu na liście raportów. Zamiast tego raportu usunięcie go z pulpitu nawigacyjnego tylko.

Aby usunąć składnik pulpitu nawigacyjnego dla składnika pulpitu nawigacyjnego, kliknij przycisk **usunąć**. Kliknięcie przycisku **usunąć** natychmiast usuwa składnik pulpitu nawigacyjnego.

## <a name="share-a-dashboard-enterprise"></a>Udostępnianie pulpitu nawigacyjnego (Enterprise)

Możesz udostępnić niestandardowe pulpity nawigacyjne dla wszystkich użytkowników w organizacji lub z użytkownikami bieżącego obiektu. Udostępnianie pulpitu nawigacyjnego można daje szybki Widok ogólny kluczowego wskaźnika wydajności. Udostępnianie pulpitu nawigacyjnego, automatycznie replikuje pulpitu nawigacyjnego dla wszystkich Cloudyn jednostek/klientów. Zmiany do udostępnionego pulpitu nawigacyjnego są automatycznie aktualizowane.

Aby udostępnić pulpit nawigacyjny wszystkich użytkowników, w tym podjednostek:

1. Na stronie głównej pulpitu nawigacyjnego kliknij **Edytuj**.
2. Kliknij przycisk **udziału** , a następnie wybierz **publicznego**.
3. Zostanie wyświetlone okno potwierdzenia globalne publicznego pulpitu nawigacyjnego.
4. Kliknij przycisk **tak** można ustawić jako globalny publicznego pulpit nawigacyjny pulpitu nawigacyjnego.

Aby udostępnić pulpit nawigacyjny z wszystkich użytkowników bieżącego obiektu:

1. Na stronie głównej pulpitu nawigacyjnego kliknij **Edytuj**.
2. Kliknij przycisk **udziału** , a następnie wybierz **Moje jednostki**.
3. Kliknij przycisk **tak** można ustawić jako publiczny pulpit nawigacyjny pulpitu nawigacyjnego.

## <a name="duplicate-a-custom-dashboard"></a>Zduplikowana niestandardowy pulpit nawigacyjny

Podczas tworzenia nowego pulpitu nawigacyjnego, można użyć właściwości podobne z istniejącego pulpitu nawigacyjnego. Można duplikować pulpit nawigacyjny, aby utworzyć nowy.

Można tylko zduplikowane niestandardowych pulpitów nawigacyjnych. Nie można duplikować standardowe pulpitów nawigacyjnych.

Aby zduplikować (klonowania) niestandardowego pulpitu nawigacyjnego.

1. Na pulpicie nawigacyjnym, które mają zostać zduplikowane, kliknij przycisk **Zapisz jako**. Otwiera nowy pulpit nawigacyjny z taką samą nazwę i numer.
2. Zmień nazwę zduplikowanych pulpitu nawigacyjnego i dowolnie modyfikować.

— Lub —

1. W sekcji Ustawienia pulpitu nawigacyjnego kliknij **Zapisz jako** w wierszu pulpit nawigacyjny, który ma być zduplikowany.
2. Otwiera zduplikowanych pulpitu nawigacyjnego.
3. Zmień nazwę pulpitu nawigacyjnego i dowolnie modyfikować.

## <a name="set-a-default-dashboard"></a>Ustaw domyślnego pulpitu nawigacyjnego

Dowolnego pulpitu nawigacyjnego można ustawić jako domyślny. Ustawienie domyślne, ułatwia są wyświetlane jako karty lewej na liście Karta pulpitu nawigacyjnego. Wyświetla domyślnego pulpitu nawigacyjnego, kiedy Otwórz Cloudyn portal.

- Chcesz ustawić jako domyślny, a następnie kliknij kartę Pulpit nawigacyjny **domyślne** po prawej stronie.

— Lub —

1. Kliknij przycisk **ustawienia pulpitu nawigacyjnego** Aby wyświetlić listę dostępnych pulpitów nawigacyjnych i wybierz pulpit nawigacyjny, który chcesz ustawić jako domyślny.  
    ![Opcje pulpitu nawigacyjnego](./media/dashboards/dashboard-options.png)
2. Kliknij przycisk **domyślne** w wierszu pulpitu nawigacyjnego. Zostanie wyświetlone okno potwierdzenia domyślne pulpitu nawigacyjnego.
3. Kliknij przycisk **Yes** (Tak). Pulpit nawigacyjny jest ustawioną wartość domyślną.

## <a name="management-dashboard"></a>Pulpit nawigacyjny zarządzania
Zarządzanie (lub pulpit nawigacyjny MSP dla użytkowników MSP) pulpit nawigacyjny zawiera najważniejsze funkcje typów głównego raportu.  
![Pulpit nawigacyjny zarządzania](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Podsumowanie kosztów jednostki (tylko wersja Enterprise)
Tego elementu widget zawiera podsumowanie jednostki zarządzanej kosztów, w tym liczbę jednostek i liczby kont.
- Kliknij przycisk elementu widget, aby otworzyć raport szczegóły przedsiębiorstwa.

### <a name="cost-over-time"></a>Koszt wraz z upływem czasu
Tego elementu widget mogą pomóc w miejscu trendów kosztów. Zawiera opis koszt w ostatnim dniu oparte na tendencji w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt w miarę upływu czasu, aby wyświetlić i filtrować dodatkowe szczegóły.

### <a name="asset-controller"></a>Kontroler zasobów
Tego elementu widget prezentuje liczba uruchomionych wystąpień z poprzedniego dnia powyżej trend użycie w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć pulpit nawigacyjny kontrolera zasobów.

### <a name="unused-ri-detector"></a>Wykrywacz RI nieużywane
Tego elementu widget prezentuje liczbę Amazon EC2 zastrzeżenia nieużywane.
- Kliknij przycisk elementu widget, aby otworzyć raport aktualnie nieużywane rezerwacji, możesz przeglądać nieużywane zastrzeżenia można modyfikować.

### <a name="cost-by-service"></a>Koszt przez usługę
Tego elementu widget prezentuje amortyzowanego koszty przez usługę w ciągu ostatnich 30 dni. Umieść kursor nad wykresu kołowego, aby zobaczyć koszty dla danej usługi.
- Kliknij przycisk elementu widget, aby otworzyć raport Rzeczywiste analizy kosztów.

### <a name="potential-savings"></a>Potencjalne oszczędności
Tego elementu widget zawiera typ wystąpienia cennik zalecenia dotyczące Amazon EC2 i Amazon usług pulpitu zdalnego.
- Kliknij przycisk Otwórz element widget raportu analizy oszczędności. Wyświetla listę według typów wystąpienia z potencjalne oszczędności kosztów.

### <a name="compute-instances---daily-trend"></a>Obliczenia bazy danych wystąpienia - dzienne trendy
Tego elementu widget Wyświetla aktywne wystąpienia według typu, w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie, w którym można zobaczyć podział wszystkich wystąpień uruchomiony w ciągu ostatnich 30 dni.

### <a name="storage-by-department"></a>Magazyn według działów
Tego elementu widget Wyświetla usługi Magazyn używany przez działów. Umieść kursor nad wykresu kołowego, aby zobaczyć wykorzystania magazynu według działów.
- Kliknij przycisk elementu widget, aby otworzyć pulpit nawigacyjny S3 śledzenia.

## <a name="cost-controller-dashboard"></a>Koszt kontrolera pulpitu nawigacyjnego
Pulpit nawigacyjny kontrolera koszt zawiera najważniejsze funkcje alokacji wstępnie ustawionymi kosztów.  
![Koszt kontrolera pulpitu nawigacyjnego](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Koszt wraz z upływem czasu
Tego elementu widget pomaga Obserwuj trendy kosztów. Zawiera opis koszt w ostatnim dniu oparte na tendencji w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt w miarę upływu czasu, aby wyświetlić i filtrować dodatkowe szczegóły.

### <a name="monthly-cost-trends"></a>Miesięczne trendów kosztów
Tego elementu widget wyróżnia planowanego amortyzowanego wydatków, oraz rzeczywistymi wydatków od początku miesiąca.
- Kliknij element widget, aby otworzyć raport bieżącego miesiąca przewidywane kosztów, co zapewnia podsumowania koszt miesięcy do daty.

Ten raport przedstawia kosztów od początku miesiąca, kosztów poprzedniego miesiąca i kosztów bieżącego miesiąca, utworzyć projekcji. Bieżącego miesiąca szacowany koszt jest obliczany przez dodanie aktualne miesięczny koszt i projekcji. Projekcja jest na podstawie jej kosztu monitorowane w ciągu ostatnich 30 dni.

### <a name="12-month-planner"></a>Planowanie 12 miesięcy
Tego elementu widget prezentuje koszty planowane w ciągu najbliższych 12 miesięcy i potencjalne oszczędności.
- Kliknij element widget do otwarcia raportu rocznego planowany koszt.

### <a name="cost-by-service"></a>Koszt przez usługę
Tego elementu widget prezentuje amortyzowanego koszty przez usługę w ciągu ostatnich 30 dni.
- Umieść kursor nad wykresu kołowego, aby zobaczyć koszty dla danej usługi.
- Kliknij przycisk elementu widget, aby otworzyć raport Rzeczywiste analizy kosztów.

### <a name="cost-by-account"></a>Koszt konta
Tego elementu widget prezentuje amortyzowanego koszty według konta w ciągu ostatnich 30 dni.
- Umieść kursor nad wykresu kołowego, aby zobaczyć koszty dla danego konta.
- Kliknij przycisk elementu widget, aby otworzyć raport Rzeczywiste analizy kosztów.

### <a name="cost-trend-by-day"></a>Trend kosztów wg dnia
Tego elementu widget najważniejsze funkcje wydatków w ciągu ostatnich 30 dni.
- Umieść kursor nad wykres słupkowy, aby zobaczyć koszty na dzień.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt w miarę upływu czasu.

### <a name="cost-trend-by-month---last-6-months"></a>Trend kosztów według miesięcy - ostatnich 6 miesięcy

Tego elementu widget prezentuje spędzają ostatnich sześciu miesięcy.
- Umieść kursor nad wykres słupkowy, aby zobaczyć koszty miesięcznie.
- Kliknij element widget, aby otworzyć raport rzeczywisty koszt w miarę upływu czasu.

## <a name="asset-controller-dashboard"></a>Pulpit nawigacyjny kontrolera zasobów

Ten pulpit nawigacyjny Wyświetla liczba uruchomionych wystąpień, dyski dostępnych i używanych dystrybucji typów wystąpień i Magazyn informacji.  
![Pulpit nawigacyjny kontrolera zasobów](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Obliczenia bazy danych wystąpienia
Tego elementu widget Wyświetla liczba uruchomionych wystąpień oparte na trendów użycia w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie.

### <a name="disks"></a>Dyski
Tego elementu widget prezentuje całkowitą liczbę i wielkość dysków, które są w użyciu i dostępne.
- Kliknij element widget, aby otworzyć raport aktywnych dysków.

### <a name="instance-type-distribution"></a>Wystąpienie typu dystrybucji
Tego elementu widget prezentuje typy wystąpienia wykresu kołowego.
- Kliknij element widget, aby otworzyć raport dystrybucji wystąpienia, który zawiera podział wystąpień active przez wybrany agregacji.

### <a name="compute-instances---daily-trend"></a>Obliczenia bazy danych wystąpienia - dzienne trendy
Tego elementu widget prezentuje wystąpienia obliczeniowe (dodatkowych zastrzeżone i na żądanie) dziennie w ciągu ostatnich 30 dni.
- Umieść kursor nad wykres tak, aby wyświetlić liczbę wystąpień obliczeń, na typ na dzień.
- Kliknij element widget, aby otworzyć raport wystąpień w czasie.

### <a name="all-buckets-s3"></a>Wszystkie pakiety (S3)
Tego elementu widget wyróżnia całkowita ilość miejsca S3 i liczba przechowywanych obiektów.
- Kliknij element widget do otwarcia pulpitu nawigacyjnego śledzenie S3. Pulpit nawigacyjny pomaga znaleźć, analizowanie i wyświetlić bieżące użycie pamięci masowej, a trendów.

### <a name="sql-db-instances-rds"></a>Wystąpienia bazy danych SQL (RDS)
Tego elementu widget prezentuje liczba uruchomionych wystąpień Amazon RDS oparte na tendencji w ciągu ostatnich 30 dni.
- Kliknij element widget, aby otworzyć raport RDS wystąpienia w miarę upływu czasu.

## <a name="optimizer-dashboard"></a>Optymalizator pulpitu nawigacyjnego
Ten pulpit nawigacyjny zawiera zalecenia redukcję zatrudnienia, nieużywanych zasobów i potencjalne oszczędności.  
![Optymalizator pulpitu nawigacyjnego](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Kalkulator RI
Tego elementu widget Wyświetla liczbę zalecenia dotyczące kupowania RI i zaznacza potencjalne roczne oszczędności.
- Kliknij element widget, aby otworzyć program Kalkulator wystąpienia zastrzeżonych, gdzie można określić, kiedy należy używać na żądanie a zastrzeżone planów cenowych.

### <a name="sizing"></a>Zmiana rozmiaru
Tego elementu widget prezentuje zalecany rozmiar i potencjalne oszczędności, jeśli zaimplementowane.
- Kliknij przycisk elementu widget, aby otworzyć raport EC2 obowiązującej koszt zmiany rozmiaru zalecenia.

### <a name="unused-ri-detector"></a>Wykrywacz RI nieużywane
Tego elementu widget prezentuje liczbę Amazon EC2 zastrzeżenia nieużywane.
- Kliknij element widget, aby otworzyć raport aktualnie nieużywane rezerwacji, możesz przeglądać nieużywane rezerwacji, które można modyfikować.

###  <a name="available-disks"></a>Dostępne dyski
Tego elementu widget prezentuje liczbę odłączyć dysków w danym wdrożeniu.
- Kliknij element widget, aby otworzyć raport odłączyć dysków.

### <a name="rds-ri-calculator"></a>Kalkulator RI usług pulpitu zdalnego
Tego elementu widget prezentuje liczbę rezerwacji zalecenia dla swoich wystąpień Amazon usług pulpitu zdalnego i potencjalne oszczędności.
- Kliknij element widget, aby otworzyć raport zalecenia kupowanie RI usług pulpitu zdalnego umożliwia wyświetlenie zalecenia Cloudyn do używania zastrzeżonych wystąpień zamiast wystąpienia na żądanie.

### <a name="rds-sizing"></a>Zmiana rozmiaru usług pulpitu zdalnego
Tego elementu widget pokazuje liczbę zaleceń zmiany rozmiaru i potencjalne oszczędności.
- Kliknij element widget, aby otworzyć raport zalecenia dotyczące rozmiaru usług pulpitu zdalnego, którym są wyświetlane szczegółowe RDS Amazon zmiany rozmiaru zalecenia.

Zalecenia dotyczące optymalizacji są oparte na danych użycia i wydajności monitorowane w ciągu ostatniego miesiąca.

## <a name="s3-tracker-dashboard"></a>Pulpit nawigacyjny S3 Tracker
Śledzenie S3 pulpit nawigacyjny pomaga znaleźć, analizowanie i wyświetlić bieżące użycie pamięci masowej, a trendów.  
![Pulpit nawigacyjny S3 Tracker](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Wszystkie pakiety
Tego elementu widget prezentuje łączny rozmiar wszystkich Twoje pakiety w GB i całkowita liczba obiektów w Twojej zasobników.
- Kliknij przycisk elementu widget, aby otworzyć raport dystrybucji S3 rozmiar. Raport pomaga analizować rozmiar S3 zasobnik, folder najwyższego poziomu, klasa magazynu i stanu przechowywania wersji.

### <a name="bucket-properties"></a>Zasobnik właściwości
Tego elementu widget prezentuje łączna liczba zasobników magazynu.
- Kliknij element widget, aby wyświetlić raport o właściwościach zasobnika S3.

### <a name="scan-status"></a>Skanowanie stanu
Tego elementu widget prezentuje podczas ostatniego skanowania S3 została wykonana i podczas kolejnego zostanie uruchomiony.
- Kliknij element widget, aby otworzyć raport o stanie skanowania S3.

### <a name="storage-by-bucket"></a>Magazyn w zasobniku
Tego elementu widget prezentuje procent używanej każdej klasy magazynu zasobnika.
- Kliknij przycisk elementu widget, aby otworzyć raport dystrybucji S3 rozmiar. Raport pomaga analizować rozmiar S3 zasobnik, folder najwyższego poziomu, klasa magazynu i stanu przechowywania wersji.

### <a name="number-of-objects-by-bucket"></a>Liczba obiektów przez zasobnik
Tego elementu widget prezentuje liczbę obiektów na zasobnik rzeczywista liczba i procent. Umieść kursor nad zasobnik, aby wyświetlić wszystkich obiektów.
- Kliknij element widget, aby otworzyć raport dystrybucji S3 rozmiaru (na podstawie skanowanie).

## <a name="cloud-comparison-dashboard"></a>Pulpit nawigacyjny porównania chmury
Pulpit nawigacyjny Cloud porównawcza ułatwia porównanie kosztów od dostawców inną chmurę, na podstawie cennika, typ Procesora i rozmiar pamięci RAM.  
![Pulpit nawigacyjny porównania chmury](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Koszt na platformie Azure dzięki typu wystąpienia
Tego elementu widget prezentuje użycia kursów na żądanie z ostatnich 30 dni. Go comares koszt z bieżącym EC2 Amazon koszt vs potencjalne koszty na platformie Azure.
- Umieść kursor nad paski do porównania kosztów na typ wystąpienia.
- Kliknij element widget otworzyć eksportowanie wdrożenia — raport analizy kosztów.

### <a name="ec2-cost-in-azure"></a>EC2 Koszt na platformie Azure
Tego elementu widget Pokazuje bieżące koszty Amazon EC2 i porównuje je do platformy Azure. Porównanie opiera się w ciągu ostatnich 30 dni użycia kursów na żądanie.
- Kliknij element widget otworzyć eksportowanie wdrożenia — raport analizy kosztów.

### <a name="ec2azure-instance-type-mapping"></a>Mapowanie typu wystąpienia EC2/Azure
Tego elementu widget prezentuje najlepsze mapowanie jednostki obliczeniowe elastycznej między Amazon EC2 i platformą Azure.
- Kliknij przycisk elementu widget, aby otworzyć raport mapowanie typu wystąpienia.
