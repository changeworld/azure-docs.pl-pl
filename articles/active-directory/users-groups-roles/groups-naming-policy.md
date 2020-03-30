---
title: Wymuszanie zasad nazewnictwa grup w usłudze Azure Active Directory | Dokumenty firmy Microsoft
description: Jak skonfigurować zasady nazewnictwa dla grup usługi Office 365 w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 141e83e21db18f21468113fd9927c2bdd2ed176d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497881"
---
# <a name="enforce-a-naming-policy-on-office-365-groups-in-azure-active-directory"></a>Wymuszanie zasad nazewnictwa w grupach usługi Office 365 w usłudze Azure Active Directory

Aby wymusić spójne konwencje nazewnictwa dla grup usługi Office 365 utworzonych lub edytowanych przez użytkowników, skonfiguruj zasady nazewnictwa grup dla dzierżaw w usłudze Azure Active Directory (Azure AD). Na przykład można użyć zasad nazewnictwa do komunikowania funkcji grupy, członkostwa, regionu geograficznego lub osoby, która utworzyła grupę. Można również użyć zasad nazewnictwa, aby ułatwić kategoryzowanie grup w książce adresowej. Za pomocą zasad można zablokować użycie określonych wyrazów w nazwach grup i aliasach.

> [!IMPORTANT]
> Korzystanie z zasad nazewnictwa usługi Azure AD dla grup usługi Office 365 wymaga posiadania, ale niekoniecznie przypisać licencję usługi Azure Active Directory Premium P1 lub licencję EDU usługi Azure AD BASIC dla każdego unikatowego użytkownika, który jest członkiem co najmniej jednej grupy usługi Office 365.

Zasady nazewnictwa są stosowane do tworzenia lub edytowania grup utworzonych w różnych obciążeniach (na przykład Outlook, Microsoft Teams, SharePoint, Exchange lub Planner). Jest on stosowany zarówno do nazwy grupy, jak i aliasu grupy. Jeśli skonfigurujesz zasady nazewnictwa w usłudze Azure AD i masz istniejące zasady nazewnictwa grupy programu Exchange, zasady nazewnictwa usługi Azure AD są wymuszane w organizacji.

Po skonfigurowaniu zasad nazewnictwa grup zasady zostaną zastosowane do nowych grup usługi Office 365 utworzonych przez użytkowników końcowych. Zasady nazewnictwa nie mają zastosowania do niektórych ról katalogów, takich jak administrator globalny lub administrator użytkownika (poniżej znajduje się pełna lista ról zwolnionych z zasad nazewnictwa grup). W przypadku istniejących grup usługi Office 365 zasady nie będą stosowane natychmiast w momencie konfiguracji. Gdy właściciel grupy zmieni nazwę grupy dla tych grup, zasady nazewnictwa zostaną wymuszane.

## <a name="naming-policy-features"></a>Funkcje zasad nazewnictwa

Zasady nazewnictwa dla grup można wymusić na dwóch różnych sposobach:

- **Zasady nazewnictwa prefiksu-sufiksu** Można zdefiniować prefiksy lub sufiksy, które są następnie dodawane automatycznie w celu wymuszenia konwencji nazewnictwa w grupach (na przykład w nazwie grupy "GRP\_JAPAN\_My Group\_Engineering", GRP\_JAPAN\_ jest prefiksem, a \_Inżynieria jest sufiksem). 

- **Niestandardowe zablokowane wyrazy** Możesz przesłać zestaw zablokowanych słów specyficznych dla twojej organizacji, które mają być blokowane w grupach utworzonych przez użytkowników (na przykład "Dyrektor generalny, lista płac, kadr").

### <a name="prefix-suffix-naming-policy"></a>Zasady nazewnictwa prefiksu-sufiksu

Ogólna struktura konwencji nazewnictwa to "Prefix[GroupName]Suffix". Chociaż można zdefiniować wiele prefiksów i sufiksów, w ustawieniu można mieć tylko jedno wystąpienie [GroupName]. Prefiksy lub sufiksy mogą być stałymi ciągami lub atrybutami użytkownika, takimi jak \[Dział,\] które są podstawione na podstawie użytkownika, który tworzy grupę. Łączna dopuszczalna liczba znaków dla ciągów prefiksu i sufiksu łącznie wynosi 53 znaki. 

Prefiksy i sufiksy mogą zawierać znaki specjalne obsługiwane w aliasie nazwy grupy i grupy. Wszystkie znaki w prefiksie lub sufiksie, które nie są obsługiwane w aliasie grupy, są nadal stosowane w nazwie grupy, ale usuwane z aliasu grupy. Z powodu tego ograniczenia prefiksy i sufiksy zastosowane do nazwy grupy mogą się różnić od tych zastosowanych do aliasu grupy. 

#### <a name="fixed-strings"></a>Stałe ciągi znaków

Ciągów można użyć, aby ułatwić skanowanie i różnicowanie grup na globalnej liście adresów i w lewej łącza nawigacyjnej obciążeń grupowych. Niektóre z typowych prefiksów to\_słowa kluczowe,\#takie jak\_"Grp Name", 'Name', ' Name'

#### <a name="user-attributes"></a>Atrybuty użytkownika

Można użyć atrybutów, które pomogą Tobie i Użytkownikom zidentyfikować dział, biuro lub region geograficzny, dla którego grupa została utworzona. Jeśli na przykład zasady nazewnictwa `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"`zdefiniujesz jako , a `User’s department = Engineering`wymuszoną nazwą grupy może być "GRP My Group Engineering". Obsługiwane atrybuty usługi \[\]Azure \[\]AD \[\]to \[Dział, Firma, \[Biuro,\]StateOrProvince\], CountryOrRegion , \[Title\]. Nieobsługiwały atrybuty użytkownika są traktowane jako stałe ciągi; na przykład\["\]postalCode ". Atrybuty rozszerzenia i atrybuty niestandardowe nie są obsługiwane.

Zaleca się używanie atrybutów, które mają wartości wypełnione dla wszystkich użytkowników w organizacji i nie używać atrybutów, które mają długie wartości.

### <a name="custom-blocked-words"></a>Niestandardowe zablokowane wyrazy

Zablokowana lista wyrazów to oddzielona przecinkami lista fraz, które mają być blokowane w nazwach grup i aliasach. Nie są wykonywane żadne wyszukiwania podstrunu. Dokładne dopasowanie między nazwą grupy a co najmniej jednym z niestandardowych zablokowanych słów jest wymagane do wyzwolenia błędu. Wyszukiwanie podciągań nie jest wykonywane, aby użytkownicy mogli używać typowych słów, takich jak "Klasa", nawet jeśli "lass" jest zablokowanym słowem.

Reguły listy zablokowanych wyrazów:

- W zablokowanych słowach nie rozróżnia się wielkość liter.
- Gdy użytkownik wprowadzi zablokowany wyraz jako część nazwy grupy, zostanie wyświetlony komunikat o błędzie z zablokowanym wyrazem.
- Nie ma żadnych ograniczeń znaków w zablokowanych wyrazach.
- Istnieje górny limit 5000 fraz, które można skonfigurować na liście zablokowanych słów. 

### <a name="roles-and-permissions"></a>Role i uprawnienia

Aby skonfigurować zasady nazewnictwa, wymagana jest jedna z ról folowing:
- Administrator globalny
- Administrator grupy
- Administrator użytkownika

Wybrani administratorzy mogą być zwolnieni z tych zasad we wszystkich obciążeniach grupowych i punktach końcowych, dzięki czemu mogą tworzyć grupy przy użyciu zablokowanych słów i z własnymi konwencjami nazewnictwa. Poniżej przedstawiono listę ról administratora wyłączonych z zasad nazewnictwa grup.

- Administrator globalny
- Wsparcie dla partnerów tier 1
- Wsparcie dla partnerów tier 2
- Administrator użytkownika
- Autorzy katalogów

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurowanie zasad nazewnictwa w witrynie Azure Portal

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta administratora grupy.
1. Wybierz **pozycję Grupy**, a następnie wybierz pozycję Zasady **nazewnictwa,** aby otworzyć stronę zasad nazewnictwa.

    ![otwieranie strony zasad nazewnictwa w centrum administracyjnym](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Wyświetlanie lub edytowanie zasad nazewnictwa prefiksu-sufiksu

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Zasada nazewnictwa grupy**.
1. Bieżący prefiks lub zasady nazewnictwa sufiksów można wyświetlać lub edytować indywidualnie, wybierając atrybuty lub ciągi, które mają być wymuszane w ramach zasad nazewnictwa.
1. Aby usunąć prefiks lub sufiks z listy, zaznacz prefiks lub sufiks, a następnie wybierz pozycję **Usuń**. Wiele elementów można usunąć w tym samym czasie.
1. Zapisz zmiany, aby nowe zasady weszły w życie, wybierając pozycję **Zapisz**.

### <a name="edit-custom-blocked-words"></a>Edytowanie niestandardowych zablokowanych wyrazów

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Zablokowane wyrazy**.

    ![edytowanie i przesyłanie listy zablokowanych słów w celu nazewnictwa zasad](./media/groups-naming-policy/blockedwords.png)

1. Wyświetl lub edytuj bieżącą listę niestandardowych zablokowanych słów, wybierając pozycję **Pobierz**.
1. Prześlij nową listę niestandardowych zablokowanych słów, wybierając ikonę pliku.
1. Zapisz zmiany, aby nowe zasady weszły w życie, wybierając pozycję **Zapisz**.

## <a name="install-powershell-cmdlets"></a>Instalowanie poleceń cmdlet programu PowerShell

Pamiętaj, aby odinstalować starszą wersję modułu Azure Active Directory PowerShell dla programu Graph z programu Windows PowerShell i zainstalować moduł [Azure Active Directory PowerShell dla programu Graph w publicznej wersji zapoznawczej 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) przed uruchomieniem poleceń programu PowerShell.

1. Otwórz aplikację Windows PowerShell jako administrator.
2. Odinstaluj poprzednią wersję programu AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Zainstaluj najnowszą wersję programu AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Jeśli zostanie wyświetlony monit o dostęp do niezaufanego repozytorium, wprowadź **Y**. Instalacja nowego modułu może potrwać kilka minut.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurowanie zasad nazewnictwa w programie PowerShell

1. Otwórz okno programu Windows PowerShell na komputerze. Można go otworzyć bez podwyższonych uprawnień.

1. Uruchom następujące polecenia, aby przygotować się do uruchomienia poleceń cmdlet.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   Na ekranie **Zaloguj się na swoje konto** wprowadź swoje konto administratora i hasło, aby połączyć się z usługą, a następnie wybierz polecenie **Zaloguj**.

1. Postępuj zgodnie z instrukcjami zawartymi w artykule [Azure Active Directory cmdlets for configuring group settings (Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy)](groups-settings-cmdlets.md), aby utworzyć ustawienia grupy dla tej dzierżawy.

### <a name="view-the-current-settings"></a>wyświetlanie bieżących ustawień

1. Pobierz bieżącą zasadę nazewnictwa, aby wyświetlić bieżące ustawienia.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Wyświetl bieżące ustawienia grupy.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ustawianie zasad nazewnictwa i niestandardowych zablokowanych wyrazów

1. Ustaw prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell. Aby ta funkcja działała poprawnie, należy dodać element [GroupName] do ustawienia.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Ustaw niestandardowe słowa zablokowane. W poniższym przykładzie pokazano, jak dodać własne słowa niestandardowe.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Zapisz ustawienia nowych zasad, które mają wejść w życie, na przykład w poniższym przykładzie.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Gotowe. Ustawisz zasady nazewnictwa i dodałeś zablokowane słowa.

## <a name="export-or-import-custom-blocked-words"></a>Eksportowanie lub importowanie niestandardowych zablokowanych wyrazów

Aby uzyskać więcej informacji, zobacz artykuł [Polecenia cmdlet usługi Azure Active Directory dotyczące konfigurowania ustawień grupy](groups-settings-cmdlets.md).

Oto przykład skryptu programu PowerShell do eksportowania wielu zablokowanych słów:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Oto przykład skryptu programu PowerShell do importowania wielu zablokowanych słów:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Usuwanie zasad nazewnictwa

### <a name="remove-the-naming-policy-using-azure-portal"></a>Usuwanie zasad nazewnictwa przy użyciu witryny Azure Portal

1. Na stronie **Zasady nazewnictwa** wybierz pozycję **Usuń zasady**.
1. Po potwierdzeniu usunięcia zasady nazewnictwa zostaną usunięte, w tym wszystkie zasady nazewnictwa prefiksów i wszelkie niestandardowe zablokowane słowa.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Usuwanie zasad nazewnictwa przy użyciu programu Azure AD PowerShell

1. Usuń prefiksy i sufiksy nazw grup w usłudze Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Usuń niestandardowe słowa zablokowane.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Zapisz ustawienia.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-office-365-apps"></a>Środowisko w aplikacjach usługi Office 365

Po ustawieniu zasad nazewnictwa grupy w usłudze Azure AD, gdy użytkownik tworzy grupę w aplikacji usługi Office 365, widzą:

- Podgląd nazwy zgodnie z zasadami nazewnictwa (z prefiksami i sufiksami) zaraz po wykreśleniu przez użytkownika nazwy grupy
- Jeśli użytkownik wprowadzi zablokowane słowa, zostanie wyświetlony komunikat o błędzie, aby usunąć zablokowane słowa.

Obciążenie | Zgodność
----------- | -------------------------------
Portale usługi Azure Active Directory | Portal usługi Azure AD i portal panelu dostępu pokazują nazwę wymuszaną zasadą nazewnictwa, gdy użytkownik wpisuje nazwę grupy podczas tworzenia lub edytowania grupy. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, wyświetlany jest komunikat o błędzie z zablokowanym wyrazem, aby użytkownik mógł go usunąć.
Program Outlook Web Access (OWA) | Program Outlook Web Access pokazuje wymuszaną nazwę zasad nazewnictwa, gdy użytkownik wpisuje nazwę grupy lub alias grupy. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, w interfejsie użytkownika jest wyświetlany komunikat o błędzie wraz z zablokowanym wyrazem, aby użytkownik mógł go usunąć.
Pulpit programu Outlook | Grupy utworzone na pulpicie programu Outlook są zgodne z ustawieniami zasad nazewnictwa. Aplikacja klasyczna programu Outlook nie wyświetla jeszcze podglądu wymuszonej nazwy grupy i nie zwraca niestandardowych zablokowanych błędów wyrazu, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa są automatycznie stosowane podczas tworzenia lub edytowania grupy, a użytkownicy widzą komunikaty o błędach, jeśli w nazwie lub aliasie grupy znajdują się niestandardowe zablokowane wyrazy.
Microsoft Teams | Usługa Microsoft Teams pokazuje wymuszaną nazwę zasad nazewnictwa grup, gdy użytkownik wprowadza nazwę zespołu. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, wraz z zablokowanym wyrazem zostanie wyświetlony komunikat o błędzie, aby użytkownik mógł go usunąć.
Program SharePoint  |  Program SharePoint pokazuje wymuszaną nazwę zasad nazewnictwa, gdy użytkownik wpisuje nazwę witryny lub grupowy adres e-mail. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, zostanie wyświetlony komunikat o błędzie wraz z zablokowanym wyrazem, aby użytkownik mógł go usunąć.
Usługa Microsoft Stream | Usługa Microsoft Stream pokazuje wymuszaną nazwę zasad nazewnictwa grupy, gdy użytkownik wpisuje nazwę grupy lub grupowy alias e-mail. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, zostanie wyświetlony komunikat o błędzie z zablokowanym wyrazem, aby użytkownik mógł go usunąć.
Aplikacja Outlook na iOS i Android | Grupy utworzone w aplikacjach programu Outlook są zgodne ze skonfigurowaną zasadą nazewnictwa. Aplikacja mobilna programu Outlook nie wyświetla jeszcze podglądu nazwy wymuszanej zasad nazewnictwa i nie zwraca niestandardowych zablokowanych błędów wyrazów, gdy użytkownik wprowadzi nazwę grupy. Jednak zasady nazewnictwa są automatycznie stosowane po kliknięciu przycisku utwórz/edytuj, a użytkownicy widzą komunikaty o błędach, jeśli w nazwie lub aliasie grupy znajdują się niestandardowe zablokowane słowa.
Grupuje aplikację mobilną | Grupy utworzone w aplikacji mobilnej Grupy są zgodne z zasadami nazewnictwa. Aplikacja mobilna Grupy nie wyświetla podglądu zasad nazewnictwa i nie zwraca niestandardowych zablokowanych błędów wyrazów, gdy użytkownik wprowadzi nazwę grupy. Ale zasady nazewnictwa są automatycznie stosowane podczas tworzenia lub edytowania grupy, a użytkownicy są przedstawiani z odpowiednimi błędami, jeśli w nazwie lub aliasie grupy znajdują się niestandardowe zablokowane wyrazy.
Planner | Planista jest zgodny z zasadami nazewnictwa. Planer pokazuje podgląd zasad nazewnictwa podczas wprowadzania nazwy planu. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, podczas tworzenia planu wyświetlany jest komunikat o błędzie.
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagement jest zgodny z zasadami nazewnictwa. Dynamics 365 pokazuje nazwę wymuszaną zasadą nazewnictwa, gdy użytkownik wpisuje nazwę grupy lub grupowy alias e-mail. Gdy użytkownik wprowadzi niestandardowe zablokowane słowo, zostanie wyświetlony komunikat o błędzie z zablokowanym wyrazem, aby użytkownik mógł go usunąć.
Synchronizacja danych szkolnych (SDS) | Grupy utworzone za pomocą sds są zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie są stosowane automatycznie. Administratorzy kart SDS muszą dołączyć prefiksy i sufiksy do nazw klas, dla których grupy muszą zostać utworzone, a następnie przekazane do usługi SDS. Grupowanie tworzenia lub edytowania nie powiedzie się w przeciwnym razie.
Program Outlook Customer Manager (OCM) | Program Outlook Customer Manager jest zgodny z zasadami nazewnictwa, które są automatycznie stosowane do grupy utworzonej w programie Outlook Customer Manager. Jeśli zostanie wykryte niestandardowe zablokowane słowo, tworzenie grup w OCM jest blokowane, a użytkownik jest zablokowany przy użyciu aplikacji OCM.
Aplikacja Classroom | Grupy utworzone w aplikacji Classroom są zgodne z zasadami nazewnictwa, ale zasady nazewnictwa nie są stosowane automatycznie, a podgląd zasad nazewnictwa nie jest wyświetlany użytkownikom podczas wprowadzania nazwy grupy w klasie. Użytkownicy muszą wprowadzić wymuszoną nazwę grupy klasy z prefiksami i sufiksami. Jeśli nie, grupa klasowa tworzy lub edytuj operację kończy się niepowodzeniem z powodu błędów.
Power BI | Obszary robocze usługi Power BI są zgodne z zasadami nazewnictwa.    
Yammer | Gdy użytkownik zalogowany do usługi Yammer za pomocą konta usługi Azure Active Directory utworzy grupę lub edytuje nazwę grupy, nazwa grupy będzie zgodna z zasadami nazewnictwa. Dotyczy to zarówno połączonych grup usługi Office 365, jak i wszystkich innych grup usługi Yammer.<br>Jeśli grupa połączona z usługą Office 365 została utworzona przed wprowadzeniem zasad nazewnictwa, nazwa grupy nie będzie automatycznie zgodna z zasadami nazewnictwa. Gdy użytkownik edytuje nazwę grupy, zostanie poproszony o dodanie prefiksu i sufiksu.
StaffHub ( StaffHub )  | Zespoły usługi StaffHub nie stosują zasad nazewnictwa, ale podstawowa grupa usługi Office 365 to robi. Nazwa zespołu StaffHub nie stosuje prefiksów i sufiksów i nie sprawdza niestandardowych zablokowanych słów. Ale StaffHub stosuje prefiksy i sufiksy i usuwa zablokowane słowa z podstawowej grupy usługi Office 365.
Exchange PowerShell | Polecenia cmdlet programu Exchange PowerShell są zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz niestandardowe zablokowane słowa, jeśli nie przestrzegają zasad nazewnictwa w nazwie grupy i aliasie grupy (mailNickname).
Polecenia cmdlet programu Azure Active Directory PowerShell | Polecenia cmdlet programu Azure Active Directory PowerShell są zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz niestandardowe zablokowane wyrazy, jeśli nie są zgodne z konwencją nazewnictwa w nazwach grup i aliasach grup.
Centrum administracyjne programu Exchange | Centrum administracyjne programu Exchange jest zgodne z zasadami nazewnictwa. Użytkownicy otrzymują odpowiednie komunikaty o błędach z sugerowanymi prefiksami i sufiksami oraz niestandardowymi zablokowanymi wyrazami, jeśli nie są zgodne z konwencją nazewnictwa w nazwie grupy i aliasie grupy.
Centrum administracyjne platformy Microsoft 365 | Centrum administracyjne usługi Microsoft 365 jest zgodne z zasadami nazewnictwa. Gdy użytkownik tworzy lub edytuje nazwy grup, zasady nazewnictwa są automatycznie stosowane, a użytkownicy otrzymują odpowiednie błędy podczas wprowadzania niestandardowych zablokowanych wyrazów. Centrum administracyjne usługi Microsoft 365 nie wyświetla jeszcze podglądu zasad nazewnictwa i nie zwraca niestandardowych zablokowanych błędów wyrazów, gdy użytkownik wprowadzi nazwę grupy.

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat grup usługi Azure AD.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Zasady wygasania dla grup usługi Office 365](groups-lifecycle.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkami grupy](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
