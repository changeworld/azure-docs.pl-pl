---
title: Ocenianie serwerów za pomocą zaimportowanych danych serwera Azure Migrate oceny serwera
description: Opisuje sposób oceny serwerów lokalnych na potrzeby migracji na platformę Azure z użyciem oceny serwera Azure Migrate przy użyciu zaimportowanych danych.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: b9ad5ea6def79c4d7f132558b8b5339bac6f1bc3
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861306"
---
# <a name="assess-servers-using-imported-data"></a>Ocenianie serwerów za pomocą zaimportowanych danych

W tym artykule wyjaśniono, jak oceniać serwery lokalne za pomocą [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool), importując metadane serwera przy użyciu woluminu CSV. W przypadku tej metody oceny nie trzeba konfigurować urządzenia Azure Migrate w celu utworzenia oceny. Jest to przydatne, jeśli:

- Przed wdrożeniem urządzenia należy utworzyć szybką ocenę początkową.
- Nie można wdrożyć urządzenia Azure Migrate w organizacji.
- Nie można udostępniać poświadczeń zezwalających na dostęp do serwerów lokalnych.
- Ograniczenia zabezpieczeń uniemożliwiają zbieranie i wysyłanie danych zebranych przez urządzenie na platformę Azure. Zaimportowany plik pozwala kontrolować dane, które są udostępniane, a wiele danych (na przykład udostępnia adresy IP) jest opcjonalne.


## <a name="before-you-start"></a>Przed rozpoczęciem

Należy pamiętać, że:

- W jednym pliku CSV można dodać maksymalnie 20000 serwerów.
- Można dodać do 20000 serwerów w projekcie Azure Migrate przy użyciu woluminu CSV.
- Informacje o serwerze można przekazać wielokrotnie przy użyciu woluminu CSV w celu Azure Migrate oceny serwera.
- Chociaż zbieranie informacji o aplikacji jest przydatne podczas oceniania środowiska lokalnego na potrzeby migracji, Azure Migrate oceny serwera nie przeprowadza oceny na poziomie aplikacji i nie przyjmuje aplikacji do konta, gdy Tworzenie oceny.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj projekt Azure Migrate.
> * Wypełnij plik CSV informacjami o serwerze.
> * Zaimportuj plik, aby dodać informacje o serwerze do oceny serwera Azure Migrate.
> * Utwórz i przejrzyj ocenę.

> [!NOTE]
> Samouczki przedstawiają najprostszą ścieżkę wdrożenia dla scenariusza, dzięki czemu można szybko skonfigurować weryfikację koncepcji. Samouczki korzystają z domyślnych opcji, jeśli jest to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułami z instrukcjami.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).


## <a name="set-azure-permissions-for-azure-migrate"></a>Ustawianie uprawnień platformy Azure dla Azure Migrate

Twoje konto platformy Azure wymaga uprawnień do utworzenia projektu Azure Migrate.

1. W Azure Portal Otwórz subskrypcję i wybierz pozycję **Kontrola dostępu (IAM)** .
2. W obszarze **Sprawdź dostęp**Znajdź odpowiednie konto i kliknij je, aby wyświetlić uprawnienia.
3. Należy mieć uprawnienia **współautora** lub **właściciela** .
    - Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś właścicielem subskrypcji.
    - Jeśli nie jesteś właścicielem subskrypcji, Pracuj z właścicielem, aby przypisać rolę.


## <a name="set-up-an-azure-migrate-project"></a>Konfigurowanie projektu Azure Migrate

Skonfiguruj nowy projekt usługi Azure Migrate w następujący sposób.

1. W witrynie Azure Portal > **Wszystkie usługi** znajdź pozycję **Azure Migrate**.
2. W obszarze **Usługi** wybierz pozycję **Azure Migrate**.
3. W obszarze **Omówienie**, **Odnajdywanie, ocena i migracja serwerów** kliknij pozycję **Oceń i zmigruj serwery**.

    ![Odnajdywanie i ocenianie serwerów](./media/tutorial-assess-import/assess-migrate.png)

4. W obszarze **Wprowadzenie** kliknij pozycję **Dodaj narzędzia**.
5. W obszarze **Projekt migracji**wybierz subskrypcję platformy Azure i utwórz grupę zasobów, jeśli jej nie masz.     
6. W obszarze **szczegóły projektu**Określ nazwę projektu i lokalizację geograficzną, w której chcesz utworzyć projekt.

    - [Przejrzyj](migrate-support-matrix.md#supported-geographies) obsługiwane lokalizacje geograficzne. Lokalizacja geograficzna projektu jest używana wyłącznie do przechowywania metadanych zebranych z lokalnych maszyn wirtualnych.
    - Podczas przeprowadzania migracji można wybrać dowolny region docelowy.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-import/migrate-project.png)


7. Kliknij przycisk **Dalej**.
8. W **narzędziu Wybierz ocenę**wybierz pozycję **Azure Migrate: Ocena serwera** > **dalej**.

    ![Tworzenie projektu Azure Migrate](./media/tutorial-assess-import/assessment-tool.png)

9. W obszarze **Wybierz narzędzie migracji** wybierz pozycję **Pomiń na razie dodawanie narzędzia migracji** > **Dalej**.
10. W obszarze **Przegląd i dodawanie narzędzi** przejrzyj ustawienia, a następnie kliknij pozycję **Dodaj narzędzia**.
11. Zaczekaj kilka minut, aż projekt usługi Azure Migrate zostanie wdrożony. Nastąpi przekierowanie do strony projektu. Jeśli nie widzisz projektu, możesz uzyskać do niego dostęp z obszaru **Serwery** na pulpicie nawigacyjnym usługi Azure Migrate.


## <a name="prepare-the-csv"></a>Przygotowywanie woluminu CSV

Pobierz szablon CSV i Dodaj do niego informacje o serwerze.


### <a name="download-the-template"></a>Pobieranie szablonu

1. W obszarze **cele migracji** > **serwery** > **Azure Migrate: Ocena serwera**, kliknij przycisk **odkryj**.
2. W obszarze **odnajdywanie maszyn**wybierz pozycję **Importuj przy użyciu. Wolumin CSV**.
3. Kliknij pozycję **Pobierz** , aby pobrać. Szablon CSV. Alternatywnie możesz [pobrać go bezpośrednio](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Przesłać. Szablon CSV](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Dodawanie informacji o serwerze

Zbierz dane serwera i Dodaj je do pliku CSV.

- Aby zebrać dane, możesz wyeksportować je z narzędzi używanych do zarządzania lokalnym serwerem, takich jak VMware vSphere lub baza danych zarządzania konfiguracją (CMDB).
- Aby przejrzeć przykładowe dane, Pobierz nasz [przykładowy plik](https://go.microsoft.com/fwlink/?linkid=2108405).


Poniższa tabela zawiera podsumowanie pól plików do wypełnienia.

**Nazwa pola** | **Wypełnione** | **Szczegóły**
--- | --- | ---
**Nazwa serwera** | Tak | Zalecamy określenie nazwy FQDN.
**Adres IP** | Nie | Adres serwera.
**Liczba rdzeni** | Tak | Liczba rdzeni procesora przypisanych do serwera.
**Pamięć** | Tak | Całkowita ilość pamięci RAM (MB) przypisana do serwera.
**Nazwa systemu operacyjnego** | Tak | System operacyjny serwera.
**Wersja systemu operacyjnego** | Nie | Wersja systemu operacyjnego serwera.
**Liczba dysków** | Nie | Niepotrzebna, jeśli podano szczegółowe szczegóły dysku.
**Rozmiar dysku 1**  | Nie | Maksymalny rozmiar dysku (GB)<br/> Możesz dodać szczegóły do większej liczby dysków, [dodając kolumny](#add-multiple-disks) w szablonie. Można dodać maksymalnie osiem dysków.
**Operacje odczytu z dysku 1** | Nie | Operacje odczytu z dysku na sekundę.
**Operacje zapisu na dysku 1** | Nie | Operacje zapisu na dysku na sekundę.
**Przepływność odczytu dysku 1** | Nie | Dane odczytane z dysku na sekundę w MB/s.
**Przepływność zapisu na dysku 1** | Nie | Dane zapisywane na dysku na sekundę (MB/s).
**Procent użycia procesora CPU** | Nie | Procent użycia procesora CPU.
**Procent wykorzystania pamięci** | Nie | Procent użycia pamięci RAM.
**Całkowita liczba odczytywanych dysków** | Nie | Operacje odczytu z dysku na sekundę.
**Całkowita liczba dysków — operacje zapisu** | Nie | Operacje zapisu na dysku na sekundę.
**Całkowita liczba dysków do odczytu** | Nie | Dane odczytane z dysku w MB na sekundę.
**Całkowita przepustowość zapisu dysków** | Nie | Dane zapisywane na dysku w MB na sekundę.
**Przepływność Ruch przychodzący w sieci** | Nie | Dane odebrane przez serwer w MB na sekundę.
**Przepływność Ruch wychodzący w sieci** | Nie | Dane przesyłane przez serwer w MB na sekundę.
**Typ oprogramowania układowego** | Nie | Oprogramowanie układowe serwera. Wartości mogą być "BIOS" lub "UEFI"
**Typ serwera** | Nie | Wartości mogą być "fizyczne" lub "wirtualne".
**Funkcji hypervisor** | Nie | Funkcja hypervisor, w której jest uruchomiona maszyna. <br/> Możliwe wartości to "VMware", "Hyper-V", "Xen", "AWS", "GCP" lub "inne".
**Numer wersji funkcji hypervisor** | Nie | Wersja funkcji hypervisor.
**Identyfikator maszyny wirtualnej** | Nie | Identyfikator maszyny wirtualnej. Jest to **InstanceUUid** dla maszyny wirtualnej VMware VCENTER lub **identyfikatora maszyny wirtualnej funkcji Hyper-** v dla funkcji Hyper-w.
**Identyfikator programu Virtual Machine Manager** | Nie | Jest to **InstanceUUid** dla programu VMware vCenter. Niewymagane w przypadku funkcji Hyper-V.
**Adres MAC**| Nie | Adres MAC serwera.
**IDENTYFIKATOR SYSTEMU BIOS** | Nie | Identyfikator systemu BIOS serwera.
**Identyfikator serwera niestandardowego**| Nie | Lokalne unikatowe identyfikatory serwera lokalnie. <br/> Przydatne do śledzenia zaimportowanego serwera według identyfikatora lokalnego.
**Nazwa aplikacji 1** | Nie | Nazwa obciążeń uruchomionych na serwerze.<br/> Możesz dodać szczegóły do większej liczby aplikacji, [dodając kolumny](#add-multiple-applications) w szablonie. Można dodać maksymalnie pięć aplikacji.
**Typ aplikacji 1** | Nie | Typ obciążenia uruchomionego na serwerze
**Wersja 1 aplikacji** | Nie | Wersja obciążenia uruchomionego na serwerze.
**Wygaśnięcie licencji aplikacji 1** | Nie | Wygaśnięcie licencji dla obciążenia (jeśli dotyczy).
**Jednostka biznesowa** | Nie | Jednostka biznesowa, do której należy serwer.
**Właściciel firmy** | Nie | Właściciel jednostki biznesowej.
**Nazwa aplikacji biznesowej** | Nie | Nazwa aplikacji, do której należy aplikacja.
**Lokalizacja** | Nie | Centrum danych, w którym znajduje się serwer programu.
**Data likwidacji serwera** | Nie | Data likwidacji serwera fizycznego lub podstawowego serwera fizycznego serwera wirtualnego

### <a name="add-operating-systems"></a>Dodawanie systemów operacyjnych

Ocena rozpoznaje nazwy określonych systemów operacyjnych. Każda określona nazwa systemu operacyjnego musi być zgodna z jedną z opcji na liście [obsługiwanych nazw](#supported-operating-system-names) .


### <a name="add-multiple-disks"></a>Dodaj wiele dysków

Szablon zawiera domyślne pola dla pierwszego dysku.  Możesz dodać podobne kolumny dla maksymalnie 8 dysków.

Na przykład aby określić wszystkie pola dla drugiego dysku, Dodaj kolumny:

Dysk 2 rozmiar dysk 2 odczyt operacje Ops dysk 2 zapis Ops dysk 2 odczyt przepływności dysk 2 przepływność zapisu

Opcjonalnie można dodać określone pola tylko dla dysku.


### <a name="add-multiple-applications"></a>Dodaj wiele aplikacji

Szablon zawiera pola dla pojedynczej aplikacji. Możesz dodać podobne kolumny dla maksymalnie pięciu aplikacji.  

Na przykład aby określić wszystkie pola dla drugiej aplikacji, Dodaj kolumny:

Aplikacja 2 Nazwa aplikacja 2 typ aplikacja 2 wersja aplikacja 2 Licencja wygasa


Opcjonalnie można dodać określone pola tylko dla aplikacji.

> [!NOTE]
> Informacje o aplikacji są przydatne podczas oceniania środowiska lokalnego na potrzeby migracji. Jednak Ocena serwera Azure Migrate nie przeprowadza obecnie oceny na poziomie aplikacji i nie uwzględnia aplikacji podczas tworzenia oceny.


## <a name="upload-the-server-information"></a>Przekaż informacje o serwerze

Po dodaniu informacji do szablonu CSV zaimportuj serwery do Azure Migrate: Ocena serwera.

1. W Azure Migrate > **odnajdywania maszyn**przejdź do wypełnionego szablonu.
2. Kliknij przycisk **Importuj**.
3. Stan importowania jest pokazywany.
    - Jeśli w stanie pojawiły się ostrzeżenia, możesz je rozwiązać lub kontynuować bez ich rozwiązywania.
    - Ulepszanie informacji o serwerze zgodnie z sugestią w ostrzeżeniach zwiększa dokładność oceny.
    - Aby wyświetlić i naprawić ostrzeżenia, jeśli są wyświetlane, kliknij pozycję **Pobierz szczegóły ostrzeżenia. Wolumin CSV**. Spowoduje to pobranie woluminu CSV z dodanymi ostrzeżeniami. Możesz przejrzeć ostrzeżenia i rozwiązać problemy zgodnie z wymaganiami.
    Jeśli błędy pojawiają się w stanie (stan importowania **nie powiodło się**), należy je usunąć przed kontynuowaniem importowania. Aby to zrobić, Pobierz wolumin CSV, który ma teraz dodane szczegóły błędu. Przejrzyj błędy i Rozwiąż je w razie konieczności. Następnie ponownie Przekaż zmodyfikowany plik.
4. Po **zakończeniu**importowania informacje o serwerze są importowane.


> [!NOTE]
> Aby zaktualizować informacje o serwerze przekazane do Azure Migrate, należy ponownie przekazać dane dla serwera, używając tej samej **nazwy serwera**. Należy pamiętać, że pole **Nazwa serwera** nie może być modyfikowane po zaimportowaniu szablonu. Usuwanie serwerów nie jest obecnie obsługiwane.

## <a name="updating-server-information"></a>Aktualizowanie informacji o serwerze

Aby zaktualizować informacje o serwerze, należy ponownie przekazać dane dla serwera z tą samą **nazwą serwera**. Nie można zmodyfikować pola **Nazwa serwera** .

Usuwanie serwerów nie jest obecnie obsługiwane.

### <a name="verify-servers-in-the-portal"></a>Weryfikowanie serwerów w portalu

Po przeprowadzeniu odnajdywania można sprawdzić, czy serwery są wyświetlane w Azure Portal.

1. Otwórz pulpit nawigacyjny Azure Migrate.
2. W **Azure Migrate serwery** > **Azure Migrate: Strona Ocena serwera** kliknij ikonę, która wyświetla liczbę **odnalezionych serwerów**.
3. Kliknij kartę **Import na podstawie** .

## <a name="set-up-an-assessment"></a>Konfigurowanie oceny

Istnieją dwa typy ocen, które można utworzyć przy użyciu Azure Migrate: Ocena serwera.

**Ocena** | **Szczegóły** | **Dane**
--- | --- | ---
**Oparta na wydajności** | Oceny oparte na wartościach danych wydajności | **Zalecany rozmiar maszyny wirtualnej**: na podstawie danych użycia procesora CPU i pamięci.<br/><br/> **Zalecany typ dysku (dysk zarządzany w warstwie Standardowa lub Premium)** : w zależności od liczby operacji we/wy na sekundę i przepływności dysków lokalnych.
**Jako lokalne** | Oceny oparte na wymiarach lokalnych. | **Zalecany rozmiar maszyny wirtualnej**: na podstawie określonego rozmiaru serwera<br/><br> **Zalecany typ dysku**: na podstawie ustawienia typu magazynu wybieranego do oceny.


### <a name="run-an-assessment"></a>Uruchamianie oceny

Uruchom ocenę w następujący sposób:

1. Zapoznaj się z [najlepszymi rozwiązaniami](best-practices-assessment.md) dotyczącymi tworzenia ocen.
2. Na karcie **serwery** w **Azure Migrate: kafelek Ocena serwera** kliknij pozycję **Oceń**.

    ![Ocena](./media/tutorial-assess-physical/assess.png)

2. W obszarze **ocenianie serwerów**Określ nazwę oceny.
3. W obszarze **Źródło odnajdywania**wybierz pozycję **maszyny dodane przez import do Azure Migrate**
3. Kliknij pozycję **Wyświetl wszystko**, aby sprawdzić właściwości oceny.

    ![Właściwości oceny](./media/tutorial-assess-physical/view-all.png)

3. W obszarze **Wybierz lub Utwórz grupę**wybierz pozycję **Utwórz nową**, a następnie określ nazwę grupy. Grupa zbiera co najmniej jedną maszynę wirtualną w celu oceny.
4. W obszarze **Dodawanie maszyn do grupy**wybierz serwery, które mają zostać dodane do grupy.
5. Kliknij pozycję **Utwórz ocenę** , aby utworzyć grupę, i uruchom ocenę.

    ![Tworzenie oceny](./media/tutorial-assess-physical/assessment-create.png)

6. Po utworzeniu oceny Wyświetl ją w obszarze **serwery** > **Azure Migrate: Ocena serwera** > **ocen**.
7. Kliknij polecenie **Eksportuj ocenę**, aby pobrać ocenę jako plik programu Excel.



## <a name="review-an-assessment"></a>Przegląd oceny

Ocena zawiera opis:

- **Gotowość platformy Azure**: czy serwery są odpowiednie do migracji na platformę Azure.
- **Oszacowanie kosztów miesięcznych**: szacowane miesięczne koszty obliczeń i magazynowania na potrzeby uruchamiania serwerów na platformie Azure.
- **Oszacowanie kosztu miesięcznego magazynu**: szacowane koszty magazynu dyskowego po migracji.

### <a name="view-an-assessment"></a>Wyświetlanie oceny

1. W obszarze **cele migracji** >  **serwery**kliknij pozycję **oceny** w **Azure Migrate: Ocena serwera**.
2. W obszarze **oceny**kliknij ocenę, aby go otworzyć.

    ![Podsumowanie oceny](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Przegląd gotowości platformy Azure

1. W obszarze **gotowość do platformy Azure**Sprawdź, czy serwery są gotowe do migracji na platformę Azure.
2. Sprawdź stan:
    - **Gotowe do platformy Azure**: Azure Migrate zaleca rozmiar maszyny wirtualnej i oszacowania kosztów dla maszyn wirtualnych w ocenie.
    - **Gotowe warunki**: pokazuje problemy i sugerowane korygowanie.
    - **Nie gotowy na platformę Azure**: zawiera problemy i sugerowane korygowanie.
    - **Nieznane gotowość**: używany, gdy Azure Migrate nie może ocenić gotowości ze względu na problemy z dostępnością danych.

2. Kliknij stan **gotowości platformy Azure** . Możesz wyświetlić szczegóły gotowości serwera i przejść do szczegółów, aby zobaczyć szczegóły serwera, w tym ustawienia obliczeń, magazynu i sieci.

### <a name="review-cost-details"></a>Przejrzyj szczegóły kosztów

Ten widok przedstawia szacowany koszt obliczeń i magazynu dla uruchomionych maszyn wirtualnych na platformie Azure.

1. Zapoznaj się z miesięcznymi kosztami obliczeniowymi i magazynem. Koszty są agregowane dla wszystkich serwerów w ocenianej grupie.

    - Oszacowania kosztów opierają się na zaleceń dotyczących rozmiaru komputera oraz jego dyskach i właściwościach.
    - Wyświetlane są szacowane miesięczne koszty obliczeń i magazynu.
    - Oszacowanie kosztów służy do uruchamiania serwerów lokalnych jako maszyn wirtualnych IaaS. Ocena serwera Azure Migrate nie uwzględnia kosztów PaaS ani SaaS.

2. Możesz przejrzeć szacunkowe oszacowanie kosztów magazynowania. Ten widok przedstawia zagregowane koszty magazynu dla ocenianej grupy, podzielone na różne typy dysków magazynu.
3. Możesz przejść do szczegółów, aby wyświetlić szczegóły dla określonych maszyn wirtualnych.

> [!NOTE]
> Klasyfikacje zaufania nie są przypisywane do ocen serwerów zaimportowanych do oceny serwera Azure Migrate przy użyciu woluminu CSV.


## <a name="supported-operating-system-names"></a>Obsługiwane nazwy systemu operacyjnego

Nazwa | Nazwa
--- | ---
**A – H** |
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD
**I-R** |
SYSTEM OPERACYJNY IBM OS/2 | systemu |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora |
**S-T** |
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO dla systemu UNIX 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** |
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>System Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>System Windows NT<br/>System Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Próg systemu Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Zaimportowane serwery do Azure Migrate: Ocena serwera przy użyciu woluminu CSV.
> * Tworzenie i przeglądanie oceny

Teraz należy [wdrożyć urządzenie](./migrate-appliance.md) pod kątem dokładniejszych ocen oraz zebrać serwery w grupach w celu uzyskania dokładniejszej oceny przy użyciu [analizy zależności](./concepts-dependency-visualization.md).
