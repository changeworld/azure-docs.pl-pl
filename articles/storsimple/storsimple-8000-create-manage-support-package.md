---
title: Utwórz pakiet obsługi serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć, odszyfrowywania i edytować pakiet obsługi dla danego urządzenia z serii StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c83caae58e73d6f2d0fe086938e0bbc5cfdf80c4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743582"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Tworzenie i zarządzanie nimi pakiet obsługi serii StorSimple 8000

## <a name="overview"></a>Przegląd

Pakiet obsługi StorSimple jest mechanizm łatwy w użyciu, która gromadzi wszystkie dzienniki odpowiednich pomagające Microsoft Support żadnych problemów urządzenia StorSimple. Dzienniki zbierane są zaszyfrowane i skompresowane.

Ten samouczek zawiera instrukcje krok po kroku, aby utworzyć i zarządzać pakietu obsługi dla danego urządzenia z serii StorSimple 8000. Jeśli pracujesz z tablicą wirtualnego StorSimple, przejdź do [generowanie pakietu dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Utwórz pakiet pomocy technicznej

W niektórych przypadkach należy ręcznie utworzyć pakiet pomocy technicznej za pośrednictwem programu Windows PowerShell dla urządzenia StorSimple. Na przykład:

* Jeśli musisz usunąć poufne informacje z plików dziennika przed udostępnianie Support firmy Microsoft.
* Jeśli masz problemy przekazywanie pakietu z powodu problemów z łącznością.

Możesz udostępniać pakietu ręcznie wygenerowane pomocy technicznej firmy Microsoft Support za pośrednictwem poczty e-mail. Wykonaj poniższe kroki, aby utworzyć pakiet obsługi w programie Windows PowerShell dla StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Aby utworzyć pakiet pomocy technicznej w programie Windows PowerShell dla urządzenia StorSimple

1. Aby rozpocząć sesję programu Windows PowerShell jako administrator na komputerze zdalnym, który jest używany do nawiązania połączenia urządzenia StorSimple, wprowadź następujące polecenie:
   
    `Start PowerShell`
2. W sesji środowiska Windows PowerShell połączenie z konsolą SSAdmin urządzenia:
   
   1. W wierszu polecenia wpisz:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. W otwartym oknie dialogowym Wprowadź hasło administratora urządzenia. Domyślne hasło jest _Password1_.
     
      ![Okno dialogowe poświadczenie programu PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Kliknij przycisk **OK**.
   4. W wierszu polecenia wpisz:
     
      `Enter-PSSession $MS`
3. W sesji, który zostanie otwarty wprowadź odpowiednie polecenie.
   
   * Udziały sieciowe, które są chronione hasłem wpisz:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Zostanie wyświetlony monit o hasło oraz hasło szyfrowania (ponieważ pakietu obsługi jest zaszyfrowany). Pakiet obsługi jest tworzona w folderze domyślnym (nazwa urządzenia dołączony bieżącą datę i godzinę).
   * W przypadku udziałów, które nie są chronione hasłem, nie trzeba `-Credential` parametru. Wprowadź następujące informacje:
     
       `Export-HcsSupportPackage`
     
       Dla obu kontrolerów w domyślnym folderze zostaje utworzony pakiet pomocy technicznej. Pakiet jest zaszyfrowany, skompresowany plik, który mogą być wysyłane do firmy Microsoft Support do rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry polecenia cmdlet Export-HcsSupportPackage

Można używać następujących parametrów, za pomocą polecenia cmdlet Export-HcsSupportPackage.

| Parametr | Wymagane opcjonalne | Opis |
| --- | --- | --- |
| `-Path` |Wymagane |Użyj, aby podać lokalizację folderu udostępnionego sieci, w której znajduje się pakiet pomocy technicznej. |
| `-EncryptionPassphrase` |Wymagane |Użyj, aby podać hasło, aby zaszyfrować pakiet pomocy technicznej. |
| `-Credential` |Optional (Opcjonalność) |Użyj, aby podać poświadczenia dostępu do udostępnionego folderu sieciowego. |
| `-Force` |Optional (Opcjonalność) |Użyj, aby pominąć krok potwierdzenie hasła szyfrowania. |
| `-PackageTag` |Optional (Opcjonalność) |Użyj, aby określić katalog w *ścieżki* w znajduje się pakiet pomocy technicznej. Wartość domyślna to [nazwa]-[bieżącą datę i time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optional (Opcjonalność) |Określ jako **klastra** (ustawienie domyślne), aby utworzyć pakiet pomocy technicznej dla obu kontrolerów. Jeśli chcesz utworzyć pakiet tylko dla bieżącego kontrolera, określ **kontrolera**. |

## <a name="edit-a-support-package"></a>Edytuj pakiet pomocy technicznej

Po wygenerowaniu pakietu obsługi może być konieczne Edytuj pakiet, aby usunąć informacje poufne. Mogą to być nazwy woluminów, urządzenia adresów IP i nazwy kopii zapasowej z plików dziennika.

> [!IMPORTANT]
> Można edytować tylko pakietu obsługi, który został wygenerowany za pomocą programu Windows PowerShell dla StorSimple. Nie można edytować pakiet utworzony w portalu Azure w usłudze Menedżer StorSimple urządzenia.

Aby edytować pakiet obsługi przed przekazaniem go w witrynie Microsoft Support, najpierw odszyfrować pakiet pomocy technicznej, Edytuj pliki i następnie ponownie go zaszyfrować. Wykonaj poniższe kroki.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Aby edytować pakiet obsługi w programie Windows PowerShell dla urządzenia StorSimple

1. Generowanie pakietu obsługi, jak opisano wcześniej, w [do utworzenia pakietu obsługi w programie Windows PowerShell dla urządzenia StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Pobierz skrypt](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Zaimportuj moduł programu Windows PowerShell. Określ ścieżkę do folderu lokalnego, w której skrypt został pobrany. Aby zaimportować moduł, wpisz:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Wszystkie pliki są *.aes* pliki skompresowane i szyfrowane. Aby zdekompresować i odszyfrowuje pliki, wpisz:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Należy pamiętać, że rzeczywisty plik rozszerzenia są teraz wyświetlane dla wszystkich plików.
   
    ![Edytuj pakiet, pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Gdy zostanie wyświetlony monit o hasło szyfrowania, wprowadź hasło, które są używane podczas tworzenia pakietu obsługi.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Przejdź do folderu, który zawiera pliki dziennika. Ponieważ pliki dziennika są teraz zdekompresować i odszyfrować, te będą mieć oryginalnego rozszerzenia pliku. Zmodyfikuj te pliki, aby usunąć wszelkie informacje specyficzne dla klienta, takich jak nazwy woluminu i adresy IP urządzeń i zapisać pliki.
7. Zamknij pliki, aby kompresować gzip i szyfrowanie AES 256. Jest to szybkości i zabezpieczeń w przesyłania pakietu pomocy technicznej za pośrednictwem sieci. Podczas kompresji i szyfrowania plików, wprowadź następujące informacje:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Edytuj pakiet, pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po wyświetleniu monitu podaj hasło szyfrowania dla pakietu obsługi zmodyfikowane.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Zapisz nowe hasło, dzięki czemu można udostępniać Microsoft Support na żądanie.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Przykład: Edytowanie plików w pakiecie pomocy technicznej w udziale chronione hasłem

Poniższy przykład pokazuje, jak można odszyfrować, edytować i ponownie zaszyfrować pakiet obsługi.

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

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [informacje zebrane w pakietu obsługi](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Dowiedz się, jak [użycie pakietów pomocy technicznej i dzienniki urządzenia do rozwiązywania problemów z wdrożeniem urządzenia](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

