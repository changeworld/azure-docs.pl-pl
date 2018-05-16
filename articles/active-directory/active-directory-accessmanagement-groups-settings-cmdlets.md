---
title: Skonfiguruj ustawienia grupy w usłudze Azure Active Directory przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Jak zarządzać ustawieniami dla grup przy użyciu poleceń cmdlet usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 02/20/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: d61907ddbd6eaecc8aeae8d2f1cea204f287daea
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy
Ten artykuł zawiera instrukcje dotyczące używania poleceń cmdlet programu PowerShell usługi Azure Active Directory (Azure AD) do tworzenia i aktualizacji grupy. Ta zawartość dotyczy tylko w grupach usługi Office 365 (nazywane również ujednoliconego grup). 

> [!IMPORTANT]
> Niektóre ustawienia wymagają licencji usługi Azure Active Directory Premium P1. Aby uzyskać więcej informacji, zobacz [ustawienia szablonu](#template-settings) tabeli.

Aby uzyskać więcej informacji na temat sposobu uniemożliwić użytkownikom bez uprawnień administratora do utworzenia *zabezpieczeń* grup, ustawianie `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` zgodnie z opisem w [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Ustawienia grup usługi Office 365 są skonfigurowane przy użyciu obiektu ustawień i obiektu SettingsTemplate. Początkowo nie widać żadnych ustawień obiektów w danym katalogu, ponieważ katalog jest skonfigurowany przy użyciu ustawień domyślnych. Aby zmienić ustawienia domyślne, należy utworzyć nowy obiekt ustawień przy użyciu ustawienia szablonu. Ustawienia szablonów są definiowane przez firmę Microsoft. Istnieje kilka szablonów różne ustawienia. Aby skonfigurować ustawienia grupy usługi Office 365 dla katalogu, należy użyć szablonu o nazwie "Group.Unified". Aby skonfigurować ustawienia grupy usługi Office 365 na pojedynczej grupy, należy użyć szablonu o nazwie "Group.Unified.Guest". Ten szablon jest używany do zarządzania dostępem gościa do grupy usługi Office 365. 

Te polecenia cmdlet są częścią tego modułu Azure Active Directory programu PowerShell w wersji 2. Aby uzyskać instrukcje jak pobrać i zainstalować moduł programu na komputerze, zapoznaj się z artykułem [Azure Active Directory programu PowerShell w wersji 2](https://docs.microsoft.com/powershell/azuread/). Można zainstalować wersji w wersji 2 modułu z [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Pobierz wartość określonych ustawień
Jeśli znasz nazwę ustawienia, które ma zostać pobrane, możesz użyć poniżej polecenia cmdlet, aby pobrać bieżącą wartość ustawienia. W tym przykładzie mamy pobierania wartości ustawienia o nazwie "UsageGuidelinesUrl." Możesz zapoznać się, że więcej informacji na temat ustawień katalogu i nazw bardziej szczegółowo w dół w tym artykule.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Utwórz ustawienia na poziomie katalogu
Te kroki tworzenia ustawień na poziomie katalogu, które mają zastosowanie do wszystkich grup usługi Office 365 w katalogu.

1. W poleceniach cmdlet DirectorySettings Podaj identyfikator SettingsTemplate, którego chcesz użyć. Jeśli nie znasz tego Identyfikatora, to polecenie cmdlet zwraca listę wszystkich szablonów ustawienia:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  To wywołanie polecenia cmdlet zwraca wszystkie szablony, które są dostępne:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Aby dodać adres URL wskazówek dotyczących użycia, należy najpierw uzyskać obiektu SettingsTemplate, który definiuje wartość adresu URL wskazówek dotyczących użycia; oznacza to, że szablon Group.Unified:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Następnie należy utworzyć nowy obiekt ustawień na podstawie tego szablonu:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Następnie zaktualizuj wartość wskazówek dotyczących użycia:
  
  ```
  $setting["UsageGuidelinesUrl"] = "https://guideline.com"
  ```  
5. Na koniec Zastosuj ustawienia:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Po pomyślnym zakończeniu polecenie cmdlet zwraca identyfikator nowego obiektu ustawień:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Ustawienia szablonu
Poniżej przedstawiono ustawienia zdefiniowane w Group.Unified SettingsTemplate. O ile nie wskazano inaczej, te funkcje wymagają licencji usługi Azure Active Directory Premium P1. 

| **Ustawienie** | **Opis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: wartość logiczna<li>Domyślnie: True |Flaga wskazująca, czy tworzenie grupy usługi Office 365 jest dozwolone w katalogu przez użytkowników bez uprawnień administratora. To ustawienie nie wymaga licencji usługi Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: ciąg<li>Wartość domyślna: "" |Identyfikator GUID grupy zabezpieczeń, dla której członkowie mogą tworzyć grup usługi Office 365, nawet wtedy, gdy EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: ciąg<li>Wartość domyślna: "" |Link do wskazówek użycia grupy. |
|  <ul><li>ClassificationDescriptions<li>Typ: ciąg<li>Wartość domyślna: "" | Rozdzielana przecinkami lista Opisy klasyfikacji. |
|  <ul><li>DefaultClassification<li>Typ: ciąg<li>Wartość domyślna: "" | Klasyfikacja, która ma być używana jako domyślna Klasyfikacja grupy, jeśli żaden nie został określony.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: ciąg<li>Wartość domyślna: "" | Ciąg o maksymalnej długości 64 znaków, który definiuje konwencji nazewnictwa skonfigurowane dla grup usługi Office 365. Aby uzyskać więcej informacji, zobacz [wymusić zasady nazewnictwa dla grup usługi Office 365 (wersja zapoznawcza)](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: ciąg<li>Wartość domyślna: "" | Ciąg rozdzielony przecinkami wyrażeń, które użytkownicy mogą nie do użycia w grupie lub aliasy. Aby uzyskać więcej informacji, zobacz [wymusić zasady nazewnictwa dla grup usługi Office 365 (wersja zapoznawcza)](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: wartość logiczna<li>Wartość domyślna: "False" | Nie należy używać
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: wartość logiczna<li>Domyślnie: False | Wartość logiczna wskazująca, czy użytkownika gościa może być właścicielem grupy. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: wartość logiczna<li>Domyślnie: True | Wartość logiczna wskazująca, czy użytkownika gościa mogą mieć dostęp do zawartości grup usługi Office 365.  To ustawienie nie wymaga licencji usługi Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: ciąg<li>Wartość domyślna: "" | Adres url link do wskazówek użycia gościa. |
|  <ul><li>AllowToAddGuests<li>Typ: wartość logiczna<li>Domyślnie: True | Wartość logiczna wskazująca, czy można dodawać gości do tego katalogu.|
|  <ul><li>ClassificationList<li>Typ: ciąg<li>Wartość domyślna: "" |Rozdzielana przecinkami lista wartości prawidłowe klasyfikacji, które mogą być stosowane do grupy usługi Office 365. |

## <a name="read-settings-at-the-directory-level"></a>Odczytaj ustawienia na poziomie katalogu
Te kroki do odczytu na poziomie katalogu ustawień, które mają zastosowanie do wszystkich grup pakietu Office w katalogu.

1. Przeczytaj wszystkich istniejących ustawień katalogu:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  To polecenie cmdlet zwraca listę wszystkich ustawień katalogu:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Odczytywanie wszystkich ustawień dla określonej grupy:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Przeczytaj wszystkie wartości obiektu ustawienia określonego katalogu, za pomocą ustawienia identyfikatora GUID ustawień katalogu:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  To polecenie cmdlet zwraca nazwy i wartości w tym obiekcie ustawienia dla tej określonej grupie:
  ```
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
  UsageGuidelinesUrl            https://guideline.com
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Ustawienia aktualizacji dla określonej grupy

1. Wyszukaj ustawienia szablonu o nazwie "Groups.Unified.Guest"
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Pobierz obiekt szablonu dla szablonu Groups.Unified.Guest:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Utwórz nowy obiekt ustawień z szablonu:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Ustawienie wymagana wartość:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Utwórz nowe ustawienie do wymaganej grupy w katalogu:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Zaktualizuj ustawienia na poziomie katalogu

Te kroki zaktualizować ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup usługi Office 365 w katalogu. Tych przykładów założono, że istnieje już obiekt ustawień w katalogu.

1. Znajdź istniejący obiekt ustawień:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Zaktualizuj tę wartość:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Zaktualizuj ustawienia:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Usuń ustawienia na poziomie katalogu
Ten krok powoduje usunięcie ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup pakietu Office w katalogu.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Odwołania do składni polecenia cmdlet
Więcej dokumentacji programu PowerShell usługi Azure Active Directory można znaleźć [polecenia cmdlet usługi Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Dodatkowe materiały

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
