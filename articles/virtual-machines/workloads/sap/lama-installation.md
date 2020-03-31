---
title: Łącznik SAP LaMa dla platformy Azure | Dokumenty firmy Microsoft
description: Zarządzanie systemami SAP na platformie Azure przy użyciu SAP LaMa
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: fda62ff0af29c7cf681d9438b02420d299535701
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293943"
---
# <a name="sap-lama-connector-for-azure"></a>Łącznik SAP LaMa dla platformy Azure

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> Ogólne oświadczenie pomocy technicznej: Zawsze otwórz zdarzenie z SAP na składnik BC-VCM-LVM-HYPERV, jeśli potrzebujesz pomocy technicznej dla SAP LaMa lub łącznika platformy Azure.

SAP LaMa jest używany przez wielu klientów do obsługi i monitorowania ich krajobrazu SAP. Od SAP LaMa 3.0 SP05, domyślnie jest dostarczany z łącznikiem do platformy Azure. Za pomocą tego łącznika można przydzielać alokacje i uruchamianie maszyn wirtualnych, kopiować i przenosić dyski zarządzane oraz usuwać dyski zarządzane. Dzięki tym podstawowym operacjom można przenosić, kopiować, klonować i odświeżać systemy SAP przy użyciu sap lama.

W tym przewodniku opisano sposób konfigurowania łącznika platformy Azure dla systemu SAP LaMa, tworzenia maszyn wirtualnych, które mogą być używane do instalowania adaptacyjnych systemów SAP i konfigurowania ich.

> [!NOTE]
> Łącznik jest dostępny tylko w sap LaMa Enterprise Edition

## <a name="resources"></a>Resources

Następujące uwagi SAP są związane z tematem SAP LaMa na platformie Azure:

| Numer notatki | Tytuł |
| --- | --- |
| [2343511] |Łącznik platformy Microsoft Azure do zarządzania krajobrazem SAP (LaMa) |
| [2350235] |SAP Landscape Management 3.0 - Edycja enterprise |

Przeczytaj także [portal pomocy SAP dla SAP LaMa](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE).

## <a name="general-remarks"></a>Uwagi ogólne

* Upewnij się, że *włączysz automatyczne tworzenie punktów instalacji* w instalatorze -> Ustawienia -> Engine  
  Jeśli SAP LaMa instaluje woluminy przy użyciu rozszerzeń SAP Adaptive na maszynie wirtualnej, musi istnieć punkt instalacji, jeśli to ustawienie nie jest włączone.

* Użyj osobnej podsieci i nie używaj dynamicznych adresów IP, aby zapobiec "kradzieży" adresu IP podczas wdrażania nowych maszyn wirtualnych i wystąpień SAP są nieprzygotowane  
  Jeśli używasz dynamicznej alokacji adresów IP w podsieci, która jest również używana przez SAP LaMa, przygotowanie systemu SAP z SAP LaMa może zakończyć się niepowodzeniem. Jeśli system SAP jest nieprzygotowany, adresy IP nie są zarezerwowane i mogą zostać przydzielone do innych maszyn wirtualnych.

* Jeśli zalogujesz się do zarządzanych hostów, upewnij się, że nie blokujesz odinstalowywania systemów plików  
  Jeśli zalogujesz się do maszyn wirtualnych systemu Linux i zmienisz katalog roboczy na katalog w punkcie instalacji, na przykład /usr/sap/AH1/ASCS00/exe, wolumin nie może zostać odinstalowany, a przeniesienie lub nieprzygotowanie nie powiedzie się.

* Pamiętaj, aby wyłączyć CLOUD_NETCONFIG_MANAGE na maszynach wirtualnych SUSE SLES Linux. Aby uzyskać więcej informacji, zobacz [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633).

## <a name="set-up-azure-connector-for-sap-lama"></a>Konfigurowanie łącznika platformy Azure dla sap lama

Łącznik platformy Azure jest dostarczany zgodnie z dodatkem SP05 sap LaMa 3.0. Zalecamy zawsze instalowanie najnowszego pakietu pomocy technicznej i poprawki dla SAP LaMa 3.0.

Łącznik platformy Azure używa interfejsu API usługi Azure Resource Manager do zarządzania zasobami platformy Azure. Sap LaMa można użyć jednostki usługi lub tożsamości zarządzanej do uwierzytelniania w tym interfejsie API. Jeśli twój SAP LaMa jest uruchomiony na maszynie Wirtualnej platformy Azure, zalecamy użycie tożsamości zarządzanej zgodnie z opisem w rozdziale [Użyj tożsamości zarządzanej, aby uzyskać dostęp do interfejsu API platformy Azure.](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d) Jeśli chcesz użyć jednostki usługi, wykonaj kroki opisane w rozdziale [Użyj jednostki usługi, aby uzyskać dostęp do interfejsu API platformy Azure.](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e)

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>Uzyskiwanie dostępu do interfejsu API platformy Azure za pomocą jednostki usługi

Łącznik platformy Azure może używać jednostki usługi do autoryzacji na platformie Microsoft Azure. Wykonaj następujące kroki, aby utworzyć jednostkę usługi dla zarządzania krajobrazem SAP (LaMa).

1. Przejdź do strony https://portal.azure.com
1. Otwieranie bloku usługi Azure Active Directory
1. Kliknij rejestracje aplikacji
1. Kliknij na Nowa rejestracja
1. Wprowadź nazwę i kliknij Zarejestruj się
1. Wybierz nową aplikację i kliknij certyfikaty & wpisy tajne na karcie Ustawienia
1. Utwórz nowy klucz tajny klienta, wprowadź opis nowego klucza, wybierz, kiedy klucz tajny powinien wygasnąć i kliknij Zapisz
1. Zapisz wartość. Jest on używany jako hasło dla jednostki usługi
1. Zapisz identyfikator aplikacji. Jest on używany jako nazwa użytkownika jednostki usługi

Podmiot zabezpieczeń usługi nie ma uprawnień do uzyskiwania dostępu do zasobów platformy Azure domyślnie. Należy nadać service principal uprawnienia dostępu do nich.

1. Przejdź do strony https://portal.azure.com
1. Otwieranie bloku Grupy zasobów
1. Wybierz grupę zasobów, której chcesz użyć
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij dodaj przypisanie roli
1. Wybierz rolę Współautora
1. Wprowadź nazwę aplikacji utworzonej powyżej
1. Klikanie pozycji Zapisz.
1. Powtórz krok od 3 do 8 dla wszystkich grup zasobów, których chcesz użyć w SAP LaMa

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>Uzyskiwanie dostępu do interfejsu API platformy Azure za pomocą tożsamości zarządzanej

Aby móc używać tożsamości zarządzanej, wystąpienie sap LaMa musi być uruchamiane na maszynie Wirtualnej platformy Azure, która ma tożsamość przypisaną przez system lub użytkownika. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, przeczytaj [Configure managed identities for Azure resources on a VM using the Azure portal](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) [artykuł Co to są tożsamości zarządzane dla zasobów platformy Azure?](../../../active-directory/managed-identities-azure-resources/overview.md)

Tożsamość zarządzana domyślnie nie ma uprawnień dostępu do zasobów platformy Azure. Musisz nadać mu uprawnienia dostępu do nich.

1. Przejdź do strony https://portal.azure.com
1. Otwieranie bloku Grupy zasobów
1. Wybierz grupę zasobów, której chcesz użyć
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij dodaj -> Dodaj przypisanie roli
1. Wybierz rolę Współautora
1. Wybierz "Maszyna wirtualna" dla "Przypisz dostęp do"
1. Wybierz maszynę wirtualną, na której działa wystąpienie SAP LaMa
1. Klikanie pozycji Zapisz.
1. Powtórz kroki dla wszystkich grup zasobów, których chcesz użyć w SAP LaMa

W konfiguracji łącznika SAP LaMa Azure wybierz opcję "Użyj tożsamości zarządzanej", aby włączyć użycie tożsamości zarządzanej. Jeśli chcesz użyć tożsamości przypisanej do systemu, pamiętaj, aby pozostawić pole Nazwa użytkownika puste. Jeśli chcesz użyć przypisanej tożsamości użytkownikowi, wprowadź przypisany do niego identyfikator tożsamości w polu Nazwa użytkownika.

### <a name="create-a-new-connector-in-sap-lama"></a>Tworzenie nowego łącznika w SAP LaMa

Otwórz witrynę SAP LaMa i przejdź do usługi Infrastruktura. Przejdź do zakładki Menedżerowie chmury i kliknij dodaj. Wybierz kartę Microsoft Azure Cloud Adapter i kliknij przycisk Dalej. Wprowadź następujące informacje:

* Etykieta: wybierz nazwę wystąpienia łącznika
* Nazwa użytkownika: Identyfikator aplikacji głównej usługi lub identyfikator użytkownika przypisanego tożsamości maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości przypisanej przez system lub użytkownika]
* Hasło: Klucz/hasło jednostki usługi. To pole można pozostawić puste, jeśli używasz tożsamości przypisanej przez system lub użytkownika.
* ADRES URL: zachowaj wartość domyślną`https://management.azure.com/`
* Interwał monitorowania (sekundy): Powinien wynosić co najmniej 300
* Użyj tożsamości zarządzanej: SAP LaMa może używać tożsamości przypisanej do systemu lub użytkownika do uwierzytelniania za pomocą interfejsu API platformy Azure. Zobacz rozdział Uzyskiwanie dostępu do interfejsu API platformy Azure w tym [przewodniku umożliwia korzystanie z tożsamości zarządzanej.](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d)
* Identyfikator subskrypcji: identyfikator subskrypcji platformy Azure
* Identyfikator dzierżawy usługi Azure Active Directory: identyfikator dzierżawy usługi Active Directory
* Host proxy: Nazwa hosta serwera proxy, jeśli SAP LaMa potrzebuje serwera proxy, aby połączyć się z Internetem
* Port serwera proxy: port TCP serwera proxy
* Zmień typ magazynu, aby zaoszczędzić koszty: Włącz to ustawienie, jeśli karta Azure powinna zmienić typ magazynu dysków zarządzanych, aby zaoszczędzić koszty, gdy dyski nie są używane. W przypadku dysków danych, do których odwołuje się konfiguracja wystąpienia SAP, karta zmieni typ dysku na Magazyn standardowy podczas wystąpienia nieprzygotowany i z powrotem do oryginalnego typu magazynu podczas przygotowywania wystąpienia. Jeśli zatrzymasz maszynę wirtualną w SAP LaMa, karta zmieni typ magazynu wszystkich podłączonych dysków, w tym dysku systemu operacyjnego na magazyn standardowy. Po uruchomieniu maszyny wirtualnej w SAP LaMa, karta zmieni typ magazynu z powrotem do oryginalnego typu magazynu.

Kliknij na Test konfiguracji, aby sprawdzić poprawność danych wejściowych. Powinieneś zobaczyć

Połączenie zakończyło się pomyślnie: połączenie z chmurą firmy Microsoft zakończyło się pomyślnie. Znaleziono 7 grup zasobów (tylko 10 żądanych grup)

w dolnej części strony internetowej.

## <a name="provision-a-new-adaptive-sap-system"></a>Udostępnienie nowego adaptacyjnego systemu SAP

Można ręcznie wdrożyć nową maszynę wirtualną lub użyć jednego z szablonów platformy Azure w [repozytorium szybkiego startu](https://github.com/Azure/azure-quickstart-templates). Zawiera szablony dla [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs), [SAP NetWeaver serwerów aplikacji](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)i bazy [danych](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database). Można również użyć tych szablonów do aprowizowania nowych hostów jako część kopii systemu / klon itp.

Zaleca się używanie oddzielnej podsieci dla wszystkich maszyn wirtualnych, którymi chcesz zarządzać za pomocą sap LaMa i nie używaj dynamicznych adresów IP, aby zapobiec "kradzieży" adresu IP podczas wdrażania nowych maszyn wirtualnych i wystąpień SAP są nieprzygotowane.

> [!NOTE]
> Jeśli to możliwe, usuń wszystkie rozszerzenia maszyny wirtualnej, ponieważ mogą one powodować długie czasy wykonywania dla odłączania dysków z maszyny wirtualnej.

Upewnij się, \<że użytkownik hanasid>\<adm, sapsid>adm i sapsys grupy istnieją na komputerze docelowym o tym samym identyfikatorze i roztrzepany lub użyj LDAP. Włącz i uruchom serwer NFS na maszynach wirtualnych, które powinny być używane do uruchamiania sap NetWeaver (A)SCS.

### <a name="manual-deployment"></a>Wdrażanie ręczne

SAP LaMa komunikuje się z maszyną wirtualną za pomocą agenta hosta SAP. Jeśli maszyny wirtualne są wdrażane ręcznie lub nie przy użyciu szablonu usługi Azure Resource Manager z repozytorium szybkiego startu, należy zainstalować najnowszy agent hosta SAP i rozszerzenia adaptacyjne SAP. Aby uzyskać więcej informacji na temat wymaganych poziomów poprawek dla platformy Azure, zobacz UWAGA SAP [2343511].

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Ręczne wdrażanie maszyny wirtualnej systemu Linux

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych wymienionych w sap note [2343511]. Dodaj dodatkowe konfiguracje adresów IP dla wystąpień SAP. Każde wystąpienie wymaga co najmniej adresu IP i musi być zainstalowane przy użyciu wirtualnej nazwy hosta.

Wystąpienie SAP NetWeaver ASCS wymaga dysków\<dla /sapmnt/ SAPSID>,\</usr/sap/ SAPSID>, /usr/sap/trans\<i /usr/sap/ sapsid>adm. Serwery aplikacji SAP NetWeaver nie potrzebują dodatkowych dysków. Wszystko związane z wystąpieniem SAP musi być przechowywane w ascs i eksportowane za pośrednictwem systemu plików NFS. W przeciwnym razie obecnie nie jest możliwe dodanie dodatkowych serwerów aplikacji przy użyciu SAP LaMa.

![SAP NetWeaver ASCS na Linuksie](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>Ręczne wdrażanie dla sap HANA

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla SAP HANA, jak wymieniono w SAP Note [2343511]. Dodaj jedną dodatkową konfigurację IP dla SAP HANA i jedną na dzierżawę HANA.

SAP HANA potrzebuje dysków dla /hana/shared, /hana/backup, /hana/data i /hana/log

![SAP HANA na Linuksie](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Ręczne wdrażanie bazy danych Oracle Database w systemie Linux

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla baz danych Oracle, wymienionych w sap note [2343511]. Dodaj jedną dodatkową konfigurację IP dla bazy danych Oracle.

Baza danych Oracle potrzebuje dysków dla /oracle, /home/oraod1 i /home/oracle

![Baza danych Oracle na Linuksie](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Ręczne wdrażanie programu Microsoft SQL Server

Utwórz nową maszynę wirtualną z jednym z obsługiwanych systemów operacyjnych dla programu Microsoft SQL Server wymienionym w sap note [2343511]. Dodaj jedną dodatkową konfigurację ip dla wystąpienia programu SQL Server.

Serwer bazy danych PROGRAMU SQL Server potrzebuje dysków do plików danych i dzienników bazy danych dla c:\usr\sap.

![Baza danych Oracle na Linuksie](media/lama/sap-lama-db-sql.png)

Upewnij się, że zainstalowany obsługiwany sterownik Microsoft ODBC dla programu SQL Server na maszynie wirtualnej, której chcesz użyć do przeniesienia serwera aplikacji SAP NetWeaver do lub jako miejsce docelowe kopiowania/klonowania systemu.

Sap LaMa nie może przenieść samego programu SQL Server, więc maszyna wirtualna, której chcesz użyć do przeniesienia wystąpienia bazy danych do lub jako cel kopiowania/klonowania systemu, wymaga preinstalowanego programu SQL Server.

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Wdrażanie maszyny wirtualnej przy użyciu szablonu platformy Azure

Pobierz następujące najnowsze dostępne archiwa z [sap software marketplace](https://support.sap.com/swdc) dla systemu operacyjnego maszyn wirtualnych:

1. SAPCAR 7.21
1. SAP AGENT HOSTA 7.21
1. ROZSZERZENIE ADAPTACYJNE SAP 1.0 EXT

Pobierz również następujące składniki z [Centrum pobierania Microsoft](https://www.microsoft.com/download)

1. Pakiet redystrybucyjny programu Microsoft Visual C++ 2010 (x64) (tylko dla systemu Windows)
1. Sterownik odbc firmy Microsoft dla programu SQL Server (tylko program SQL Server)

Składniki są wymagane do wdrożenia szablonu. Najprostszym sposobem udostępnienia ich szablonowi jest przekazanie ich na konto magazynu platformy Azure i utworzenie sygnatury dostępu współdzielonego (SAS).

Szablony mają następujące parametry:

* sapSystemId: Identyfikator systemu SAP. Służy do tworzenia układu dysku (na przykład /usr/sap/\<sapsid>).

* nazwa komputera: nazwa komputera nowej maszyny wirtualnej. Ten parametr jest również używany przez SAP LaMa. Korzystając z tego szablonu do aprowizowania nowej maszyny wirtualnej jako części kopii systemu, SAP LaMa czeka, aż zostanie osiągnięty host o tej nazwie komputera.

* osType: typ systemu operacyjnego, który chcesz wdrożyć.

* dbtype: Typ bazy danych. Ten parametr jest używany do określenia, ile dodatkowych konfiguracji adresów IP należy dodać i jak powinien wyglądać układ dysku.

* sapSystemSize: rozmiar systemu SAP, który chcesz wdrożyć. Służy do określania typu i rozmiaru wystąpienia maszyny wirtualnej.

* adminUsername: Nazwa użytkownika maszyny wirtualnej.

* adminPassword: Hasło do maszyny wirtualnej. Można również podać klucz publiczny dla SSH.

* sshKeyData: Publiczny klucz SSH dla maszyn wirtualnych. Obsługiwane tylko dla systemów operacyjnych Linux.

* podsieciId: Identyfikator podsieci, której chcesz użyć.

* deployEmptyTarget: Można wdrożyć pusty obiekt docelowy, jeśli chcesz użyć maszyny wirtualnej jako miejsce docelowe dla przeniesienia wystąpienia lub podobne. W takim przypadku nie są dołączone żadne dodatkowe dyski ani konfiguracje IP.

* sapcarLocation: Lokalizacja aplikacji sapcar, która pasuje do wdrażany system operacyjny. sapcar służy do wyodrębniania archiwów, które udostępniasz w innych parametrach.

* sapHostAgentArchiveLocation: Lokalizacja archiwum agenta hosta SAP. Agent hosta SAP jest wdrażany w ramach tego wdrożenia szablonu.

* sapacExtLocation: Lokalizacja rozszerzenia sap adaptacyjne. Uwaga SAP [2343511] zawiera listę minimalnego poziomu poprawki wymaganego dla platformy Azure.

* vcRedistLocation: Lokalizacja środowiska wykonawczego VC, który jest wymagany do zainstalowania rozszerzenia SAP Adaptive Extensions. Ten parametr jest wymagany tylko dla systemu Windows.

* odbcDriverLocation: Lokalizacja sterownika ODBC, który chcesz zainstalować. Obsługiwany jest tylko sterownik OdBC firmy Microsoft dla programu SQL Server.

* sapadmPassword: Hasło użytkownika sapadm.

* sapadmId: Linux Identyfikator użytkownika sapadm użytkownika. Nie jest wymagane dla systemu Windows.

* sapsysGid: Identyfikator grupy Linux grupy sapsys. Nie jest wymagane dla systemu Windows.

* _artifactsLocation: podstawowy identyfikator URI, w którym znajdują się artefakty wymagane przez ten szablon. Gdy szablon zostanie wdrożony przy użyciu towarzyszących skryptów, zostanie użyta prywatna lokalizacja w subskrypcji i ta wartość zostanie wygenerowana automatycznie. Potrzebne tylko wtedy, gdy nie wdrożysz szablonu z gitHub.

* _artifactsLocationSasToken: SasToken wymagane do uzyskania dostępu do _artifactsLocation. Gdy szablon jest wdrażany przy użyciu towarzyszących skryptów, sasToken zostanie wygenerowany automatycznie. Potrzebne tylko wtedy, gdy nie wdrożysz szablonu z gitHub.

### <a name="sap-hana"></a>SAP HANA

W poniższych przykładach zakładamy, że instalujesz SAP HANA z identyfikatorem systemu HN1 i systemem SAP NetWeaver o identyfikatorze systemu AH1. Wirtualne nazwy hostów to hn1-db dla wystąpienia HANA, ah1-db dla dzierżawy HANA używane przez system SAP NetWeaver, ah1-ascs dla SAP NetWeaver ASCS i ah1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Instalowanie sap NetWeaver ASCS dla SAP HANA przy użyciu dysków zarządzanych platformy Azure

Przed uruchomieniem menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta systemu ASCS. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

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

Uruchom swpm i użyj *ah1-ascs* dla *nazwy hosta wystąpienia ASCS*.

![Linux][Logo_Linux] Linux  
Dodaj następujący parametr profilu do profilu agenta hosta SAP, który znajduje się w /usr/sap/hostctrl/exe/host_profile. Aby uzyskać więcej informacji, zobacz UWAGA SAP [2628497].
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Zainstaluj SAP NetWeaver ASCS dla SAP HANA na platformie Azure NetAppFiles (ANF) BETA

> [!NOTE]
> Ta funkcja jest jeszcze ani GA. Aby uzyskać więcej informacji, zapoznaj się z notatką SAP [2815988] (widoczną tylko dla klientów w wersji zapoznawczej).
Otwórz zdarzenie SAP na składniku BC-VCM-LVM-HYPERV i poproś o dołączenie karty pamięci masowej LaMa do wersji zapoznawczej usługi Azure NetApp Files

Anf zapewnia nfs dla platformy Azure. W kontekście SAP LaMa upraszcza to tworzenie wystąpień ABAP Central Services (ASCS) i późniejszą instalację serwerów aplikacji. Wcześniej wystąpienie ASCS musiało działać również jako serwer NFS, a parametr acosprep/nfs_paths musiał zostać dodany do host_profile SAP Hostagent.

#### <a name="anf-is-currently-available-in-these-regions"></a>ANF jest obecnie dostępny w następujących regionach:

Australia Wschodnia, Środkowe Stany Usa, Wschodnie stany USA, Wschodnie Stany Usa 2, Europa Północna, Południowo-Środkowe STANY USA, Europa Zachodnia i Zachodnie Stany Usa 2.

#### <a name="network-requirements"></a>Wymagania sieciowe

Anf wymaga delegowanej podsieci, która musi być częścią tej samej sieci wirtualnej co serwery SAP. Oto przykład takiej konfiguracji.
Na tym ekranie pokazano utworzenie sieci wirtualnej i pierwszej podsieci:

![SAP LaMa tworzy sieć wirtualną dla platformy Azure ANF ](media/lama/sap-lama-createvn-50.png)

W następnym kroku utworzy się podsieć delegowane dla microsoft.NetApp/volumes.

![SAP LaMa dodać delegowaną podsieć ](media/lama/sap-lama-addsubnet-50.png)

![Sap LaMa lista podsieci ](media/lama/sap-lama-subnets.png)

Teraz konto NetApp musi zostać utworzone w witrynie Azure portal:

![SAP LaMa utworzyć konto NetApp ](media/lama/sap-lama-create-netappaccount-50.png)

![Utworzone konto SAP LaMa NetApp ](media/lama/sap-lama-netappaccount.png)

W ramach konta NetApp pula pojemności określa rozmiar i typ dysków dla każdej puli:

![SAP LaMa tworzy pulę pojemności NetApp ](media/lama/sap-lama-capacitypool-50.png)

![Utworzona pula pojemności SAP LaMa NetApp ](media/lama/sap-lama-capacitypool-list.png)

Woluminy NFS można teraz zdefiniować. Ponieważ w jednej puli będą dostępne woluminy dla wielu systemów, należy wybrać samodyreżujący schemat nazewnictwa. Dodanie identyfikatora SID pomaga grupować powiązane woluminy razem. W przypadku ASCS i asa potrzebne są następujące wierzchowce: */sapmnt/\<SID\>*, */usr/sap/\<SID\>*, i */home/\<sid\>adm*. Opcjonalnie */usr/sap/trans* jest potrzebny dla centralnego katalogu transportu, który jest przynajmniej używany przez wszystkie systemy jednego krajobrazu.

> [!NOTE]
> Podczas fazy BETA nazwa woluminów musi być unikatowa w ramach subskrypcji.

![SAP LaMa utworzyć wolumin 1 ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa utworzyć wolumin 2 ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa utworzyć tom 3 ](media/lama/sap-lama-createvolume3-80.png)

Te kroki należy powtórzyć również dla innych woluminów.

![Sap LaMa lista utworzonych woluminów ](media/lama/sap-lama-volumes.png)

Teraz te woluminy muszą być zamontowane w systemach, w których zostanie przeprowadzona początkowa instalacja z SAP SWPM.

Najpierw należy utworzyć punkty instalacji. W takim przypadku identyfikator SID jest AN1, więc należy wykonać następujące polecenia:

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
Następnie woluminy ANF zostaną zamontowane za pomocą następujących poleceń:

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
Polecenia mount mogą być również pochodne z portalu. Lokalne punkty montażu muszą być dostosowane.

Użyj polecenia df -h, aby zweryfikować.

![SAP LaMa punkty systemu operacyjnego poziom ](media/lama/sap-lama-mounts.png)

Teraz instalacja z SWPM musi być wykonana.

Te same kroki muszą być wykonywane dla co najmniej jednego wystąpienia AS.

Po pomyślnej instalacji system musi zostać wykryty w sap LaMa.

Punkty instalacji powinny wyglądać następująco dla ASCS i as wystąpienia:

![SAP LaMa mount points ](media/lama/sap-lama-ascs.png) in LaMa (Jest to przykład. Adresy IP i ścieżka eksportu różnią się od tych, które były używane wcześniej)


#### <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

Jeśli system SAP HANA zostanie zainstalowany przy użyciu narzędzia polecenia hdblcm, użyj parametru --hostname, aby podać wirtualną nazwa hosta. Należy dodać adres IP wirtualnej nazwy hosta bazy danych do interfejsu sieciowego. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

Dodaj inną wirtualną nazwę hosta i adres IP dla nazwy używanej przez serwery aplikacji do łączenia się z dzierżawą HANA.

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

Uruchom instalację wystąpienia bazy danych swpm na maszynie wirtualnej serwera aplikacji, a nie na maszynie wirtualnej HANA. Użyj *ah1-db* dla *hosta bazy danych* w oknie dialogowym Baza danych dla systemu *SAP*.

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>Instalowanie serwera aplikacji SAP NetWeaver dla sap hana

Przed uruchomieniem menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta serwera aplikacji. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

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

Zaleca się użycie parametru profilu SAP NetWeaver dbs/hdb/hdb_use_ident, aby ustawić tożsamość używaną do znajdowania klucza w magazynie użytkowników HDB. Ten parametr można dodać ręcznie po zainstalowaniu wystąpienia bazy danych za pomocą programu SWPM lub uruchomić

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

Jeśli ustawisz go ręcznie, należy również utworzyć nowe wpisy magazynu użytkowników HDB.

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

Użyj *ah1-di-0* dla *nazwy hosta wystąpienia PAS* w oknie dialogowym *Wystąpienie serwera aplikacji podstawowych*.

#### <a name="post-installation-steps-for-sap-hana"></a>Kroki poinstalacyjne dla sap HANA

Przed podjęciem próby wykonania kopii zapasowej dzierżawcy, przeniesieniem dzierżawy lub utworzeniem replikacji systemu należy wykonać kopię zapasową bazy danych systemowej i wszystkich baz danych dzierżawy.

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

W poniższych przykładach zakładamy, że system SAP NetWeaver jest instalowy z identyfikatorem systemu AS1. Wirtualne nazwy hostów są as1-db dla wystąpienia programu SQL Server używanego przez system SAP NetWeaver, as1-ascs dla SAP NetWeaver ASCS i as1-di-0 dla pierwszego serwera aplikacji SAP NetWeaver.

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>Instalowanie sap NetWeaver ASCS dla programu SQL Server

Przed uruchomieniem menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta systemu ASCS. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

Uruchom swpm i użyj *as1-ascs* dla *nazwy hosta wystąpienia ASCS*.

#### <a name="install-sql-server"></a>Instalacja programu SQL Server

Należy dodać adres IP wirtualnej nazwy hosta bazy danych do interfejsu sieciowego. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

Uruchom instalację wystąpienia bazy danych usługi SWPM na maszynie wirtualnej serwera SQL. Użyj SAPINST_USE_HOSTNAME=*as1-db,* aby zastąpić nazwa hosta używaną do łączenia się z programem SQL Server. Jeśli maszyna wirtualna została wdrożona przy użyciu szablonu Usługi Azure Resource Manager, upewnij się, że katalog używany dla plików danych bazy danych został ustawiony na *C:\sql\data* and database log file to *C:\sql\log*.

Upewnij się, że użytkownik *NT AUTHORITY\SYSTEM* ma dostęp do programu SQL Server i ma rolę serwera *sysadmin*. Aby uzyskać więcej informacji, zobacz UWAGA SAP [1877727] i [2562184].

#### <a name="install-sap-netweaver-application-server"></a>Instalowanie serwera aplikacji SAP NetWeaver

Przed uruchomieniem menedżera aprowizacji oprogramowania SAP (SWPM) należy zainstalować adres IP wirtualnej nazwy hosta serwera aplikacji. Zalecanym sposobem jest użycie sapacext. Jeśli adres IP jest montowyny przy użyciu sapacext, upewnij się, że ponownie zainstaluj adres IP po ponownym uruchomieniu komputera.

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

Użyj *as1-di-0* dla *nazwy hosta wystąpienia PAS* w oknie dialogowym *Wystąpienie serwera aplikacji podstawowych*.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="errors-and-warnings-during-discover"></a>Błędy i ostrzeżenia podczas odnajdywanie

* Odmówiono uprawnienia SELECT
  * [Microsoft] [Sterownik programu ODBC SQL Server] [SQL Server] Odmówiono uprawnienia SELECT dla obiektu "log_shipping_primary_databases", bazy danych "msdb", schematu "dbo". [SOAPFaultException]  
  Odmówiono uprawnienia SELECT dla obiektu "log_shipping_primary_databases", bazy danych "msdb", schematu "dbo".
  * Rozwiązanie  
    Upewnij się, że *program NT AUTHORITY\SYSTEM* ma dostęp do programu SQL Server. Patrz uwaga SAP [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>Błędy i ostrzeżenia dotyczące sprawdzania poprawności wystąpienia

* Wyjątek został zgłoszony podczas sprawdzania poprawności magazynu użytkowników HDB  
  * zobacz Przeglądarka dzienników  
    com.sap.nw.lm.aci.api.validation.RuntimeValidationException: Wyjątek w walidatorze o identyfikatorze "RuntimeHDBConnectionValidator" (Sprawdzanie poprawności: "VALIDATION_HDB_USERSTORE"): nie można pobrać magazynu hdbuserstore  
    Magazyn użytkowników HANA nie znajduje się we właściwej lokalizacji
  * Rozwiązanie  
    Upewnij się, że /usr/sap/AH1/hdbclient/install/installation.ini jest poprawny

### <a name="errors-and-warnings-during-a-system-copy"></a>Błędy i ostrzeżenia podczas kopiowania systemu

* Wystąpił błąd podczas sprawdzania poprawności kroku inicjowania obsługi administracyjnej systemu
  * Spowodowane przez: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 150 -h\;hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hak -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hak -r
  * Rozwiązanie  
    Wykonywanie kopii zapasowych wszystkich baz danych w źródłowym systemie HANA

* Rozpoczęcie *kroku* kopiowania systemu wystąpienia bazy danych
  * Operacja agenta hosta '000D3A282BC91EE8A1D76CF1F92E2944' nie powiodła się (OperationException). FaultCode: '127', Komunikat: 'Wykonanie polecenia nie powiodło się. : [Microsoft][ODBC SQL Server Driver][SQL Server]Użytkownik nie ma uprawnień do zmiany bazy danych 'AS2', baza danych nie istnieje lub baza danych nie jest w stanie, który umożliwia sprawdzanie dostępu.')
  * Rozwiązanie  
    Upewnij się, że *program NT AUTHORITY\SYSTEM* ma dostęp do programu SQL Server. Patrz uwaga SAP [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>Błędy i ostrzeżenia podczas klonowania systemu

* Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia w kroku *Wymuszony rejestr i Uruchom agenta wystąpienia* serwera aplikacji lub ASCS
  * Wystąpił błąd podczas próby zarejestrowania agenta wystąpienia. (RemoteException: 'Nie można załadować danych\\wystąpienia z profilu ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Nie można uzyskać dostępu do profilu '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0': Brak takiego pliku lub katalogu.')
  * Rozwiązanie  
   Upewnij się, że udział sapmnt w ASCS/SCS ma pełny dostęp dla SAP_AS1_GlobalAdmin

* Błąd w kroku *Włącz ochronę przed uruchamianiem klonowania*
  * Nie można otworzyć\\pliku ' as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Przyczyna: Brak takiego pliku lub katalogu
  * Rozwiązanie  
    Konto komputera serwera aplikacji wymaga dostępu do zapisu do profilu

### <a name="errors-and-warnings-during-create-system-replication"></a>Błędy i ostrzeżenia podczas tworzenia replikacji systemu

* Wyjątek podczas klikania przycisku Utwórz replikację systemu
  * Spowodowane przez: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException Calling '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 150 -h\;hn1-db -o level=0 status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hak -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hak -r
  * Rozwiązanie  
    Sprawdź, czy sapacext `<hanasid` może być wykonany jako>adm

* Błąd, gdy pełna kopia nie jest włączona w kroku magazynu
  * Wystąpił błąd podczas raportowania komunikatu atrybutu kontekstu dla ścieżki IStorageCopyData.storageVolumeCopyList:1 i pole targetStorageSystemId
  * Rozwiązanie  
    Ignoruj ostrzeżenia w kroku i spróbuj ponownie. Ten problem zostanie rozwiązany w nowym pakiecie pomocy technicznej/patchu SAP LaMa.

### <a name="errors-and-warnings-during-relocate"></a>Błędy i ostrzeżenia podczas przenoszenia

* Ścieżka "/usr/sap/AH1" nie jest dozwolona dla nfs reexports.
  * Szczegółowe informacje można znaleźć w obiekcie SAP Note [2628497.]
  * Rozwiązanie  
    Dodaj eksport ASCS do profilu ascs hostagenta. Patrz uwaga SAP [2628497]

* Funkcja nie została zaimplementowana podczas przenoszenia ASCS
  * Polecenie wyjście: exportfs: host:/usr/sap/AX1: Funkcja nie została zaimplementowana
  * Rozwiązanie  
    Upewnij się, że usługa serwera NFS jest włączona na docelowej maszynie wirtualnej przenoszenia

### <a name="errors-and-warnings-during-application-server-installation"></a>Błędy i ostrzeżenia podczas instalacji serwera aplikacji

* Błąd podczas wykonywania kroku SAPinst: getProfileDir
  * BŁĄD: (Ostatni błąd zgłoszony przez krok: Caught ESAPinstException w wywołaniu modułu: Walidator kroku '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_readProfileDir|ind|ind|ind|readProfile|0|getProfileDir' zgłosił błąd: Węzeł \\\as1-ascs\sapmnt\AS1\SYS\profile nie istnieje. Uruchom SAPinst w trybie interaktywnym, aby rozwiązać ten problem)
  * Rozwiązanie  
    Upewnij się, że swpm jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Tego użytkownika można skonfigurować w Kreatorze instalacji serwera aplikacji

* Błąd podczas wykonywania kroku SAPinst: askUnicode
  * BŁĄD: (Ostatni błąd zgłoszony przez krok: Caught ESAPinstException w wywołaniu modułu: Walidator kroku '| NW_DI|ind|ind|ind|ind|0|0| NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0| NW_getUnicode|ind|ind|ind|unicode|0|askUnicode' zgłosił błąd: Uruchom SAPinst w trybie interaktywnym, aby rozwiązać ten problem)
  * Rozwiązanie  
    Jeśli używasz najnowszego jądra SAP, SWPM nie może określić, czy system jest już systemem unicode przy użyciu serwera komunikatów ASCS. Więcej informacji można znaleźć w notatce SAP [2445033.]  
    Ten problem zostanie rozwiązany w nowym pakiecie pomocy technicznej/patchu SAP LaMa.  
    Ustaw parametr profilu OS_UNICODE=uc w domyślnym profilu systemu SAP, aby obejść ten problem.

* Błąd podczas wykonywania kroku SAPinst: dCheckGivenServer
  * Błąd podczas wykonywania kroku SAPinst: dCheckGivenServer" version="1.0" ERROR: \<(Ostatni błąd zgłoszony przez krok: p> Instalacja została anulowana przez użytkownika. \</p>
  * Rozwiązanie  
    Upewnij się, że swpm jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Tego użytkownika można skonfigurować w Kreatorze instalacji serwera aplikacji

* Błąd podczas wykonywania kroku SAPinst: checkClient
  * Błąd podczas wykonywania kroku SAPinst: checkClient" version="1.0" ERROR: \<(Ostatni błąd zgłoszony przez krok: p> Instalacja została anulowana przez użytkownika. \</p>)
  * Rozwiązanie  
    Upewnij się, że sterownik Microsoft ODBC dla programu SQL Server jest zainstalowany na maszynie wirtualnej, na której chcesz zainstalować serwer aplikacji

* Błąd podczas wykonywania kroku SAPinst: copyScripts
  * Ostatni błąd zgłoszony przez krok: wywołanie systemowe nie powiodło się. SZCZEGÓŁY: Błąd 13 (0x0000000d) (Odmowa uprawnień) podczas wykonywania wywołania systemowego "fopenU" z parametrem (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), linia (494) w pliku (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), ślad stosu:  
  CThrThread.cpp: 85: CThrThread::threadFunction()  
  CSiServiceSet.cpp: 63: CSiServiceSet::executeService()  
  CSiStepExecute.cpp: 913: CSiStepExecute::execute()  
  EJSController.cpp: 179: EJSControllerImpl::executeScript()  
  JSExtension.hpp: 1136: CallFunctionBase::call()  
  iaxxcfile.cpp: 183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp: 1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp: 773: CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp: 233: CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 29: CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp: 265: CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp: 58: CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath \\& \aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp: 456: CSyFileStream2Impl::open()
  * Rozwiązanie  
    Upewnij się, że swpm jest uruchomiony z użytkownikiem, który ma dostęp do profilu. Tego użytkownika można skonfigurować w Kreatorze instalacji serwera aplikacji

* Błąd podczas wykonywania kroku SAPinst: askPasswords
  * Ostatni błąd zgłoszony przez krok: wywołanie systemowe nie powiodło się. SZCZEGÓŁY: Błąd 5 (0x00000005) (Odmowa dostępu.) w wykonaniu wywołania systemowego "NetValidatePasswordPolicy" z parametrem (...), linia (359) w pliku (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), ślad stosu:  
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
  iaxxcaccount.cpp: 107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp: 1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp: 430: CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp: 297: ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * Rozwiązanie  
    Upewnij się, że dodano regułę hosta w *kroku Izolacja,* aby umożliwić komunikację z maszyny Wirtualnej do kontrolera domeny

## <a name="next-steps"></a>Następne kroki
* [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure][hana-ops-guide]
* [Planowanie i implementacja maszyn wirtualnych platformy Azure dla systemu SAP][planning-guide]
* [Wdrożenie maszyn wirtualnych platformy Azure dla systemu SAP][deployment-guide]
* [Wdrożenie usługi DBMS maszyn wirtualnych platformy Azure dla systemu SAP][dbms-guide]
