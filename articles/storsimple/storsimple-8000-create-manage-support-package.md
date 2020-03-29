---
title: Tworzenie pakietu pomocy technicznej serii StorSimple 8000
description: Dowiedz się, jak utworzyć, odszyfrować i edytować pakiet pomocy technicznej dla urządzenia z serii StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 9ca033f6f786c0142261dafa31b93b71a8b3336a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277069"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Tworzenie pakietu pomocy technicznej dla serii StorSimple 8000 i zarządzanie nim

## <a name="overview"></a>Omówienie

Pakiet pomocy technicznej StorSimple to łatwy w użyciu mechanizm, który zbiera wszystkie odpowiednie dzienniki, aby pomóc pomocy technicznej firmy Microsoft w rozwiązywaniu problemów z urządzeniem StorSimple. Zebrane dzienniki są szyfrowane i kompresowane.

Ten samouczek zawiera instrukcje krok po kroku, aby utworzyć pakiet pomocy technicznej dla urządzenia z serii StorSimple serii 8000 i zarządzać nim. Jeśli pracujesz z StorSimple Virtual Array, przejdź do [generowania pakietu dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

W niektórych przypadkach należy ręcznie utworzyć pakiet pomocy technicznej za pośrednictwem programu Windows PowerShell dla StorSimple. Przykład:

* Jeśli chcesz usunąć poufne informacje z plików dziennika przed udostępnieniem ich w pomocy technicznej firmy Microsoft.
* Jeśli masz problemy z przesłaniem pakietu z powodu problemów z łącznością.

Ręcznie wygenerowany pakiet pomocy technicznej można udostępnić za pośrednictwem poczty e-mail pomocy technicznej firmy Microsoft. Wykonaj następujące kroki, aby utworzyć pakiet pomocy technicznej w programie Windows PowerShell for StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Aby utworzyć pakiet pomocy technicznej w programie Windows PowerShell for StorSimple

1. Aby uruchomić sesję programu Windows PowerShell jako administrator na komputerze zdalnym używanym do łączenia się z urządzeniem StorSimple, wprowadź następujące polecenie:
   
    `Start PowerShell`
2. W sesji programu Windows PowerShell połącz się z konsolą SSAdmin urządzenia:
   
   1. W wierszu polecenia wprowadź:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. W oknie dialogowym, które zostanie otwarte, wprowadź hasło administratora urządzenia. Domyślnym hasłem jest _Password1_.
     
      ![Okno dialogowe Poświadczenia programu PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Kliknij przycisk **OK**.
   4. W wierszu polecenia wprowadź:
     
      `Enter-PSSession $MS`
3. W sesji, która zostanie otwarta, wprowadź odpowiednie polecenie.
   
   * W przypadku udziałów sieciowych chronionych hasłem należy wprowadzić:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Zostanie wyświetlony monit o podanie hasła i hasła szyfrowania (ponieważ pakiet pomocy technicznej jest zaszyfrowany). Pakiet pomocy technicznej jest następnie tworzony w folderze domyślnym (nazwa urządzenia dołączana z bieżącą datą i godziną).
   * W przypadku udziałów, które nie są `-Credential` chronione hasłem, parametr nie jest potrzebny. Wprowadź następujące dane:
     
       `Export-HcsSupportPackage`
     
       Pakiet pomocy technicznej jest tworzony dla obu kontrolerów w folderze domyślnym. Pakiet jest zaszyfrowanym, skompresowanym plikiem, który można wysłać do pomocy technicznej firmy Microsoft w celu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Kontakt z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry polecenia cmdlet Export-HcsSupportPackage

Za pomocą polecenia cmdlet Export-HcsSupportPackage można użyć następujących parametrów.

| Parametr | Wymagane/Opcjonalne | Opis |
| --- | --- | --- |
| `-Path` |Wymagany |Służy do podawania lokalizacji folderu udostępnionego sieci, w którym jest umieszczany pakiet pomocy technicznej. |
| `-EncryptionPassphrase` |Wymagany |Służy do zapewnienia hasła, aby pomóc zaszyfrować pakiet pomocy technicznej. |
| `-Credential` |Optional (Opcjonalność) |Służy do poświadczania poświadczeń dostępu dla folderu udostępnionego w sieci. |
| `-Force` |Optional (Opcjonalność) |Służy do pomijania kroku potwierdzenia hasła szyfrowania. |
| `-PackageTag` |Optional (Opcjonalność) |Służy do określania katalogu w obszarze *Ścieżka,* w którym jest umieszczany pakiet pomocy technicznej. Wartość domyślna to [nazwa urządzenia]-[bieżąca data i godzina:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optional (Opcjonalność) |Określ jako **klaster** (domyślnie), aby utworzyć pakiet pomocy technicznej dla obu kontrolerów. Jeśli chcesz utworzyć pakiet tylko dla bieżącego kontrolera, określ **controller**. |

## <a name="edit-a-support-package"></a>Edytowanie pakietu pomocy technicznej

Po wygenerowaniu pakietu pomocy technicznej może być konieczne edytowanie pakietu w celu usunięcia poufnych informacji. Może to obejmować nazwy woluminów, adresy IP urządzeń i nazwy kopii zapasowych z plików dziennika.

> [!IMPORTANT]
> Można edytować tylko pakiet pomocy technicznej, który został wygenerowany za pośrednictwem programu Windows PowerShell dla StorSimple. Nie można edytować pakietu utworzonego w witrynie Azure portal za pomocą usługi StorSimple Device Manager.

Aby edytować pakiet pomocy technicznej przed przekazaniem go w witrynie pomocy technicznej firmy Microsoft, należy najpierw odszyfrować pakiet pomocy technicznej, edytować pliki, a następnie ponownie zaszyfrować. Wykonaj poniższe kroki.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Aby edytować pakiet pomocy technicznej w programie Windows PowerShell for StorSimple

1. Generowanie pakietu pomocy technicznej zgodnie z wcześniejszym opisem w [programie Aby utworzyć pakiet pomocy technicznej w programie Windows PowerShell for StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Pobierz skrypt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na swoim kliencie.
3. Importowanie modułu programu Windows PowerShell. Określ ścieżkę do folderu lokalnego, w którym pobrano skrypt. Aby zaimportować moduł, wprowadź:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Wszystkie pliki są *plikami .aes,* które są skompresowane i zaszyfrowane. Aby rozpakować i odszyfrować pliki, wprowadź:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Należy zauważyć, że rzeczywiste rozszerzenia plików są teraz wyświetlane dla wszystkich plików.
   
    ![Edytuj pakiet pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Po wyświetleniu monitu o hasło szyfrowania wprowadź hasło używane podczas tworzenia pakietu pomocy technicznej.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Przejdź do folderu zawierającego pliki dziennika. Ponieważ pliki dziennika są teraz dekompresowane i odszyfrowane, będą one miały oryginalne rozszerzenia plików. Zmodyfikuj te pliki, aby usunąć wszelkie informacje specyficzne dla klienta, takie jak nazwy woluminów i adresy IP urządzenia, i zapisz pliki.
7. Zamknij pliki, aby skompresować je za pomocą gzip i zaszyfrować za pomocą AES-256. Ma to na celu szybkość i bezpieczeństwo w przesyłaniu pakietu pomocy technicznej przez sieć. Aby skompresować i zaszyfrować pliki, wprowadź następujące elementy:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Edytuj pakiet pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po wyświetleniu monitu podaj hasło szyfrowania zmodyfikowanego pakietu pomocy technicznej.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Zapisz nowe hasło, aby udostępnić je pomocy technicznej firmy Microsoft na żądanie.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Przykład: Edytowanie plików w pakiecie pomocy technicznej w udziale chronionym hasłem

W poniższym przykładzie pokazano, jak odszyfrować, edytować i ponownie zaszyfrować pakiet pomocy technicznej.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [informacjach zebranych w pakiecie pomocy technicznej](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Dowiedz się, jak [używać pakietów pomocy technicznej i dzienników urządzeń do rozwiązywania problemów z wdrażaniem urządzenia](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

