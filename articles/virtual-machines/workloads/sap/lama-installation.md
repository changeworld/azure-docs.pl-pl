---
title: Łącznik SAP LaMa na platformie Azure | Dokumentacja firmy Microsoft
description: Zarządzanie systemami SAP na platformie Azure przy użyciu SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/17/2018
ms.author: sedusch
ms.openlocfilehash: f09f66e81ec4878aedebfee9be4c0c67b75c8ad6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463008"
---
# <a name="sap-lama-connector-for-azure"></a>Łącznik SAP LaMa dla platformy Azure

[1877727]: https://launchpad.support.sap.com/#/notes/1877727
[2343511]: https://launchpad.support.sap.com/#/notes/2343511
[2350235]: https://launchpad.support.sap.com/#/notes/2350235
[2562184]: https://launchpad.support.sap.com/#/notes/2562184
[2628497]: https://launchpad.support.sap.com/#/notes/2628497
[2445033]: https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Oświadczenie dotyczące obsługi ogólne: Zawsze Otwórz zdarzenie z oprogramowaniem SAP w składniku BC-VCM-LVM — funkcji Hyper-v, jeśli potrzebujesz pomocy technicznej SAP LaMa lub łącznik usługi Azure.

SAP LaMa jest używany przez wielu klientów do obsługi i monitorowania ich środowiskiem SAP. Ponieważ SP05 3.0 LaMa SAP go jest dostarczany za pomocą łącznika Azure domyślnie. Ten łącznik umożliwia cofnięcie przydziału i zatrzymuj maszyny wirtualne, skopiuj i Przenieś dyski zarządzane i usuwanie dysków zarządzanych. Za pomocą tych podstawowych operacji można przenosić, skopiuj, klonowanie i Odśwież systemów SAP za pomocą SAP LaMa.

W tym przewodniku opisano, jak można skonfigurować łącznik usługi Azure dla LaMa SAP, Tworzenie maszyn wirtualnych, które mogą służyć do instalowania adaptacyjne systemów SAP i sposobach ich konfigurowania.

> [!NOTE]
> Łącznik jest dostępna tylko w wersji Enterprise LaMa SAP

## <a name="resources"></a>Zasoby

Poniższe uwagi SAP są związane z tym tematem z LaMa SAP na platformie Azure:

| Numer | Stanowisko |
| --- | --- |
| [2343511] |Microsoft Azure łącznika SAP krajobrazu zarządzania (LaMa) |
| [2350235] |SAP krajobrazu zarządzania 3.0 — Enterprise edition |

Przeczytaj również [portalu pomocy SAP dla LaMa SAP](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Uwagi ogólne

* Upewnij się umożliwić *automatyczne tworzenie Punkt_instalacji* w konfiguracji -> Ustawienia -> aparat  
  Jeśli SAP LaMa instaluje woluminów przy użyciu rozszerzenia adaptacyjne SAP na maszynie wirtualnej, punkt instalacji muszą istnieć, jeśli to ustawienie nie jest włączona.

* Użyj osobnej podsieci i nie Użyj dynamiczne adresy IP, aby zapobiec IP adresu "kradzież" podczas wdrażania nowych maszyn wirtualnych i wystąpieniami platformy SAP są nieprzygotowane  
  Jeśli używasz dynamiczne przydzielanie adresów IP w podsieci, która jest również używany przez LaMa SAP, przygotowywanie systemu SAP za pomocą LaMa SAP może zakończyć się niepowodzeniem. W przypadku systemu SAP nieprzygotowane adresów IP nie są zastrzeżone i może być Pobierz przydzieloną dla innych maszyn wirtualnych.

* Jeśli użytkownik loguje się na zarządzane hosty, upewnij się, że nie blokuje systemy plików z trwająca Dezinstalacja  
  Jeśli możesz zalogować się do maszyn wirtualnych systemu Linux, a następnie zmień katalog roboczy do katalogu w punkt instalacji, na przykład /usr/sap/AH1/ASCS00/exe, wolumin nie może być odinstalowane i zmienić lokalizację lub unprepare — kończy się niepowodzeniem.

## <a name="set-up-azure-connector-for-sap-lama"></a>Skonfiguruj łącznik usługi Azure dla LaMa SAP

Łącznik usługi Azure jest dostarczany od SAP LaMa 3.0 SP05. Zalecamy zainstalowanie zawsze najnowsze pakietu dla pomocy technicznej i poprawki dla programu SAP LaMa 3.0. Łącznik usługi Azure używa nazwy głównej usługi, do autoryzacji dla Microsoft Azure. Wykonaj następujące kroki, aby utworzyć nazwę główną usługi SAP krajobrazu zarządzania (LaMa).

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok usługi Azure Active Directory
1. Kliknij pozycję rejestracje aplikacji
1. Kliknij pozycję Dodaj.
1. Wprowadź nazwę, wybierz typ aplikacji "Aplikacja/interfejsu API sieci Web", wprowadź adres URL logowania (na przykład http:\//localhost) i kliknij pozycję Utwórz
1. Adres URL logowania nie jest używany i może być dowolny prawidłowy adres URL
1. Wybierz nową aplikację, a następnie kliknij przycisk na kluczach, na karcie Ustawienia
1. Wprowadź opis nowego klucza, wybierz pozycję "Nigdy nie wygasa" i kliknij przycisk przy zapisywaniu
1. Zanotuj wartość. Jest ona używana jako hasło jednostki usługi
1. Zanotuj identyfikator aplikacji. Jest ona używana jako nazwa jednostki usługi

Nazwa główna usługi nie ma uprawnień do dostępu do zasobów platformy Azure, domyślnie. Musisz nadać uprawnienia jednostki usługi do nich dostęp.

1. Przejdź do strony https://portal.azure.com
1. Otwórz blok grupy zasobów
1. Wybierz grupę zasobów, których chcesz użyć
1. Kliknij przycisk kontroli dostępu (IAM)
1. Kliknij pozycję Dodaj przypisanie roli
1. Wybierz rolę współautora
1. Wprowadź nazwę aplikacji, które zostały utworzone powyżej
1. Klikanie pozycji Zapisz.
1. Powtórz krok 3-8 dla wszystkich grup zasobów, których chcesz użyć w LaMa SAP

Otwórz witrynę sieci Web SAP LaMa i przejdź do infrastruktury. Przejdź do karty menedżerów chmury, a następnie kliknij przycisk Dodaj. Wybierz kartę chmury Microsoft Azure, a następnie kliknij przycisk Dalej. Wprowadź następujące informacje:

* Etykieta: Wybierz nazwę instancji łącznika
* Nazwa użytkownika: Identyfikator aplikacji nazwy głównej usługi
* Hasło: Hasło klucza jednostki usługi
* Adres URL: Zachowaj domyślne https://management.azure.com/
* Monitorowanie interwał (w sekundach): Powinien być co najmniej 300
* Identyfikator subskrypcji: Identyfikator subskrypcji platformy Azure
* Identyfikator dzierżawy usługi Azure Active Directory: Identyfikator dzierżawy usługi Active Directory
* Host serwera proxy: Nazwa hosta serwera proxy, jeśli SAP LaMa wymaga serwera proxy, aby nawiązać połączenie z Internetem
* Port serwera proxy: Port TCP serwera proxy

Polecenie konfiguracji testu, aby sprawdzić poprawność wprowadzanych danych. Powinien zostać wyświetlony

Pomyślnie nawiązano połączenie: Połączenie firmy Microsoft w chmurze powiodło się. 7 grup zasobów znaleziono (wymagane tylko 10 grupy)

w dolnej części witryny sieci Web.

## <a name="provision-a-new-adaptive-sap-system"></a>Aprowizacja nowego adaptacyjne systemu SAP

Można ręcznie wdrożyć nową maszynę wirtualną lub użyj jednego z szablonów platformy Azure w [repozytorium z pakietu quickstart](https://github.com/Azure/azure-quickstart-templates). Zawiera szablony [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [serwery aplikacji SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)i [bazy danych](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Te szablony umożliwia również aprowizować nowe hosty jako część systemu kopiowania/klona itp.

Zalecamy użycie osobnej podsieci dla wszystkich maszyn wirtualnych, czy mają być zarządzane za pomocą LaMa SAP i nie używaj dynamiczne adresy IP, aby zapobiec, IP adresu "kradzież" podczas wdrażania nowych maszyn wirtualnych i wystąpieniami platformy SAP są nieprzygotowane.

> [!NOTE]
> Jeśli to możliwe należy usunąć wszystkie rozszerzenia maszyny wirtualnej, ponieważ mogą one powodować kontekście długich czasów dla odłączanie dysków z maszyny wirtualnej.

Upewnij się, że użytkownik \<hanasid > adm, \<sapsid > sapsys adm i grupa istnieje na maszynie docelowej, z tym samym Identyfikatorem i gid lub użyć protokołu LDAP. Włącz i uruchom serwer systemu plików NFS na maszynach wirtualnych, które powinny być używane do uruchamiania SAP NetWeaver (A) SCS.

### <a name="manual-deployment"></a>Ręczne wdrażanie

SAP LaMa komunikuje się z maszyną wirtualną przy użyciu agenta hosta SAP. W przypadku wdrożenia maszyn wirtualnych, ręcznie lub nie przy użyciu szablonu usługi Azure Resource Manager z repozytorium Szybki Start, upewnij się zainstalować najnowszą wersję agenta hosta SAP i SAP rozszerzenia adaptacyjne. Aby uzyskać więcej informacji na temat poziomów wymagane poprawki dla platformy Azure, patrz Uwaga SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ręczne wdrażanie maszyny wirtualnej z systemem Linux

Utwórz nową maszynę wirtualną przy użyciu jednego z systemów obsługiwaną operacją na liście Uwaga SAP [2343511]. Dodaj dodatkowe konfiguracje adresów IP dla wystąpień SAP. Każde wystąpienie wymaga co najmniej na adresie IP i musi być zainstalowany przy użyciu wirtualnej nazwy hosta.

Wystąpienie SAP NetWeaver ASCS musi dysków dla /sapmnt/\<SAPSID >, / usr/sap/\<SAPSID > oraz/usr/sap/trans/usr/sap/\<sapsid > adm. Serwery aplikacji SAP NetWeaver dodatkowe dyski nie jest konieczne. Wszystkie elementy powiązane ze wystąpienie SAP muszą być przechowywane na ASCS i eksportowania za pomocą systemu plików NFS. W przeciwnym razie jest obecnie nie można dodać dodatkowych serwerów aplikacji za pomocą SAP LaMa.

![Oprogramowanie SAP NetWeaver ASCS w systemie Linux](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ręczne wdrażanie platformy SAP Hana

Tworzenie nowej maszyny wirtualnej przy użyciu jednego z systemów operacyjnych obsługiwanych dla oprogramowania SAP HANA, zgodnie z zaleceniami z Uwaga SAP [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla oprogramowania SAP HANA i jeden dla dzierżawy platformy HANA.

SAP HANA wymaga dysków na potrzeby /hana/shared, /hana/backup, /hana/data i /hana/log

![Oprogramowanie SAP HANA w systemie Linux](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ręczne wdrażanie dla baz danych Oracle w systemie Linux

Utwórz nową maszynę wirtualną przy użyciu jednego z systemów obsługiwaną operacją dla baz danych Oracle, zgodnie z zaleceniami z Uwaga SAP [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla bazy danych programu Oracle.

Baza danych programu Oracle wymaga dysków /oracle, /home/oraod1 i /home/oracle

![Baza danych Oracle w systemie Linux](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ręczne wdrażanie programu Microsoft SQL Server

Utwórz nową maszynę wirtualną przy użyciu jednego z systemów operacyjnych obsługiwanych dla programu Microsoft SQL Server zgodnie z zaleceniami z Uwaga SAP [2343511]. Dodaj jedną dodatkową konfigurację adresu IP dla wystąpienia programu SQL Server.

Serwer bazy danych programu SQL Server wymaga dysków dane z bazy danych i plików dziennika i dyskach c:\usr\sap.

![Baza danych Oracle w systemie Linux](media/lama/sap-lama-db-sql.png)

Upewnij się, że Zainstaluj obsługiwany sterownik ODBC firmy Microsoft dla programu SQL Server na maszynie wirtualnej, którego chcesz użyć do przeniesienia serwera aplikacji SAP NetWeaver lub jako docelowej kopiowania/klonowania systemu.

SAP LaMa nie można przenieść sam program SQL Server, dzięki czemu maszyny wirtualnej, który chcesz użyć do przeniesienia wystąpienie bazy danych do lub jako element docelowy kopiowania/klonowania systemu wymaga programu SQL Server, wstępnie.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Wdrażanie maszyny wirtualnej przy użyciu szablonu platformy Azure

Pobierz następujące najnowsze dostępne archiwa z [Marketplace oprogramowanie SAP](https://support.sap.com/swdc) systemu operacyjnego maszyn wirtualnych:

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. EXT ADAPTACYJNE ROZSZERZENIA 1.0 SAP

Również pobrać następujące składniki z [Microsoft Download Center](https://www.microsoft.com/download)

1. Pakiet redystrybucyjny Microsoft Visual C++ 2010 (x64) (tylko Windows)
1. Sterownik ODBC firmy Microsoft dla programu SQL Server (tylko program SQL Server)

Składniki są wymagane do wdrożenia szablonu. Najprostszym sposobem, aby udostępnić je do szablonu jest przekazać je na konto magazynu platformy Azure i tworzenie sygnatury dostępu współdzielonego (SAS).

Szablony mają następujące parametry:

* sapSystemId: Identyfikator systemu SAP Służy do tworzenia układu dysku (naprzykład/usr/sap/\<sapsid >).

* computerName: Nazwa komputera nowej maszyny wirtualnej. Ten parametr jest również używany przez LaMa SAP. Gdy możesz użyć tego szablonu do aprowizacji nowej maszyny wirtualnej w ramach kopii systemu SAP LaMa czeka, aż hosta przy użyciu tej nazwy komputera jest osiągalna.

* osType: Typ systemu operacyjnego, którą chcesz wdrożyć.

* Atrybut DbType: Typ bazy danych. Ten parametr jest używany w celu ustalenia, ile dodatkowe konfiguracje adresów IP, należy dodać i jak powinna wyglądać układ dysku.

* sapSystemSize: Rozmiar systemu SAP, którą chcesz wdrożyć. Służy do określenia rozmiaru i typu wystąpienia maszyny wirtualnej.

* adminUsername: Nazwa użytkownika dla maszyny wirtualnej.

* adminPassword: Hasło dla maszyny wirtualnej. Możesz również podać klucz publiczny SSH.

* sshKeyData: Publiczny klucz SSH dla maszyn wirtualnych. Obsługiwane tylko w systemach operacyjnych Linux.

* subnetId: Identyfikator podsieci, w której chcesz użyć.

* deployEmptyTarget: Można wdrożyć obiekt docelowy pusty, jeśli chcesz korzystać z maszyn wirtualnych jako obiekt docelowy dla przemieść wystąpienia lub podobne. W tym przypadku są dołączone nie dodatkowe dyski lub konfiguracji adresów IP.

* sapcarLocation: Lokalizacja aplikacji sapcar, który odpowiada systemowi operacyjnemu wdrażania. sapcar jest używany do wyodrębniania archiwów, podane w innych parametrów.

* sapHostAgentArchiveLocation: Lokalizacja archiwum agenta hosta SAP. Agent hosta SAP jest wdrażany jako część wdrożenia tego szablonu.

* sapacExtLocation: Lokalizacja rozszerzenia adaptacyjne SAP. Uwaga SAP [2343511] Wyświetla minimalny poziom poprawek wymagane dla platformy Azure.

* vcRedistLocation: Lokalizacja środowiska uruchomieniowego VC, który jest wymagany do zainstalowania rozszerzenia adaptacyjne SAP. Ten parametr jest tylko wymagane dla Windows.

* odbcDriverLocation: Lokalizację sterownika ODBC, którą chcesz zainstalować. Tylko sterownik ODBC firmy Microsoft dla programu SQL Server jest obsługiwana.

* sapadmPassword: Hasło dla użytkownika sapadm.

* sapadmId: Identyfikator użytkownika systemu Linux sapadm użytkownika. Nie jest wymagana dla Windows.

* sapsysGid: Identyfikator grupy systemu Linux grupy sapsys. Nie jest wymagana dla Windows.

* _artifactsLocation: Podstawowy identyfikator URI, gdzie znajdują się artefaktów wymaganych przez ten szablon. Po wdrożeniu szablonu za pomocą skryptów towarzyszący lokalizacji prywatnej, w ramach subskrypcji, który będzie używany, a ta wartość zostanie automatycznie wygenerowany. Ta opcja jest wymagane tylko, jeśli nie są wdrażane przez szablon z serwisu GitHub.

* _artifactsLocationSasToken: SasToken, wymagane do uzyskania dostępu _artifactsLocation. Po wdrożeniu szablonu za pomocą skryptów towarzyszący sasToken zostanie wygenerowany automatycznie. Ta opcja jest wymagane tylko, jeśli nie są wdrażane przez szablon z serwisu GitHub.

### <a name="sap-hana"></a>SAP HANA

W poniższych przykładach założono, instalowanie platformy SAP HANA przy użyciu systemu identyfikator HN1 i system SAP NetWeaver przy użyciu systemu AH1 identyfikator. Nazwy hostów wirtualnych są hn1 bazy danych dla tego wystąpienia HANA ah1 bazy danych dla dzierżawy HANA używaną przez system SAP NetWeaver ah1-ascs dla SAP NetWeaver ASCS i ah1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>Zainstaluj oprogramowanie SAP NetWeaver ASCS platformy SAP Hana

Przed rozpoczęciem Menedżera inicjowania obsługi oprogramowania SAP (SWPM), musisz zainstalować adres IP hosta wirtualnego programu ASCS. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

Uruchom SWPM i użyj *ah1 ascs* dla *nazwę hosta wystąpienia ASCS*.

![Linux][Logo_Linux] systemu Linux  
Dodaj następujący parametr profilu do profil agenta hosta SAP, który znajduje się w folderze /usr/sap/hostctrl/exe/host_profile. Aby uzyskać więcej informacji, patrz Uwaga SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

Po zainstalowaniu oprogramowania SAP HANA przy użyciu hdblcm narzędzie wiersza polecenia, użyj parametru--nazwy hosta, aby podać nazwę hosta wirtualnego. Należy dodać adres IP wirtualnych nazwy hosta bazy danych do interfejsu sieciowego. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

Dodaj kolejną wirtualnej nazwy hosta i adresu IP dla nazwy, która jest używany przez serwery aplikacji, aby połączyć z dzierżawą platformy HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Uruchom instalację SWPM wystąpienia bazy danych na maszynie wirtualnej serwera aplikacji, a nie na maszynie wirtualnej platformy HANA. Użyj *ah1 db* dla *bazy danych hosta* w oknie dialogowym *bazy danych systemu SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Zainstaluj oprogramowanie SAP NetWeaver aplikacji serwera dla oprogramowania SAP HANA

Przed rozpoczęciem Menedżera inicjowania obsługi oprogramowania SAP (SWPM), musisz zainstalować adresu IP wirtualnej nazwy hosta serwera aplikacji. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

Zalecane jest, aby ustawić tożsamość, która jest używana do znajdowania klucza w elemencie userstore HDB za pomocą oprogramowania SAP NetWeaver profilu parametr baz danych/hdb/hdb_use_ident. Można ręcznie dodać ten parametr, po zakończeniu instalacji wystąpienia bazy danych za pomocą SWPM lub uruchomić SWPM z

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Jeśli ustawisz go ręcznie, należy utworzyć nowe wpisy w elemencie userstore HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Użyj *ah1-di-0* dla *nazwę hosta wystąpienia PAS* w oknie dialogowym *podstawowe wystąpienie serwera aplikacji*.

#### <a name="post-installation-steps-for-sap-hana"></a>Kroki po instalacji SAP HANA

Upewnij się utworzyć kopię zapasową SYSTEMDB i wszystkich baz danych dzierżaw, przed podjęciem próby kopiowanie dzierżawy, Przenieś dzierżawy lub utworzyć replikacji systemu.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

W poniższych przykładach założono, zainstalować system SAP NetWeaver przy użyciu systemu AS1 identyfikator. Nazwy hostów wirtualnych są as1 bazy danych dla wystąpienia programu SQL Server używane przez system SAP NetWeaver, as1-ascs dla SAP NetWeaver ASCS i as1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Zainstaluj oprogramowanie SAP NetWeaver ASCS dla programu SQL Server

Przed rozpoczęciem Menedżera inicjowania obsługi oprogramowania SAP (SWPM), musisz zainstalować adres IP hosta wirtualnego programu ASCS. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Uruchom SWPM i użyj *as1 ascs* dla *nazwę hosta wystąpienia ASCS*.

#### <a name="install-sql-server"></a>Zainstaluj program SQL Server

Należy dodać adres IP wirtualnych nazwy hosta bazy danych do interfejsu sieciowego. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Uruchom instalację SWPM wystąpienia bazy danych na maszynie wirtualnej programu SQL server. Użyj SAPINST_USE_HOSTNAME =*as1-db* zastąpić nazwę hosta, używane do łączenia z programem SQL Server. Jeśli wdrożono maszynę wirtualną przy użyciu szablonu usługi Azure Resource Manager, upewnij się ustawić katalog służący do plików danych bazy danych do *C:\sql\data* i plik dziennika bazy danych do *C:\sql\log*.

Upewnij się, że użytkownik *NT AUTHORITY\SYSTEM* ma dostęp do programu SQL Server i pełni rolę serwera *sysadmin*. Aby uzyskać więcej informacji, patrz Uwaga SAP [1877727] i [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Zainstaluj oprogramowanie SAP NetWeaver aplikacji serwera

Przed rozpoczęciem Menedżera inicjowania obsługi oprogramowania SAP (SWPM), musisz zainstalować adresu IP wirtualnej nazwy hosta serwera aplikacji. Zalecaną metodą jest używać sapacext. W przypadku instalowania adresu IP, przy użyciu sapacext, pamiętaj ponownie zainstalować adresu IP po ponownym uruchomieniu.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Użyj *as1-di-0* dla *nazwę hosta wystąpienia PAS* w oknie dialogowym *podstawowe wystąpienie serwera aplikacji*.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-and-warnings-during-discover"></a>Błędy i ostrzeżenia podczas odnajdywania

* Odmówiono uprawnienia SELECT
  * [Microsoft] [Sterownik ODBC SQL Server] [SQL Server] Nie przyznano uprawnienia SELECT wobec obiektu "log_shipping_primary_databases", baza danych msdb, schematu "dbo". [SOAPFaultException]  
  Nie przyznano uprawnienia SELECT wobec obiektu "log_shipping_primary_databases", baza danych msdb, schematu "dbo".
  * Rozwiązanie  
    Upewnij się, że *NT AUTHORITY\SYSTEM* mają dostęp do serwera SQL. Patrz Uwaga SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Błędy i ostrzeżenia walidacji dla wystąpienia

* Zgłoszono wyjątek podczas walidacji w elemencie userstore HDB  
  * Zobacz Podgląd dziennika  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: Wyjątek podczas weryfikacji o identyfikatorze "RuntimeHDBConnectionValidator" (Sprawdzanie poprawności: "VALIDATION_HDB_USERSTORE"): Nie można pobrać hdbuserstore  
    HANA w elemencie userstore nie znajduje się w poprawnej lokalizacji
  * Rozwiązanie  
    Upewnij się, że ten /usr/sap/AH1/hdbclient/install/installation.ini jest prawidłowa

### <a name="errors-and-warnings-during-a-system-copy"></a>Błędy i ostrzeżenia podczas kopiowania systemu

* Wystąpił błąd podczas sprawdzania poprawności systemu kroku zastrzegania
  * Przyczyną: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException podczas wywoływania "/ usr/sap/hostctrl/exe/sapacext - ShowHanaBackups -m HN1 -f 50 - h hn1-db - o poziomie = 0\;stan = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u systemu -p hook - r "| /usr/SAP/hostctrl/exe/sapacext - ShowHanaBackups -m 50 - h -f HN1 hn1-db - o poziomie = 0\;stan = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u systemu -p hook - r
  * Rozwiązanie  
    Wykonaj kopię zapasową wszystkich baz danych w źródle HANA system

* Krok kopiowania system *Start* wystąpienia bazy danych
  * Operacja agenta hosta "000D3A282BC91EE8A1D76CF1F92E2944" nie powiodło się (OperationException. FaultCode: "127", komunikat: "Nie można wykonać polecenia. : Użytkownik [Microsoft] [SQL Server sterownika ODBC] [SQL Server] nie ma uprawnień do zmiany bazy danych "AS2", baza danych nie istnieje lub baza danych nie jest w stanie, który umożliwiałby Sprawdzanie uprawnień dostępu. ")
  * Rozwiązanie  
    Upewnij się, że *NT AUTHORITY\SYSTEM* mają dostęp do serwera SQL. Patrz Uwaga SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Błędy i ostrzeżenia podczas klonowania systemu

* Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia w kroku *wymuszone zarejestrować i uruchomić agenta wystąpienia* serwera aplikacji lub ASCS
  * Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia. (RemoteException: "Nie można załadować danych wystąpienia z profilu"\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0 ":  Nie można uzyskać dostępu profilu "\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0": Nie ma takiego pliku lub katalogu. ")
  * Rozwiązanie  
   Upewnij się, że sapmnt udziale ASCS/SCS ma pełny dostęp do SAP_AS1_GlobalAdmin

* Błąd w kroku *włączania ochrony uruchamiania dla klonu*
  * Nie można otworzyć pliku "\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0" Przyczyna: Nie ma takiego pliku lub katalogu
  * Rozwiązanie  
    Konto komputera serwera aplikacji wymaga dostępu do profilu zapisu

### <a name="errors-and-warnings-during-create-system-replication"></a>Błędy i ostrzeżenia podczas tworzenia replikacji systemu

* Wyjątek po kliknięciu replikacji tworzenia systemu
  * Przyczyną: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException podczas wywoływania "/ usr/sap/hostctrl/exe/sapacext - ShowHanaBackups -m HN1 -f 50 - h hn1-db - o poziomie = 0\;stan = 5\;port = 35013 pf = / usr/sap/hostctrl / exe/host_profile -R -T dev_lvminfo -u systemu -p hook - r "| /usr/SAP/hostctrl/exe/sapacext - ShowHanaBackups -m 50 - h -f HN1 hn1-db - o poziomie = 0\;stan = 5\;port = 35013 pf = / usr/sap/hostctrl/exe/host_profile - R -T dev_lvminfo -u systemu -p hook - r
  * Rozwiązanie  
    Sprawdzić, czy mogą być wykonywane sapacext jako `<hanasid`> usługi adm

* Wystąpił błąd podczas pełnego kopiowania nie jest włączona w kroku magazynu
  * Wystąpił błąd podczas zgłaszania wiadomość atrybutu kontekstu dla ścieżki IStorageCopyData.storageVolumeCopyList:1 i targetStorageSystemId pola
  * Rozwiązanie  
    Ignoruj ostrzeżenia o w kroku, a następnie spróbuj ponownie. Ten problem zostanie rozwiązany w nowych obsługi pakietu/poprawkę z LaMa SAP.

### <a name="errors-and-warnings-during-relocate"></a>Błędy i ostrzeżenia podczas przemieszczanie

* Ścieżka "/ usr/sap/AH1" nie jest dozwolona dla reexports systemu plików nfs.
  * Zapoznaj się uwagę [2628497] Aby uzyskać szczegółowe informacje.
  * Rozwiązanie  
    Dodaj ASCS eksportuje ASCS HostAgent profilu. Patrz Uwaga SAP [2628497]

* Nie zaimplementowano przy przenoszeniu ASCS — funkcja
  * Dane wyjściowe polecenia: exportfs: host: / usr/sap/AX1: Funkcja nie zaimplementowana.
  * Rozwiązanie  
    Upewnij się, że usługa serwera systemu plików NFS jest włączony na docelowej maszynie wirtualnej przemieszczanie

### <a name="errors-and-warnings-during-application-server-installation"></a>Błędy i ostrzeżenia podczas instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: getProfileDir
  * BŁĄD: (Ostatni błąd zgłoszony przez krok: Przechwycono ESAPinstException w wywołaniu modułu: Moduł sprawdzania poprawności kroku "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_readProfileDir | ind | ind | ind | ind | readProfile | 0 | getProfileDir "zgłosił błąd: Węzeł \\\as1-ascs\sapmnt\AS1\SYS\profile nie istnieje. W trybie interaktywnym, aby rozwiązać ten problem, należy uruchomić SAPinst)
  * Rozwiązanie  
    Upewnij się, że SWPM działa z użytkownikiem, który ma dostęp do tego profilu. Temu użytkownikowi można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: askUnicode
  * BŁĄD: (Ostatni błąd zgłoszony przez krok: Przechwycono ESAPinstException w wywołaniu modułu: Moduł sprawdzania poprawności kroku "| NW_DI | ind | ind | ind | ind | 0 | 0 | NW_GetSidFromProfiles | ind | ind | ind | ind | getSid | 0 | NW_getUnicode | ind | ind | ind | ind | unicode | 0 | askUnicode "zgłosił błąd: W trybie interaktywnym, aby rozwiązać ten problem, należy uruchomić SAPinst)
  * Rozwiązanie  
    Jeśli korzystasz z najnowszych jądra SAP, SWPM nie może określić, czy system jest już przy użyciu serwera wiadomości ASCS systemu unicode. Patrz Uwaga SAP [2445033] Aby uzyskać więcej informacji.  
    Ten problem zostanie rozwiązany w nowych obsługi pakietu/poprawkę z LaMa SAP.  
    Ustaw parametr profilu OS_UNICODE = uc w profilu domyślnym systemu SAP w celu obejścia tego problemu.

* Wystąpił błąd podczas wykonywania kroku SAPinst: dCheckGivenServer
  * Error executing SAPinst step: dCheckGivenServer" version="1.0" ERROR: (Ostatni błąd zgłoszony przez krok: \<p > Instalacja została anulowana przez użytkownika. \</p>
  * Rozwiązanie  
    Upewnij się, że SWPM działa z użytkownikiem, który ma dostęp do tego profilu. Temu użytkownikowi można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: checkClient
  * Wystąpił błąd podczas wykonywania kroku SAPinst: checkClient "wersja ="1.0"Błąd: (Ostatni błąd zgłoszony przez krok: \<p > Instalacja została anulowana przez użytkownika. \</p>)
  * Rozwiązanie  
    Upewnij się, że sterownik Microsoft ODBC dla programu SQL Server jest zainstalowany na maszynie wirtualnej, na którym chcesz zainstalować serwer aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: copyScripts
  * Ostatni błąd zgłoszony przez krok: Nie powiodło się. SZCZEGÓŁY: Błąd 13 (0x0000000d) (odmowa uprawnień) podczas wykonywania systemu wywołanie "fopenU, za pomocą parametru (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd w), wiersz (494) w pliku (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib / filesystem/syxxcfstrm2.cpp), ślad stosu:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction iastring const args_t const & nazwę i (argumenty)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl (const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Rozwiązanie  
    Upewnij się, że SWPM działa z użytkownikiem, który ma dostęp do tego profilu. Temu użytkownikowi można skonfigurować w Kreatorze instalacji serwera aplikacji

* Wystąpił błąd podczas wykonywania kroku SAPinst: askPasswords
  * Ostatni błąd zgłoszony przez krok: Nie powiodło się. SZCZEGÓŁY: Błąd (0x00000005) 5 (odmowa dostępu.) podczas wykonywania wywołania systemowego "NetValidatePasswordPolicy" za pomocą parametru (...), wiersz (359) w pliku (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), ślad stosu:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  CSiStepExecute.cpp: 764: CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp: 56: DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp: 85: DarkModeDialog::submit()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction iastring const args_t const & nazwę i (argumenty)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy (args_t const & _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,***) const)
  * Rozwiązanie  
    Upewnij się dodać regułę hosta w kroku *izolacji* umożliwia komunikację z maszyny Wirtualnej do kontrolera domeny

## <a name="next-steps"></a>Kolejne kroki
* [SAP HANA w podręczniku obsługi platformy Azure][hana-ops-guide]
* [Azure maszyny wirtualne, planowania i implementacji dla rozwiązania SAP][planning-guide]
* [Wdrażania maszyn wirtualnych platformy Azure dla rozwiązania SAP][deployment-guide]
* [Wdrażania systemu DBMS na maszynach wirtualnych platformy Azure dla rozwiązania SAP][dbms-guide]
