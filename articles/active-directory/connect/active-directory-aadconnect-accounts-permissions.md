---
title: 'Azure AD Connect: Konta i uprawnienia | Dokumentacja firmy Microsoft'
description: W tym temacie opisuje konta używane i utworzyć i wymagane uprawnienia.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 30763f88a7d78678411abd7fe7cc6375e00cb6f6
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824272"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Konta i uprawnienia
Kreator instalacji Azure AD Connect oferuje dwa różne ścieżki:

* W ustawieniach Express kreator wymaga wyższego poziomu uprawnień.  Jest tak, aby łatwo, można ustawić konfiguracji bez konieczności tworzenia użytkowników, lub skonfigurować uprawnienia.
* W niestandardowych ustawień kreatora oferuje więcej opcji i opcje. Istnieją jednak niektórych sytuacjach, w których należy upewnić się, że użytkownik ma odpowiednie uprawnienia.

## <a name="related-documentation"></a>Dokumentacja pokrewna
Jeśli nie odczytał dokumentacji na [integrowanie tożsamości lokalnych z usługą Azure Active Directory](../active-directory-aadconnect.md), Poniższa tabela zawiera linki do powiązanych tematów.

|Temat |Link|  
| --- | --- |
|Pobieranie programu Azure AD Connect | [Pobieranie programu Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalowanie przy użyciu ustawień ekspresowych | [Ekspresowa instalacja programu Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Instalowanie przy użyciu ustawień dostosowanych | [Niestandardowa instalacja programu Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Uaktualnianie przy użyciu narzędzia DirSync | [Uaktualnianie z narzędzia Azure AD Sync (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Po instalacji | [Weryfikowanie instalacji i przypisywanie licencji](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>Ustawienia instalacji ekspresowej
W ustawieniach Express Kreator instalacji zapyta o poświadczenia administratora przedsiębiorstwa usługi AD DS.  Jest to tak lokalnej usługi Active Directory można skonfigurować za pomocą wymaganych uprawnień do usługi Azure AD Connect. Jeśli uaktualniasz z narzędzia DirSync, poświadczenia Administratorzy przedsiębiorstwa usług AD DS są używane do resetowania hasła dla konta używanego przez narzędzie DirSync. Należy również poświadczenia administratora globalnego usługi Azure AD.

| Strona kreatora | Poświadczenia zbierane | Wymagane uprawnienia | Używany do |
| --- | --- | --- | --- |
| ND |Użytkownik uruchamiający Kreatora instalacji |Administrator serwera lokalnego |<li>Tworzy konta lokalnego, który jest używany jako [konta usługi aparatu synchronizacji](#azure-ad-connect-sync-service-account). |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie [konto usługi Azure AD](#azure-ad-service-account) używanego do operacji w toku synchronizacji w usłudze Azure AD.</li> |
| Łączenie z usługami AD DS |Lokalnych poświadczeń usługi Active Directory |Członek grupy Administratorzy przedsiębiorstwa (EA) w usłudze Active Directory |<li>Tworzy [konta](#active-directory-account) w usłudze Active Directory i przyznaje uprawnienia do niego. Jest to utworzone konto używane do odczytywania i zapisywania informacji o katalogu podczas synchronizacji.</li> |

### <a name="enterprise-admin-credentials"></a>Poświadczenia administratora przedsiębiorstwa
Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Administratorem przedsiębiorstwa nie Administrator domeny powinien upewnij się, że uprawnienia w usłudze Active Directory można ustawić we wszystkich domenach.

### <a name="global-admin-credentials"></a>Poświadczenia administratora globalnego
Te poświadczenia są używane tylko podczas instalacji i nie są używane po zakończeniu instalacji. Pozwala utworzyć [konto usługi Azure AD](#azure-ad-service-account) używane do synchronizowania zmian do usługi Azure AD. Konto umożliwia również synchronizacji jako funkcję w usłudze Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Uprawnienia utworzony usług AD DS konta dla ustawień ekspresowych
[Konta](#active-directory-account) utworzony dla odczytu i zapisu w usługach AD DS są następujące uprawnienia podczas tworzenia ekspresowej ustawienia:

| Uprawnienie | Używany do |
| --- | --- |
| <li>Replikować zmiany katalogu</li><li>Replikowanie katalogu zmienia wszystkie |Synchronizacja skrótów haseł |
| Odczyt/zapis wszystkich właściwości użytkownika |Hybrydowe Import i Exchange |
| Odczyt/zapis wszystkich właściwości iNetOrgPerson |Hybrydowe Import i Exchange |
| Grupa wszystkich właściwości odczytu/zapisu |Hybrydowe Import i Exchange |
| Skontaktuj się z wszystkich właściwości odczytu/zapisu |Hybrydowe Import i Exchange |
| Resetowanie hasła |Przygotowanie do włączania funkcji zapisywania zwrotnego haseł |

## <a name="custom-settings-installation"></a>Niestandardowe ustawienia instalacji
Azure AD Connect wersji 1.1.524.0 i później ma opcję umożliwiającą programowi Kreator Azure AD Connect, Utwórz konto używane do łączenia z usługą Active Directory.  Wcześniejszych wersji wymagają utworzeniu konta przed rozpoczęciem instalacji. Należy przyznawać temu kontu uprawnienia można znaleźć w [Tworzenie konta usług AD DS](#create-the-ad-ds-account). 

| Strona kreatora | Poświadczenia zbierane | Wymagane uprawnienia | Używany do |
| --- | --- | --- | --- |
| ND |Użytkownik uruchamiający Kreatora instalacji |<li>Administrator serwera lokalnego</li><li>Jeśli przy użyciu pełnego serwera SQL, użytkownik musi być administratora systemu (SA) w programie SQL</li> |Domyślnie tworzy konta lokalnego, który jest używany jako [konta usługi aparatu synchronizacji](#azure-ad-connect-sync-service-account). Konto jest tworzony tylko, gdy administrator określi określonego konta. |
| Zainstaluj usługi synchronizacji i opcji konta usługi |Usługi AD lub poświadczenia konta użytkownika lokalnego |Użytkownik, uprawnienia są udzielane przez Kreatora instalacji |Jeśli administrator określa konto, to konto jest używane jako konto usługi dla usługi synchronizacji. |
| Łączenie z usługą Azure AD |Poświadczenia katalogu usługi Azure AD |Rola administratora globalnego w usłudze Azure AD |<li>Włączanie synchronizacji w katalogu usługi Azure AD.</li>  <li>Tworzenie [konto usługi Azure AD](#azure-ad-service-account) używanego do operacji w toku synchronizacji w usłudze Azure AD.</li> |
| Podłączanie katalogów |Lokalnych poświadczeń usługi Active Directory dla każdego lasu, która jest połączona z usługą Azure AD |Uprawnienia zależą od funkcji, które można włączyć i znajdują się w [Tworzenie konta usług AD DS](#create-the-ad-ds-account) |To konto jest używane do odczytywania i zapisywania informacji o katalogu podczas synchronizacji. |
| Serwery usług AD FS |Dla każdego serwera na liście Kreator służy do zbierania poświadczeń podczas logowania użytkownika, uruchom kreatora są niewystarczające do połączenia |Administrator domeny |Instalowanie i konfigurowanie roli serwera usług AD FS. |
| Serwery proxy aplikacji sieci Web |Dla każdego serwera na liście Kreator służy do zbierania poświadczeń podczas logowania użytkownika, uruchom kreatora są niewystarczające do połączenia |Administratora lokalnego na komputerze docelowym |Instalowanie i konfigurowanie roli serwera proxy. |
| Poświadczenia relacji zaufania serwera proxy |Poświadczenia zaufania usługi federacyjnej (poświadczenia serwera proxy używane do rejestrowania certyfikatu relacji zaufania z FS |Konta domeny, które ma uprawnienia administratora lokalnego serwera usług AD FS |Wstępnej rejestracji certyfikatu zaufania FS WAP. |
| Strona usług AD FS konta usługi, "Użyć opcji konta użytkownika domeny" |Poświadczenia konta użytkownika AD |Użytkownik domeny |AD konto użytkownika, którego poświadczenia są udostępniane jest używane jako konto logowania usługi AD FS. |

### <a name="create-the-ad-ds-account"></a>Tworzenie konta usług AD DS
Konta, określ na **Podłączanie katalogów** strony musi być obecny w usłudze Active Directory przed instalacją.  Musi również mieć wymagane uprawnienia. Kreator instalacji nie Sprawdź, czy uprawnienia i problemy mogą znajdować się tylko podczas synchronizacji.

Uprawnienia wymagane jest zależny od funkcji opcjonalnych można włączyć. Jeśli masz wiele domen, muszą być przyznane uprawnienia dla wszystkich domen w lesie. Jeśli nie włączysz żadnego z tych funkcji, domyślnie **użytkownika domeny** uprawnienia są wystarczające.

| Cecha | Uprawnienia |
| --- | --- |
| Funkcja msDS-ConsistencyGuid |Uprawnienia do zapisu do atrybutu msDS-ConsistencyGuid udokumentowane w [zagadnienia dotyczące projektowania — przy użyciu msDS-ConsistencyGuid jako sourceAnchor](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor). | 
| Synchronizacja skrótów haseł |<li>Replikować zmiany katalogu</li>  <li>Replikowanie katalogu zmienia wszystkie |
| Wdrożenie hybrydowe programu Exchange |Uprawnienia do zapisu w atrybutach w [zapisywania zwrotnego hybrydowego programu Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) dla użytkowników, grup i kontakty. |
| Folder publiczny poczty programu Exchange |Uprawnienia odczytu do atrybutów w [folderu publicznego poczty programu Exchange](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder) dla folderów publicznych. | 
| Zapisywanie zwrotne haseł |Uprawnienia do zapisu w atrybutach w [wprowadzenie do zarządzania hasłami](../authentication/howto-sspr-writeback.md) dla użytkowników. |
| Zapisywanie zwrotne urządzeń |Uprawnienia przyznane przy użyciu skryptu programu PowerShell, zgodnie z opisem w [zapisu zwrotnego urządzeń](active-directory-aadconnect-feature-device-writeback.md). |
| Zapisywanie zwrotne grup |Przeczytaj, tworzenia, aktualizowania lub usuwania grupy obiektów dla synchronizacji **grup usługi Office 365**.  Aby uzyskać więcej informacji, zobacz [zapisu zwrotnego grup](active-directory-aadconnect-feature-preview.md#group-writeback).|

## <a name="upgrade"></a>Uaktualnienie
Po uaktualnieniu z jednej wersji programu Azure AD Connect do nowej wersji, potrzebne są następujące uprawnienia:

>[!IMPORTANT]
>Począwszy od kompilacji 1.1.484, Azure AD Connect wprowadzono usterki regresji, który wymaga uprawnień administratora systemu do uaktualnienia bazy danych SQL.  Ten problem został rozwiązany w kompilacji 1.1.647.  W przypadku uaktualniania do tej kompilacji, należy najpierw uprawnienia administratora systemu.  Uprawnienia dbo nie są wystarczające.  Próba uaktualnienia bez uprawnień administratora systemu Azure AD Connect uaktualnienie zakończy się niepowodzeniem i Azure AD Connect nie będzie już działać poprawnie później.  Firma Microsoft o tym pamiętać i działa, aby rozwiązać ten problem.


| Podmiot zabezpieczeń | Wymagane uprawnienia | Używany do |
| --- | --- | --- |
| Użytkownik uruchamiający Kreatora instalacji |Administrator serwera lokalnego |Aktualizowanie plików binarnych. |
| Użytkownik uruchamiający Kreatora instalacji |Element członkowski ADSyncAdmins |Zmiany reguły synchronizacji i innych konfiguracji. |
| Użytkownik uruchamiający Kreatora instalacji |Jeśli używasz pełnego serwera SQL: DBO (lub podobny) Aparat bazy danych usługi synchronizacji |Wprowadź zmiany poziomu bazy danych, takich jak aktualizowanie tabel z nowymi kolumnami. |

## <a name="more-about-the-created-accounts"></a>Więcej informacji na temat utworzonych kont
### <a name="active-directory-account"></a>Konto usługi Active Directory
Jeśli używasz ustawień ekspresowych, konto jest tworzone w usłudze Active Directory, które jest używane do synchronizacji. Utworzone konto znajduje się w domenie głównej lasu w kontenerze Użytkownicy i ma jego nazwa jest prefiksem **MSOL_**. Utworzone konto jest długo złożone hasło, które nie wygasa. Jeśli masz zasady haseł w domenie, upewnij się, że długie i złożone hasła będzie dozwolone dla tego konta.

![Konto usługi AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

Jeśli używasz ustawienia niestandardowe, następnie jest odpowiedzialny za utworzenie konta, przed rozpoczęciem instalacji.

### <a name="azure-ad-connect-sync-service-account"></a>Konto usługi synchronizacji programu Azure AD Connect
Usługa synchronizacji mogą być uruchamiane w różnych kont. Mogą działać w ramach **wirtualnego konta usługi** (VSA), **konta usługi zarządzanego przez grupę** (gMSA/autonomiczne) lub konta zwykłych użytkowników. Obsługiwane opcje zostały zmienione z 2017 kwietnia wersji programu Connect po wykonaniu pierwszą instalację. W przypadku uaktualniania z wcześniejszych wersji programu Azure AD Connect nie są dostępne te opcje dodatkowe.

| Typ konta | Opcja instalacji | Opis |
| --- | --- | --- |
| [Konta usług wirtualnych](#virtual-service-account) | 2017 Express i niestandardowych, kwietnia i nowsze | Jest to opcja używane dla wszystkich instalacji ekspresowej, z wyjątkiem instalacji na kontrolerze domeny. Dla niestandardowego jest domyślną opcją chyba że używana jest inną opcją. |
| [Konto usługi zarządzane przez grupę](#group-managed-service-account) | Niestandardowe, 2017 kwietnia i nowsze | Jeśli używasz zdalnego serwera SQL, następnie zaleca się używania konto usługi zarządzane przez grupę. |
| [Konto użytkownika](#user-account) | 2017 Express i niestandardowych, kwietnia i nowsze | Konto użytkownika z prefiksem AAD_ jest tworzony tylko podczas instalacji zainstalowany w systemie Windows Server 2008 i zainstalować na kontrolerze domeny. |
| [Konto użytkownika](#user-account) | 2017 Express i niestandardowych, marca i starszych wersji | Konto lokalne, i jest poprzedzony prefiksem AAD_ został utworzony podczas instalacji. Podczas korzystania z niestandardowej instalacji, można określić inne konto. |

Jeśli używasz Połącz z kompilacją z 2017 marca lub wcześniej, następnie nie należy resetować hasło konta usługi, ponieważ Windows niszczy klucze szyfrowania ze względów bezpieczeństwa. Nie można zmienić konta na inne konto, bez ponownego instalowania Azure AD Connect. Jeśli uaktualnieniu do kompilacji z 2017 kwietnia lub później, jest obsługiwane zmienić hasła dla konta usługi, ale nie można zmienić konto używane.

> [!Important]
> Konto usługi można ustawić tylko w pierwszej instalacji. Zmiana konta usługi, po zakończeniu instalacji nie jest obsługiwana.

Znajduje się tabela wartości domyślnych, zalecane oraz obsługiwane opcje dla konta usługi synchronizacji.

Legenda:

- **Bold** wskazuje domyślną opcją i w większości przypadków to zalecana opcja.
- *Kursywa* wskazuje to zalecana opcja, gdy nie jest opcją domyślną.
- 2008 — opcja domyślna w systemie Windows Server 2008
- Inne niż — bold — opcja obsługiwana
- Konto lokalne — konta użytkownika lokalnego na serwerze
- Konto domeny — konta użytkownika domeny
- Autonomiczne - [autonomiczny konto usługi zarządzane](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [grupy konto usługi zarządzane](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Niestandardowy | Zdalny program SQL</br>Niestandardowy |
| --- | --- | --- | --- |
| **komputer autonomicznego/Grupa robocza** | Nieobsługiwane | **VSA**</br>Konto lokalne (2008)</br>Konto lokalne |  Nieobsługiwane |
| **komputerze przyłączonym do domeny** | **VSA**</br>Konto lokalne (2008) | **VSA**</br>Konto lokalne (2008)</br>Konto lokalne</br>Konto domeny</br>sMSA,gMSA | **gMSA**</br>Konto domeny |
| **Kontroler domeny** | **Konto domeny** | *gMSA*</br>**Konto domeny**</br>sMSA| *gMSA*</br>**Konto domeny**|

#### <a name="virtual-service-account"></a>Konta usług wirtualnych
Konto usługi wirtualnego jest specjalnym rodzajem konto nie ma hasła, który jest zarządzany przez system Windows.

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

Atrybut VSA ma być używana w scenariuszach, gdzie aparatu synchronizacji i SQL są na tym samym serwerze. Jeśli używasz zdalnego programu SQL, a następnie zaleca się używania [konta usługi zarządzanego przez grupę](#managed-service-account) zamiast tego.

Ta funkcja wymaga systemu Windows Server 2008 R2 lub nowszym. Jeśli Azure AD Connect można zainstalować w systemie Windows Server 2008, a następnie instalację przełączy się [konta użytkownika](#user-account) zamiast tego.

#### <a name="group-managed-service-account"></a>Konto usługi zarządzane przez grupę
Jeśli używasz zdalnego serwera SQL, a następnie zaleca się przy użyciu **konto usługi zarządzane przez grupę**. Aby uzyskać więcej informacji na temat sposobu przygotowania usługi Active Directory dla konta usług zarządzanych grupy, zobacz [omówienie kont usług zarządzanych grupy](https://technet.microsoft.com/library/hh831782.aspx).

Aby użyć tej opcji na [zainstalowanie wymaganych składników](active-directory-aadconnect-get-started-custom.md#install-required-components) wybierz pozycję **Użyj istniejącego konta usługi**i wybierz **konto usługi zarządzane przez**.  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  
Jest też obsługiwana do użycia [autonomiczne zarządzane konto usługi](https://technet.microsoft.com/library/dd548356.aspx). Jednak te można używać tylko na komputerze lokalnym i nie przynosi żadnej z nich korzystać za pośrednictwem domyślnego konta usługi wirtualnego.

Ta funkcja wymaga systemu Windows Server 2012 lub nowszym. Jeśli musisz używać starszych systemów operacyjnych i używać zdalnego programu SQL, a następnie należy użyć [konta użytkownika](#user-account).

#### <a name="user-account"></a>Konto użytkownika
Konto Usługa lokalna jest tworzone przez Kreatora instalacji (chyba że możesz określić konto, które ma być używane w niestandardowych ustawieniach). Konto jest prefiksem **AAD_** i używane przez usługę synchronizacji rzeczywiste do działania jako usługa. Jeśli program Azure AD Connect na kontrolerze domeny, utworzeniu konta w domenie. **AAD_** konto usługi musi znajdować się w domenie, jeśli:
   - w przypadku używania serwera zdalnego programem SQL server
   - Użyj serwera proxy, który wymaga uwierzytelniania

![Konto usługi synchronizacji](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Utworzone konto jest długo złożone hasło, które nie wygasa.

To konto jest używane do przechowywania haseł do innych kont w bezpieczny sposób. Te hasła konta jest przechowywany jako zaszyfrowany w bazie danych. Klucze prywatne dla kluczy szyfrowania są chronione przy użyciu szyfrowania klucza tajnego usługi kryptograficzne przy użyciu interfejsu API ochrony danych systemu Windows (DPAPI).

Jeśli używasz pełnego serwera SQL, konto usługi jest DBO utworzonej bazy danych aparatu synchronizacji. Usługa nie będzie działać zgodnie z założeniami z innymi uprawnieniami. Tworzona jest również identyfikator logowania SQL.

Konto ma również przyznane uprawnienia do plików, kluczy rejestru i inne obiekty powiązane z aparatu synchronizacji.

### <a name="azure-ad-service-account"></a>Konto usługi Azure AD
Konto w usłudze Azure AD jest tworzone do użycia usługi synchronizacji. To konto może zostać zidentyfikowane na podstawie nazwy wyświetlanej.

![Konto usługi AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount2.png)

Nazwa serwera, którego konto jest używane w można zidentyfikować w drugiej części nazwy użytkownika. Na rysunku nazwa serwera jest DC1. Jeśli masz przemieszczania serwerów, każdy serwer ma własnego konta.

Konto usługi jest tworzone długo złożone hasło, które nie wygasa. Ma przyznane uprawnienia roli specjalne **konta synchronizacji katalogu** która ma tylko uprawnienia do wykonywania zadań synchronizacji katalogu. Nie można udzielić tej roli wbudowanych specjalne poza Kreatora programu Azure AD Connect. Azure portal zawiera tego konta z rolą **użytkownika**.

Istnieje limit 20 kont usługi synchronizacji w usłudze Azure AD. Aby uzyskać listę istniejących kont usługi Azure AD w usługi Azure AD, uruchom następujące polecenie cmdlet programu PowerShell usługi Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Aby usunąć nieużywane usługi Azure AD kont usług, uruchom następujące polecenie cmdlet programu PowerShell usługi Azure AD: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](../active-directory-aadconnect.md).
