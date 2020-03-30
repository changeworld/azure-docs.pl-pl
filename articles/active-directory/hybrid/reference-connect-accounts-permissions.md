---
title: 'Usługa Azure AD Connect: konta i uprawnienia | Dokumenty firmy Microsoft'
description: W tym temacie opisano konta używane i utworzone oraz wymagane uprawnienia.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253769"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Accounts and permissions (Azure AD Connect: konta i uprawnienia)

## <a name="accounts-used-for-azure-ad-connect"></a>Konta używane w usłudze Azure AD Connect

![przegląd kont](media/reference-connect-accounts-permissions/account5.png)

Usługa Azure AD Connect używa 3 kont w celu synchronizowania informacji z lokalnej lub usługi Windows Server Active Directory z usługą Azure Active Directory.  Te konta są:

- **Konto łącznika usług AD DS**: używane do odczytu/zapisu informacji w usłudze Active Directory systemu Windows Server

- **Konto usługi ADSync**: używane do uruchamiania usługi synchronizacji i uzyskiwania dostępu do bazy danych SQL

- **Konto usługi Azure AD Connector:** służy do zapisywania informacji w usłudze Azure AD

Oprócz tych trzech kont używanych do uruchamiania usługi Azure AD Connect, do zainstalowania usługi Azure AD Connect potrzebne będą również następujące dodatkowe konta.  Są to:

- **Konto administratora lokalnego:** administrator, który instaluje usługę Azure AD Connect i który ma uprawnienia administratora lokalnego na komputerze.

- **Konto administratora przedsiębiorstwa usług AD DS**: Opcjonalnie używane do tworzenia "konta łącznika usług AD DS" powyżej.

- **Konto administratora globalnego usługi Azure AD:** służy do tworzenia konta usługi Azure AD Connector i konfigurowania usługi Azure AD.

- **Konto SA SQL (opcjonalnie)**: używane do tworzenia bazy danych ADSync podczas korzystania z pełnej wersji programu SQL Server.  Ten program SQL Server może być lokalny lub zdalny dla instalacji usługi Azure AD Connect.  To konto może być tym samym kontem co administrator przedsiębiorstwa.  Inicjowanie obsługi administracyjnej bazy danych może być teraz wykonywane poza pasmem przez administratora SQL, a następnie instalowane przez administratora usługi Azure AD Connect z prawami właściciela bazy danych.  Aby uzyskać informacje na ten temat, zobacz [Instalowanie usługi Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> Od kompilacji 1.4.###.# nie jest już obsługiwana używanie konta administratora przedsiębiorstwa lub administratora domeny jako konta łącznika usług AD DS.  Jeśli podczas określania **użycia istniejącego konta**zostanie podjęta próba wprowadzenia konta, które jest administratorem przedsiębiorstwa lub administratorem domeny, zostanie wyświetlony błąd.

> [!NOTE]
> Jest obsługiwany do zarządzania kontami administracyjnymi używanymi w usłudze Azure AD Connect z lasu administracyjnego ESAE (znany również jako "Czerwony las").
> Dedykowane lasy administracyjne pozwalają organizacjom hostować konta administratora, stacje robocze i grupy w środowisku zapewniającym silniejsze opcje zabezpieczeń niż te używane w środowisku produkcyjnym.
> Aby dowiedzieć się więcej o dedykowanych lasach administracyjnych, zapoznaj się z [podejściem ESAE do projektowania lasów administracyjnych](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> Rola Administratora globalnego nie jest wymagana po wstępnej konfiguracji, a jedynym wymaganym kontem będzie konto roli **Konta synchronizacji katalogów.** Nie musi to oznaczać, że będziesz chciał po prostu usunąć konto z rolą Administratora globalnego. Lepiej jest zmienić rolę na mniej potężną rolę, ponieważ całkowite usunięcie konta może spowodować problemy, jeśli kiedykolwiek będziesz musiał ponownie uruchomić kreatora. Zmniejszając uprawnienia roli, zawsze można ponownie podnieść uprawnienia, jeśli trzeba ponownie korzystać z kreatora usługi Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalowanie usługi Azure AD Connect
Kreator instalacji usługi Azure AD Connect oferuje dwie różne ścieżki:

* W ustawieniach ekspresowych kreator wymaga więcej uprawnień.  Jest to tak, że można łatwo skonfigurować konfigurację, bez konieczności tworzenia użytkowników lub konfigurowania uprawnień.
* W ustawieniach niestandardowych kreator oferuje więcej opcji i opcji. Istnieją jednak sytuacje, w których należy upewnić się, że masz prawidłowe uprawnienia samodzielnie.



## <a name="express-settings-installation"></a>Instalacja ustawień ekspresowych
W ustawieniach expressu kreator instalacji prosi o następujące pytania:

  - Poświadczenia administratora przedsiębiorstwa usług AD DS
  - Poświadczenia administratora globalnego usługi Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>Poświadczenia administratora usług AD DS enterprise
Konto administratora w przedsiębiorstwie usług AD DS służy do konfigurowania lokalnej usługi Active Directory. Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Administrator przedsiębiorstwa, a nie administrator domeny, powinni upewnić się, że uprawnienia w usłudze Active Directory można ustawić we wszystkich domenach.

W przypadku uaktualniania z programu DirSync poświadczenia administratorów przedsiębiorstwa usług AD DS są używane do resetowania hasła dla konta używanego przez program DirSync. Potrzebne są również poświadczenia administratora globalnego usługi Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Poświadczenia administratora globalnego usługi Azure AD
Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Służy do tworzenia konta usługi Azure AD Connector używanego do synchronizowania zmian w usłudze Azure AD. Konto umożliwia również synchronizację jako funkcję w usłudze Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Konto łącznika usług AD DS wymagane uprawnienia do ustawień ekspresowych
Konto łącznika usług AD DS jest tworzone do odczytu i zapisywania w systemie Windows Server AD i ma następujące uprawnienia podczas tworzenia przez ustawienia ekspresowe:

| Uprawnienie | Używana do |
| --- | --- |
| <li>Replikowanie zmian w katalogu</li><li>Wszystkie zmiany katalogu replikacji |Synchronizacja skrótu hasła |
| Odczyt/Zapis wszystkich właściwości Użytkownik |Hybryda importu i wymiany |
| Odczyt/Zapis wszystkich właściwości iNetOrgPerson |Hybryda importu i wymiany |
| Grupa odczytu/zapisu wszystkich właściwości |Hybryda importu i wymiany |
| Odczyt/Zapis wszystkich właściwości Kontakt |Hybryda importu i wymiany |
| Resetowanie hasła |Przygotowanie do włączania zapisywania haseł |

### <a name="express-installation-wizard-summary"></a>Podsumowanie kreatora instalacji ekspresowej

![Instalacja ekspresowa](./media/reference-connect-accounts-permissions/express.png)

Poniżej przedstawiono podsumowanie stron kreatora instalacji ekspresowej, zebranych poświadczeń i do czego są używane.

| Strona kreatora | Zebrane poświadczenia | Wymagane uprawnienia | Używany do |
| --- | --- | --- | --- |
| Nie dotyczy |Użytkownik uruchamiany kreatora instalacji |Administrator serwera lokalnego |<li>Tworzy konto usługi ADSync, które jest używane do uruchamiania usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Utworzenie konta usługi Azure AD Connector używanego do operacji synchronizacji w toku w usłudze Azure AD.</li> |
| Łączenie z usługami AD DS |Lokalne poświadczenia usługi Active Directory |Członek grupy Administratorzy przedsiębiorstwa (EA) w usłudze Active Directory |<li>Tworzy konto łącznika usług AD DS w usłudze Active Directory i udziela mu uprawnień. To utworzone konto służy do odczytu i zapisu informacji o katalogu podczas synchronizacji.</li> |


## <a name="custom-installation-settings"></a>Niestandardowe ustawienia instalacji

Po zainstalowaniu ustawień niestandardowych kreator oferuje więcej opcji i opcji. 

### <a name="custom-installation-wizard-summary"></a>Podsumowanie kreatora instalacji niestandardowej

Poniżej przedstawiono podsumowanie stron kreatora instalacji niestandardowej, zebranych poświadczeń i do czego są używane.

![Instalacja ekspresowa](./media/reference-connect-accounts-permissions/customize.png)

| Strona kreatora | Zebrane poświadczenia | Wymagane uprawnienia | Używany do |
| --- | --- | --- | --- |
| Nie dotyczy |Użytkownik uruchamiany kreatora instalacji |<li>Administrator serwera lokalnego</li><li>W przypadku korzystania z pełnego programu SQL Server użytkownik musi być administratorem systemu (SA) w języku SQL</li> |Domyślnie tworzy konto lokalne, które jest używane jako konto usługi aparatu synchronizacji. Konto jest tworzone tylko wtedy, gdy administrator nie określi określonego konta. |
| Instalowanie usług synchronizacji, opcja Konto usługi |Poświadczenia konta usługi AD lub konta użytkownika lokalnego |Użytkownik, uprawnienia są przyznawane przez kreatora instalacji |Jeśli administrator określa konto, to konto jest używane jako konto usługi dla usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Utworzenie konta usługi Azure AD Connector używanego do operacji synchronizacji w toku w usłudze Azure AD.</li> |
| Podłączanie katalogów |Lokalne poświadczenia usługi Active Directory dla każdego lasu połączonego z usługą Azure AD |Uprawnienia zależą od tego, które funkcje można włączyć i można je znaleźć na koncie Tworzenie łącznika usług AD DS |To konto służy do odczytywania i zapisywania informacji katalogowych podczas synchronizacji. |
| Serwery usług AD FS |Dla każdego serwera na liście kreator zbiera poświadczenia, gdy poświadczenia logowania użytkownika z uruchomionym kreatorem są niewystarczające do nawiązania połączenia |Administrator domeny |Instalacja i konfiguracja roli serwera usług AD FS. |
| Serwery proxy aplikacji sieci Web |Dla każdego serwera na liście kreator zbiera poświadczenia, gdy poświadczenia logowania użytkownika z uruchomionym kreatorem są niewystarczające do nawiązania połączenia |Administrator lokalny na komputerze docelowym |Instalacja i konfiguracja roli serwera WAP. |
| Poświadczenia zaufania serwera proxy |Poświadczenia zaufania usługi federacyjnej (poświadczenia używane przez serwer proxy do rejestrowania certyfikatu zaufania z urzędu certyfikacji |Konto domeny, które jest administratorem lokalnym serwera usług AD FS |Początkowa rejestracja certyfikatu zaufania FS-WAP. |
| Strona Konto usługi usług AD FS "Użyj opcji konta użytkownika domeny" |Poświadczenia konta użytkownika usługi AD |Użytkownik domeny |Konto użytkownika usługi Azure AD, którego poświadczenia są podane, jest używane jako konto logowania usługi AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Tworzenie konta łącznika usług AD DS

>[!IMPORTANT]
>Nowy moduł programu PowerShell o nazwie ADSyncConfig.psm1 został wprowadzony z kompilacją **1.1.880.0** (wydaną w sierpniu 2018 r.), która zawiera kolekcję poleceń cmdlet ułatwiające konfigurowanie prawidłowych uprawnień usługi Active Directory dla konta usługi Azure AD DS Connector.
>
>Aby uzyskać więcej informacji, zobacz [Usługa Azure AD Connect: Konfigurowanie uprawnień konta łącznika usług AD DS](how-to-connect-configure-ad-ds-connector-account.md)

Konto określone na stronie **Połącz katalogi** musi być obecne w usłudze Active Directory przed instalacją.  Usługa Azure AD Connect w wersji 1.1.524.0 i nowsze wersje ma możliwość umożliwienia kreatorowi usługi Azure AD Connect **utworzenia konta łącznika usług AD DS** używanego do łączenia się z usługą Active Directory.  

Musi również mieć wymagane uprawnienia przyznane. Kreator instalacji nie weryfikuje uprawnień, a wszelkie problemy są znajdowane tylko podczas synchronizacji.

Uprawnienia, których potrzebujesz, zależą od opcjonalnych funkcji, które można włączyć. Jeśli masz wiele domen, uprawnienia muszą być przyznane dla wszystkich domen w lesie. Jeśli żadna z tych funkcji nie zostanie włączona, wystarczające są domyślne uprawnienia **użytkownika domeny.**

| Funkcja | Uprawnienia |
| --- | --- |
| funkcja ms-DS-ConsistencyGuid |Uprawnienia do zapisu do atrybutu ms-DS-ConsistencyGuid udokumentowane w [pojęciach projektu — używanie ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótu hasła |<li>Replikowanie zmian w katalogu</li>  <li>Wszystkie zmiany katalogu replikacji |
| Wdrażanie hybrydowe programu Exchange |Uprawnienia do zapisu atrybutów udokumentowanych w [programie Exchange hybrydowy storcowanie](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontaktów. |
| Folder publiczny programu Exchange Mail |Uprawnienia do odczytu atrybutów udokumentowanych w [folderze publicznym poczty programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do zapisu do atrybutów udokumentowanych w [wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia przyznane za pomocą skryptu programu PowerShell zgodnie z opisem w [writeback urządzenia](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Umożliwia zapisywanie grup **usługi Office 365** do lasu z zainstalowanym programem Exchange.  Aby uzyskać więcej informacji, zobacz [Grupowe odpisy .](how-to-connect-preview.md#group-writeback)|

## <a name="upgrade"></a>Uaktualnienie
Podczas uaktualniania z jednej wersji usługi Azure AD Connect do nowej wersji, należy wykonać następujące uprawnienia:

>[!IMPORTANT]
>Począwszy od kompilacji 1.1.484, usługa Azure AD Connect wprowadziła błąd regresji, który wymaga uprawnień sysadmin do uaktualnienia bazy danych SQL.  Ten błąd jest korygowany w kompilacji 1.1.647.  Jeśli uaktualniasz do tej kompilacji, będziesz potrzebować uprawnień sysadmin.  Uprawnienia Dbo nie są wystarczające.  Jeśli spróbujesz uaktualnić usługę Azure AD Connect bez uprawnień sysadmin, uaktualnienie zakończy się niepowodzeniem i usługa Azure AD Connect nie będzie już działać poprawnie później.  Firma Microsoft jest tego świadoma i pracuje nad jego poprawą.


| Główne | Wymagane uprawnienia | Używana do |
| --- | --- | --- |
| Użytkownik uruchamiany kreatora instalacji |Administrator serwera lokalnego |Aktualizuj pliki binarne. |
| Użytkownik uruchamiany kreatora instalacji |Członek ADSyncAdmins |Wprowadzanie zmian w regułach synchronizacji i innej konfiguracji. |
| Użytkownik uruchamiany kreatora instalacji |Jeśli używasz pełnego serwera SQL: DBO (lub podobne) bazy danych aparatu synchronizacji |Wprowadzanie zmian na poziomie bazy danych, takich jak aktualizowanie tabel za pomocą nowych kolumn. |

## <a name="more-about-the-created-accounts"></a>Więcej informacji o utworzonych kontach
### <a name="ad-ds-connector-account"></a>Konto łącznika usług AD DS
Jeśli używasz ustawień ekspresowych, w usłudze Active Directory zostanie utworzone konto używane do synchronizacji. Utworzone konto znajduje się w domenie głównej lasu w kontenerze Użytkownicy i ma swoją nazwę poprzedzoną **MSOL_**. Konto jest tworzone przy za pomocą długiego złożonego hasła, które nie wygasa. Jeśli masz zasady haseł w domenie, upewnij się, że długie i złożone hasła będą dozwolone dla tego konta.

![Konto usługi AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Jeśli używasz ustawień niestandardowych, to jesteś odpowiedzialny za utworzenie konta przed rozpoczęciem instalacji.  Zobacz Tworzenie konta łącznika usług AD DS.

### <a name="adsync-service-account"></a>Konto usługi ADSync
Usługa synchronizacji może działać na różnych kontach. Może działać w ramach **konta usługi wirtualnej** (VSA), **konta usługi zarządzanej grupy** (gMSA/sMSA) lub zwykłego konta użytkownika. Obsługiwane opcje zostały zmienione wraz z kwietniową wersją Connect 2017 po wykonaniu nowej instalacji. Jeśli uaktualnisz z wcześniejszej wersji usługi Azure AD Connect, te dodatkowe opcje nie są dostępne.

| Typ konta | Opcja instalacji | Opis |
| --- | --- | --- |
| [Konto usługi wirtualnej](#virtual-service-account) | Ekspresowe i niestandardowe, kwiecień 2017 r. i nowsze | Jest to opcja używana dla wszystkich instalacji ekspresowych, z wyjątkiem instalacji na kontrolerze domeny. Dla niestandardowych jest to opcja domyślna, chyba że używana jest inna opcja. |
| [Konto usługi zarządzane przez grupę](#group-managed-service-account) | Custom, kwiecień 2017 r. i nowsze | Jeśli używasz zdalnego serwera SQL, zalecamy użycie konta usługi zarządzanej przez grupę. |
| [Konto użytkownika](#user-account) | Ekspresowe i niestandardowe, kwiecień 2017 r. i nowsze | Konto użytkownika z prefiksem AAD_ jest tworzone tylko podczas instalacji w systemie Windows Server 2008 i po zainstalowaniu na kontrolerze domeny. |
| [Konto użytkownika](#user-account) | Ekspresowe i niestandardowe, marzec 2017 r. i wcześniejsze | Podczas instalacji tworzone jest konto lokalne z AAD_. Podczas korzystania z instalacji niestandardowej można określić inne konto. |

Jeśli używasz Connect with a build z marca 2017 lub wcześniej, nie należy resetować hasła na koncie usługi, ponieważ system Windows niszczy klucze szyfrowania ze względów bezpieczeństwa. Nie można zmienić konta na inne konto bez ponownej instalacji usługi Azure AD Connect. Jeśli uaktualnisz do kompilacji z kwietnia 2017 lub później, jest obsługiwana zmiana hasła na koncie usługi, ale nie można zmienić używanego konta.

> [!Important]
> Konto usługi można ustawić tylko przy pierwszej instalacji. Nie jest obsługiwana zmiana konta usługi po zakończeniu instalacji.

Jest to tabela domyślnych, zalecanych i obsługiwanych opcji dla konta usługi synchronizacji.

Legenda:

- **Pogrubienie** wskazuje opcję domyślną i w większości przypadków zalecaną opcję.
- Kursywa wskazuje zalecaną opcję, gdy nie jest to opcja *domyślna.*
- 2008 - Opcja domyślna po zainstalowaniu w systemie Windows Server 2008
- Niepogrubienie — opcja obsługiwana
- Konto lokalne — konto użytkownika lokalnego na serwerze
- Konto domeny — konto użytkownika domeny
- sMSA — [samodzielne konto usługi zarządzanej](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA — [konto usługi zarządzanej grupy](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Niestandardowy | Zdalny sql</br>Niestandardowy |
| --- | --- | --- | --- |
| **samodzielna/maszyna grupy roboczej** | Nieobsługiwane | **Vsa**</br>Konto lokalne (2008)</br>Konto lokalne |  Nieobsługiwane |
| **maszyna przyłączona do domeny** | **Vsa**</br>Konto lokalne (2008) | **Vsa**</br>Konto lokalne (2008)</br>Konto lokalne</br>Konto domeny</br>sMSA,gMSA | **Gmsa**</br>Konto domeny |
| **Kontroler domeny** | **Konto domeny** | *Gmsa*</br>**Konto domeny**</br>z o.o.| *Gmsa*</br>**Konto domeny**|

#### <a name="virtual-service-account"></a>Konto usługi wirtualnej
Konto usługi wirtualnej to specjalny typ konta, które nie ma hasła i jest zarządzane przez system Windows.

![Vsa](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA jest przeznaczony do użycia w scenariuszach, w których aparat synchronizacji i SQL znajdują się na tym samym serwerze. Jeśli używasz zdalnego języka SQL, zalecamy użycie konta usługi zarządzanej grupy.

Ta funkcja wymaga systemu Windows Server 2008 R2 lub nowszego. Jeśli zainstalujesz usługę Azure AD Connect w systemie Windows Server 2008, instalacja powróci do korzystania z [konta użytkownika.](#user-account)

#### <a name="group-managed-service-account"></a>Konto usługi zarządzanej grupy
Jeśli używasz zdalnego serwera SQL, zalecamy użycie **konta usługi zarządzanej przez grupę**. Aby uzyskać więcej informacji na temat przygotowania usługi Active Directory dla konta usługi zarządzanej grupowe, zobacz [Omówienie kont usług zarządzanych grupy](https://technet.microsoft.com/library/hh831782.aspx).

Aby użyć tej opcji, na stronie [Zainstaluj wymagane składniki](how-to-connect-install-custom.md#install-required-components) wybierz pozycję Użyj **istniejącego konta usługi**i wybierz pozycję Konto usługi **zarządzanej**.  
![Vsa](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Obsługiwane jest również używanie [autonomicznego konta usługi zarządzanej](https://technet.microsoft.com/library/dd548356.aspx). Jednak te mogą być używane tylko na komputerze lokalnym i nie ma żadnych korzyści, aby korzystać z nich za pomocą domyślnego konta usługi wirtualnej.

Ta funkcja wymaga systemu Windows Server 2012 lub nowszego. Jeśli chcesz użyć starszego systemu operacyjnego i użyć zdalnego SQL, musisz użyć [konta użytkownika](#user-account).

#### <a name="user-account"></a>Konto użytkownika
Kreator instalacji tworzy lokalne konto usługi (chyba że zostanie określone konto, którego chcesz użyć w ustawieniach niestandardowych). Konto jest poprzedzone **AAD_** i używane do uruchamiania jako usługi synchronizacji rzeczywistej. Jeśli zainstalujesz usługę Azure AD Connect na kontrolerze domeny, konto zostanie utworzone w domenie. Konto usługi **AAD_** musi znajdować się w domenie, jeśli:
   - używasz serwera zdalnego z uruchomionym serwerem SQL
   - używasz serwera proxy, który wymaga uwierzytelniania

![Konto usługi synchronizacji](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Konto jest tworzone przy za pomocą długiego złożonego hasła, które nie wygasa.

To konto służy do przechowywania haseł dla innych kont w bezpieczny sposób. Te inne hasła kont są przechowywane zaszyfrowane w bazie danych. Klucze prywatne kluczy szyfrowania są chronione za pomocą szyfrowania klucza tajnego usług kryptograficznych przy użyciu interfejsu API ochrony danych systemu Windows (DPAPI).

Jeśli używasz pełnego programu SQL Server, konto usługi jest DBO utworzonej bazy danych dla aparatu synchronizacji. Usługa nie będzie działać zgodnie z oczekiwaniami z innymi uprawnieniami. Zostanie również utworzony identyfikator SQL.

Konto ma również uprawnienia do plików, kluczy rejestru i innych obiektów związanych z Aparatem synchronizacji.

### <a name="azure-ad-connector-account"></a>Konto łącznika usługi Azure AD
Konto w usłudze Azure AD jest tworzone do użytku usługi synchronizacji. To konto można zidentyfikować za pomocą jego nazwy wyświetlanej.

![Konto usługi AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Nazwę serwera, na których jest używane konto, można zidentyfikować w drugiej części nazwy użytkownika. Na zdjęciu nazwa serwera to DC1. Jeśli masz serwery przejściowe, każdy serwer ma swoje własne konto.

Konto jest tworzone przy za pomocą długiego złożonego hasła, które nie wygasa. Przyznawana jest specjalna rola **Konta synchronizacji katalogów,** która ma tylko uprawnienia do wykonywania zadań synchronizacji katalogów. Tej specjalnej wbudowanej roli nie można udzielić poza kreatorem usługi Azure AD Connect. Portal Azure pokazuje to konto z rolą **Użytkownik**.

Istnieje limit 20 kont usługi synchronizacji w usłudze Azure AD. Aby uzyskać listę istniejących kont usługi Azure AD w usłudze Azure AD, uruchom następujące polecenie cmdlet usługi Azure AD PowerShell:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Aby usunąć nieużywane konta usługi Azure AD, uruchom następujące polecenie cmdlet usługi Azure AD PowerShell:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Przed użyciem powyższych poleceń programu PowerShell należy zainstalować [moduł programu Azure Active Directory PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) i połączyć się z wystąpieniem usługi Azure AD przy użyciu usługi [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Aby uzyskać dodatkowe informacje dotyczące zarządzania hasłem dla konta usługi Azure AD Łącznik i resetowania go, zobacz [Zarządzanie kontem usługi Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Dokumentacja pokrewna
Jeśli nie zapoznałeś się z dokumentacją dotyczącą [integrowania tożsamości lokalnych z usługą Azure Active Directory,](whatis-hybrid-identity.md)poniższa tabela zawiera łącza do tematów pokrewnych.

|Temat |Link|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](how-to-connect-install-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnienie przy użyciu narzędzia Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywania licencji](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
