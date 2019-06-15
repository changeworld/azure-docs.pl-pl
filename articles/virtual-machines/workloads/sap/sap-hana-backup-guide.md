---
title: Przewodnik tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Przewodniku kopia zapasowa dla oprogramowania SAP HANA zawiera dwie główne możliwości tworzenia kopii zapasowych dla oprogramowania SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60937091"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Przewodnik po wykonywaniu kopii zapasowych dla oprogramowania SAP HANA w usłudze Azure Virtual Machines

## <a name="getting-started"></a>Wprowadzenie

Przewodniku kopia zapasowa dla oprogramowania SAP HANA uruchomionych na maszynach wirtualnych platformy Azure tylko opisano tematy specyficzne dla platformy Azure. Ogólne platformy SAP HANA kopii zapasowej elementy pokrewne, na ten temat można znaleźć w dokumentacji platformy SAP HANA (zobacz _dokumentacja usługi backup platformy SAP HANA_ w dalszej części tego artykułu).

Ten artykuł koncentruje się na dwóch głównych kopii zapasowej możliwości platformy SAP HANA na maszynach wirtualnych platformy Azure:

- HANA kopii zapasowej systemu plików w maszynie wirtualnej systemu Linux platformy Azure (zobacz [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md))
- Kopia zapasowa HANA na podstawie migawek magazynu ręcznie za pomocą funkcji migawki obiektu blob magazynu platformy Azure lub usługi Kopia zapasowa Azure (zobacz [kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md))

SAP HANA oferuje interfejs API, który umożliwia tworzenia kopii zapasowej narzędzi innych firm łączyć bezpośrednio z platformą SAP HANA kopii zapasowej. (Który nie jest w zakresie tego przewodnika). Nie ma żadnych bezpośrednią integrację oprogramowania SAP HANA przy użyciu usługi Azure Backup dostępnych po prawej stronie teraz na podstawie tego interfejsu API.

SAP HANA jest oficjalnie obsługiwane w różnych typów maszyn wirtualnych platformy Azure, takich jak Azure serii M. Aby uzyskać pełną listę oprogramowania SAP HANA certyfikowane maszyny wirtualne platformy Azure, zapoznaj się z [Znajdź certyfikowane platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ten artykuł zostanie zaktualizowany po nowych ofert platformy SAP Hana na platformie Azure, stają się dostępne.

Brak dostępnej także hybrydowego rozwiązania SAP HANA na platformie Azure, w którym oprogramowanie SAP HANA jest uruchomiona niezwirtualizowanych na serwerach fizycznych. Jednakże oprogramowanie SAP HANA w usłudze Azure backup w przewodniku obejmuje czystego środowiska platformy Azure, w którym działa oprogramowanie SAP HANA w maszynie Wirtualnej platformy Azure, a nie platformy SAP HANA uruchomionych na &quot;dużych wystąpień.&quot; Zobacz [platformy SAP HANA (duże wystąpienia) — omówienie i architektura na platformie Azure](hana-overview-architecture.md) Aby uzyskać więcej informacji na temat tego rozwiązania tworzenia kopii zapasowych na &quot;dużych wystąpień&quot; na podstawie migawek magazynu.

Ogólne informacje dotyczące produktów SAP, obsługiwane na platformie Azure można znaleźć w [1928533 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1928533).

Następujących trzech wyników nadaj Omówienie opcji tworzenia kopii zapasowych oprogramowania SAP HANA przy użyciu natywnych możliwości platformy Azure, obecnie, a także pokazać trzech potencjalnych przyszłych scenariusze tworzenia kopii zapasowej. Powiązane artykuły [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) i [kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md) opisano te opcje, które bardziej szczegółowo, uwzględniając rozmiar i wydajność dla rozwiązania SAP HANA kopie zapasowe multi terabajtów.

![Poniższy rysunek przedstawia dwie możliwości zapisywania bieżący stan maszyny Wirtualnej](media/sap-hana-backup-guide/image001.png)

Poniższy rysunek pokazuje możliwość zapisywania w bieżącym stanie maszyny Wirtualnej za pośrednictwem usługi Azure Backup lub ręcznego migawki dysków maszyny Wirtualnej. W przypadku tej metody jeden&#39;ma Zarządzanie kopiami zapasowymi platformy SAP HANA. Wyzwanie w scenariuszu migawki dysku jest spójności systemu plików, stan dysku spójnych z aplikacją. Temat spójności zostało omówione w sekcji _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_ w dalszej części tego artykułu. Możliwości i ograniczenia usługi Azure Backup, powiązanych kopii zapasowych oprogramowania SAP HANA zostały również omówione w dalszej części tego artykułu.

![Poniższy rysunek pokazuje, że opcje pobierania oprogramowania SAP HANA pliku kopii zapasowej wewnątrz maszyny Wirtualnej](media/sap-hana-backup-guide/image002.png)

Poniższy rysunek przedstawia opcje dla pliku kopii zapasowej platformy SAP HANA wewnątrz maszyny Wirtualnej, a następnie przechowywanie ich pliki kopii zapasowej HANA gdzieś w przeciwnym razie przy użyciu różnych narzędzi. Wykonywanie kopii zapasowej HANA wymaga więcej czasu niż w przypadku rozwiązania tworzenia kopii zapasowych opartych na migawkach, ale zapewnia on korzyści dotyczące integralność i spójność. Szczegółowe informacje znajdują się w dalszej części tego artykułu.

![Poniższy rysunek pokazuje potencjalnych przyszłych scenariusz tworzenia kopii zapasowych platformy SAP HANA](media/sap-hana-backup-guide/image003.png)

Poniższy rysunek pokazuje potencjalnych przyszłych scenariusz tworzenia kopii zapasowych platformy SAP HANA. SAP HANA dozwolone, wykonywania kopii zapasowych z replikacji pomocniczej, czy dodać dodatkowe opcje strategii tworzenia kopii zapasowych. Obecnie nie jest możliwe zgodnie z wpisu w witrynie typu wiki biblioteki SAP HANA:

_&quot;Czy jest możliwe do wykonania kopii zapasowych na stronie dodatkowej?_

_Nie, obecnie można tylko dane i zalogować się wykonywanie kopii zapasowych na stronie głównej. Jeśli kopia zapasowa automatycznego logowania jest włączona, po przejęcie na stronie dodatkowej kopii zapasowych dziennika automatycznie zostanie zapisany istnieje.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Zasoby SAP HANA w kopii zapasowej

### <a name="sap-hana-backup-documentation"></a>Dokumentacja usługi backup platformy SAP HANA

- [Wprowadzenie do programu SAP HANA administracji](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planowanie z kopią zapasową i strategii odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Planowanie kopii zapasowych platformy HANA przy użyciu ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Harmonogram kopii zapasowych danych (SAP HANA z Panelu sterowania)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Często zadawane pytania dotyczące platformy SAP HANA kopii zapasowej w [1642148 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Często zadawane pytania dotyczące platformy SAP HANA migawki bazy danych i magazynu w [2039883 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Systemy plików nieodpowiednią kartę sieciową dla kopii zapasowych i odzyskiwania w [1820529 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Dlaczego SAP HANA w kopii zapasowej?

Usługa Azure storage oferuje dostępność i niezawodność gotowych (zobacz [wprowadzenie do usługi Microsoft Azure Storage](../../../storage/common/storage-introduction.md) Aby uzyskać więcej informacji o usłudze Azure storage).

To minimum potrzebne do &quot;kopii zapasowej&quot; jest zależą od umowy SLA platformy Azure, umieszczanie plików danych i dziennika platformy SAP HANA na wirtualnych dyskach twardych platformy Azure dołączonych do serwera SAP HANA maszyny Wirtualnej. Ta metoda obejmuje błędami maszyny Wirtualnej, ale nie potencjalnego uszkodzenia danych SAP HANA i pliki dziennika lub błędów logicznych, takich jak usuwanie danych lub plików przypadkowo. Kopie zapasowe są również wymagane dla zgodności lub z przyczyn prawnych. Krótko mówiąc zawsze istnieje potrzeba tworzenia kopii zapasowych oprogramowania SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sprawdzanie poprawności kopia zapasowa oprogramowania SAP HANA
Korzystając z migawek magazynu, zalecane jest uruchomienie przywracania testu w innym systemie. To podejście zapewnia sposób upewnić się, że kopii zapasowej jest poprawny i wewnętrznych procesów do utworzenia kopii zapasowej i przywracanie działają, zgodnie z oczekiwaniami. Gdy ta operacja jest znaczny próg wykluczający lokalnie, jest znacznie łatwiejsze do wykonania w chmurze, zapewniając tymczasowo zasobów niezbędnych do tego celu.

Zachowaj należy pamiętać, wykonując proste przywracania i sprawdzania, czy HANA jest uruchomiona i uruchamiania nie są wystarczające. W idealnym przypadku jeden należy uruchomić sprawdzanie spójności tabeli należy upewnić się, że przywrócona baza danych jest dobrym rozwiązaniem. SAP HANA oferuje kilka rodzajów sprawdzania spójności, które opisano w [1977584 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informacje o sprawdzaniu spójności tabeli można także znaleźć w witrynie internetowej SAP w [tabeli i sprawdzania spójności katalogu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Dla standardowych plików kopii zapasowych przywracania testu nie jest konieczne. Istnieją dwa narzędzia SAP HANA, które pomagają sprawdzić, której kopia zapasowa może służyć do przywracania: hdbbackupdiag i hdbbackupcheck. Zobacz [ręcznie sprawdzanie, czy odzyskiwanie jest możliwe](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) Aby uzyskać więcej informacji na temat tych narzędzi.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Zalety i wady HANA kopii zapasowej w porównaniu z migawek magazynu

SAP&#39;t dają pierwszeństwo kopii zapasowej albo HANA w porównaniu z migawki magazynu. Wyświetla listę ich zalet i wad, dzięki czemu jeden można określić, której mają zostać użyte w zależności od sytuacji i technologii magazynowania danych (zobacz [planowania Backup i strategii odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Na platformie Azure, należy zwrócić uwagę na fakt, że usługi Azure blob migawki funkcji&#39;spójności systemu plików gwarancji t (zobacz [Using migawek obiektów blob za pomocą programu PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Następnej sekcji _wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu_, w tym artykule omówiono pewne zagadnienia dotyczące tej funkcji.

Ponadto jedna ma poznanie skutków rozliczeń, pracując często z migawek obiektów blob, zgodnie z opisem w tym artykule: [Opis sposobu migawek naliczane opłaty za](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— go jest&#39;t jako oczywiste, jak przy użyciu platformy Azure dyski wirtualne.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Wyjaśnienie pojęcia spójności danych SAP HANA podczas robienia migawek magazynu

Spójności aplikacji i systemu plików to złożony problem, podczas robienia migawek magazynu. Najprostszym sposobem, aby uniknąć problemów jest zamknięta platformy SAP HANA, lub może być nawet całej maszyny wirtualnej. Zamknięcie systemu może być doable pokaz lub prototypu albo nawet systemie deweloperskim, ale nie jest to opcja w systemie produkcyjnym.

Na platformie Azure, ma jedną należy pamiętać, że usługi Azure blob migawki funkcji&#39;spójności systemu plików gwarancji t. Współpracuje jednak przy użyciu oprogramowania SAP HANA migawki funkcji, tak długo, jak tylko jeden dysk wirtualny jest zaangażowane. Ale nawet w przypadku pojedynczego dysku, dodatkowe elementy muszą być sprawdzone. [2039883 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2039883) zawiera ważne informacje dotyczące tworzenia kopii zapasowych oprogramowania SAP HANA, za pośrednictwem migawek magazynu. Na przykład wymienia, że w systemie plików XFS jest niezbędne do uruchomienia **xfs\_freeze** przed uruchomieniem migawki magazynu w celu zagwarantowania spójności (zobacz [xfs\_freeze(8) - strony ataków typu man systemu Linux ](https://linux.die.net/man/8/xfs_freeze) szczegółowe informacje na temat **xfs\_freeze**).

Staje się temat spójności, nawet trudniejsze w przypadku, gdy system pojedynczy plik obejmuje wiele dysków/woluminów. Na przykład za pomocą mdadm lub LVM i stosowanie. Uwaga SAP wymienionych powyżej stany:

_&quot;Jednak należy pamiętać, że system magazynu ma spójności We/Wy podczas tworzenia migawki magazynu na wolumin danych SAP HANA, czyli tworzenia migawek woluminu specyficzne dla usługi danych SAP HANA musi być niepodzielną operacją.&quot;_

Zakładając, że istnieje system plików XFS, obejmujące cztery dyski wirtualne platformy Azure, w poniższych krokach przedstawiono migawkę spójną, który reprezentuje obszar danych HANA:

- Przygotuj migawkę HANA
- Zablokuj systemu plików (na przykład użyć **xfs\_freeze**)
- Utwórz wszystkie migawki obiektu blob konieczne na platformie Azure
- Odblokuj systemu plików
- Upewnij się, migawki HANA

Zaleca się procedurę powyżej we wszystkich przypadkach należy dla bezpieczeństwa, niezależnie od tego, jakiego systemu plików. Czy jest to jeden dysk lub rozkładanie za pośrednictwem mdadm lub LVM na wielu dyskach.

Należy potwierdzić migawki platformy HANA. Ze względu na &quot;kopii przy zapisie,&quot; platformy SAP HANA mogą nie wymagać dodatkowego miejsca na dysku w tym trybie przygotowania migawki. Jego&#39;również nie można uruchomić nowych kopii zapasowych, dopóki nie został potwierdzony migawki platformy SAP HANA.

Usługa Azure Backup używa rozszerzeń maszyny Wirtualnej platformy Azure, aby zadbać o spójności systemu plików. Te rozszerzenia maszyny Wirtualnej nie są dostępne do użycia autonomicznego. Jedna nadal ma zarządzać spójności platformy SAP HANA. Zapoznaj się z artykułem powiązane [SAP HANA usługi Azure Backup na poziomie plików](sap-hana-backup-file-level.md) Aby uzyskać więcej informacji.

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia harmonogramu tworzenia kopii zapasowych oprogramowania SAP HANA

Artykuł platformy SAP HANA [planowania Backup i strategii odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) stany podstawowy plan, tworzyć kopie zapasowe:

- Magazynu migawek (dziennie)
- Kopia zapasowa kompletne dane przy użyciu formatu pliku lub bacint (raz w tygodniu)
- Tworzenie kopii zapasowych automatycznego logowania

Opcjonalnie jeden można wykonać całkowicie bez migawek magazynu; one może zostać zastąpione przez kopie zapasowe delta HANA, takich jak przyrostowych lub różnicowej kopii zapasowych (zobacz [różnicowe kopie zapasowe](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

Przewodnik administrowania HANA zawiera przykładową listę. Sugerują one, że jeden odzyskiwanie do określonego punktu SAP HANA w czasie za pomocą następującej sekwencji tworzenia kopii zapasowych:

1. Pełne dane kopii zapasowej
2. Różnicowa kopia zapasowa
3. Przyrostowa kopia zapasowa 1
4. Przyrostowa kopia zapasowa 2
5. Tworzenie kopii zapasowych dzienników

Dotyczące dokładnie harmonogramu kiedy i jak często ma się zdarzyć określonego typu kopii zapasowej nie jest możliwe zapewniają ogólne wytyczne — jest zbyt specyficzne dla klienta i zależy od liczby zmian danych występują w systemie. Jeden podstawowy po stronie SAP, który może być traktowany jako ogólne wskazówki, zaleca się jeden HANA pełnej upewnij w kopii zapasowej raz w tygodniu.
Dotyczące kopii zapasowych dziennika można znaleźć w dokumentacji platformy SAP HANA [kopie zapasowe dziennika](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP również zaleca się wykonanie niektórych celów wykaz kopii zapasowych, aby zapobiec rośnie w nieskończoność (zobacz [wykaz kopii zapasowych i magazynu kopii zapasowych dla gospodarstw domowych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Pliki konfiguracji SAP HANA

Jak wspomniano w często zadawanych Pytaniach w [1642148 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1642148), pliki konfiguracji SAP HANA niebędące częścią standardowych kopii zapasowej platformy HANA. Nie są one niezbędne do przywrócenia systemu. Konfiguracja platformy HANA można zmieniać ręcznie po przywróceniu. W przypadku, gdy jeden chce uzyskać tę samą konfigurację niestandardowej podczas procesu przywracania, jest niezbędne do tworzenia kopii zapasowych plików konfiguracji HANA oddzielnie.

Korzystał standardowa HANA kopie zapasowe do dedykowanego systemu plików kopii zapasowej HANA jeden można także skopiować pliki konfiguracji do tego samego systemu plików kopii zapasowej, a następnie skopiuj wszystko ze sobą do miejsca docelowego końcowy magazynu, takich jak chłodna usługi blob storage.

### <a name="sap-hana-cockpit"></a>SAP HANA w Panelu sterowania

SAP HANA w Panelu sterowania oferuje możliwości monitorowania oraz zarządzania nimi platformy SAP HANA za pośrednictwem przeglądarki. Umożliwia również obsługę kopii zapasowych oprogramowania SAP HANA i dlatego może służyć jako alternatywa dla programu SAP HANA Studio i ABAP DBACOCKPIT (zobacz [SAP HANA w Panelu sterowania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) Aby uzyskać więcej informacji).

![Poniższy rysunek pokazuje ekranu administracyjnego bazy danych Panelu sterowania programu SAP HANA](media/sap-hana-backup-guide/image004.png)

Poniższy rysunek pokazuje ekranu administracyjnego bazy danych Panelu sterowania programu SAP HANA i tworzenia kopii zapasowej kafelka po lewej stronie. Oglądanie kafelka kopii zapasowej wymaga odpowiednich uprawnień użytkowników dla konta logowania.

![Kopie zapasowe mogą być monitorowane w SAP HANA w Panelu sterowania, gdy są one stałe](media/sap-hana-backup-guide/image005.png)

Kopie zapasowe mogą być monitorowane w SAP HANA w Panelu sterowania, gdy są stałe, i po jej zakończeniu szczegóły kopii zapasowej nie są dostępne.

![Przykładem dotyczącym używania programu Firefox na maszynie Wirtualnej platformy Azure z systemem SLES 12 z pulpit gnome](media/sap-hana-backup-guide/image006.png)

Poprzednich zrzutach ekranu zostały wykonane z maszyny Wirtualnej z systemem Windows Azure. To zadanie jest przykładem dotyczącym używania programu Firefox na maszynie Wirtualnej platformy Azure z systemem SLES 12 z pulpit gnome. Przedstawia on także definiować harmonogramów tworzenia kopii zapasowych oprogramowania SAP HANA w Panelu sterowania programu SAP HANA. Zgodnie z jedną można również wyświetlić, sugeruje daty/godziny jako prefiksu plików kopii zapasowej. W systemie SAP HANA Studio domyślny prefiks to &quot;COMPLETE\_danych\_kopii zapasowej&quot; podczas wykonywania kopii zapasowej całego pliku. Zaleca się użycie unikatowy prefiks.

### <a name="sap-hana-backup-encryption"></a>Szyfrowanie kopii zapasowej platformy SAP HANA

SAP HANA oferuje szyfrowanie danych i dziennika. Jeśli nie są zaszyfrowane dane oprogramowania SAP HANA i dziennika, następnie wykonywanie kopii zapasowych również nie są szyfrowane. Jest klienta na potrzeby szyfrowania kopii zapasowych oprogramowania SAP HANA pewnego rodzaju rozwiązania innej firmy. Zobacz [danych i szyfrowanie woluminu dziennika](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) Aby dowiedzieć się więcej na temat szyfrowania SAP HANA.

Na Microsoft Azure klient może używać funkcji szyfrowania maszyn wirtualnych IaaS do szyfrowania. Na przykład jeden wystarczą dedykowanych dysków z danymi dołączonych do maszyny Wirtualnej, które są używane do przechowywania kopii zapasowych oprogramowania SAP HANA, a następnie utwórz kopie tych dysków.

Usługa Azure Backup może obsługiwać zaszyfrowanych maszyn wirtualnych/dysków (zobacz [jak kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Innym rozwiązaniem byłoby Obsługa maszyna wirtualna SAP HANA i jej dysków bez szyfrowania w celu przechowywania plików kopii zapasowych oprogramowania SAP HANA na koncie magazynu, dla którego włączono szyfrowanie (zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Ustawienia testu

### <a name="test-virtual-machine-on-azure"></a>Testowa maszyna wirtualna na platformie Azure

Aby można było wykonać Nasze testy, instalacja oprogramowania SAP HANA w maszynie Wirtualnej GS5 platformy Azure został użyty podczas następujących testów/przywracania kopii zapasowej. Zasady są takie same jak dla maszyn wirtualnych serii M.

![Poniższy rysunek pokazuje część omówienie portalu Azure dla platformy HANA testowej maszyny Wirtualnej](media/sap-hana-backup-guide/image007.png)

Poniższy rysunek pokazuje część omówienie portalu Azure dla platformy HANA testowej maszyny Wirtualnej.

### <a name="test-backup-size"></a>Rozmiar kopii zapasowej na test

![Poniższy rysunek została pobrana z konsoli programu kopii zapasowej w HANA Studio i wskazuje rozmiar pliku kopii zapasowej 229 GB dla serwera indeksu HANA](media/sap-hana-backup-guide/image008.png)

Tabela fikcyjna zostało wypełnione z użyciem danych do pobrania łączna ilość danych kopii zapasowej o rozmiarze ponad 200 GB miejsca do wyprowadzenia prawdziwe dane dotyczące wydajności. Rysunek została pobrana z konsoli programu kopii zapasowej w HANA Studio i wskazuje rozmiar pliku kopii zapasowej 229 GB dla serwera indeksu platformy HANA. W przypadku testów domyślny prefiks kopii zapasowej "COMPLETE_DATA_BACKUP" w programie SAP HANA Studio był używany. W systemach rzeczywistej produkcji bardziej użyteczny, prefiks powinien być zdefiniowany. SAP HANA w Panelu sterowania sugeruje daty/godziny.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testowanie narzędzia do kopiowania plików bezpośrednio do usługi Azure storage

Do przesyłania plików kopii zapasowych oprogramowania SAP HANA bezpośrednio do usługi Azure blob storage lub udziałami plików platformy Azure, narzędzia blobxfer był używany, ponieważ obsługuje ona zarówno elementy docelowe i mogą łatwo zintegrować do skryptów automatyzacji ze względu na jego interfejsu wiersza polecenia. Narzędzie blobxfer jest dostępne na [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Szacowany rozmiar kopii zapasowej na test

Należy oszacować rozmiar kopii zapasowej środowiska SAP Hana. Te dane szacunkowe pomaga zwiększyć wydajność, definiując rozmiar maksymalny pliku kopii zapasowej dla liczby plików kopii zapasowej z powodu równoległości podczas kopiowania plików. (Te szczegóły zostały omówione w dalszej części tego artykułu). Jeden należy również postanowić, czy należy wykonać pełną kopię zapasową lub różnicowej kopii zapasowej (przyrostowych lub różnicowej).

Na szczęście jest prostą instrukcję SQL, która szacowania rozmiaru plików kopii zapasowej: **wybierz \* od M\_kopii zapasowej\_rozmiar\_SZACUNKI** (zobacz [szacowania Miejsce wymagane w systemie plików do tworzenia kopii zapasowych danych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Dane wyjściowe tej instrukcji SQL odpowiada prawie dokładnie tak rzeczywisty rozmiar pełnych danych kopii zapasowej na dysku](media/sap-hana-backup-guide/image009.png)

Dla systemu testowego dane wyjściowe tej instrukcji SQL odpowiada prawie dokładnie tak rzeczywisty rozmiar pełnych danych kopii zapasowej na dysku.

### <a name="test-hana-backup-file-size"></a>Rozmiar pliku kopii zapasowej HANA testu

![Konsola kopii zapasowej HANA Studio umożliwia jeden ograniczyć maksymalny rozmiar pliku kopii zapasowej plików HANA](media/sap-hana-backup-guide/image010.png)

Konsola kopii zapasowej HANA Studio umożliwia jeden ograniczyć maksymalny rozmiar pliku kopii zapasowej plików HANA. W środowisku przykładowe tej funkcji umożliwia uzyskanie kilka mniejszych plików kopii zapasowej zamiast jednego pliku kopii zapasowej 230 GB. Mniejszy rozmiar pliku ma znaczący wpływ na wydajność (zapoznaj się z artykułem powiązane [SAP HANA usługi Azure Backup na poziomie plików](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Podsumowanie

Oparte na wynikach testów, które w poniższej tabeli przedstawiono zalety i wady rozwiązania, aby utworzyć kopię zapasową bazy danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure.

**Tworzenie kopii zapasowych oprogramowania SAP HANA w systemie plików, a następnie skopiuj pliki kopii zapasowej do końcowego miejsca docelowego kopii zapasowej**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Zachowaj HANA kopii zapasowych na dyskach maszyny Wirtualnej                      |Nie wysiłków dodatkowego zarządzania     |Eats całe lokalne miejsce na dysku maszyny Wirtualnej           |
|Narzędzie Blobxfer skopiuj pliki kopii zapasowej do magazynu obiektów blob |Równoległość topologii w celu skopiować wielu plików decyzja o korzystaniu z chłodnego magazynu obiektów blob | Konserwacja dodatkowe narzędzia i skrypty niestandardowe | 
|Kopiowanie obiektu blob za pośrednictwem programu Powershell lub interfejsu wiersza polecenia                    |Nie dodatkowe narzędzia konieczne, można osiągnąć za pomocą programu Azure Powershell lub interfejsu wiersza polecenia |ręczny proces, klient ma obsługę skryptów i zarządzania skopiowany obiektów blob do przywrócenia|
|Skopiuj do udziału NFS                                  |Przetwarzanie końcowe plików kopii zapasowej na innej maszyny Wirtualnej bez wpływu na serwerze HANA|Powolne proces kopiowania|
|Kopiuj Blobxfer do usługi Azure File Service                |Nie będzie jeść miejsca na dyskach lokalnych maszyn wirtualnych|Nie bezpośrednio zapisać pomocy technicznej przy HANA kopii zapasowej, ograniczenie rozmiaru udziału plików aktualnie na 5 TB|
|Agent usługi Azure Backup                                 | Jest preferowanym rozwiązaniem         | Obecnie nie są dostępne w systemie Linux    |



**Kopii zapasowych oprogramowania SAP HANA na podstawie migawek magazynu**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Umożliwia kopii zapasowych maszyn wirtualnych w oparciu migawek obiektów blob | Korzystając z nie Przywracanie na poziomie pliku, wymaga utworzenia nowej maszyny Wirtualnej dla procesu przywracania, która następnie oznacza konieczność nowy klucz licencji platformy SAP HANA|
|Migawki obiektów blob ręczne                              | Elastyczność w tworzeniu i Przywróć określone dyski maszyny Wirtualnej bez konieczności zmieniania Unikatowy identyfikator maszyny Wirtualnej|Wszystkie ręcznych, które mają być wykonane przez klienta|

## <a name="next-steps"></a>Kolejne kroki
* [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) opisy opcji kopii zapasowych opartych na plikach.
* [Kopia zapasowa oprogramowania SAP HANA na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md) opisy opcji kopii zapasowych opartych na migawkach magazynu.
* Aby dowiedzieć się, jak zadbać o wysokiej dostępności i plan odzyskiwania po awarii oprogramowania SAP Hana na platformie Azure (duże wystąpienia), zobacz [platformy SAP HANA (duże wystąpienia) o wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
