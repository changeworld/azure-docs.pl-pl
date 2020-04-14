---
title: Tworzenie kontenera profilu FSLogix Usługi domenowe Usługi domenowe Active Directory — Azure
description: W tym artykule opisano sposób tworzenia kontenera profilu FSLogix za pomocą plików azure i usług domenowych Active Directory platformy Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dd01b950435fadb96a961b6bb1c6b28ff436907a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265774"
---
# <a name="create-an-fslogix-profile-container-with-azure-files"></a>Tworzenie kontenera profilu FSLogix za pomocą usługi Azure Files

W tym artykule pokazano, jak utworzyć kontener profilu FSLogix za pomocą plików Azure i usług domenowych Active Directory platformy Azure (AD DS).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że zostało już skonfigurowane wystąpienie usług Azure AD DS. Jeśli jeszcze go nie masz, postępuj zgodnie z instrukcjami w [obszarze Utwórz podstawową domenę zarządzaną,](../active-directory-domain-services/tutorial-create-instance.md) a następnie wróć tutaj.

## <a name="add-azure-ad-ds-admins"></a>Dodawanie administratorów usług Ad DS

Aby dodać dodatkowych administratorów, należy utworzyć nowego użytkownika i przyznać im uprawnienia.

Aby dodać administratora:

1. Wybierz **pozycję Azure Active Directory** z paska bocznego, a następnie wybierz pozycję Wszyscy **użytkownicy**, a następnie wybierz pozycję **Nowy użytkownik**.

2.  Wprowadź szczegóły użytkownika w polach.

3. W okienku usługi Azure Active Directory po lewej stronie ekranu wybierz pozycję **Grupy**.

4. Wybierz grupę **Administratorzy kontrolera domeny usługi AAD.**

5. W lewym okienku wybierz pozycję **Członkowie**, a następnie wybierz pozycję **Dodaj członków** w głównym okienku. Spowoduje to wyświetlenia listy wszystkich użytkowników dostępnych w usłudze Azure AD. Wybierz nazwę utworzonego właśnie profilu użytkownika.

## <a name="set-up-an-azure-storage-account"></a>Konfigurowanie konta usługi Azure Storage

Teraz nadszedł czas, aby włączyć uwierzytelnianie usług Ad DS za pomocą bloku komunikatów serwera (SMB). 

Aby włączyć uwierzytelnianie:

1. Jeśli jeszcze tego nie zrobiłeś, skonfiguruj i wdrażaj ogólne konto usługi Azure Storage w wersji 2, postępując zgodnie z instrukcjami w [obszarze Tworzenie konta usługi Azure Storage.](../storage/common/storage-account-create.md)

2. Po zakończeniu konfigurowania konta wybierz pozycję **Przejdź do zasobu**.

3. Wybierz **opcję Konfiguracja** z okienka po lewej stronie ekranu, a następnie włącz **uwierzytelnianie usługi Azure Active Directory dla plików platformy Azure** w głównym okienku. Po zakończeniu wybierz pozycję **Zapisz**.

4. Wybierz **pozycję Przegląd** w okienku po lewej stronie ekranu, a następnie wybierz pozycję **Pliki** w głównym okienku.

5. Wybierz **pozycję Udział plików** i wprowadź **nazwę** i **przydział** w polach wyświetlanych po prawej stronie ekranu.

## <a name="assign-access-permissions-to-an-identity"></a>Przypisywanie uprawnień dostępu do tożsamości

Inni użytkownicy będą potrzebować uprawnień dostępu, aby uzyskać dostęp do udziału plików. Aby to zrobić, musisz przypisać każdemu użytkownikowi rolę z odpowiednimi uprawnieniami dostępu.

Aby przypisać użytkownikom uprawnienia dostępu:

1. W witrynie Azure Portal otwórz utworzony w witrynie [Konfigurowanie konta usługi Azure Storage](#set-up-an-azure-storage-account).

2. Wybierz **pozycję Kontrola dostępu (IAM)**.

3. Wybierz **pozycję Dodaj przypisanie roli**.

4. Na karcie **Dodawanie przypisania roli** wybierz odpowiednią wbudowaną rolę z listy ról. Aby uzyskać odpowiednie uprawnienia, musisz przynajmniej wybrać **współautora udziału w danych magazynu SMB** dla konta.

5. Aby **przypisać dostęp do**, wybierz **użytkownika, grupę lub jednostkę usługi Azure Active Directory**.

6. Wybierz nazwę lub adres e-mail docelowej tożsamości usługi Azure Active Directory.

7. Wybierz **pozycję Zapisz**.

## <a name="get-the-storage-account-access-key"></a>Uzyskiwanie klucza dostępu do konta magazynu

Następnie musisz uzyskać klucz dostępu do swojego konta magazynu.

Aby uzyskać klucz dostępu do konta magazynu:

1. Na pasku bocznym portalu Azure wybierz pozycję **Konta magazynu**.

2. Z listy kont magazynu wybierz konto, dla którego włączono usługi Azure AD DS i utworzono role niestandardowe w powyższych krokach.

3. W obszarze **Ustawienia**wybierz pozycję **Klawisze programu Access** i skopiuj klucz z **key1**.

4. Przejdź do karty **Maszyny wirtualne** i znajdź dowolną maszynę wirtualną, która stanie się częścią puli hostów.

5. Wybierz nazwę maszyny wirtualnej (VM) w obszarze **Maszyny wirtualne (adVM)** i wybierz **pozycję Połącz**

    Spowoduje to pobranie pliku RDP, który pozwoli Ci zalogować się do maszyny Wirtualnej przy użyciu własnych poświadczeń.

    ![Zrzut ekranu przedstawiający kartę RDP w oknie Połącz z maszyną wirtualną.](media/rdp-tab.png)

6. Po zalogowaniu się do maszyny Wirtualnej uruchom wiersz polecenia jako administrator.

7. Uruchom następujące polecenie:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
     ```

    - Wymień `<desired-drive-letter>` na wybraną literę dysku (na `y:`przykład).
    - Zastąp `<storage-account-name>` wszystkie wystąpienia nazwą konta magazynu określonego wcześniej.
    - Zamień `<share-name>` na nazwę utworzonego wcześniej udziału.
    - Zamień `<storage-account-key>` klucz konta magazynu z platformy Azure.

    Przykład:  
  
     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Uruchom następujące polecenie, aby udzielić użytkownikowi pełnego dostępu do udziału usługi Azure Files.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(f)
     ```

    - Zamień `<mounted-drive-letter>` ją literą dysku, z którego ma korzystać użytkownik.
    - Zamień `<user-email>` ją na nazwy UPN użytkownika, który będzie używał tego profilu do uzyskiwania dostępu do maszyn wirtualnych hosta sesji.

    Przykład:
     
     ```cmd
     icacls y: /grant john.doe@contoso.com:(f)
     ```

## <a name="create-a-profile-container"></a>Tworzenie kontenera profilu

Teraz, gdy Twoje profile są gotowe do pracy, utwórzmy kontener profilu FSLogix.

Aby skonfigurować kontener profilu FSLogix:

1. Zaloguj się do maszyny wirtualnej hosta sesji skonfigurowanej na początku tego artykułu, a następnie [pobierz i zainstaluj agenta FSLogix](/fslogix/install-ht/).

2. Rozpakuj pobrany plik agenta FSLogix i przejdź do**wersji** **x64,** > a następnie otwórz **plik FSLogixAppsSetup.exe**.

3. Po uruchomieniu instalatora, wybierz **zgadzam się z warunkami licencji.** Jeśli dotyczy, podaj nowy klucz.

4. Wybierz pozycję **Zainstaluj**.

5. Otwórz **dysk C**, a następnie przejdź do Program **Files** > **FSLogix** > **Apps,** aby upewnić się, że agent FSLogix został poprawnie zainstalowany.

     >[!NOTE]
     > Jeśli w puli hostów znajduje się wiele maszyn wirtualnych, należy powtórzyć kroki od 1 do 5 dla każdej maszyny Wirtualnej.

6. Uruchom **Edytor rejestru** (RegEdit) jako administrator.

7. Przejdź do **aplikacji Komputer** > **HKEY_LOCAL_MACHINE** > **FSLogix****software** > , kliknij prawym przyciskiem myszy **na FSLogix**, wybierz pozycję **Nowy**, a następnie wybierz **klawisz**.

8. Utwórz nowy klucz o nazwie **Profile**.

9.  Kliknij prawym przyciskiem myszy **pozycję Profile**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **DWORD (32-bitowa) Wartość.** Nazwij wartość **Włączono** i ustaw wartość **Data** na **1**.

    ![Zrzut ekranu przedstawiający klucz Profiles. Wyróżniony REG_DWORD plik, a jego wartość Data ustawiona jest na 1.](media/dword-value.png)

10. Kliknij prawym przyciskiem myszy **pozycję Profile**, wybierz pozycję **Nowy**, a następnie wybierz pozycję **Wartość wielostrunowa**. Nazwij wartość **VHDLocations** i ustaw wprowadź identyfikator `\\fsprofile.file.core.windows.net\share` URI dla udziału usługi Azure Files jako wartość danych.

    ![Zrzut ekranu przedstawiający klucz Profiles przedstawiający plik VHDLocations. Jego wartość Data pokazuje identyfikator URI dla udziału usługi Azure Files.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Przypisywanie użytkowników do hosta sesji

Teraz musisz przypisać użytkowników do hosta sesji.

Aby przypisać użytkowników:

1. Uruchom program Windows PowerShell jako administrator, a następnie uruchom następujące polecenie cmdlet, aby zalogować się do pulpitu wirtualnego systemu Windows za pomocą programu PowerShell:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Po wyświetleniu monitu o poświadczenia wprowadź tego samego użytkownika, któremu przyznano rolę TenantCreator, RDS Owner lub RDS Contributor w dzierżawie pulpitu wirtualnego systemu Windows.

2. Uruchom następujące polecenia cmdlet, aby przypisać użytkownika do grupy pulpitów zdalnych:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Podobnie jak wcześniejsze polecenia cmdlet, `<your-wvd-tenant>` `<wvd-pool>`należy `<user-principal>` zastąpić , i z odpowiednimi wartościami.

    Przykład:

     ```powershell
     $pool1 = "contoso"
     
     $tenant = "contoso"
     
     $appgroup = "Desktop Application Group"
     
     $user1 = "jane.doe@contoso.com"
     
     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Upewnij się, że Twój profil działa

Teraz wszystko, co musisz zrobić, to upewnić się, że utworzony profil istnieje i działa zgodnie z przeznaczeniem.

Aby zweryfikować swój profil:

1. Otwórz przeglądarkę i przejdź do [klienta sieci Web pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

2. Zaloguj się przy pomocy konta użytkownika przypisanego do grupy Pulpit zdalny.

3. Po ustanowieniu sesji użytkownika otwórz witrynę Azure portal i zaloguj się przy za pomocą konta administracyjnego.

4. Na pasku bocznym wybierz pozycję **Konta magazynu**.

5. Wybierz konto magazynu skonfigurowane jako udział plików dla puli hostów sesji i włączone za pomocą usług Azure AD DS.

6. Wybierz ikonę **Pliki,** a następnie rozwiń udział.

    Jeśli wszystko jest poprawnie skonfigurowane, powinien zostać wyświetlony **katalog** o sformatowanej w ten sposób: `<user SID>-<username>`.

## <a name="next-steps"></a>Następne kroki

Jeśli szukasz alternatywnych sposobów tworzenia kontenerów profilu FSLogix, zapoznaj się z następującymi artykułami:

- [Utwórz kontener profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md).
- [Tworzenie kontenera profilu FSLogix dla puli hostów przy użyciu plików NetApp platformy Azure](create-fslogix-profile-container.md)

Bardziej szczegółowe informacje na temat pojęć związanych z kontenerami FSlogix dla plików platformy Azure można znaleźć w [kontenerach profilu FSLogix i plikach platformy Azure.](fslogix-containers-azure-files.md)
