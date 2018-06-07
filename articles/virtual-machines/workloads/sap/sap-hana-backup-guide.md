---
title: Podręcznik tworzenia kopii zapasowej programu SAP HANA na maszynach wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Podręcznik tworzenia kopii zapasowej programu SAP HANA udostępnia dwie główne możliwości tworzenia kopii zapasowej dla SAP HANA na maszynach wirtualnych Azure
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
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9346ed6b26b4fa4a7875354153b6847938253d1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657013"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Przewodnik po wykonywaniu kopii zapasowych dla oprogramowania SAP HANA w usłudze Azure Virtual Machines

## <a name="getting-started"></a>Wprowadzenie

W przewodniku tworzenia kopii zapasowej SAP HANA uruchomionych na maszynach wirtualnych Azure tylko opisano tematy specyficzne dla platformy Azure. Ogólne SAP HANA kopii zapasowej elementów pokrewnych, zapoznaj się z dokumentacją SAP HANA (zobacz _dokumentacji kopii zapasowej SAP HANA_ dalszej części tego artykułu).

Ten artykuł koncentruje się na dwie główne kopii zapasowej możliwości SAP HANA na maszynach wirtualnych Azure:

- HANA tworzenia kopii zapasowej w systemie plików w maszynie wirtualnej systemu Linux platformy Azure (zobacz [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md))
- HANA kopii zapasowej na podstawie migawki magazynu ręcznie przy użyciu funkcji migawki obiektu blob magazynu Azure lub usługi Online Backup (zobacz [SAP HANA kopii zapasowych oparte na magazynu migawek](sap-hana-backup-storage-snapshots.md))

SAP HANA oferuje kopii zapasowej interfejsu API, który umożliwia narzędzia tworzenia kopii zapasowych innych firm do integracji bezpośrednio z programu SAP HANA. (To nie jest w zakres tego podręcznika.) Nie ma żadnych bezpośrednia integracja SAP HANA z prawej dostępna teraz oparta na ten interfejs API usługi Azure Backup.

SAP HANA jest oficjalnie obsługiwana dla typu maszyny Wirtualnej Azure GS5 jako pojedynczego wystąpienia o dodatkowe ograniczenia w przypadku obciążeń OLAP (zobacz [Znajdź certyfikowane platform IaaS](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) w witrynie sieci Web programu SAP). Ten artykuł zostanie zaktualizowany po nowych ofert dla SAP HANA na platformie Azure staną się dostępne.

Brak dostępnej również SAP HANA hybrydowego na platformie Azure, w którym SAP HANA działa niezwirtualizowanych na serwerach fizycznych. Jednak w tym przewodniku kopia zapasowa SAP HANA Azure dotyczą czystego środowiska platformy Azure, gdzie SAP HANA działa w maszynie Wirtualnej platformy Azure, nie SAP HANA systemem &quot;dużych wystąpień.&quot; Zobacz [omówienie SAP HANA (duże wystąpień) i architektury na platformie Azure](hana-overview-architecture.md) Aby uzyskać więcej informacji o tym rozwiązaniu kopii zapasowej na &quot;dużych wystąpień&quot; oparty na pamięci masowej migawki.

Ogólne informacje o produktach SAP obsługiwane na platformie Azure można znaleźć w [1928533 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1928533).

Trzy poniższe rysunki omówienie SAP HANA opcje tworzenia kopii zapasowej za pomocą natywnej funkcji Azure obecnie oraz wyświetlane trzy możliwe scenariusze tworzenia kopii zapasowej przyszłych. Pokrewne artykuły [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) i [SAP HANA kopii zapasowych oparte na magazynu migawek](sap-hana-backup-storage-snapshots.md) opisano te opcje bardziej szczegółowo w tym zagadnienia dotyczące SAP HANA kopii zapasowych, które są multi terabajtów rozmiaru i wydajności.

![Na poniższym rysunku pokazano dwie możliwości zapisywania bieżący stan maszyny Wirtualnej](media/sap-hana-backup-guide/image001.png)

Poniższy rysunek przedstawia możliwości zapisywania w bieżącym stanie maszyny Wirtualnej za pośrednictwem usługi Azure Backup lub ręcznego migawek dysków maszyny Wirtualnej. Z tej metody, co&#39;było zarządzanie kopiami zapasowymi SAP HANA. Żądania w scenariuszu migawki dysku jest spójności systemu plików i stan dysku spójnych z aplikacją. Temat spójności została szczegółowo opisana w sekcji _spójność danych SAP HANA podejmując magazynu migawek_ dalszej części tego artykułu. Możliwości i ograniczeń związanych z SAP HANA kopii zapasowych usługi Kopia zapasowa Azure również są omówione w dalszej części tego artykułu.

![Na poniższym rysunku pokazano, że opcje pobierania SAP HANA plików kopii zapasowych w ramach maszyny Wirtualnej](media/sap-hana-backup-guide/image002.png)

Na poniższym rysunku pokazano opcje kopii zapasowej pliku SAP HANA wewnątrz maszyny Wirtualnej, a następnie zapisanie jej pliki kopii zapasowej HANA innym else przy użyciu różnych narzędzi. Utworzenie kopii zapasowej HANA wymaga więcej czasu niż rozwiązanie tworzenia kopii zapasowej migawki, ale ma zalety dotyczące integralności i spójność. Bardziej szczegółowe informacje znajdują się w dalszej części tego artykułu.

![Na poniższym rysunku pokazano potencjalnych przyszłych SAP HANA kopii zapasowej scenariusza](media/sap-hana-backup-guide/image003.png)

Na poniższym rysunku przedstawiono potencjalne przyszłych scenariusz tworzenia kopii zapasowej SAP HANA. SAP HANA dozwolone wykonywania kopii zapasowych z replikacji dodatkowej, czy dodać dodatkowe opcje strategii tworzenia kopii zapasowych. Obecnie nie jest możliwe zgodnie z post w witrynie Wiki SAP HANA:

_&quot;Czy jest możliwe do wykonania kopii zapasowych na stronie dodatkowej?_

_Nie, obecnie można tylko pobierają dane i kopii zapasowych na głównej stronie dziennika. Jeśli włączono tworzenie kopii zapasowej dziennika automatyczne po przejęciu na stronie dodatkowej kopie zapasowe dziennika automatycznie zostanie zapisany istnieje.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Zasoby SAP HANA kopii zapasowej

### <a name="sap-hana-backup-documentation"></a>SAP HANA dokumentacji kopii zapasowej

- [Wprowadzenie do programu SAP HANA administracji](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planowanie z kopią zapasową i strategii odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Planowanie kopii zapasowej HANA przy użyciu ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Harmonogram kopii zapasowych danych (SAP HANA Panelu sterowania)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Często zadawane pytania dotyczące SAP HANA kopii zapasowej w [1642148 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Często zadawane pytania dotyczące migawek magazynu i bazy danych SAP HANA w [2039883 Uwaga SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Systemy plików nieodpowiednie sieci kopii zapasowych i odzyskiwania w [1820529 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Dlaczego SAP HANA kopii zapasowej?

Magazyn Azure oferuje dostępność i niezawodność fabrycznej (zobacz [wprowadzenie do usługi Microsoft Azure Storage](../../../storage/common/storage-introduction.md) Aby uzyskać więcej informacji na temat usługi Azure storage).

Minimum dla &quot;kopii zapasowej&quot; jest zależne od umowy SLA platformy Azure, przechowywanie plików danych i dziennika SAP HANA na wirtualnych dyskach twardych platformy Azure dołączonych do serwera SAP HANA maszyny Wirtualnej. Ta metoda obejmuje błędy maszyny Wirtualnej, ale nie potencjalnego uszkodzenia danych SAP HANA i pliki dziennika lub błędów logicznych, takich jak usuwanie danych lub plików przypadkowo. Kopie zapasowe są również wymagane dla zgodności lub z przyczyn prawnych. Innymi słowy zawsze konieczne jest kopii zapasowych SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sprawdzanie poprawności SAP HANA kopii zapasowej
Podczas korzystania z magazynu migawek, zalecane jest uruchomienie przywracania testu w innym systemie. Takie podejście umożliwia upewnij się, że kopia zapasowa jest prawidłowe i wewnętrznych procesów do utworzenia kopii zapasowej i przywracanie działają, zgodnie z oczekiwaniami. Jest to znaczny próg wykluczający lokalnie, jest znacznie łatwiejsze do wykonania w chmurze, zapewniając tymczasowo zasoby niezbędne do tego celu.

Zachowaj pamiętać, że ten proste przywracanie i sprawdzania, czy działa HANA i uruchomiona jest niewystarczająca. Najlepiej, jeśli jeden należy uruchomić sprawdzanie spójności tabeli, aby upewnić się, że przywróconej bazy danych są poprawnie. SAP HANA oferuje kilka rodzajów sprawdzenie spójności z opisem w temacie [1977584 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informacje o sprawdzaniu spójności tabeli można także znaleźć w witrynie internetowej SAP w [tabeli i sprawdzania spójności katalogu](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Standardowy plik kopii zapasowych przywracania testu nie jest konieczne. Istnieją dwa narzędzia SAP HANA pomagających w celu sprawdzenia, których kopia zapasowa może służyć do przywracania: hdbbackupdiag i hdbbackupcheck. Zobacz [ręcznie sprawdzanie, czy odzyskiwanie jest możliwe](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) uzyskać więcej informacji o tych narzędzi.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Zalet i wad HANA tworzenia kopii zapasowej i pamięci masowej migawki

SAP&#39;t dają pierwszeństwo tworzenia kopii zapasowej albo HANA i pamięci masowej migawki. Wyświetlane ich zalet i wad, dzięki czemu jedną można określić, który ma zostać użyty w zależności od sytuacji i technologii dostępnej pamięci (zobacz [planowania Your kopii zapasowej i odzyskiwania strategii](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Na platformie Azure, należy zwrócić uwagę na fakt, że obiektów blob platformy Azure migawki funkcji&#39;spójności systemu plików gwarancji t (zobacz [migawki obiektu blob korzystanie z programu PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Następna sekcja _spójność danych SAP HANA podejmując magazynu migawek_, omówiono pewne kwestie dotyczące tej funkcji.

Ponadto jedna ma Niezrozumienie konsekwencji rozliczeń, podczas pracy często z migawki obiektu blob, zgodnie z opisem w tym artykule: [zrozumienia sposobu migawki Naliczanie opłat](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— go&#39;t tak oczywiste jako przy użyciu wirtualnej platformy Azure dyski.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Spójność danych SAP HANA podejmując pamięci masowej migawki

Zgodność aplikacji i systemu plików jest złożonych problemów podczas wykonywania migawki magazynu. Najprostszym sposobem, aby uniknąć problemów może być zamknięta SAP HANA a może nawet całej maszyny wirtualnej. Zamknięcie systemu może być doable pokaz lub prototypu lub nawet programistycznej, ale nie jest opcją systemu produkcji.

Na platformie Azure, co ma należy pamiętać, że obiektów blob platformy Azure migawki funkcja&#39;spójności systemu plików gwarancji t. Współpracuje jednak za pomocą SAP HANA migawki funkcji, dopóki dotyczy tylko jednego dysku wirtualnego. Ale nawet w przypadku jednego dysku, należy je zaewidencjonować dodatkowe elementy. [SAP 2039883 Uwaga](https://launchpad.support.sap.com/#/notes/2039883) zawiera ważne informacje dotyczące kopii zapasowych SAP HANA za pomocą magazynu migawek. Na przykład informacje o, że w systemie plików XFS jest niezbędne do uruchomienia **xfs\_Zablokuj** przed rozpoczęciem migawkę magazynu w celu zagwarantowania spójności (zobacz [xfs\_freeze(8) — strona man systemu Linux ](https://linux.die.net/man/8/xfs_freeze) szczegółowe informacje na temat **xfs\_Zablokuj**).

Temat spójności staje się nawet trudniejsze w przypadku, gdy system pojedynczy plik obejmuje wiele dysków/woluminów. Na przykład za pomocą mdadm lub LVM i stosowanie. Uwaga SAP, wymienione powyżej stany:

_&quot;Ale należy pamiętać, aby zagwarantować spójności We/Wy podczas tworzenia migawki magazynu dla każdego woluminu danych SAP HANA systemu magazyn, tj. musi ona mieć niepodzielną operację tworzenia migawek woluminu specyficznych dla usług danych SAP HANA.&quot;_

Zakładając, że jest system plików XFS obejmującej cztery dyski wirtualne platformy Azure, w poniższych krokach przedstawiono migawki spójne, która reprezentuje obszar danych HANA:

- Przygotowanie HANA migawki
- Zablokuj systemu plików (na przykład użyć **xfs\_Zablokuj**)
- Utwórz wszystkie migawki obiektu blob konieczne na platformie Azure
- Odblokuj systemu plików
- Potwierdź HANA migawki

Zaleca się używanie powyższą procedurę we wszystkich przypadkach jako dla bezpieczeństwa, niezależnie od tego, który system plików. Lub, jeśli jest to jeden dysk lub rozkładanie za pośrednictwem mdadm lub LVM na wielu dyskach.

Należy potwierdzić migawki HANA. Ze względu na &quot;kopii przy zapisie,&quot; SAP HANA nie mogą wymagać dodatkowego miejsca na dysku w tym trybie przygotowania migawki. Go&#39;także nie można rozpocząć nowej kopii zapasowych, dopóki nie został potwierdzony migawki SAP HANA.

Usługa Kopia zapasowa Azure korzysta z rozszerzeń maszyny Wirtualnej Azure automatyzującą spójności systemu plików. Te rozszerzenia maszyny Wirtualnej nie są dostępne do użycia autonomicznego. Jeden nadal ma zarządzać spójności SAP HANA. Zapoznaj się z artykułem pokrewne [SAP HANA Azure Backup na poziomie plików](sap-hana-backup-file-level.md) Aby uzyskać więcej informacji.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA strategii harmonogramu wykonywania kopii zapasowych

Artykuł SAP HANA [planowania Your kopii zapasowej i odzyskiwania strategii](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) stanowi podstawowy plan, aby tworzyć kopie zapasowe:

- Magazynu migawek (dziennie)
- Kopia zapasowa pełnych danych przy użyciu formatu pliku lub bacint (raz w tygodniu)
- Kopie zapasowe dziennika automatyczne

Opcjonalnie co można przejść całkowicie bez migawek magazynu; mogły one zostać zastąpione przez kopie zapasowe delta HANA, na przykład przyrostowa lub różnicowej kopii zapasowych (zobacz [różnicowe kopie zapasowe](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

Przewodnik administracji HANA zawiera przykładową listę. Sugeruje, że jeden odzyskanie stanu SAP HANA do określonego punktu w czasie przy użyciu następującej kopii zapasowych:

1. Pełne kopie zapasowe
2. Różnicowej kopii zapasowej
3. Przyrostowa kopia zapasowa 1
4. Przyrostowa kopia zapasowa 2
5. Kopie zapasowe dziennika

Dotyczące dokładnego harmonogramu, kiedy i jak często określonego typu kopii zapasowej powinno się zdarzyć, nie jest możliwe zapewniają ogólne wytyczne — jest zbyt właściwe dla klienta i zależy od liczby zmian danych występują w systemie. Co podstawowa z strony SAP, które są widoczne jako ogólne wskazówki, zaleca się jeden HANA pełnej upewnij w kopii zapasowej raz w tygodniu.
Dotyczące kopii zapasowych dziennika, zobacz dokumentację programu SAP HANA [kopie zapasowe dziennika](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP zaleca, wykonanie niektórych celów katalog kopii zapasowej, aby zapobiec rośnie nieograniczonej (zobacz [Housekeeping katalogu kopii zapasowej i przechowywania kopii zapasowych](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Pliki konfiguracji SAP HANA

Jak już wspomniano w sekcji FAQ w [1642148 Uwaga SAP](https://launchpad.support.sap.com/#/notes/1642148), pliki konfiguracji SAP HANA nie są częścią standardowej HANA kopii zapasowej. Nie są one niezbędne do przywrócenia systemu. Konfiguracja HANA można zmienić ręcznie po przywróceniu. W przypadku, gdy jeden chcesz pobrać tej samej konfiguracji niestandardowej podczas procesu przywracania, należy wykonać kopię zapasową plików konfiguracji HANA oddzielnie.

Jeśli standardowa HANA kopie zapasowe mają być dedykowanego systemu HANA pliku kopii zapasowej, jeden można również skopiować pliki konfiguracji do tej samej kopii zapasowej systemu plików, a następnie skopiuj wszystko razem do miejsca docelowego końcowego magazynu, takich jak chłodnej magazynu obiektów blob.

### <a name="sap-hana-cockpit"></a>SAP HANA w Panelu sterowania

Panel sterowania programu SAP HANA oferuje możliwości monitorowania i zarządzania nimi SAP HANA za pośrednictwem przeglądarki. Umożliwia również obsługę kopii zapasowych SAP HANA i dlatego mogą być używane jako alternatywę dla SAP HANA Studio i ABAP DBACOCKPIT (zobacz [SAP HANA w Panelu sterowania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) Aby uzyskać więcej informacji).

![Na poniższym rysunku pokazano ekranu administracyjnego bazy danych SAP HANA Panelu sterowania](media/sap-hana-backup-guide/image004.png)

Tej ilustracji przedstawiono ekranu administracyjnego bazy danych SAP HANA Panelu sterowania i tworzenia kopii zapasowej kafelka po lewej stronie. Wyświetlanie fragmentu kopii zapasowej wymaga odpowiednich uprawnień użytkowników dla konta logowania.

![Kopie zapasowe mogą być monitorowane w Panelu sterowania programu SAP HANA, gdy są one uruchomione](media/sap-hana-backup-guide/image005.png)

Kopie zapasowe mogą być monitorowane w Panelu sterowania programu SAP HANA podczas są uruchomione, a po zakończeniu tworzenia kopii zapasowej szczegóły są dostępne.

![Przykład za pomocą przeglądarki Firefox na SLES 12 maszyny Wirtualnej platformy Azure przy użyciu pulpitu Gnome](media/sap-hana-backup-guide/image006.png)

Wprowadzono poprzednich zrzutach ekranu z maszyny Wirtualnej systemu Windows Azure. Ten zestaw jest na przykład za pomocą przeglądarki Firefox na SLES 12 maszyny Wirtualnej platformy Azure przy użyciu Gnome pulpitu. Pokazuje opcję, aby zdefiniować SAP HANA harmonogramy tworzenia kopii zapasowej w Panelu sterowania programu SAP HANA. Zgodnie z jedną można również sprawdzić, sugeruje daty/godziny jako prefiksu plików kopii zapasowej. W systemie SAP HANA Studio jest domyślny prefiks &quot;COMPLETE\_danych\_kopii zapasowej&quot; podczas wykonywania kopii zapasowej całego pliku. Zaleca się użycie unikatowy prefiks.

### <a name="sap-hana-backup-encryption"></a>SAP HANA szyfrowania kopii zapasowych

SAP HANA oferuje szyfrowania danych i dziennika. Jeśli nie są zaszyfrowane SAP HANA danych i dziennika, następnie kopii zapasowych również nie są szyfrowane. Jest klientowi korzystanie z jakiegoś rozwiązań innych firm do szyfrowania kopii zapasowych SAP HANA. Zobacz [danych i szyfrowania woluminu dziennika](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) Aby dowiedzieć się więcej o szyfrowaniu SAP HANA.

W systemie Microsoft Azure klient może szyfrowanie za pomocą funkcji szyfrowania maszyn wirtualnych IaaS. Na przykład jeden można użyć dedykowanego dysków z danymi podłączona do maszyny Wirtualnej, które są używane do przechowywania kopii zapasowych SAP HANA, a następnie utwórz kopie tych dysków.

Usługa Kopia zapasowa Azure może obsługiwać maszyny wirtualne/dyskach zaszyfrowanych (zobacz [jak wykonać kopię zapasową i przywrócić szyfrowane maszyn wirtualnych w usłudze Kopia zapasowa Azure](../../../backup/backup-azure-vms-encryption.md)).

Inną opcją będzie obsługa maszyny Wirtualnej SAP HANA i jego dysków bez szyfrowania i przechowywania plików kopii zapasowych SAP HANA na koncie magazynu, dla którego włączono szyfrowania (zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Ustawienia testu

### <a name="test-virtual-machine-on-azure"></a>Testowej maszyny wirtualnej na platformie Azure

Instalacja SAP HANA w maszynie Wirtualnej platformy Azure GS5 została użyta dla następujących testów wykonywania kopii zapasowej i przywracania.

![Na poniższym rysunku pokazano część portalu Azure przeglądzie HANA testowej maszyny Wirtualnej](media/sap-hana-backup-guide/image007.png)

Na poniższym rysunku pokazano część portalu Azure przeglądzie HANA testowej maszyny Wirtualnej.

### <a name="test-backup-size"></a>Rozmiar kopii zapasowej testu

![Tę wartość została pobrana z konsoli programu kopii zapasowej w HANA Studio i przedstawia rozmiar pliku kopii zapasowej 229 GB dla serwera indeksu HANA](media/sap-hana-backup-guide/image008.png)

Fikcyjny tabeli został wypełniony z danymi w celu pobrania łączna ilość danych kopii zapasowej rozmiar ponad 200 GB do uzyskania prawdziwe dane dotyczące wydajności. Wartość została pobrana z konsoli programu kopii zapasowej w HANA Studio i przedstawia rozmiar pliku kopii zapasowej 229 GB dla serwera indeksu HANA. Dla testów użyto domyślnego prefiks kopii zapasowej "COMPLETE_DATA_BACKUP" w SAP HANA Studio. W systemach rzeczywistej produkcji lepszym rozwiązaniem prefiks powinien być zdefiniowany. Panel sterowania programu SAP HANA sugeruje daty/godziny.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testowanie narzędzia do kopiowania plików bezpośrednio do magazynu Azure

Aby przenieść pliki kopii zapasowej SAP HANA bezpośrednio do magazynu obiektów blob platformy Azure lub udziały plików platformy Azure, narzędzie blobxfer użyto ponieważ obsługuje ona zarówno elementy docelowe i mogą łatwo integrować do skryptów automatyzacji z powodu jego interfejsu wiersza polecenia. Narzędzie blobxfer jest dostępne na [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Szacowanie rozmiaru kopii zapasowej testu

Jest ważne oszacować rozmiar kopii zapasowej programu SAP HANA. Ta Szacowana pomaga zwiększyć wydajność, definiując rozmiar pliku kopii zapasowej maksymalną liczbę plików kopii zapasowej z powodu równoległości podczas kopiowania plików. (Te szczegóły opisano szczegółowo w dalszej części tego artykułu). Jeden należy również postanowić, czy należy wykonać pełną kopię zapasową lub różnicowej kopii zapasowej (przyrostowe lub różnicowa).

Na szczęście istnieje prostych instrukcji SQL, która szacowany rozmiar kopii zapasowej plików: **wybierz \* z M\_kopii zapasowej\_rozmiar\_uzyskać szacunkowe wartości** (zobacz [szacowania Miejsce wymagane w systemie plików do tworzenia kopii zapasowych danych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Dane wyjściowe w tej instrukcji SQL odpowiada prawie dokładnie rzeczywisty rozmiar kopii zapasowej danych na dysku](media/sap-hana-backup-guide/image009.png)

Dla systemu testowego danych wyjściowych instrukcji SQL odpowiada prawie dokładnie rzeczywisty rozmiar kopii zapasowej danych na dysku.

### <a name="test-hana-backup-file-size"></a>Rozmiar pliku kopii zapasowej HANA testu

![Konsola kopii zapasowej HANA Studio umożliwia jedna, aby ograniczyć maksymalny rozmiar plików HANA kopii zapasowej](media/sap-hana-backup-guide/image010.png)

Konsola kopii zapasowej HANA Studio pozwala jedna, aby ograniczyć maksymalny rozmiar plików HANA kopii zapasowej. W środowisku próbki tej funkcji umożliwia uzyskanie kilka mniejszych plików kopii zapasowej zamiast jeden plik kopii zapasowej 230 GB. Pliki mają mniejszy rozmiar, ma znaczący wpływ na wydajność (zobacz powiązanego artykułu [SAP HANA Azure Backup na poziomie plików](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Podsumowanie

Oparte na wynikach testu, który w poniższych tabelach przedstawiono zalet i wad rozwiązania, aby utworzyć kopię zapasową bazy danych SAP HANA uruchomionych na maszynach wirtualnych platformy Azure.

**Tworzenie kopii zapasowej SAP HANA systemu plików, a następnie skopiuj pliki kopii zapasowej do miejsca docelowego kopii zapasowej**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Zachowaj kopie zapasowe HANA dysków maszyny Wirtualnej                      |Nie działań na dodatkowe Zarządzanie     |Eats miejsca na dysku lokalnym maszyny Wirtualnej           |
|Narzędzie Blobxfer, aby skopiować pliki kopii zapasowej do magazynu obiektów blob |Równoległość można skopiować wielu plików decyzja o korzystaniu z magazynu obiektów blob chłodnych | Konserwacja dodatkowe narzędzia i skrypty niestandardowe | 
|Kopiowanie obiektów blob za pośrednictwem programu Powershell lub interfejsu wiersza polecenia                    |Dodatkowe narzędzia niezbędne, można osiągnąć za pomocą programu Azure Powershell lub interfejsu wiersza polecenia |ręczny proces, klient ma obsługę skryptów i zarządzania skopiowane obiekty BLOB do odtworzenia|
|Skopiuj do udziału NFS                                  |Przetwarzanie końcowe kopii zapasowej plików na innych maszyn wirtualnych bez wpływu na serwerze HANA|Powolne proces kopiowania|
|Kopiuj Blobxfer do usługi plików Azure                |Nie będzie jeść miejsca na dyskach lokalnych maszyn wirtualnych|Nie bezpośrednio zapisać pomocy technicznej przez HANA kopii zapasowej, ograniczenie rozmiaru udziału plików dla 5 TB|
|Agent usługi Kopia zapasowa Azure                                 | Byłoby preferowaną rozwiązania         | Obecnie nie jest dostępna w systemie Linux    |



**Kopia zapasowa SAP HANA oparty na pamięci masowej migawki**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Usługa Kopia zapasowa Azure                               | Zezwala na podstawie kopii zapasowej maszyny Wirtualnej na migawki obiektu blob | Korzystając z nie przywracania na poziomie plików, wymaga utworzenia nowej maszyny Wirtualnej dla procesu przywracania, który następnie pociąga za sobą konieczność klucz licencji SAP HANA|
|Migawki obiektu blob ręcznie                              | Możliwość tworzenia i przywracania określone dyski maszyny Wirtualnej bez zmieniania Unikatowy identyfikator maszyny Wirtualnej|Wszystkie czynności ręcznych, które mają być wykonane przez klienta|

## <a name="next-steps"></a>Kolejne kroki
* [SAP HANA kopia zapasowa Azure na poziomie plików](sap-hana-backup-file-level.md) zawiera opis opcji tworzenia kopii zapasowych opartych na plikach.
* [Kopia zapasowa SAP HANA oparte na magazynu migawek](sap-hana-backup-storage-snapshots.md) zawiera opis opcji tworzenia kopii zapasowej na podstawie migawki magazynu.
* Aby dowiedzieć się jak ustanowić wysokiej dostępności i planu odzyskiwania po awarii programu SAP HANA na platformie Azure (wystąpienia duże), zobacz [SAP HANA (duże wystąpień) wysokiej dostępności i odzyskiwania po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
