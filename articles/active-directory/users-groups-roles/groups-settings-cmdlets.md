---
title: Konfigurowanie ustawień grupy przy użyciu programu PowerShell — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zarządzać ustawieniami grup przy użyciu poleceń cmdlet usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/26/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c9b07e7524488d0336a55af6e1d5f36af59a870
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729819"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy
Ten artykuł zawiera instrukcje dotyczące korzystania z poleceń cmdlet programu PowerShell usługi Azure Active Directory (Azure AD) do tworzenia i aktualizowania grup. Ta zawartość dotyczy tylko do grup usługi Office 365 (czasami nazywany grupy ujednolicone). 

> [!IMPORTANT]
> Niektóre ustawienia wymagają licencji usługi Azure Active Directory Premium P1. Aby uzyskać więcej informacji, zobacz [ustawienia szablonu](#template-settings) tabeli.

Aby uzyskać więcej informacji na temat uniemożliwić użytkownikom niebędącym administratorami tworzenie grup zabezpieczeń, należy ustawić `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` zgodnie z opisem w [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Ustawienia grupy usługi Office 365 są skonfigurowane przy użyciu obiektu ustawień i SettingsTemplate obiektu. Początkowo nie widać żadnych ustawień obiektów w katalogu, ponieważ katalog jest skonfigurowany przy użyciu ustawień domyślnych. Aby zmienić ustawienia domyślne, należy utworzyć nowy obiekt ustawień przy użyciu ustawienia szablonu. Ustawienia szablonów są definiowane przez firmę Microsoft. Dostępnych jest kilka szablonów różne ustawienia. Aby skonfigurować ustawienia grupy usługi Office 365 dla katalogu, należy użyć szablonu o nazwie "Group.Unified". Aby skonfigurować ustawienia grupy usługi Office 365 na jedną grupę, szablon o nazwie "Group.Unified.Guest". Ten szablon służy do zarządzania dostępem gościa do grupy usługi Office 365. 

Polecenia cmdlet są częścią tego modułu usługi Azure Active Directory PowerShell w wersji 2. Aby uzyskać instrukcje jak pobrać i zainstalować moduł na komputerze, zapoznaj się z artykułem [usługi Azure Active Directory PowerShell w wersji 2](https://docs.microsoft.com/powershell/azuread/). Można zainstalować w wersji 2 wersję modułu na podstawie [galerii programu PowerShell](https://www.powershellgallery.com/packages/AzureAD/).



## <a name="create-settings-at-the-directory-level"></a>Utwórz ustawienia na poziomie katalogu
Te kroki służą utworzeniu ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup usługi Office 365 w katalogu. Polecenie cmdlet Get-AzureADDirectorySettingTemplate jest dostępna tylko w [modułu programu PowerShell usługi Azure AD w wersji zapoznawczej dla wykresu](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. W poleceniach cmdlet DirectorySettings należy określić identyfikator SettingsTemplate, którego chcesz użyć. Jeśli nie znasz tego Identyfikatora, to polecenie cmdlet zwraca listę wszystkich szablonów ustawienia:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   To wywołanie polecenia cmdlet zwraca wszystkie szablony, które są dostępne:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Aby dodać adres URL wskazówek dotyczących użycia, należy najpierw Pobierz obiekt SettingsTemplate, który definiuje wartość adresu URL wskazówkami dotyczącymi użycia; oznacza to, że szablon Group.Unified:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj wartość wskazówkami dotyczącymi użycia:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Następnie należy zastosować:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Można odczytać wartości za pomocą:

  ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Aktualizowanie ustawień na poziomie katalogu
Aby zaktualizować wartość dla UsageGuideLinesUrl w szablonie ustawienie, po prostu Edytuj adres URL z kroku 4 powyżej, a następnie wykonać kroku 5, aby ustawić nową wartość.

Aby usunąć wartość UsageGuideLinesUrl, Edytuj adres URL, aby być ciągiem pustym przy użyciu kroku 4 powyżej:

 ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Wykonać krok 5, aby ustawić nową wartość.

## <a name="template-settings"></a>Ustawienia szablonu
Poniżej przedstawiono ustawienia zdefiniowane w Group.Unified SettingsTemplate. O ile nie wskazano inaczej, te funkcje wymagają licencji usługi Azure Active Directory Premium P1. 

| **Ustawienie** | **Opis** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Wpisz: Boolean<li>Wartość domyślna: True |Flaga wskazująca, czy tworzenie grupy usługi Office 365 jest dozwolone w katalogu przez użytkowników bez uprawnień administratora. To ustawienie nie wymaga licencji usługi Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Wpisz: String<li>Wartość domyślna: "" |Identyfikator GUID grupy zabezpieczeń, dla której członkowie mogą tworzyć grupy usługi Office 365, nawet wtedy, gdy EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Wpisz: String<li>Wartość domyślna: "" |Łącze do wytyczne dotyczące grupowego użycia. |
|  <ul><li>ClassificationDescriptions<li>Wpisz: String<li>Wartość domyślna: "" | Rozdzielana przecinkami lista Opisy klasyfikacji. Wartość ClassificationDescriptions jest prawidłowy tylko w następującym formacie:<br>$setting[“ClassificationDescriptions”] ="Classification:Description,Classification:Description"<br>Jeśli klasyfikacja odpowiada ciągów w ClassificationList.|
|  <ul><li>DefaultClassification<li>Wpisz: String<li>Wartość domyślna: "" | Klasyfikacja, który ma być używana jako domyślna klasyfikacja dla grupy, jeśli żaden nie został określony.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Wpisz: String<li>Wartość domyślna: "" | Ciąg o maksymalnej długości 64 znaków, który definiuje konwencji nazewnictwa skonfigurowane dla grup usługi Office 365. Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup usługi Office 365](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Wpisz: String<li>Wartość domyślna: "" | Ciąg rozdzielony przecinkami wyrażeń, które użytkownicy mogą nie używać w grupie nazw ani aliasów. Aby uzyskać więcej informacji, zobacz [wymuszanie zasad nazewnictwa dla grup usługi Office 365](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Wpisz: Boolean<li>Wartość domyślna: "False" | Nie używaj
|  <ul><li>AllowGuestsToBeGroupOwner<li>Wpisz: Boolean<li>Wartość domyślna: False | Wartość logiczna wskazująca, czy użytkownik-Gość może być właścicielem grupy. |
|  <ul><li>AllowGuestsToAccessGroups<li>Wpisz: Boolean<li>Wartość domyślna: True | Wartość logiczna wskazująca, czy użytkownik-Gość może mieć dostęp do zawartości grup usługi Office 365.  To ustawienie nie wymaga licencji usługi Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Wpisz: String<li>Wartość domyślna: "" | Adres url linku z wytycznymi dotyczącymi użycia gościa. |
|  <ul><li>AllowToAddGuests<li>Wpisz: Boolean<li>Wartość domyślna: True | Wartość logiczna wskazująca, czy można dodawać gości do tego katalogu.|
|  <ul><li>ClassificationList<li>Wpisz: String<li>Wartość domyślna: "" |Rozdzielana przecinkami lista wartości prawidłowe klasyfikacji, które mogą być stosowane do grup usługi Office 365. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Przykład: Konfigurowanie zasad gościa dla grup na poziomie katalogu
1. Pobierz wszystkie szablony ustawienia:
  ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Aby ustawić zasady gościa dla grup na poziomie katalogu, należy Group.Unified szablonu
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Następnie utwórz nowy obiekt ustawień na podstawie tego szablonu:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Następnie zaktualizuj ustawienia AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Następnie należy zastosować:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Można odczytać wartości za pomocą:

  ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Odczytaj ustawienia na poziomie katalogu

Jeśli znasz nazwę ustawienia, które chcesz pobrać, możesz użyć poniższe polecenie cmdlet, aby pobrać bieżącą wartość ustawienia. W tym przykładzie firma Microsoft pobierania wartości ustawienia o nazwie "UsageGuidelinesUrl." 

  ```powershell
  (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
  ```
Te kroki odczytać ustawienia na poziomie katalogu, które mają zastosowanie do wszystkich grup Office w katalogu.

1. Odczyt wszystkich istniejących ustawień katalogu:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   To polecenie cmdlet zwraca listę wszystkich ustawień katalogu:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Odczyt wszystkich ustawień dla konkretnej grupy:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Odczyt wszystkich wartości ustawień katalogu określonego katalogu obiektu ustawień, za pomocą ustawienia Identyfikatora GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   To polecenie cmdlet zwraca nazwy i wartości w tym obiekcie ustawienia dla tej konkretnej grupy:
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

## <a name="remove-settings-at-the-directory-level"></a>Usuń ustawienia na poziomie katalogu
Ten krok spowoduje usunięcie ustawień na poziomie katalogu, które mają zastosowanie do wszystkich grup Office w katalogu.
  ```powershell
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="update-settings-for-a-specific-group"></a>Ustawienia aktualizacji dla określonej grupy

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
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Utwórz nowy obiekt ustawień z szablonu:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ustawienie wymagana wartość:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Utwórz nowe ustawienie do wymaganej grupy w katalogu:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $SettingCopy
   ```
6. Aby sprawdzić ustawienia, uruchom następujące polecenie:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Dokumentacja dotycząca składni poleceń cmdlet
Można znaleźć więcej dokumentacji usługi Azure Active Directory PowerShell w [poleceń cmdlet usługi Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Materiały uzupełniające

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
