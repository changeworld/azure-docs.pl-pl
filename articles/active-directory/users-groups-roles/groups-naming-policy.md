---
title: Wymuszanie zasad nazewnictwa grupy — grupy usługi Office 365 — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak skonfigurować zasady nazewnictwa dla grup usługi Office 365 w usłudze Azure Active Directory (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 846eb3a43955fe05531f619869878b3978ad5b9d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690256"
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory"></a>Wymuszanie zasad nazewnictwa dla grup usługi Office 365 w usłudze Azure Active Directory

Aby wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników, należy skonfigurować grupę zasady nazewnictwa dla dzierżawców w usłudze Azure Active Directory (Azure AD). Na przykład można użyć zasad nazewnictwa do komunikowania się funkcji grupę, członkostwo, regionu geograficznego lub twórca grupy. Zasady nazewnictwa mogą również wykorzystać do kategoryzowania grup w książce adresowej. Aby zablokować określone słowa w grupie nazwy i aliasy użycia, można użyć zasad.

> [!IMPORTANT]
> Przy użyciu zasad nazewnictwa usługi Azure AD dla grup usługi Office 365 wymaga posiadania, ale nie musi przypisać licencję usługi Azure Active Directory Premium P1 lub licencję usługi Azure AD podstawowa EDU każdy unikatowy użytkownik, który jest członkiem jednej lub kilku grup usługi Office 365.

Nazewnictwa zasady są stosowane do tworzenia lub edytowania grup utworzonych na potrzeby różnych obciążeń (na przykład programu Outlook, Microsoft Teams, SharePoint, Exchange lub terminarz). Jest stosowany do nazwy grupy i alias grupy. Jeśli masz istniejącą grupę Exchange zasady nazewnictwa skonfigurować zasady nazewnictwa w usłudze Azure AD, programu Azure AD, zasady nazewnictwa są wymuszane w Twojej organizacji.

## <a name="naming-policy-features"></a>Funkcje zasad nazewnictwa

Za wymuszania zasad nazewnictwa dla grup na dwa sposoby:

- **Zasady nazewnictwa prefiksu i sufiksu** można zdefiniować prefiksów lub sufiksów, które następnie są automatycznie dodawane do wymusić konwencję nazewnictwa grup (na przykład, w polu Nazwa grupy "DGRP\_Japonii\_Moja grupa\_ Działu inżynierii", DGRP\_Japonii\_ jest prefiksem, a \_inżynierii jest sufiksem). 

- **Niestandardowe zablokowane wyrazy** zbiór słów zablokowanych określonych możesz przekazać do Twojej organizacji zostanie zablokowane w grup utworzonych przez użytkowników (na przykład "wp list płac, Dyrektor Generalny").

### <a name="prefix-suffix-naming-policy"></a>Zasady nazewnictwa prefiksu i sufiksu

Ogólną strukturę konwencji nazewnictwa jest "Prefiks [GroupName] sufiks". Chociaż można zdefiniować wiele prefiksów i sufiksów, w ustawieniu może mieć tylko jedno wystąpienie elementu [GroupName]. Prefiksy lub sufiksy może być stałych ciągów lub atrybutów użytkownika takich jak \[działu\] są zastępowane, zależnie od użytkownika, który jest tworzona grupa. Dopuszczalna liczba znaków w ciągach z prefiksem i sufiksem, połączone to 53 znaków. 

Prefiksy i sufiksów może zawierać znaków specjalnych, które są obsługiwane przez nazwę grupy i alias grupy. Wszystkie znaki w prefiksu lub sufiksu, które nie są obsługiwane przez alias grupy nadal są stosowane w polu Nazwa grupy, ale usunięta z aliasu grupy. Ze względu na to ograniczenie może się różnić od zasad stosowanych do aliasu grupy prefiksy i sufiksów stosowane do nazwy grupy. 

#### <a name="fixed-strings"></a>Stałych ciągów

Aby ułatwić skanowania i rozróżniania grup w globalnej liście adresowej i łącza nawigacji po lewej stronie obciążeń grupy, można użyć ciągów. Niektóre typowe prefiksy są słowami kluczowymi, takich jak "DGRP\_Name", "\#Nazwa ','\_nazwę"

#### <a name="user-attributes"></a>Atrybuty użytkownika

Można użyć atrybuty, które mogą pomóc Ci i zidentyfikować użytkowników, działów, pakietu office lub regionie geograficznym, w którym grupa została utworzona. Na przykład po zdefiniowaniu zasad nazewnictwa jako `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`, i `User’s department = Engineering`, a następnie nazwę grupy wymuszone, może być "DGRP Moje grupy Engineering." Obsługiwane usługi Azure AD atrybuty są \[działu\], \[firmy\], \[Office\], \[StanLubProwincja\], \[CountryOrRegion \], \[Tytuł\]. Atrybuty użytkownika nieobsługiwany są traktowane jako stałych ciągów; na przykład "\[KodPocztowy\]". Rozszerzeń atrybuty oraz atrybuty niestandardowe nie są obsługiwane.

Firma Microsoft zaleca używanie atrybutów, które mają wartości wypełnione dla wszystkich użytkowników w Twojej organizacji i nie używaj atrybuty, które mają długi wartości.

### <a name="custom-blocked-words"></a>Podasz niestandardowe wyrazy zablokowane

Listę zablokowanych wyrazów jest przecinkami lista wyrażeń do zablokowania w grupie nazwy i aliasy. Nie wyszukiwania ciągów podrzędne są wykonywane. Dokładne dopasowanie między nazwę grupy i co najmniej jeden niestandardowy słów zablokowanych jest wymagana do wyzwolenia błąd. Wyszukiwanie zamienionego nie jest wykonywane, dzięki czemu użytkownicy mogą używać popularne wyrazy, takie jak "Class", nawet jeśli "klasy" jest blokowane słowo.

Blokowane słowo reguł listy:

- Zablokowane słowa nie są z uwzględnieniem wielkości liter.
- Gdy użytkownik wprowadzi zablokowanego wyrazu, jako część nazwy grupy, zobaczą komunikat o błędzie z zablokowanego wyrazu.
- Istnieją ograniczenia znak słowa zablokowane.
- Brak górny limit 5000 wyrażeń, które można skonfigurować na liście zablokowanych słów. 

### <a name="administrator-override"></a>Zastąpienie administratora

Wybranym administratorom można wykluczone z tych zasad we wszystkich obciążeń grupy i punktów końcowych w celu tworzenia grup przy użyciu słowa zablokowane i własnych konwencji nazewnictwa. Poniżej przedstawiono listę ról administratora jest wyłączone z grupy, zasady nazewnictwa.

- Administrator globalny
- Pomoc techniczna dla partnerów warstwy 1
- Pomoc techniczna dla partnerów warstwy 2
- Administrator użytkownika
- Zapisywanie katalogów

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>Konfigurowanie grupy, zasady nazewnictwa dla dzierżawcy przy użyciu witryny Azure portal (wersja zapoznawcza)

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora użytkowników.
1. Wybierz **grup**, a następnie wybierz **zasady nazewnictwa** aby otworzyć stronę Zasady nazewnictwa.

    ![Otwórz stronę Zasady nazewnictwa w Centrum administracyjnym](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetl lub Edytuj zasady nazewnictwa prefiksu i sufiksu

1. Na **zasady nazewnictwa** wybierz opcję **zasady nazewnictwa grupy**.
1. Można wyświetlić lub edytować bieżący prefiks lub sufiks nadawanie zasadom nazw indywidualnie, wybierając atrybutów lub ciągów, które mają zostać wymuszone jako część zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, wybierz prefiks lub sufiks, a następnie wybierz **Usuń**. W tym samym czasie można usunąć wielu elementów.
1. Zapisz zmiany, aby nowe zasady zaczną obowiązywać, wybierając **Zapisz**.

### <a name="view-or-edit-the-custom-blocked-words"></a>Wyświetl lub Edytuj niestandardowe wyrazy zablokowane

1. Na **zasady nazewnictwa** wybierz opcję **zablokowane wyrazy**.

    ![edytować i przekazywać listy zablokowanych słowa dla zasady nazewnictwa](./media/groups-naming-policy/blockedwords-preview.png)

1. Wyświetlenie lub Edycja bieżącą listę zablokowanych podasz niestandardowe wyrazy, wybierając **Pobierz**.
1. Przekaż nową listę zablokowanych podasz niestandardowe wyrazy, wybierając ikonę pliku.
1. Zapisz zmiany, aby nowe zasady zaczną obowiązywać, wybierając **Zapisz**.

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Zainstaluj polecenia cmdlet programu PowerShell, aby skonfigurować zasady nazewnictwa

Pamiętaj, aby odinstalować starszą wersję modułu Azure Active Directory PowerShell dla programu Graph z programu Windows PowerShell i zainstalować moduł [Azure Active Directory PowerShell dla programu Graph w publicznej wersji zapoznawczej 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) przed uruchomieniem poleceń programu PowerShell.

1. Otwórz aplikację Windows PowerShell jako administrator.
2. Odinstaluj poprzednią wersję programu AzureADPreview.
  
   ```powershell
   Uninstall-Module AzureADPreview
   ```

3. Zainstaluj najnowszą wersję programu AzureADPreview.
  
   ```powershell
   Install-Module AzureADPreview
   ```

   Jeśli zostanie wyświetlony monit o dostęp do niezaufanych repozytorium, wprowadzić **Y**. Zainstalowanie nowego modułu może zająć kilka minut.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurowanie grupy, zasady nazewnictwa dla dzierżawcy przy użyciu usługi Azure AD PowerShell

1. Otwórz okno programu Windows PowerShell na komputerze. Możesz go otworzyć, bez podwyższonego poziomu uprawnień.

1. Uruchom następujące polecenia, aby przygotować się do uruchomienia poleceń cmdlet.
  
   ```powershell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```
   Na ekranie **Zaloguj się na swoje konto** wprowadź swoje konto administratora i hasło, aby połączyć się z usługą, a następnie wybierz polecenie **Zaloguj**.

1. Postępuj zgodnie z instrukcjami zawartymi w artykule [Azure Active Directory cmdlets for configuring group settings (Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy)](groups-settings-cmdlets.md), aby utworzyć ustawienia grupy dla tej dzierżawy.

### <a name="view-the-current-settings"></a>wyświetlanie bieżących ustawień

1. Pobrać bieżące zasady nazewnictwa, aby wyświetlić bieżące ustawienia.
  
   ```powershell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Wyświetl bieżące ustawienia grupy.
  
   ```powershell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ustaw zasady nazewnictwa i podasz niestandardowe wyrazy zablokowane

1. Ustaw prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell. Aby ta funkcja działała poprawnie, należy dodać element [GroupName] do ustawienia.
  
   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Ustaw niestandardowe słowa zablokowane. W poniższym przykładzie pokazano, jak dodać własne słowa niestandardowe.
  
   ```powershell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Zapisz ustawienia nowych zasad do obowiązywać, takie jak w poniższym przykładzie.
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Gotowe. Już skonfigurować zasady nazewnictwa i dodać zablokowanych słów.

## <a name="export-or-import-the-list-of-custom-blocked-words-using-azure-ad-powershell"></a>Eksportowanie lub importowanie listy podasz niestandardowe wyrazy zablokowane przy użyciu usługi Azure AD PowerShell

Aby uzyskać więcej informacji, zobacz artykuł [poleceń cmdlet usługi Azure Active Directory, do konfigurowania ustawień grupy](groups-settings-cmdlets.md).

Poniżej przedstawiono przykładowy skrypt programu PowerShell można wyeksportować wiele słów zablokowany:

```powershell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Poniżej przedstawiono przykładowy skrypt programu PowerShell, aby zaimportować wiele słów zablokowany:

```powershell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Usuń zasady nazewnictwa

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Usuń zasady nazewnictwa przy użyciu witryny Azure portal (wersja zapoznawcza)

1. Na **zasady nazewnictwa** wybierz opcję **usuwanie zasady**.
1. Po użytkownik potwierdzi usunięcie zasad nazewnictwa zostanie usunięty, w tym wszystkie sufiks prefiks nazwy zasad i wszystkie zablokowane podasz niestandardowe wyrazy.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Usuń zasady nazewnictwa przy użyciu usługi Azure AD Powershell

1. Usuń prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell.
  
   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Usuń niestandardowe słowa zablokowane.
  
   ```powershell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Zapisz ustawienia.
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="naming-policy-experiences-across-office-365-apps"></a>Zasady nazewnictwa napotka w aplikacjach usługi Office 365

Po ustawieniu zasady nazewnictwa grup w usłudze Azure AD, gdy użytkownik tworzy grupę w aplikacji usługi Office 365, zobaczy:

- A w wersji zapoznawczej nazwy zgodnie z zasadami nazewnictwa (ze prefiksy lub sufiksy) tak szybko, jak użytkownik wpisze nazwę grupy
- Jeśli użytkownik wprowadzi zablokowanych słów, zobaczą komunikat o błędzie, mogą usuwać zablokowanych słów.

Obciążenie | Zgodność
----------- | -------------------------------
Usługa Azure Active Directory portali | W portalu usługi Azure AD i portalu panelu dostępu Pokaż nazwy wymuszona zasada po użytkownik wpisuje nazwę grupy, podczas tworzenia lub edytowania grupy. Gdy użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie z zablokowanego wyrazu jest wyświetlana tak, aby je usunąć użytkownika.
Outlook Web Access (OWA) | W programie Outlook Web Access zawiera zasady nazewnictwa wymuszane nazwa, gdy użytkownik wpisuje nazwę grupy lub alias grupy. Kiedy użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany w Interfejsie użytkownika wraz z zablokowanego wyrazu, aby je usunąć użytkownika.
Aplikacja klasyczna Outlook | Grupy utworzone w aplikacja klasyczna Outlook są zgodne z ustawieniami zasad nazewnictwa. Aplikacja klasyczna Outlook jeszcze nie wyświetla nazwę grupy wymuszone w wersji zapoznawczej i nie zwraca błędy niestandardowe zablokowanego wyrazu, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa jest automatycznie stosowane podczas tworzenia lub edytowania grupy i użytkownicy widzą komunikaty o błędach, jeśli podasz niestandardowe wyrazy zablokowane w grupie nazwę lub alias.
Microsoft Teams | Microsoft Teams zawiera grupy nazewnictwa nazwa_zasad wymuszane, gdy użytkownik wprowadzi nazwę zespołu. Po użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany wraz z zablokowanego wyrazu tak, aby je usunąć użytkownika.
Sharepoint  |  Program SharePoint jest wyświetlana nazwa nazewnictwa wymuszona zasada po użytkownik wpisze w witrynie nazwę lub adres e-mail grupy. Gdy użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany, wraz z zablokowanego wyrazu tak, aby je usunąć użytkownika.
Microsoft Stream | Microsoft Stream przedstawiono grupy nazewnictwa nazwa_zasad wymuszane, gdy użytkownik wpisuje nazwę grupy lub alias e-mail grupy. Po użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany z zablokowanego wyrazu, dzięki czemu użytkownik może usunąć go.
Program Outlook dla systemów iOS i aplikacji dla systemu Android | Grupy utworzone w aplikacji Outlook są zgodne ze skonfigurowanymi zasadami nazewnictwa. Aplikacja mobilna Outlook jeszcze nie wyświetla nazwy wymuszona zasada w wersji zapoznawczej i nie zwraca błędy niestandardowe zablokowanego wyrazu, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa stosowany jest automatycznie po kliknięciu przycisku Utwórz/Edytuj, a użytkownicy widzą komunikaty o błędach, jeśli podasz niestandardowe wyrazy zablokowane w grupie nazwę lub alias.
Grupy aplikacji mobilnej | Grupy utworzone w aplikacji mobilnej grup są zgodne z zasadami nazewnictwa. Aplikacja mobilna grup nie są wyświetlane zasady nazewnictwa w wersji zapoznawczej i nie zwraca błędy niestandardowe zablokowanego wyrazu, gdy użytkownik wprowadzi nazwę grupy. Ale nazewnictwa zasady są stosowane automatycznie, podczas tworzenia lub edytowania grupy i użytkownicy są prezentowane z błędami odpowiednie, jeśli podasz niestandardowe wyrazy zablokowane w grupie nazwę lub alias.
Planner | Planista to zgodne z zasadami nazewnictwa. Planista pokazuje nazewnictwa zasad (wersja zapoznawcza), podczas wprowadzania nazwy planu. Gdy użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany, gdy utworzony plan.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement jest zgodne z zasadami nazewnictwa. Dynamics 365 zawiera nazwy wymuszona zasada, gdy użytkownik wpisuje nazwę grupy lub alias e-mail grupy. Gdy użytkownik wprowadzi niestandardowe zablokowanego wyrazu, komunikat o błędzie jest wyświetlany za pomocą zablokowanego wyrazu, dzięki czemu użytkownik może usunąć go.
School Data Sync (SDS) | Przestrzegać zasady nazewnictwa grup utworzonych przy użyciu narzędzia SDS, ale zasady nazewnictwa nie są stosowane automatycznie. Administratorzy SDS mają można dołączyć prefiksów i sufiksów do nazw klas, dla których grupy muszą być tworzone i następnie przekazywane do narzędzia SDS. Tworzenie grupy lub edytowanie może zakończyć się niepowodzeniem w przeciwnym razie.
Outlook Customer Manager (OCM) | Program Outlook Customer Manager jest zgodne z zasadami nazewnictwa, która jest automatycznie stosowany do grupy utworzone w programie Outlook Customer Manager. W przypadku wykrycia niestandardowe zablokowanego wyrazu, tworzenie grupy w OCM jest zablokowane, a użytkownik jest zablokowany za pomocą aplikacji OCM.
Aplikacja classroom | Grup utworzonych w aplikacji Classroom, który jest zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie są stosowane automatycznie, a nazewnictwa zasad (wersja zapoznawcza) nie jest widoczne dla użytkowników podczas podawania nazwy grupy klas. Użytkownicy muszą wprowadzić nazwę grupy wymuszone klasy za pomocą prefiksów i sufiksów. W przeciwnym razie grupy klas, tworzenia lub edytowania operacja kończy się niepowodzeniem z błędami.
Power BI | Obszary robocze programu Power BI są zgodne z zasadami nazewnictwa.    
Yammer | Gdy użytkownik zalogowany do usługi Yammer za pomocą konta usługi Azure Active Directory tworzy grupę lub umożliwia edytowanie nazwy grupy, nazwę grupy będą stosować się zasady nazewnictwa. Dotyczy to zarówno do grupy usługi Office 365, połączonych i innych grup w usłudze Yammer.<br>Jeśli połączonej grupy usługi Office 365 zostało utworzone przed zasady nazewnictwa znajduje się w miejscu, nazwa grupy nie zostaną automatycznie wprowadzone zasady nazewnictwa. Gdy użytkownik edytuje nazwę grupy, zostanie wyświetlony monit można dodać prefiksu i sufiksu.
StaffHub  | Zespoły usługi StaffHub nie wykonuj zasady nazewnictwa, ale nie podstawowej grupy usługi Office 365. Nazwa zespołu StaffHub nie ma zastosowania, prefiksów i sufiksów i nie sprawdza obecności podasz niestandardowe wyrazy zablokowane. Ale StaffHub mają zastosowanie prefiksów i sufiksów i usuwa zablokowanych słów z podstawowej grupy usługi Office 365.
Exchange PowerShell | Polecenia cmdlet programu PowerShell programu Exchange są zgodne z zasadami nazewnictwa. Użytkownicy otrzymają odpowiedniego komunikatu o błędzie z sugerowanych prefiksów i sufiksów, a dla zablokowanych podasz niestandardowe wyrazy, jeśli nie stosują zasady nazewnictwa nazwę grupy i alias grupy (mailNickname).
Polecenia cmdlet środowiska PowerShell usługi Active Directory systemu Azure | Polecenia cmdlet środowiska PowerShell usługi Active Directory systemu Azure są zgodne z zasady nazewnictwa. Użytkownicy otrzymają odpowiedniego komunikatu o błędzie z sugerowanych prefiksów i sufiksów, a dla zablokowanych podasz niestandardowe wyrazy, jeśli ich nie są zgodne z konwencją nazw grup i alias grupy.
Centrum administracyjne Exchange | Centrum administracyjne Exchange jest zgodna z zasady nazewnictwa. Użytkownicy otrzymają odpowiedniego komunikatu o błędzie z sugerowanych prefiksów i sufiksów, a dla zablokowanych podasz niestandardowe wyrazy, jeśli ich nie są zgodne z konwencji nazewnictwa, nazwę grupy i alias grupy.
Centrum administracyjne usługi Microsoft 365 | Centrum administracyjne usługi Microsoft 365 jest zgodna z zasady nazewnictwa. Gdy użytkownik tworzy lub zmiany nazwy grupy, zasady nazewnictwa jest automatycznie stosowany i użytkownicy otrzymają odpowiednie błędy po użytkownik podał podasz niestandardowe wyrazy zablokowane. Centrum administracyjne usługi Microsoft 365 jeszcze nie wyświetla podgląd zasady nazewnictwa i nie zwraca błędy niestandardowe zablokowanego wyrazu, gdy użytkownik wprowadzi nazwę grupy.

## <a name="next-steps"></a>Kolejne kroki

Te artykuły zawierają dodatkowe informacje na temat grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zasady wygasania grup usługi Office 365](groups-lifecycle.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
