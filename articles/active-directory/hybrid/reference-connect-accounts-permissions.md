---
title: 'Program Azure AD Connect: Konta i uprawnienia | Dokumentacja firmy Microsoft'
description: W tym temacie opisano konta używane i utworzone i wymagane są uprawnienia.
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
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b1aadb84bc92981b9adf1b1affa69f5f2ec25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919165"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Program Azure AD Connect: Konta i uprawnienia

## <a name="accounts-used-for-azure-ad-connect"></a>Konta używane dla usługi Azure AD Connect

![omówienie kont](media/reference-connect-accounts-permissions/account5.png)

Program Azure AD Connect używa kont 3, aby można było zsynchronizować informacji z lokalnych lub Windows Server Active Directory do usługi Azure Active Directory.  Te konta są:

- **Konta DS łącznika usługi AD**: używany do odczytu/zapisu informacji usługi Active Directory systemu Windows Server

- **Konto usługi ADSync**: używane do uruchamiania usługi synchronizacji i dostęp do bazy danych SQL

- **Konto usługi Azure AD Connector**: używany do zapisywania informacji do usługi Azure AD

Oprócz tych trzech kont używanych do uruchamiania usługi Azure AD Connect należy również następujące dodatkowe konta do zainstalowania Azure AD Connect.  Są to:

- **Konto administratora lokalnego**: Administrator, który jest instalowany program Azure AD Connect i kto ma uprawnienia administratora lokalnego na komputerze.

- **Konto administratora przedsiębiorstwa usług AD**: Opcjonalnie umożliwia tworzenie "łącznik usługi AD DS konto" powyżej.

- **Konta usługi Azure AD administratora globalnego**: używane do utworzenia konta łącznika usługi Azure AD i skonfigurować usługę Azure AD.

- **(Opcjonalnie) dla konta SQL SA**: użyty do utworzenia bazy danych ADSync, korzystając z pełnej wersji programu SQL Server.  Serwer SQL może być lokalne lub zdalne do instalacji usługi Azure AD Connect.  To konto może być to samo konto jako Administrator przedsiębiorstwa.  Teraz inicjowania obsługi usługi bazy danych mogą być wykonywane poza pasmem administrator usługi SQL, a następnie przez administratora usługi Azure AD Connect z uprawnieniami właściciela bazy danych.  Aby uzyskać informacje na ten temat, zobacz [zainstalować program Azure AD Connect przy użyciu uprawnień administratora delegowanego SQL](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>Instalowanie usługi Azure AD Connect
Kreatora instalacji usługi Azure AD Connect oferuje dwie różne ścieżki:

* W ustawieniach Express kreator wymaga wyższego poziomu uprawnień.  Jest to tak, aby go skonfigurować konfigurację, bez konieczności tworzenia użytkowników lub skonfigurować uprawnienia.
* W obszarze ustawienia niestandardowego kreatora oferuje więcej możliwości i opcji. Istnieją jednak sytuacje, w których należy upewnić się, że możesz mieć odpowiednie uprawnienia.



## <a name="express-settings-installation"></a>Ekspresowa instalacja ustawienia
Kreator instalacji ustawień ekspresowych prośbą o do wykonania poniższych czynności:

  - Poświadczenia administratora przedsiębiorstwa usługi AD DS
  - Poświadczenia administratora globalnego usługi AD platformy Azure

### <a name="ad-ds-enterprise-admin-credentials"></a>Poświadczenia administratora przedsiębiorstwa usług AD
Konto administratora przedsiębiorstwa usługi AD DS jest używane do konfigurowania usługi Active Directory w środowisku lokalnym. Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Administrator przedsiębiorstwa nie Administrator domeny powinien upewnij się, że uprawnienia w usłudze Active Directory można ustawić we wszystkich domenach.

Jeśli uaktualniasz z narzędzia DirSync, poświadczenia Administratorzy przedsiębiorstwa usług AD DS są używane do resetowania hasła dla konta używanego przez narzędzie DirSync. Należy również poświadczenia administratora globalnego usługi Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Poświadczenia administratora globalnego usługi AD platformy Azure
Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Służy do utworzenia konta łącznika usługi Azure AD używane do synchronizowania zmian z usługą Azure AD. To konto umożliwia również synchronizacji jako funkcję w usłudze Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Usługi AD DS łącznika konta wymagane uprawnienia dla ustawień ekspresowych
Konta usługi AD DS łącznika jest tworzony dla odczytu i zapisu do systemu Windows Server AD i ma następujące uprawnienia w tworzeniu ustawień ekspresowych:

| Uprawnienie | Używana do |
| --- | --- |
| <li>Replikuj zmiany katalogu</li><li>Replikacja katalogu zmienia wszystkie |Synchronizacja skrótów haseł |
| Odczyt/zapis wszystkich właściwości użytkownika |Importowanie i Exchange hybrydowych |
| Odczyt/zapis wszystkich właściwości iNetOrgPerson |Importowanie i Exchange hybrydowych |
| Grupa wszystkich właściwości odczytu/zapisu |Importowanie i Exchange hybrydowych |
| Skontaktuj się z wszystkich właściwości odczytu/zapisu |Importowanie i Exchange hybrydowych |
| Resetowanie hasła |Przygotowanie do włączania funkcji zapisywania zwrotnego haseł |

### <a name="express-installation-wizard-summary"></a>Podsumowanie Kreatora instalacji ekspresowej

![Instalacja ekspresowa](./media/reference-connect-accounts-permissions/express.png)

Oto Podsumowanie stron kreatora instalacji ekspresowej, poświadczenia gromadzone, i ich zastosowania.

| Strona kreatora | Poświadczenia zebrane | Wymagane uprawnienia | Używane dla |
| --- | --- | --- | --- |
| ND |Uruchomienie Kreatora instalacji użytkownika |Administrator serwera lokalnego |<li>Tworzy konto usługi ADSync, który służy do uruchamiania usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie konta łącznika usługi Azure AD, które jest używane dla operacji synchronizacji w toku w usłudze Azure AD.</li> |
| Łączenie z usługami AD DS |Poświadczenia usługi Active Directory w środowisku lokalnym |Członek grupy Administratorzy przedsiębiorstwa (EA) w usłudze Active Directory |<li>Tworzy konto usługi AD DS Connector w usłudze Active Directory, a następnie przyznaje uprawnienia do niego. Utworzone konto służy do odczytywania i zapisywania informacji o katalogu podczas synchronizacji.</li> |


## <a name="custom-installation-settings"></a>Ustawienia instalacji niestandardowej

Za pomocą ustawień niestandardowych instalacji Kreator oferuje więcej możliwości i opcji. 

### <a name="custom-installation-wizard-summary"></a>Podsumowanie Kreatora instalacji niestandardowej

Oto Podsumowanie strony Kreatora instalacji niestandardowej poświadczenia gromadzone, i ich zastosowania.

![Instalacja ekspresowa](./media/reference-connect-accounts-permissions/customize.png)

| Strona kreatora | Poświadczenia zebrane | Wymagane uprawnienia | Używane dla |
| --- | --- | --- | --- |
| ND |Uruchomienie Kreatora instalacji użytkownika |<li>Administrator serwera lokalnego</li><li>Jeśli przy użyciu pełnej wersji programu SQL Server, użytkownik musi być administratora systemu (SA) w tabeli SQL</li> |Domyślnie tworzy konta lokalnego, która jest używana jako konto usługi aparatu synchronizacji. To konto jest tworzony tylko w sytuacji, gdy administrator określi określonego konta. |
| Zainstaluj usługi synchronizacji i opcji konta usługi |Usługi AD lub poświadczenia konta użytkownika lokalnego |Użytkownik, uprawnienia są przyznawane przez Kreatora instalacji |Jeśli administrator określa konto, to konto jest używane jako konto usługi dla usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie konta łącznika usługi Azure AD, które jest używane dla operacji synchronizacji w toku w usłudze Azure AD.</li> |
| Podłączanie katalogów |W środowisku lokalnym poświadczeń usługi Active Directory dla każdego lasu, która jest połączona z usługą Azure AD |Uprawnienia są zależne od funkcji, które można włączyć i znajdują się w tworzenie konta usługi AD DS łącznika |To konto jest używane do odczytywania i zapisywania informacji o katalogu podczas synchronizacji. |
| Serwery usług AD FS |Dla każdego serwera, na liście Kreator umożliwia zbieranie informacji o poświadczenia podczas poświadczeń logowania użytkownika, uruchom kreatora są niewystarczające do łączenia z |Administrator domeny |Instalacja i Konfiguracja roli serwera usług AD FS. |
| Serwery proxy aplikacji sieci Web |Dla każdego serwera, na liście Kreator umożliwia zbieranie informacji o poświadczenia podczas poświadczeń logowania użytkownika, uruchom kreatora są niewystarczające do łączenia z |Administrator lokalny na komputerze docelowym |Instalacja i Konfiguracja roli serwera proxy aplikacji sieci Web. |
| Poświadczenia relacji zaufania serwera proxy |Poświadczenia relacji zaufania usługi federacyjnej (poświadczenia serwera proxy używa do rejestrowania certyfikatu relacji zaufania z FS |Konto domeny, które jest lokalnym administratorem serwera usług AD FS |Wstępnej rejestracji certyfikatu zaufania FS WAP. |
| Strona usług AD FS, konto usługi, "Użyj opcji konta użytkownika domeny" |Poświadczenia konta użytkownika usługi AD |Domena użytkownik |Konto użytkownika usługi Azure AD, którego poświadczenia są udostępniane jest używane jako konto logowania usługi AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Tworzenie konta usługi AD DS łącznika

>[!IMPORTANT]
>Moduł programu PowerShell o nazwie ADSyncConfig.psm1 wprowadzono w systemie kompilacji **1.1.880.0** (wydanej w sierpniu 2018 r.), zawiera kolekcję poleceń cmdlet ułatwiają konfigurowanie odpowiednich uprawnień usługi Active Directory do usługi Azure AD DS Konta łącznika.
>
>Aby uzyskać więcej informacji, zobacz [program Azure AD Connect: Konfigurowanie uprawnień konta DS łącznika AD](how-to-connect-configure-ad-ds-connector-account.md)

Konta określane na **Podłączanie katalogów** strony musi być obecny w usłudze Active Directory przed instalacją.  Usługa Azure AD Connect w wersji w 1.1.524.0, a później ma opcję, aby umożliwić Kreator Azure AD Connect, Utwórz **konta usługi AD DS łącznika** używane do łączenia z usługą Active Directory.  

Musi również mieć wymagane uprawnienia. Kreator instalacji nie Sprawdź, czy uprawnienia i wszelkie problemy mogą znajdować się tylko podczas synchronizacji.

Wymagane uprawnienia, które jest zależny od funkcji opcjonalnych można włączyć. Jeśli masz wiele domen, muszą mieć uprawnienia dla wszystkich domen w lesie. Jeśli nie włączysz żadnego z tych funkcji, wartość domyślna **użytkownika domeny** uprawnienia są wystarczające.

| Cecha | Uprawnienia |
| --- | --- |
| funkcja MS-DS-ConsistencyGuid |Uprawnienia do zapisu do atrybutu ms-DS-ConsistencyGuid udokumentowane w artykule [pojęć dotyczących projektowania — przy użyciu ms-DS-ConsistencyGuid jako sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótów haseł |<li>Replikuj zmiany katalogu</li>  <li>Replikacja katalogu zmienia wszystkie |
| Wdrożenie hybrydowe programu Exchange |Uprawnienia do zapisu do atrybutów w [zapisywania zwrotnego hybrydowego programu Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontakty. |
| Folder publiczne poczty programu Exchange |Odczyt atrybutów udokumentowane w artykule [folderu publicznego poczty Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do zapisu do atrybutów w [wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia udzielone za pomocą skryptu programu PowerShell, zgodnie z opisem w [zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md). |
| Zapisywanie zwrotne grup |Umożliwia zapisywanie zwrotne **grup usługi Office 365** do lasu za pomocą programu Exchange jest zainstalowana.  Aby uzyskać więcej informacji, zobacz [zapisu zwrotnego grup](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Uaktualnienie
Kiedy uaktualniasz z jednej wersji programu Azure AD Connect do nowej wersji, potrzebne są następujące uprawnienia:

>[!IMPORTANT]
>Program Azure AD Connect, począwszy od kompilacji 1.1.484 wprowadzono usterkę regresji, co wymaga uprawnień administratora systemu, aby uaktualnić bazę danych SQL.  Ten problem został rozwiązany w kompilacji 1.1.647.  Jeśli uaktualniasz z bieżącą kompilacją, konieczne będzie uprawnienia administratora systemu.  Uprawnienia dbo nie są wystarczające.  Jeśli użytkownik podejmie próbę uaktualnić program Azure AD Connect bez uprawnień administratora systemu, uaktualnienie nie powiedzie i Azure AD Connect nie będzie już działać poprawnie po tym dniu.  Firma Microsoft zapoznała się z tym i dokłada starań, aby rozwiązać ten problem.


| Jednostki | Wymagane uprawnienia | Używana do |
| --- | --- | --- |
| Uruchomienie Kreatora instalacji użytkownika |Administrator serwera lokalnego |Aktualizowanie plików binarnych. |
| Uruchomienie Kreatora instalacji użytkownika |Członek ADSyncAdmins |Wprowadź zmiany do reguły synchronizacji i innych konfiguracji. |
| Uruchomienie Kreatora instalacji użytkownika |Jeśli używasz pełnej wersji programu SQL server: DBO (lub podobny) z bazy danych aparatu synchronizacji |Wprowadź zmiany poziomu bazy danych, takie jak aktualizowanie tabel z nowymi kolumnami. |

## <a name="more-about-the-created-accounts"></a>Więcej informacji na temat utworzonych kont
### <a name="ad-ds-connector-account"></a>Konta DS łącznika usługi AD
Jeśli używasz ustawień ekspresowych, konto jest tworzone w usłudze Active Directory, które jest używane do synchronizacji. Utworzono konto znajduje się w domenie głównej lasu w kontenerze Użytkownicy i ma jego nazwa jest prefiksem **MSOL_** . Konto zostanie utworzone za pomocą długie, złożone hasło, które nie wygasa. Jeśli masz zasady haseł w domenie, upewnij się, że długie i złożone hasła będzie dozwolone dla tego konta.

![Konto usługi AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Jeśli używasz ustawienia niestandardowe, jesteś odpowiedzialny za utworzenie konta, przed rozpoczęciem instalacji.  Zobacz temat Tworzenie konta usługi AD DS łącznika.

### <a name="adsync-service-account"></a>Konto usługi ADSync
Usługa synchronizacji może działać w ramach różnych kont. Można uruchomić w obszarze **konta usługi wirtualnej** (VSA) **konta usługi zarządzanego przez grupę** (gMSA/autonomiczne zarządzane konta usług), lub kontem zwykłego użytkownika. Obsługiwane opcje zostały zmienione z 2017 roku kwietnia wersji programu Connect po wykonaniu pierwszej instalacji. Jeśli uaktualniasz ze starszych wersji programu Azure AD Connect, te dodatkowe opcje nie są dostępne.

| Typ konta | Opcja instalacji | Opis |
| --- | --- | --- |
| [Wirtualnego konta usługi](#virtual-service-account) | 2017 Express, jak i niestandardowe, kwiecień i nowsze | Jest to opcja, używany dla wszystkich instalacji ekspresowej, z wyjątkiem instalacji na kontrolerze domeny. Niestandardowe jest to opcja domyślna, chyba że jest używany przez inną opcję. |
| [Konta usług zarządzane przez grupę](#group-managed-service-account) | Niestandardowe, 2017 kwietnia lub nowszy | Jeśli używasz zdalnego programu SQL server, następnie firma Microsoft zaleca użycie konta usług zarządzanych przez grupę. |
| [Konto użytkownika](#user-account) | 2017 Express, jak i niestandardowe, kwiecień i nowsze | Konto użytkownika z prefiksem AAD_ jest tworzony tylko podczas instalacji zainstalowany w systemie Windows Server 2008 i zainstalowana na kontrolerze domeny. |
| [Konto użytkownika](#user-account) | 2017 Express, jak i niestandardowe, marca i starszych wersji | Konto lokalne z prefiksem AAD_ jest tworzona podczas instalacji. Korzystając z niestandardowej instalacji, można określić inne konto. |

Jeśli używasz Połącz z kompilacją z 2017 marca lub wcześniej, następnie nie należy resetować hasła dla konta usługi, ponieważ Windows niszczy klucze szyfrowania ze względów bezpieczeństwa. Nie można zmienić konta na inne konto, bez ponownej instalacji usługi Azure AD Connect. Jeśli możesz uaktualnić do kompilacji z 2017 kwietnia lub później, będzie ona wówczas obsługiwane można zmienić hasła dla konta usługi, ale nie można zmienić konto używane.

> [!Important]
> Konta usługi można ustawić tylko w pierwszej instalacji. Zmiana konta usługi, po zakończeniu instalacji nie jest obsługiwana.

Jest to tabela domyślnych opcji zalecane i obsługiwanych dla konta usługi synchronizacji.

Legendy:

- **Pogrubienie** wskazuje to opcja domyślna i w większości przypadków to zalecana opcja.
- *Kursywa* wskazuje to zalecana opcja, jeśli nie jest opcją domyślną.
- 2008 — opcja domyślna zainstalowany w systemie Windows Server 2008
- Obsługiwaną opcją non pogrubienie-
- Konto lokalne — konto użytkownika lokalnego na serwerze
- Konto domeny — konto użytkownika domeny
- autonomiczne zarządzane konta usług - [autonomicznego konta usługi zarządzanego](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [konta grupy usługi zarządzane](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Niestandardowy | Zdalne usługi SQL</br>Niestandardowy |
| --- | --- | --- | --- |
| **komputer autonomicznego/Grupa robocza** | Nieobsługiwane | **VSA**</br>Konto lokalne (2008)</br>Konto lokalne |  Nieobsługiwane |
| **komputerze przyłączonym do domeny** | **VSA**</br>Konto lokalne (2008) | **VSA**</br>Konto lokalne (2008)</br>Konto lokalne</br>Konto domeny</br>sMSA,gMSA | **gMSA**</br>Konto domeny |
| **Kontroler domeny** | **Konto domeny** | *gMSA*</br>**Konto domeny**</br>sMSA| *gMSA*</br>**Konto domeny**|

#### <a name="virtual-service-account"></a>Wirtualnego konta usługi
Konta usługi wirtualnej to specjalny typ konta, które nie ma hasła i jest zarządzana przez Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

Atrybut VSA ma być używana w scenariuszach, w których SQL i aparat synchronizacji na tym samym serwerze. Jeśli używasz zdalnego programu SQL, następnie zaleca się użyć konto usługi zarządzane przez grupę.

Ta funkcja wymaga systemu Windows Server 2008 R2 lub nowszym. Jeśli Zainstaluj program Azure AD Connect w systemie Windows Server 2008, a następnie instalację przełączy się [konta użytkownika](#user-account) zamiast tego.

#### <a name="group-managed-service-account"></a>Konto usługi zarządzanych przez grupę
Jeśli używasz zdalnego programu SQL server, a następnie zaleca się przy użyciu **konto usługi zarządzane przez grupę**. Aby uzyskać więcej informacji na temat przygotowywania usługi Active Directory dla konta usług zarządzane grupy, zobacz [omówienie kont usług zarządzanych przez grupę](https://technet.microsoft.com/library/hh831782.aspx).

Aby użyć tej opcji na [instalowanie wymaganych składników](how-to-connect-install-custom.md#install-required-components) wybierz opcję **Użyj istniejącego konta usługi**i wybierz **konta usługi zarządzanego przez**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Obsługiwana jest również użyć [autonomiczne zarządzane konto usługi](https://technet.microsoft.com/library/dd548356.aspx). Jednak te można używać tylko na komputerze lokalnym, a nie przynosi żadnej z nich korzystać za pośrednictwem domyślnego konta usługi wirtualnej.

Ta funkcja wymaga systemu Windows Server 2012 lub nowszy. Jeśli musisz używać starszych systemów operacyjnych i zdalne usługi SQL, a następnie należy użyć [konta użytkownika](#user-account).

#### <a name="user-account"></a>Konto użytkownika
Konto Usługa lokalna jest tworzone przez Kreatora instalacji (chyba że określisz konto do użycia w ustawieniach niestandardowych). To konto jest poprzedzana **AAD_** i używane przez usługę synchronizacji rzeczywistego, aby był uruchamiany jako. Jeśli zainstalujesz program Azure AD Connect na kontrolerze domeny, konto zostanie utworzone w domenie. **AAD_** konto usługi musi znajdować się w domenie, jeśli:
   - Użyj serwerze zdalnym z uruchomionym programem SQL server
   - Użyj serwera proxy, który wymaga uwierzytelniania

![Konto usługi synchronizacji](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

Konto zostanie utworzone za pomocą długie, złożone hasło, które nie wygasa.

To konto jest używane do przechowywania hasła dla innych kont w bezpieczny sposób. Te hasła kont są przechowywane w bazie danych. Klucze prywatne klucze szyfrowania są chronione przy użyciu szyfrowania klucz tajny usługi kryptograficzne przy użyciu interfejsu API ochrony danych Windows (DPAPI).

Jeśli używasz pełnej wersji programu SQL Server, konto usługi jest DBO utworzoną bazę danych do aparatu synchronizacji. Usługa nie będzie działać zgodnie z założeniami przy użyciu innych uprawnień. Tworzona jest również identyfikator logowania SQL.

To konto ma również przyznane uprawnienia do plików, kluczy rejestru i inne obiekty powiązane z aparatem synchronizacji.

### <a name="azure-ad-connector-account"></a>Konto usługi Azure AD Connector
Zostanie utworzone konto w usłudze Azure AD do użycia usługi synchronizacji. To konto można zidentyfikować przez jego nazwę wyświetlaną.

![Konto usługi AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

Nazwa serwera, którego konto jest używane na można zidentyfikować w drugiej części nazwy użytkownika. Na ilustracji nazwa serwera jest DC1. Jeśli masz przemieszczania serwerów, każdy serwer ma swoje własne konta.

Konto zostanie utworzone za pomocą długie, złożone hasło, które nie wygasa. Że udzielono mu specjalną rolę **konta synchronizacji katalogu** zawierający tylko uprawnienia do wykonywania zadań synchronizacji katalogu. Nie można udzielić tego specjalne wbudowana rola poza Kreatora programu Azure AD Connect. Witryna Azure portal pokazuje tego konta z rolą **użytkownika**.

Istnieje limit 20 kont usługi synchronizacji w usłudze Azure AD. Aby uzyskać listę istniejących kont usługi Azure AD w usłudze Azure AD, uruchom następujące polecenie cmdlet programu PowerShell usługi Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Aby usunąć nieużywane usługi Azure AD konta usług, uruchom następujące polecenie cmdlet programu PowerShell usługi Azure AD: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Zanim będzie można użyć poleceń programu PowerShell powyżej będą musieli zainstalować [Azure Active Directory PowerShell module wykres](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) i nawiąż połączenie z wystąpieniem usługi Azure AD przy użyciu [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Aby uzyskać dodatkowe informacje na temat sposobu zarządzania lub zresetuj hasło konta łącznika usługi Azure AD, zobacz [zarządzania kontem usługi Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Dokumentacja pokrewna
Jeśli nie przeczytać dokumentację na [integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md), Poniższa tabela zawiera linki do powiązanych tematów.

|Temat |Łącze|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](how-to-connect-install-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./how-to-connect-install-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnianie z narzędzia Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywanie licencji](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
