---
title: Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines | Microsoft Docs
description: Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA oferuje dwie główne możliwości tworzenia kopii zapasowych dla SAP HANA na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 8de83cbb7060e6ca5390720a4a241be71bb9dc92
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617425"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na platformie Azure Virtual Machines

## <a name="getting-started"></a>Wprowadzenie

Przewodnik tworzenia kopii zapasowych dla SAP HANA uruchomionych na maszynach wirtualnych platformy Azure zawiera tylko opis tematów dotyczących platformy Azure. Aby uzyskać ogólne SAP HANA elementów powiązanych z kopią zapasową, zapoznaj się z dokumentacją SAP HANA ( _SAP HANA Zobacz dokumentację dotyczącą tworzenia kopii zapasowych_ w dalszej części tego artykułu

Ten artykuł dotyczy dwóch głównych możliwości tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure:

- Usługa HANA kopia zapasowa w systemie plików na maszynie wirtualnej z systemem Linux (zobacz [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md))
- Tworzenie kopii zapasowych HANA na podstawie migawek magazynu przy użyciu funkcji migawek obiektów BLOB usługi Azure Storage ręcznie lub Azure Backup usługi (zobacz [SAP HANA Backup na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md))

SAP HANA oferuje interfejs API tworzenia kopii zapasowych, dzięki któremu narzędzia do tworzenia kopii zapasowych innych firm mogą integrować bezpośrednio z SAP HANA. (To nie jest w zakresie tego przewodnika). Nie ma żadnej bezpośredniej integracji SAP HANA z usługą Azure Backup dostępną obecnie na podstawie tego interfejsu API.

SAP HANA jest oficjalnie obsługiwana w różnych typach maszyn wirtualnych platformy Azure, takich jak Azure M-Series. Aby uzyskać pełną listę SAP HANA certyfikowane maszyny wirtualne platformy Azure, zapoznaj się z tematem [Znajdowanie certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ten artykuł zostanie zaktualizowany w miarę dostępności nowych ofert dla SAP HANA na platformie Azure.

Istnieje również SAP HANA rozwiązanie hybrydowe dostępne na platformie Azure, w którym SAP HANA nie są zwirtualizowane na serwerach fizycznych. SAP HANA ten przewodnik dotyczący usługi Azure Backup obejmuje jednak czyste środowisko platformy Azure, w którym SAP HANA działa na maszynie wirtualnej platformy Azure, a nie SAP HANA uruchomione na &quot;dużych wystąpieniach. Aby uzyskać więcej informacji&quot; &quot;na temat tego rozwiązania do tworzenia kopii zapasowych na platformie Azure, zapoznaj się z tematem [SAP HANA (duże wystąpienia) — omówienie i architektura w](hana-overview-architecture.md) systemie&quot;

Ogólne informacje o produktach SAP obsługiwanych na platformie Azure można znaleźć w temacie [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Poniższe trzy ilustracje zawierają Omówienie opcji tworzenia kopii zapasowych SAP HANA przy użyciu natywnych możliwości platformy Azure, a także pokazują trzy potencjalne scenariusze tworzenia kopii zapasowych. Powiązane artykuły [SAP HANA Azure Backup na poziomie plików](sap-hana-backup-file-level.md) i [SAP HANA kopii zapasowej na podstawie migawek magazynu](sap-hana-backup-storage-snapshots.md) opisują te opcje bardziej szczegółowo, w tym informacje o rozmiarach i wydajności dla SAP HANA kopii zapasowych, które są w rozmiarze wielu terabajtów.

![Ten rysunek przedstawia dwie możliwości zapisywania bieżącego stanu maszyny wirtualnej](media/sap-hana-backup-guide/image001.png)

Na tym rysunku przedstawiono możliwość zapisania bieżącego stanu maszyny wirtualnej za pośrednictwem usługi Azure Backup lub ręcznej migawki dysków maszyn wirtualnych. W tym podejściu jeden z&#39;nich musi zarządzać kopiami zapasowymi SAP HANA. Wyzwaniem scenariusza tworzenia migawek dysków jest spójność systemu plików i stan dysku spójnego z aplikacją. Temat spójności został omówiony w sekcji _SAP HANA spójność danych podczas tworzenia migawek magazynu_ w dalszej części tego artykułu. Możliwości i ograniczenia usługi Azure Backup związanej z kopiami zapasowymi SAP HANA zostały również omówione w dalszej części tego artykułu.

![Na tym rysunku przedstawiono opcje tworzenia kopii zapasowej pliku SAP HANA w ramach maszyny wirtualnej](media/sap-hana-backup-guide/image002.png)

Na tym rysunku przedstawiono opcje tworzenia kopii zapasowej plików SAP HANA w ramach maszyny wirtualnej, a następnie przechowywania plików kopii zapasowych HANA w innym miejscu przy użyciu różnych narzędzi. Tworzenie kopii zapasowej HANA wymaga więcej czasu niż rozwiązanie do tworzenia kopii zapasowych oparte na migawce, ale ma zalety dotyczące integralności i spójności. Więcej szczegółów znajduje się w dalszej części tego artykułu.

![Na rysunku przedstawiono potencjalne przyszłe SAP HANA scenariusz tworzenia kopii zapasowej](media/sap-hana-backup-guide/image003.png)

Na rysunku przedstawiono potencjalne przyszłe scenariusze SAP HANA tworzenia kopii zapasowej. Jeśli SAP HANA zezwalać na tworzenie kopii zapasowych z poziomu pomocniczej replikacji, zostaną dodane dodatkowe opcje dla strategii tworzenia kopii zapasowych. Obecnie nie jest to możliwe zgodnie z wpisem w SAP HANA wiki:

_&quot;można tworzyć kopie zapasowe po stronie dodatkowej?_

_Nie. obecnie można wykonywać kopie zapasowe danych i dzienników tylko po stronie głównej. Jeśli automatyczna kopia zapasowa dziennika jest włączona, po przejściu na stronę dodatkową kopie zapasowe dziennika zostaną automatycznie zapisaniu.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Zasoby SAP dla kopii zapasowych HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentacja kopii zapasowej SAP HANA

- [Wprowadzenie do administrowania SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Zaplanuj tworzenie kopii zapasowych HANA przy użyciu ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Planowanie kopii zapasowych danych (Panel sterowania SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Często zadawane pytania dotyczące SAP HANA kopii zapasowej w programie [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Często zadawane pytania dotyczące SAP HANA baz danych i migawek magazynu w programie [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nieodpowiednie systemy plików sieciowych na potrzeby tworzenia kopii zapasowych i odzyskiwania w programie [SAP uwaga 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Dlaczego SAP HANA kopii zapasowej?

Usługa Azure Storage zapewnia dostępność i niezawodność (zobacz [wprowadzenie do Microsoft Azure Storage](../../../storage/common/storage-introduction.md) , aby uzyskać więcej informacji na temat usługi Azure Storage).

Minimum dla &quot;kopii zapasowej&quot; polega na tym, że usługa Azure umowy SLA, SAP HANA przechowywanie plików danych i dzienników w wirtualnych dyskach twardych platformy Azure dołączonych do maszyny wirtualnej serwera SAP HANA. Takie podejście obejmuje awarie maszyn wirtualnych, ale nie potencjalne uszkodzenie danych SAP HANA i plików dziennika, a także błędy logiczne, takie jak usuwanie danych i plików. Kopie zapasowe są również wymagane w celu zapewnienia zgodności lub z przyczyn prawnych. W krótkim momencie istnieje konieczność tworzenia kopii zapasowych SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sprawdzanie poprawności SAP HANA kopii zapasowej
W przypadku korzystania z migawek magazynu zaleca się uruchomienie przywracania testowego w innym systemie. Takie podejście umożliwia zapewnienie, że kopia zapasowa jest poprawna, a wewnętrzne procesy tworzenia kopii zapasowych i przywracania działają zgodnie z oczekiwaniami. Chociaż jest to znaczący utrudnienie w środowisku lokalnym, znacznie łatwiej jest wykonać w chmurze, zapewniając w ten sposób tymczasowe zasoby.

Należy pamiętać, że wykonywanie prostych operacji przywracania i sprawdzania, czy platformy HANA nie działają i nie są wystarczające. W idealnym przypadku należy uruchomić sprawdzanie spójności tabeli, aby upewnić się, że przywrócona baza danych jest w prawidłowym obszarze. SAP HANA oferuje kilka rodzajów kontroli spójności opisanych w artykule [SAP uwaga 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informacje o sprawdzaniu spójności tabeli można także znaleźć w witrynie sieci Web SAP w obszarze [Sprawdzanie spójności w tabelach i wykazie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

W przypadku kopii zapasowych plików standardowych przywracanie testowe nie jest konieczne. Istnieją dwa SAP HANA narzędzia, które ułatwiają sprawdzenie, która kopia zapasowa może być używana do przywracania: hdbbackupdiag i hdbbackupcheck. Zobacz [ręczne sprawdzanie, czy jest możliwe odzyskanie,](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) Aby uzyskać więcej informacji o tych narzędziach.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Zalety i wady tworzenia kopii zapasowych HANA i migawki magazynu

&#39;System SAP nie nadaje preferencji do tworzenia kopii ZAPASowych Hana i magazynu. Zawiera listę ich informatyków i wad, dlatego można określić, która z nich ma być używana w zależności od sytuacji i dostępnej technologii magazynowania (zobacz [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Na platformie Azure należy pamiętać, że funkcja&#39;migawek obiektów blob platformy Azure nie gwarantuje spójności systemu plików (zobacz [Używanie migawek obiektów BLOB w programie PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). W następnej sekcji, _SAP HANA spójności danych podczas tworzenia migawek magazynu_, omówiono niektóre zagadnienia dotyczące tej funkcji.

Ponadto, jeden musi zrozumieć wpływ rozliczeń podczas pracy często z migawkami obiektów blob, zgodnie z opisem w tym artykule: zrozumienie, w [jaki sposób migawki naliczane są opłaty](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— jest&#39;t tak samo jak w przypadku korzystania z usługi Azure Virtual Disks.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA spójności danych podczas tworzenia migawek magazynu

Spójności systemu plików i aplikacji jest skomplikowanym problemem podczas tworzenia migawek magazynu. Najprostszym sposobem uniknięcia problemów jest zamknięcie SAP HANA lub nawet całej maszyny wirtualnej. Zamknięcie może być doable z pokazem lub prototypem, a nawet systemem programistycznym, ale nie jest opcją dla systemu produkcyjnego.

Na platformie Azure należy pamiętać, że funkcja&#39;migawek obiektów blob platformy Azure nie gwarantuje spójności systemu plików. Działa ona jednak prawidłowo przy użyciu funkcji migawek SAP HANA, o ile istnieje tylko jeden dysk wirtualny. Jednak nawet w przypadku jednego dysku należy sprawdzić dodatkowe elementy. [Uwaga dotycząca oprogramowania SAP 2039883](https://launchpad.support.sap.com/#/notes/2039883) zawiera ważne informacje dotyczące SAP HANA kopii zapasowych za pośrednictwem migawek magazynu. Na przykład informacje o tym, że w systemie plików XFS, należy uruchomić **XFS\_zamrozić** przed rozpoczęciem migawki magazynu w celu zagwarantowania spójności (zobacz [XFS\_Zablokuj (8)-Linux Man](https://linux.die.net/man/8/xfs_freeze) , aby uzyskać więcej informacji na temat **XFS\_Zablokuj**.

Temat spójności jest jeszcze bardziej trudniejszy w przypadku, gdy pojedynczy system plików obejmuje wiele dysków/woluminów. Na przykład przy użyciu mdadm lub LVM i rozłożenia. Uwaga dotycząca oprogramowania SAP wymienionego powyżej stanów:

_&quot;jednak należy pamiętać, że system magazynu musi zagwarantować spójność we/wy podczas tworzenia migawki magazynu dla SAP HANA woluminu danych, tj. snapshotting SAP HANAego woluminu danych określonego dla usługi musi być operacją niepodzielną.&quot;_

Zakładając, że istnieje system plików XFS z czterema dyskami wirtualnymi platformy Azure, następujące kroki zapewniają spójną migawkę, która reprezentuje obszar danych HANA:

- Przygotowanie migawki platformy HANA
- Zablokuj system plików (na przykład użyj **xfs\_Zablokuj**)
- Utwórz wszystkie wymagane migawki obiektów BLOB na platformie Azure
- Odblokuj system plików
- Potwierdź migawkę HANA

Zalecenie polega na użyciu procedury powyżej we wszystkich przypadkach, gdy ma być po stronie bezpieczeństwa, niezależnie od tego, który system plików. Lub jeśli jest to pojedynczy dysk lub rozłożenie, za pośrednictwem mdadm lub LVM na wielu dyskach.

Ważne jest, aby potwierdzić migawkę HANA. Ze względu na &quot;kopiowania przy zapisie&quot; SAP HANA może nie wymagać dodatkowego miejsca na dysku w tym trybie przygotowywania migawek. &#39;Nie można również uruchomić nowych kopii zapasowych, dopóki migawka SAP HANA nie zostanie potwierdzona.

Usługa Azure Backup używa rozszerzeń maszyny wirtualnej platformy Azure, aby zachować spójność systemu plików. Te rozszerzenia maszyn wirtualnych nie są dostępne dla autonomicznego użycia. Nadal istnieje konieczność zarządzania spójnością SAP HANA. Aby uzyskać więcej informacji, zapoznaj się z artykułem poświęconym [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md) .

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategia planowania tworzenia kopii zapasowych SAP HANA

W SAP HANA artykule [Planowanie strategii tworzenia kopii zapasowych i odzyskiwania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) stan podstawowego planu wykonywania kopii zapasowych:

- Migawka magazynu (codziennie)
- Wykonaj kopię zapasową danych przy użyciu formatu pliku lub bacint (raz w tygodniu)
- Automatyczne kopie zapasowe dzienników

Opcjonalnie można całkowicie zakończyć pracę bez migawek magazynu; mogą zostać zastąpione przez różnicowe kopie zapasowe HANA, takie jak przyrostowe lub różnicowe kopie zapasowe (zobacz przyrostowe [kopie zapasowe](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm))

Przewodnik administrowania HANA zawiera przykładową listę. Sugeruje ona, że jeden odzyskanie SAP HANA do określonego punktu w czasie przy użyciu następującej sekwencji kopii zapasowych:

1. Pełna kopia zapasowa danych
2. Różnicowa kopia zapasowa
3. Przyrostowa kopia zapasowa 1
4. Przyrostowa kopia zapasowa 2
5. Kopie zapasowe dzienników

W odniesieniu do dokładnego harmonogramu, kiedy i jak często ma być określony typ kopii zapasowej, nie jest możliwe ogólne wytyczne — jest to zbyt specyficzne dla klienta i zależy od liczby zmian danych w systemie. Jednym z podstawowych rekomendacji po stronie SAP, które mogą być postrzegane jako ogólne wskazówki, jest jednokrotne utworzenie kopii zapasowej HANA.
W odniesieniu do kopii zapasowych [dzienników zapoznaj się z dokumentacją SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)Backup.

System SAP zaleca również wykonywanie niektórych dla gospodarstw domowych wykazu kopii zapasowych, aby zachować jego nieograniczoną liczbę (zobacz [dla gospodarstw domowych dla wykazu kopii zapasowych i magazynu kopii zapasowych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Pliki konfiguracji SAP HANA

Zgodnie z opisem w sekcji często zadawane pytania w programie [SAP uwaga 1642148](https://launchpad.support.sap.com/#/notes/1642148)pliki konfiguracji SAP HANA nie są częścią standardowej kopii zapasowej Hana. Przywracanie systemu nie jest konieczne. Konfigurację platformy HANA można zmienić ręcznie po przywróceniu. Jeśli jedna z nich chce uzyskać tę samą konfigurację niestandardową podczas procesu przywracania, należy wykonać kopię zapasową plików konfiguracji HANA osobno.

Jeśli standardowe kopie zapasowe HANA przechodzą do dedykowanego systemu plików z kopii zapasowej HANA, jeden może także skopiować pliki konfiguracyjne do tego samego pliku kopii zapasowej, a następnie skopiować wszystko do ostatecznego miejsca docelowego magazynu, takiego jak chłodny magazyn obiektów BLOB.

### <a name="sap-hana-cockpit"></a>Panel sterowania SAP HANA

SAP HANA Panel sterowania oferuje możliwość monitorowania SAP HANA i zarządzania nimi za pośrednictwem przeglądarki. Umożliwia również obsługę kopii zapasowych SAP HANA, dlatego mogą być używane jako alternatywa dla SAP HANA Studio i ABAP DBACOCKPIT (zobacz [Panel sterowania SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) , aby uzyskać więcej informacji).

![Ten rysunek przedstawia ekran administrowania bazą danych SAP HANA panelu sterowania](media/sap-hana-backup-guide/image004.png)

Ten rysunek przedstawia ekran administrowania bazą danych SAP HANA panelu sterowania oraz kafelek kopia zapasowa po lewej stronie. Wyświetlanie kafelka kopii zapasowej wymaga odpowiednich uprawnień użytkownika dla konta logowania.

![Kopie zapasowe można monitorować w SAP HANA panelu sterowania, gdy są one wykonywane](media/sap-hana-backup-guide/image005.png)

Kopie zapasowe mogą być monitorowane w SAP HANA panelu sterowania, a po zakończeniu są dostępne wszystkie szczegóły kopii zapasowej.

![Przykład korzystania z programu Firefox na maszynie wirtualnej Azure SLES 12 z pulpitem GNOME](media/sap-hana-backup-guide/image006.png)

Poprzednie zrzuty ekranu zostały wykonane z maszyny wirtualnej z systemem Windows Azure. Jest to przykład za pomocą przeglądarki Firefox na maszynie wirtualnej Azure SLES 12 z pulpitem GNOME. Pokazuje on opcję definiowania SAP HANA harmonogramów kopii zapasowych w panelu sterowania SAP HANA. Jak również można zobaczyć, sugeruje datę i godzinę jako prefiks plików kopii zapasowej. W programie SAP HANA Studio domyślny prefiks jest &quot;kompletny\_dane\_kopia ZAPASowa&quot; podczas wykonywania pełnej kopii zapasowej plików. Zalecane jest używanie unikatowego prefiksu.

### <a name="sap-hana-backup-encryption"></a>SAP HANA szyfrowanie kopii zapasowej

SAP HANA oferuje szyfrowanie danych i dziennika. Jeśli SAP HANA dane i dziennik nie są szyfrowane, kopie zapasowe również nie są szyfrowane. W celu zaszyfrowania SAP HANA kopii zapasowych klienci mogą korzystać z innej formy rozwiązań innych firm. Zapoznaj się z artykułem [szyfrowanie woluminów danych i dziennika](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) , aby dowiedzieć się więcej na temat szyfrowania SAP HANA.

Na Microsoft Azure klient może użyć funkcji szyfrowania maszyny wirtualnej IaaS do zaszyfrowania. Na przykład jedna z nich może korzystać z dedykowanych dysków danych podłączonych do maszyny wirtualnej, które są używane do przechowywania SAP HANA kopii zapasowych, a następnie tworzyć kopie tych dysków.

Usługa Azure Backup może obsługiwać zaszyfrowane maszyny wirtualne/dyski (zobacz [jak utworzyć kopię zapasową i przywrócić zaszyfrowane maszyny wirtualne za pomocą Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Kolejną opcją jest zachowanie SAP HANA maszyny wirtualnej i jej dysków bez szyfrowania i przechowywanie SAP HANA plików kopii zapasowej na koncie magazynu, dla którego włączono szyfrowanie (zobacz [Azure szyfrowanie usługi Storage w przypadku danych przechowywanych w spoczynku](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Konfiguracja testu

### <a name="test-virtual-machine-on-azure"></a>Przetestuj maszynę wirtualną na platformie Azure

Aby można było wykonać nasze testy, w ramach następujących testów kopii zapasowej/przywracania użyto instalacji SAP HANAej na maszynie wirtualnej GS5 platformy Azure. Zasady są takie same, jak w przypadku maszyn wirtualnych z serii M.

![Ten rysunek przedstawia część Azure Portal przegląd dla testowej maszyny wirtualnej platformy HANA](media/sap-hana-backup-guide/image007.png)

Ten rysunek przedstawia część Azure Portal przegląd dla testowej maszyny wirtualnej platformy HANA.

### <a name="test-backup-size"></a>Rozmiar kopii zapasowej testu

![Ten rysunek został pobrany z konsoli tworzenia kopii zapasowych w programie HANA Studio i przedstawia rozmiar pliku kopii zapasowej wynoszący 229 GB dla serwera indeksu HANA](media/sap-hana-backup-guide/image008.png)

Fikcyjna tabela została uzupełniona o dane, aby uzyskać łączny rozmiar kopii zapasowej danych wynoszący ponad 200 GB, aby uzyskać realistyczne dane wydajności. Rysunek został pobrany z konsoli tworzenia kopii zapasowych w programie HANA Studio i przedstawia rozmiar pliku kopii zapasowej wynoszący 229 GB dla serwera indeksów platformy HANA. Dla testów użyto domyślnego prefiksu kopii zapasowej "COMPLETE_DATA_BACKUP" w programie SAP HANA Studio. W rzeczywistych systemach produkcyjnych należy zdefiniować bardziej przydatny prefiks. Panel sterowania SAP HANAą proponuje datę/godzinę.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Narzędzie testowe do kopiowania plików bezpośrednio do usługi Azure Storage

Aby przetransferować SAP HANA pliki kopii zapasowej bezpośrednio do usługi Azure Blob Storage lub udziałów plików platformy Azure, użyto narzędzia blobxfer, ponieważ obsługuje on oba elementy docelowe i może być łatwo zintegrowane ze skryptami automatyzacji ze względu na interfejs wiersza polecenia. Narzędzie blobxfer jest dostępne w witrynie [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Szacowanie rozmiaru kopii zapasowej testu

Ważne jest, aby oszacować rozmiar kopii zapasowej SAP HANA. To oszacowanie ułatwia zwiększenie wydajności przez zdefiniowanie maksymalnego rozmiaru pliku kopii zapasowej dla wielu plików kopii zapasowej z powodu równoległości podczas kopiowania plików. (Te szczegóły zostały wyjaśnione w dalszej części tego artykułu). Jeden z nich musi również decydować, czy należy wykonać pełną kopię zapasową, czy przyrostową kopię zapasową (przyrostową czy różnicową)

Na szczęście istnieje prosta instrukcja SQL, która szacuje rozmiar plików kopii zapasowej: **wybierz \* z M\_kopia zapasowa\_rozmiar\_oszacowania** (zobacz [oszacowania miejsca wymaganego w systemie plików dla kopii zapasowej danych](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Dane wyjściowe tej instrukcji SQL dopasowują niemal dokładnie rzeczywisty rozmiar pełnej kopii zapasowej danych na dysku](media/sap-hana-backup-guide/image009.png)

W przypadku systemu testowego dane wyjściowe tej instrukcji SQL dopasowują niemal dokładnie rzeczywisty rozmiar pełnej kopii zapasowej danych na dysku.

### <a name="test-hana-backup-file-size"></a>Testowanie rozmiaru pliku kopii zapasowej HANA

![Konsola tworzenia kopii zapasowych HANA Studio pozwala na ograniczenie maksymalnego rozmiaru plików kopii zapasowych HANA](media/sap-hana-backup-guide/image010.png)

Konsola tworzenia kopii zapasowych HANA Studio pozwala na ograniczenie maksymalnego rozmiaru plików kopii zapasowych HANA. W przykładowym środowisku funkcja ta umożliwia uzyskanie wielu mniejszych plików kopii zapasowej zamiast plików kopii zapasowych 1 230 GB. Mniejszy rozmiar pliku ma znaczny wpływ na wydajność (zobacz pokrewny artykuł [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Podsumowanie

W oparciu o wyniki testów w poniższych tabelach przedstawiono zalety i wady rozwiązań służących do tworzenia kopii zapasowej bazy danych SAP HANA działającej na maszynach wirtualnych platformy Azure.

**Utwórz kopię zapasową SAP HANA w systemie plików i skopiuj pliki kopii zapasowej później do ostatecznego miejsca docelowego kopii zapasowej**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Zachowaj kopie zapasowe HANA na dyskach maszyn wirtualnych                      |Brak dodatkowych wysiłków związanych z zarządzaniem     |Eats do lokalnego miejsca na dysku maszyny wirtualnej           |
|Narzędzie Blobxfer do kopiowania plików kopii zapasowej do magazynu obiektów BLOB |Równoległość do kopiowania wielu plików, wybór użycia chłodnego magazynu obiektów BLOB | Obsługa dodatkowych narzędzi i skryptów niestandardowych | 
|Kopiowanie obiektu BLOB za pomocą programu PowerShell lub interfejsu wiersza polecenia                    |Nie jest wymagane dodatkowe narzędzie, może być realizowane za pośrednictwem programu Azure PowerShell lub interfejsu wiersza polecenia |proces ręczny, klient musi zająć się obsługą skryptów i zarządzaniem skopiowanymi obiektami BLOB na potrzeby przywracania|
|Kopiuj do udziału NFS                                  |Przetwarzanie końcowe plików kopii zapasowej na innej maszynie wirtualnej bez wpływu na serwer HANA|Proces powolnej kopiowania|
|Blobxfer kopiowanie do usługi plików platformy Azure                |Nie zajmują miejsca na lokalnych dyskach maszyn wirtualnych|Brak bezpośredniej obsługi zapisu przez kopię zapasową HANA, ograniczenie rozmiaru udziału plików, który jest obecnie 5 TB|
|Agent usługi Kopia zapasowa Azure                                 | Byłoby preferowane rozwiązanie         | Obecnie niedostępne w systemie Linux    |



**SAP HANA kopii zapasowej na podstawie migawek magazynu**

|Rozwiązanie                                           |Specjaliści                                 |Wady                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Usługa Azure Backup                               | Umożliwia tworzenie kopii zapasowej maszyny wirtualnej na podstawie migawek obiektów BLOB | Gdy przywracanie na poziomie plików nie jest używane, wymaga utworzenia nowej maszyny wirtualnej dla procesu przywracania, co oznacza konieczność nowego klucza licencji SAP HANA|
|Ręczne migawki obiektów BLOB                              | Elastyczność tworzenia i przywracania określonych dysków maszyny wirtualnej bez konieczności zmiany unikatowego identyfikatora maszyny wirtualnej|Wszystkie ręczne czynności, które należy wykonać przez klienta|

## <a name="next-steps"></a>Następne kroki
* [SAP HANA Azure Backup na poziomie pliku](sap-hana-backup-file-level.md) zawiera opis opcji tworzenia kopii zapasowej opartej na plikach.
* [SAP HANA kopia zapasowa oparta na migawkach magazynu](sap-hana-backup-storage-snapshots.md) zawiera opis opcji tworzenia kopii zapasowej opartej na migawce magazynu.
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
