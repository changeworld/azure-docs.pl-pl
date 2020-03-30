---
title: Konfigurowanie ustawień grupy przy użyciu programu PowerShell — Azure AD | Dokumenty firmy Microsoft
description: Jak zarządzać ustawieniami grup przy użyciu poleceń cmdlet usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d56bb7c30a8289fe7f261979dca6a4ffe2bfe99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048155"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy

Ten artykuł zawiera instrukcje dotyczące tworzenia i aktualizowania grup poleceń cmdell usługi Azure Active Directory (Azure AD) w celu tworzenia i aktualizowania grup. Ta zawartość dotyczy tylko grup usługi Office 365 (czasami nazywanych ujednoliconymi grupami).

> [!IMPORTANT]
> Niektóre ustawienia wymagają licencji Usługi Azure Active Directory Premium P1. Aby uzyskać więcej informacji, zobacz tabelę [Ustawienia szablonu.](#template-settings)

Aby uzyskać więcej informacji na temat zapobiegania tworzeniu grup `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` zabezpieczeń przez użytkowników niebędących administratorami, ustawić zgodnie z opisem w [temacie Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Ustawienia grup usługi Office 365 są konfigurowane przy użyciu obiektu Ustawienia i obiektu SettingsTemplate. Początkowo nie widać żadnych obiektów Ustawienia w katalogu, ponieważ katalog jest skonfigurowany z ustawieniami domyślnymi. Aby zmienić ustawienia domyślne, należy utworzyć nowy obiekt ustawień przy użyciu szablonu ustawień. Szablony ustawień są definiowane przez firmę Microsoft. Istnieje kilka różnych szablonów ustawień. Aby skonfigurować ustawienia grupy usługi Office 365 dla katalogu, należy użyć szablonu o nazwie "Group.Unified". Aby skonfigurować ustawienia grupy usługi Office 365 w jednej grupie, użyj szablonu o nazwie "Group.Unified.Guest". Ten szablon służy do zarządzania dostępem gościa do grupy usługi Office 365. 

Polecenia cmdlet są częścią modułu programu Azure Active Directory PowerShell V2. Instrukcje dotyczące pobierania i instalowania modułu na komputerze można znaleźć w artykule [Usługa Azure Active Directory PowerShell w wersji 2](https://docs.microsoft.com/powershell/azuread/). Wersję modułu w wersji 2 można zainstalować z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Instalowanie poleceń cmdlet programu PowerShell

Przed uruchomieniem poleceń programu PowerShell w programie PowerShell należy odinstalować starszą wersję modułu programu Azure Active Directory PowerShell dla programu Windows dla [programu Graph — public Preview release (później niż 2.0.0.137).](https://www.powershellgallery.com/packages/AzureADPreview)

1. Otwórz aplikację Windows PowerShell jako administrator.
2. Odinstaluj poprzednią wersję programu AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Zainstaluj najnowszą wersję programu AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Tworzenie ustawień na poziomie katalogu
Te kroki tworzą ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup usługi Office 365 w katalogu. Polecenie cmdlet Get-AzureADDirectorySettingTemplate jest dostępne tylko w [module Azure AD PowerShell Preview for Graph.](https://www.powershellgallery.com/packages/AzureADPreview)

1. W poleceniach cmdlet DirectorySettings należy określić identyfikator tablicy settingstemplate, której chcesz użyć. Jeśli ten identyfikator nie jest znam, to polecenie cmdlet zwraca listę wszystkich szablonów ustawień:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   To wywołanie polecenia cmdlet zwraca wszystkie dostępne szablony:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Aby dodać adres URL wytycznych użycia, najpierw musisz uzyskać obiekt SettingsTemplate, który definiuje wartość adresu URL wytycznych użycia; oznacza to, że szablon Group.Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj wartość wytycznych użycia:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Następnie zastosuj ustawienie:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Wartości można odczytać za pomocą:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Aktualizowanie ustawień na poziomie katalogu
Aby zaktualizować wartość UsageGuideLinesUrl w szablonie ustawień, przeczytaj bieżące ustawienia z usługi Azure AD, w przeciwnym razie możemy zastąpić istniejące ustawienia inne niż UsageGuideLinesUrl.

1. Pobierz bieżące ustawienia z Group.Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Sprawdź bieżące ustawienia:
   
   ```powershell
   $Setting.Values
   ```
   
   Dane wyjściowe:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Aby usunąć wartość UsageGuideLinesUrl, edytuj adres URL jako pusty ciąg:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Zapisz aktualizację w katalogu:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Ustawienia szablonu
Poniżej przedstawiono ustawienia zdefiniowane w platformie Group.Unified SettingsTemplate. O ile nie wskazano inaczej, te funkcje wymagają licencji Usługi Azure Active Directory Premium P1. 

| **Ustawienie** | **Opis** |
| --- | --- |
|  <ul><li>EnableGroupCreation (In enablegroupCreation)<li>Typ: Boolean<li>Domyślnie: Prawda |Flaga wskazująca, czy tworzenie grupy usługi Office 365 jest dozwolone w katalogu przez użytkowników niebędących administratorami. To ustawienie nie wymaga licencji Usługi Azure Active Directory Premium P1.|
|  <ul><li>Grupa Dozwolona Grupa Grupy<li>Typ: ciąg<li>Domyślnie: "" |Identyfikator GUID grupy zabezpieczeń, dla której członkowie mogą tworzyć grupy usługi Office 365 nawet wtedy, gdy EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: ciąg<li>Domyślnie: "" |Łącze do wytycznych dotyczących użytkowania grupy. |
|  <ul><li>KlasyfikacjaOpiny<li>Typ: ciąg<li>Domyślnie: "" | Lista opisów klasyfikacji rozdzielona przecinkami. Wartość classificatione jest prawidłowa tylko w tym formacie:<br>$setting["ClassificationDescriptions"] ="Klasyfikacja:Opis,Klasyfikacja:Opis"<br>gdzie Klasyfikacja pasuje do wpisu na liście klasyfikacji.<br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels == True.|
|  <ul><li>Klasyfikacja domyślna<li>Typ: ciąg<li>Domyślnie: "" | Klasyfikacja, która ma być używana jako domyślna klasyfikacja dla grupy, jeśli nie została określona.<br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels == True.|
|  <ul><li>PrefiksSuszkłakość wymagalności<li>Typ: ciąg<li>Domyślnie: "" | Ciąg o maksymalnej długości 64 znaków definiujący konwencję nazewnictwa skonfigurowaną dla grup usługi Office 365. Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365](groups-naming-policy.md). |
| <ul><li>Lista customblockedWords<li>Typ: ciąg<li>Domyślnie: "" | Oddzielony przecinkami ciąg fraz, których użytkownicy nie będą mogli używać w nazwach grup lub aliasach. Aby uzyskać więcej informacji, zobacz [Wymuszanie zasad nazewnictwa dla grup usługi Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: Boolean<li>Domyślnie: "Fałsz" | Nie używać
|  <ul><li>AllowGuestsToBeGroupOwner (Właściciel)<li>Typ: Boolean<li>Domyślnie: Fałsz | Wartość logiczna wskazująca, czy użytkownik-gość może być właścicielem grup. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: Boolean<li>Domyślnie: Prawda | Wartość logiczna wskazująca, czy użytkownik-gość może mieć dostęp do zawartości grup usługi Office 365.  To ustawienie nie wymaga licencji Usługi Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: ciąg<li>Domyślnie: "" | Adres URL łącza do wytycznych dotyczących użycia gościa. |
|  <ul><li>AllowToAddGuests<li>Typ: Boolean<li>Domyślnie: Prawda | Wartość logiczna wskazująca, czy dozwolone jest dodawanie gości do tego katalogu. <br>To ustawienie może zostać zastąpione i stać się tylko do odczytu, jeśli *EnableMIPLabels* jest ustawiona na *True* i zasady gościa jest skojarzony z etykietą czułości przypisaną do grupy.<br>Jeśli AllowToAddGuests ustawienie jest ustawiona na False na poziomie dzierżawy, wszelkie AllowToAddGuests ustawienie na poziomie grupy jest ignorowana. Jeśli chcesz włączyć dostęp gościa tylko dla kilku grup, należy ustawić AllowToAddGuests być prawdziwe na poziomie dzierżawy, a następnie selektywnie wyłączyć go dla określonych grup. |
|  <ul><li>Lista klasyfikacji<li>Typ: ciąg<li>Domyślnie: "" | Lista prawidłowych wartości klasyfikacji rozdzielanych przecinkami, które można zastosować do grup usługi Office 365. <br>To ustawienie nie ma zastosowania, gdy EnableMIPLabels == True.|
|  <ul><li>EnableMIPLabels<li>Typ: Boolean<li>Domyślnie: "Fałsz" |Flaga wskazująca, czy etykiety wrażliwości opublikowane w Centrum zgodności usługi Microsoft 365 mogą być stosowane do grup usługi Office 365. Aby uzyskać więcej informacji, zobacz [Przypisywanie etykiet czułości dla grup usługi Office 365](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Przykład: Konfigurowanie zasad gościa dla grup na poziomie katalogu
1. Pobierz wszystkie szablony ustawień:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Aby ustawić zasady gościa dla grup na poziomie katalogu, potrzebny jest szablon Group.Unified
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj ustawienie AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Następnie zastosuj ustawienie:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Wartości można odczytać za pomocą:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Ustawienia odczytu na poziomie katalogu

Jeśli znasz nazwę ustawienia, które chcesz pobrać, możesz użyć poniższego polecenia cmdlet, aby pobrać bieżącą wartość ustawień. W tym przykładzie pobieramy wartość dla ustawienia o nazwie "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Te kroki odczytują ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup pakietu Office w katalogu.

1. Przeczytaj wszystkie istniejące ustawienia katalogu:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   To polecenie cmdlet zwraca listę wszystkich ustawień katalogu:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Przeczytaj wszystkie ustawienia dla określonej grupy:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Odczytuj wszystkie wartości ustawień katalogu określonego obiektu ustawień katalogu przy użyciu identyfikatora GUID ustawień:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   To polecenie cmdlet zwraca nazwy i wartości w tym obiekcie ustawień dla tej określonej grupy:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Usuwanie ustawień na poziomie katalogu
Ten krok powoduje usunięcie ustawień na poziomie katalogu, które dotyczą wszystkich grup pakietu Office w katalogu.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Tworzenie ustawień dla określonej grupy

1. Wyszukaj szablon ustawień o nazwie "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Pobieranie obiektu szablonu dla szablonu Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Utwórz nowy obiekt ustawień na podstawie szablonu:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ustaw ustawienie na wymaganą wartość:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Pobierz identyfikator grupy, do której chcesz zastosować to ustawienie:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Utwórz nowe ustawienie dla wymaganej grupy w katalogu:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Aby zweryfikować ustawienia, uruchom następujące polecenie:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Aktualizowanie ustawień dla określonej grupy
1. Pobierz identyfikator grupy, której ustawienie chcesz zaktualizować:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Pobierz ustawienie grupy:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Zaktualizuj ustawienie grupy zgodnie z potrzebami, np.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Następnie uzyskaj identyfikator ustawienia dla tej konkretnej grupy:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Otrzymasz odpowiedź podobną do tej:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Następnie można ustawić nową wartość dla tego ustawienia:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Można odczytać wartość ustawienia, aby upewnić się, że została ona poprawnie zaktualizowana:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Odwołanie do składni cmdlet
Więcej dokumentacji programu Azure Active Directory PowerShell można znaleźć w [poleceniach cmdlet usługi Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Dodatkowa lektura

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
