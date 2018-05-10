---
title: Nazwa ustawienia zasad grupy dla grup usługi Office 365 w usłudze Azure Active Directory (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Jak skonfigurować wygaśnięcia dla grup usługi Office 365 w usłudze Azure Active Directory (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: curtand
manager: michael.tillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/29/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 1cfd85eda9ef59a2265a991cabe5f1b6ee83563d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Wymuszanie zasad nazewnictwa dla grup usługi Office 365 w usłudze Azure Active Directory (wersja zapoznawcza)

Aby wymusić spójne konwencji nazewnictwa grup usługi Office 365 utworzonych lub edytowanych przez użytkowników, należy skonfigurować grupę nazewnictwa zasad dla dzierżawców w usłudze Azure Active Directory (Azure AD). Na przykład można użyć zasad nazewnictwa do komunikowania się funkcji grupy członkostwa, regionu geograficznego lub twórca grupy. Zasady nazewnictwa mogą również wykorzystać do skategoryzowania grup w książce adresowej. Zasady służy do blokowania słów z użycia grupy nazw i aliasów.

> [!IMPORTANT]
> Przy użyciu zasad nazewnictwa grup Office 365 w wersji zapoznawczej wymaga licencji usługi Azure Active Directory Premium P1 i Azure AD podstawowa Education licencji dla każdego unikatowego użytkownika należącego do jednej lub więcej grup usługi Office 365.

Zasady nazewnictwa są stosowane do tworzenia lub edytowania grup utworzonych przez obciążenia (na przykład, Outlook, Teams firmy Microsoft, SharePoint, Exchange lub planowania). Jest stosowana do nazwy grupy i aliasu grupy. Jeśli istnieje już grupa wymiany zasad nazewnictwa skonfigurować zasady nazewnictwa w usłudze Azure AD, Azure AD nazewnictwa zasad została zastosowana.

## <a name="naming-policy-features"></a>Funkcje zasad nazewnictwa
Zasady nazewnictwa dla grup usługi Office 365 można wymusić na dwa sposoby:

-   **Zasady nazewnictwa prefiksu i sufiksu** można zdefiniować prefiksy lub sufiksy nazw, które następnie są automatycznie dodawane do wymuszania konwencję nazewnictwa grup (na przykład w nazwie grupy "DGRP\_Japonii\_Moja grupa\_ Działu inżynierii", DGRP\_Japonii\_ prefiksu, i \_Engineering jest sufiksem). 

-   **Niestandardowe zablokowane słowa** możesz przekazać zestaw wyrazów zablokowanych określonych dla organizacji, zablokowanie grup utworzonych przez użytkowników (na przykład "Prezes, Lista płac, HR").

### <a name="prefix-suffix-naming-policy"></a>Zasady nazewnictwa prefiksu i sufiksu

Ogólna struktura konwencji nazewnictwa jest "Prefiks [Nazwa_grupy] sufiks". Podczas można zdefiniować wiele prefiksów i sufiksów, może mieć tylko jedno wystąpienie [Nazwa_grupy] w ustawieniu. Prefiksy lub sufiksy może być stały ciągów lub atrybutów użytkownika takich jak \[działu\] które są zastępowane oparte na użytkownika, który jest tworzona grupa. Dopuszczalna liczba znaków z ciągów prefiksu i sufiksu łączyć to 53 znaków. 

Prefiksy i sufiksów może zawierać znaków specjalnych, które są obsługiwane przez nazwę grupy i aliasu grupy. Znaki prefiksu lub sufiksu, które nie są obsługiwane w aliasie grupy nadal są stosowane w polu Nazwa grupy, ale usunięte z aliasu grupy. Z powodu tego ograniczenia prefiksów i sufiksów stosować do nazwy grupy mogą się różnić od, które są stosowane do aliasu grupy. 

#### <a name="fixed-strings"></a>Ciągi o stałej

Ciągi umożliwia łatwiejsze do skanowania i odróżnienia go od grupy globalnej liście adresowej i linki nawigacji po lewej stronie obciążeń grupy. Niektóre typowe prefiksy są słowa kluczowe, takie jak "DGRP\_nazwy", "\#nazwy","\_nazwę"

#### <a name="user-attributes"></a>Atrybuty użytkownika

Można użyć atrybutów, które mogą pomóc Ci i zidentyfikować użytkowników, działów, office lub regionu geograficznego, dla którego utworzono grupę. Na przykład w przypadku definiowania zasad nazewnictwa jako `PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”`, i `User’s department = Engineering`, nazwa grupy wymuszone może być "DGRP mojej grupy Engineering." Obsługiwane usługi Azure AD atrybuty \[działu\], \[firmy\], \[Office\], \[StanLubProwincja\], \[CountryOrRegion \], \[Tytuł\]. Atrybuty użytkownika nieobsługiwany są traktowane jako ciągi fixed; na przykład "\[KodPocztowy\]". Atrybuty rozszerzenia oraz atrybuty niestandardowe nie są obsługiwane.

Firma Microsoft zaleca użycie atrybuty, które mają wartości wypełnione dla wszystkich użytkowników w organizacji i nie używaj atrybutów, które mają długi wartości.

### <a name="custom-blocked-words"></a>Niestandardowe słowa zablokowanych

Listę zablokowanych word jest rozdzielana przecinkami lista fraz zablokowanie grupy nazw i aliasów. Brak wyszukiwań podciąg są wykonywane. Dokładnego dopasowania między nazwą grupy i co najmniej jeden niestandardowy wyrazów zablokowanych jest wymagana do wyzwolenia awarii. Nie jest prowadzone wyszukiwanie podciąg, dzięki czemu użytkownicy mogą używać popularnych wyrazów, takich jak "Class", nawet jeśli "klasy" jest słowem zablokowany.

Reguły listy zablokowanych word:
- Zablokowane wyrazy nie są z uwzględnieniem wielkości liter.
- Gdy użytkownik wprowadza zablokowanych word jako część nazwy grupy, zobaczy komunikat o błędzie wyrazem zablokowany.
- Nie ma żadnych ograniczeń znak słowa zablokowany.
- Brak maksymalnie 5000 wyrażeń, które można skonfigurować na liście zablokowanych słów. 

### <a name="administrator-override"></a>Zastąpienie administratora

Wybrane Administratorzy mogą być wyłączone z tych zasad, we wszystkich obciążeń grupy i punktów końcowych, tak, aby mogli tworzyć grup przy użyciu słowa zablokowane i z własnych konwencje nazewnictwa. Poniżej przedstawiono listę ról administratora wykluczone z grupy zasad nazewnictwa.

- Administrator globalny
- Obsługa warstwy 1 partnera
- Obsługa warstwy 2 partnera
- Administrator konta użytkownika
- Zapisywanie katalogów

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>Zainstaluj polecenia cmdlet programu PowerShell, aby skonfigurować zasady nazewnictwa

Należy odinstalować wszelkie starszej wersji środowiska PowerShell usługi Azure Active Directory Graph modułu dla środowiska Windows PowerShell i zainstalować [Azure Active Directory PowerShell wykresu — publicznej wersji zapoznawczej 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) przed ponownym uruchomieniem Polecenia programu PowerShell. 

1. Otwórz aplikację programu Windows PowerShell jako administrator.
2. Odinstaluj wszelkie wcześniejsze wersje AzureADPreview.
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. Zainstaluj najnowszą wersję AzureADPreview.
  
  ````
  Install-Module AzureADPreview
  ````
Jeśli zostanie wyświetlony monit o dostępie do niezaufanych repozytorium, wpisz **Y**. Może upłynąć kilka minut, aż do zainstalowania nowego modułu.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Konfigurowanie grupy nazewnictwa zasad dla dzierżawy przy użyciu programu PowerShell usługi Azure AD

1. Otwórz okno programu Windows PowerShell na komputerze. Możesz otworzyć go bez podniesionych uprawnień.

2. Uruchom następujące polecenia, aby przygotować się do uruchamiania poleceń cmdlet.
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  W **Zaloguj się do swojego konta** ekranu, który zostanie otwarty, wprowadź konto administratora i hasło, należy połączyć się z usługą, a następnie wybierz **Zaloguj**.

3. Postępuj zgodnie z instrukcjami [poleceń cmdlet usługi Azure Active Directory do konfigurowania ustawień grupy](active-directory-accessmanagement-groups-settings-cmdlets.md) można utworzyć grupy ustawień dla tej dzierżawy.

### <a name="view-the-current-settings"></a>Wyświetl bieżące ustawienia

1. Pobierz bieżące zasady nazewnictwa, aby wyświetlić bieżące ustawienia.
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. Wyświetl bieżące ustawienia grupy.
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ustaw zasady nazewnictwa i niestandardowych słów zablokowanych

1. Ustaw grupy prefiksów i sufiksów nazw w programie Azure AD PowerShell.
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. Ustaw niestandardowe zablokowanych słowa, które ma zostać ograniczony. Poniższy przykład przedstawia sposób dodawania niestandardowych słów.
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. Zapisz ustawienia nowych zasad zadziałało, takich jak w poniższym przykładzie.
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
To już wszystko. Został ustawiony zasad nazewnictwa i dodać zablokowanych słów.

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>Eksportowanie lub importowanie listę niestandardowych słów zablokowanych

Aby uzyskać więcej informacji, zobacz artykuł [poleceń cmdlet usługi Azure Active Directory do konfigurowania ustawień grupy](active-directory-accessmanagement-groups-settings-cmdlets.md).

Poniżej przedstawiono przykładowy skrypt programu PowerShell, aby wyeksportować wiele słów zablokowany:

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

Poniżej przedstawiono przykładowy skrypt programu PowerShell, aby zaimportować wiele słów zablokowany:

````
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
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Zasady nazewnictwa napotka w aplikacjach pakietu Office 365

Po ustawieniu zasad grupy nazewnictwa w usłudze Azure AD, gdy użytkownik tworzy grupę w aplikacji usługi Office 365, zobaczy: 

* A Podgląd nazwy zgodnie z zasadami nazewnictwa (z prefiksów i sufiksów) tak szybko, jak typy użytkownika w polu Nazwa grupy
* Jeśli użytkownik wprowadzi zablokowanych wyrazy, będzie zobaczy komunikat o błędzie, będą oni mogli usunąć zablokowanych słów.

Obciążenie | Zgodność
----------- | -------------------------------
Portale usługi Azure Active Directory | Portalu usługi Azure AD i portalu panelu dostępu wyświetlić nazwy wymuszona zasada, gdy użytkownik wpisze w polu Nazwa grupy, podczas tworzenia lub edytowania grupy. Gdy użytkownik wprowadza niestandardowych word zablokowanych, komunikat o błędzie wyrazem zablokowanych jest wyświetlana, dzięki czemu użytkownik może go usunąć.
Outlook Web Access (OWA) | W programie Outlook Web Access zawiera zasady nazewnictwa wymuszane nazwy, gdy użytkownik wpisuje nazwę grupy lub aliasu grupy. Gdy użytkownik wprowadza niestandardowych word zablokowanych, komunikat o błędzie jest wyświetlany w interfejsu użytkownika wraz z zablokowanych word, dzięki czemu użytkownik może go usunąć.
Aplikacja klasyczna Outlook | Grupy utworzone w aplikacja klasyczna Outlook są zgodne z ustawieniami zasad nazewnictwa. Aplikacja klasyczna Outlook jeszcze nie wyświetla nazwę grupy wymuszone w wersji zapoznawczej i nie zwraca błędy niestandardowe word zablokowane, gdy użytkownik wprowadza nazwę grupy. Zasady nazewnictwa jest stosowana automatycznie, podczas tworzenia lub edytowania grupy i użytkownicy widzą komunikaty o błędach w przypadku niestandardowych słów zablokowanych nazwę grupy lub alias.
Microsoft Teams | Teams firmy Microsoft zawiera grupę nazewnictwa Nazwa zasady wymuszane, gdy użytkownik wprowadza nazwę zespołu. Gdy użytkownik wprowadza niestandardowych word zablokowanych, komunikat o błędzie jest wyświetlany wraz z zablokowanych word, dzięki czemu użytkownik może go usunąć.
Sharepoint  |  Programu SharePoint zawiera nazwy wymuszona zasada typy użytkowników, a witryna nazwa lub adres e-mail grupy. Kiedy użytkownik wprowadzi niestandardowych word zablokowanych komunikat o błędzie jest wyświetlany, wraz z zablokowanych word, dzięki czemu użytkownik może go usunąć.
Microsoft Stream | Stream firmy Microsoft zawiera grupę nazewnictwa Nazwa zasady wymuszane, gdy użytkownik wpisuje nazwę grupy lub alias e-mail grupy. Gdy użytkownik wprowadza niestandardowych word zablokowanych, komunikat o błędzie jest wyświetlany wyrazem zablokowanych, użytkownik może go usunąć.
IOS programu Outlook i aplikacji systemu Android | Grupy utworzone w aplikacji Outlook są zgodne ze skonfigurowanymi zasadami nazewnictwa. Outlook aplikacji mobilnej jeszcze nie wyświetla podgląd nazwy zasad wymuszane, a nie zwraca błędy niestandardowe word zablokowane, gdy użytkownik wprowadza nazwę grupy. Jednak po kliknięciu przycisku Utwórz/Edytuj automatycznie są stosowane zasady nazewnictwa i użytkowników wyświetlić komunikaty o błędach w przypadku niestandardowych słów zablokowanych nazwę grupy lub aliasu.
Grupy aplikacji mobilnej | Grupy utworzone w aplikacji mobilnej grup są zgodne z zasadami nazewnictwa. Grupy aplikacji mobilnej nie są wyświetlane w wersji zapoznawczej zasad nazewnictwa i nie zwraca błędy niestandardowe word zablokowane, gdy użytkownik wprowadza nazwę grupy. Podczas tworzenia lub edytowania grupy są automatycznie stosowane zasady nazewnictwa, a użytkowników zostanie zaprezentowany odpowiednie błędy występują niestandardowe słowa zablokowanych nazwę grupy lub alias.
Planner | Planowanie jest zgodne z zasadami nazewnictwa. Planowanie Podgląd nazewnictwa zasad podczas wprowadzania nazwy planu. Gdy użytkownik wprowadza niestandardowych zablokowanych programu word, komunikat o błędzie jest wyświetlany podczas tworzenia planu.
Dynamics 365 naboru klienta | Dynamics 365 naboru klienta jest zgodne z zasadami nazewnictwa. Dynamics 365 zawiera nazwy wymuszona zasada, gdy użytkownik wpisuje nazwę grupy lub alias e-mail grupy. Gdy użytkownik wprowadzi niestandardowych word zablokowanych, komunikat o błędzie jest wyświetlany wyrazem zablokowanych, użytkownik może go usunąć.
Synchronizacja danych służbowych (SDS) | Grup utworzonych za pomocą SDS są zgodne z zasadami nazewnictwa, ale nie jest automatycznie stosowane zasady nazewnictwa. Administratorzy SDS mają dołączyć prefiksów i sufiksów nazw klas, dla których grup należy utworzyć i następnie przekazywane do SDS. Utwórz grupy lub edycji nie powiedzie się inaczej.
Menedżer klienta programu Outlook (OCM) | Menedżer klienta programu Outlook jest zgodne z zasadami nazewnictwa, który jest automatycznie stosowane do grupy utworzone w Menedżerze klienta programu Outlook. W przypadku wykrycia niestandardowych word zablokowanych tworzenie grup w OCM jest zablokowane, a użytkownik jest zablokowany za pomocą aplikacji OCM.
Klasy aplikacji | Grupy utworzone w klasie aplikacji jest zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie jest stosowana automatycznie, a nazewnictwa Podgląd zasad nie jest widoczne dla użytkowników podczas wprowadzania nazwy grupy klas. Użytkownicy muszą wprowadzić nazwę grupy wymuszone klasy z prefiksów i sufiksów. Jeśli nie, Grupa klas tworzenia lub edytowania operacja kończy się niepowodzeniem z błędami.
Power BI | Obszary robocze programu Power BI są zgodne z zasadami nazewnictwa.    
Yammer | Usługi Yammer podłączonymi grupami nie wymuszają skonfigurowanymi zasadami nazewnictwa. W przypadku organizacji z włączonymi zasadami nazewnictwa Yammer tworzy starszych grup Yammer, które nie są podłączone do usługi Office 365 dla grup, które nie są zgodne z zasadami nazewnictwa.
StaffHub  | Zespoły StaffHub nie są zgodne z zasadami nazewnictwa, ale nie podstawowej grupy usługi Office 365. Nazwa zespołu StaffHub nie ma zastosowania prefiksów i sufiksów i nie sprawdza niestandardowych słów zablokowany. Jednak StaffHub zastosować prefiksów i sufiksów i usuwa zablokowanych słowa z podstawowej grupy usługi Office 365.
Exchange PowerShell | Polecenia cmdlet programu PowerShell programu Exchange są zgodne z zasadami nazewnictwa. Użytkownicy będą otrzymywać wiadomości odpowiednie błąd z sugerowanych prefiksów i sufiksów i niestandardowych słów zablokowane, jeśli nie stosują zasady nazewnictwa Nazwa grupy i aliasu grupy (mailNickname).
Polecenia cmdlet środowiska PowerShell usługi Active Directory systemu Azure | Polecenia cmdlet środowiska PowerShell usługi Active Directory systemu Azure są zgodne z zasadami nazewnictwa. Użytkownicy będą otrzymywać wiadomości odpowiednie błąd z sugerowanych prefiksów i sufiksów i niestandardowych słów zablokowane, jeśli nie stosują konwencji nazewnictwa nazwy grup i aliasu grupy.
Centrum administracyjne Exchange | Centrum administracyjne Exchange jest zgodne z zasadami nazewnictwa. Użytkownicy będą otrzymywać wiadomości odpowiednie błąd z sugerowanych prefiksów i sufiksów i niestandardowych słów zablokowane, jeśli nie stosują konwencji nazewnictwa, nazwa_grupy i aliasu grupy.
Centrum administracyjne usługi Office 365 | Centrum administracyjne usługi Office 365 jest zgodne z zasadami nazewnictwa. Gdy użytkownik tworzy lub zmiany nazwy grup, automatycznie są stosowane zasady nazewnictwa i użytkownicy otrzymują odpowiednie błędy po wprowadzeniu niestandardowych słów zablokowany. Centrum administracyjne usługi Office 365 jeszcze nie wyświetla podgląd zasad nazewnictwa i nie zwraca błędy niestandardowe word zablokowane, gdy użytkownik wprowadza nazwę grupy.

## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o grup usługi Azure AD.

* [Zobacz istniejących grup](active-directory-groups-view-azure-portal.md)
* [Zasady wygasania dla grup usługi Office 365](active-directory-groups-lifecycle-azure-portal.md)
* [Zarządzanie ustawieniami grupy](active-directory-groups-settings-azure-portal.md)
* [Elementy członkowskie grupy zarządzania](active-directory-groups-members-azure-portal.md)
* [Zarządzanie członkostwami grup](active-directory-groups-membership-azure-portal.md)
* [Dynamiczne reguły dla użytkowników w grupie zarządzania](active-directory-groups-dynamic-membership-azure-portal.md)
