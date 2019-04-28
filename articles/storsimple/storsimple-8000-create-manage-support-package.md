---
title: Tworzenie pakietu dla pomocy technicznej serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć, odszyfrowywania i edytować pakietu dla pomocy technicznej dla danego urządzenia z serii StorSimple 8000.
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
ms.openlocfilehash: dfc2d8d763a1eb64a37af73e03992f2d948a6856
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481869"
---
# <a name="create-and-manage-a-support-package-for-storsimple-8000-series"></a>Tworzenie i zarządzanie nimi pakietu dla pomocy technicznej dla serii StorSimple 8000

## <a name="overview"></a>Omówienie

Pakiet dla pomocy technicznej usługi StorSimple jest mechanizm łatwy w użyciu, który gromadzi informacje o wszystkich dzienników istotnych uzyskanymi Microsoft Support rozwiązać wszelkie problemy z urządzeniem StorSimple. Zebrane dzienniki są zaszyfrowane i skompresowane.

Ten samouczek zawiera instrukcje krok po kroku, aby utworzyć i zarządzać pakietu dla pomocy technicznej dla danego urządzenia z serii StorSimple 8000. Jeśli pracujesz z rozwiązania StorSimple Virtual Array, przejdź do strony [wygenerować pakiet dziennika](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="create-a-support-package"></a>Tworzenie pakietu pomocy technicznej

W niektórych przypadkach należy ręcznie utworzyć pakietu dla pomocy technicznej za pośrednictwem programu Windows PowerShell dla usługi StorSimple. Na przykład:

* Jeśli musisz usunąć poufne informacje z plików dzienników przed udostępnianiem Microsoft Support.
* Jeśli występują trudności, przekazywanie pakietu ze względu na problemy z łącznością.

Możesz udostępniać pakietu ręcznie wygenerowanego pomocy technicznej firmy Microsoft Support za pośrednictwem poczty e-mail. Wykonaj poniższe kroki, aby utworzyć pakiet dla pomocy technicznej w programie Windows PowerShell dla usługi StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Aby utworzyć pakiet dla pomocy technicznej w programie Windows PowerShell dla usługi StorSimple

1. Aby rozpocząć sesję środowiska Windows PowerShell jako administrator na komputerze zdalnym, która umożliwia łączenie z urządzeniem StorSimple, wprowadź następujące polecenie:
   
    `Start PowerShell`
2. W sesji programu Windows PowerShell łączenie z konsolą SSAdmin urządzenia:
   
   1. W wierszu polecenia wprowadź:
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   2. W wyświetlonym oknie dialogowym Wprowadź hasło administratora urządzenia. Domyślne hasło jest _Password1_.
     
      ![Okno dialogowe poświadczenie programu PowerShell](./media/storsimple-8000-create-manage-support-package/IC740962.png)
   3. Kliknij przycisk **OK**.
   4. W wierszu polecenia wprowadź:
     
      `Enter-PSSession $MS`
3. W otwartej sesji wprowadź odpowiednie polecenie.
   
   * Udziały sieciowe, które są chronione hasłem wpisz:
     
       `Export-HcsSupportPackage -Path <\\IP address\location of the shared folder> -Include Default -Credential domainname\username`
     
       Zostanie wyświetlony monit o hasła i hasło szyfrowania (ponieważ jest zaszyfrowany pakietu dla pomocy technicznej). Pakiet dla pomocy technicznej jest tworzona w folderze domyślnym (nazwa urządzenia dołączany wraz z bieżącą datę i godzinę).
   * Dla akcji, które nie są chronione hasłem, nie ma potrzeby `-Credential` parametru. Wprowadź następujące dane:
     
       `Export-HcsSupportPackage`
     
       Dla obu kontrolerów, w folderze domyślnym, tworzony jest pakiet dla pomocy technicznej. Pakiet jest zaszyfrowanych, skompresowanych plików, które mogą być wysyłane do firmy Microsoft Support do rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Parametry polecenia cmdlet polecenia Export-HcsSupportPackage

Można użyć następujących parametrów za pomocą polecenia cmdlet polecenia Export-HcsSupportPackage.

| Parametr | Wymagane/opcjonalne | Opis |
| --- | --- | --- |
| `-Path` |Wymagane |Użyj, aby wprowadzić lokalizację sieciowy folder udostępniony, w którym znajduje się pakiet dla pomocy technicznej. |
| `-EncryptionPassphrase` |Wymagane |Użyj, aby podać hasło w celu zaszyfrowania pakietu dla pomocy technicznej. |
| `-Credential` |Optional (Opcjonalność) |Użyj, aby podać poświadczenia dostępu do udostępnionego folderu sieciowego. |
| `-Force` |Optional (Opcjonalność) |Użyj, aby pominąć krok potwierdzenie hasła szyfrowania. |
| `-PackageTag` |Optional (Opcjonalność) |Użyj, aby określić katalog, w obszarze *ścieżki* w jest umieszczany pakietu dla pomocy technicznej. Wartość domyślna to [nazwa]-[bieżącą datę i time:yyyy-MM-dd-HH-mm-ss]. |
| `-Scope` |Optional (Opcjonalność) |Określ jako **klastra** (ustawienie domyślne), aby utworzyć pakiet dla pomocy technicznej dla obu kontrolerów. Jeśli chcesz utworzyć pakiet tylko dla bieżącego kontrolera, należy określić **kontrolera**. |

## <a name="edit-a-support-package"></a>Edytuj pakiet dla pomocy technicznej

Po wygenerowaniu pakietu dla pomocy technicznej, może być konieczne edytowanie pakiet do usunięcia informacji poufnych. Może to obejmować nazwy woluminów, adresy IP urządzeń i nazwy kopii zapasowej w plikach dziennika.

> [!IMPORTANT]
> Można edytować tylko pakiet pomocy technicznej, który został wygenerowany za pomocą programu Windows PowerShell dla usługi StorSimple. Nie można edytować pakiet utworzony w witrynie Azure portal, za pomocą usługi Menedżer urządzeń StorSimple.

Aby edytować pakiet dla pomocy technicznej przed przekazaniem go w witrynie Microsoft Support, najpierw odszyfrować pakietu dla pomocy technicznej, edytować pliki, a następnie ponownie go zaszyfrować. Wykonaj poniższe kroki.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Aby edytować pakiet dla pomocy technicznej w programie Windows PowerShell dla usługi StorSimple

1. Generowanie pakietu dla pomocy technicznej zgodnie z opisem we wcześniejszej części, [do utworzenia pakietu dla pomocy technicznej w programie Windows PowerShell dla usługi StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Pobierz skrypt](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Zaimportuj moduł programu Windows PowerShell. Określ ścieżkę do folderu lokalnego, w której pobrano skrypt. Aby zaimportować moduł, wpisz:
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Wszystkie pliki są *.aes* pliki, które są kompresowane i szyfrowane. Aby zdekompresować i odszyfrowuje pliki, wpisz:
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Należy pamiętać, rzeczywiste rozszerzeniami są teraz wyświetlane dla wszystkich plików.
   
    ![Edytuj pakiet dla pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750706.png)
5. Po wyświetleniu monitu o wpisanie hasła szyfrowania, wprowadź hasło, którego użyto podczas tworzenia pakietu dla pomocy technicznej.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Przejdź do folderu, który zawiera pliki dziennika. Ponieważ pliki dziennika są teraz dekompresja i odszyfrować, te będą mieć oryginalnego rozszerzenia pliku. Modyfikowanie tych plików, aby usunąć wszelkie informacje specyficzne dla klienta, takich jak wolumin nazwy i adresy IP urządzeń i Zapisz pliki.
7. Zamykanie plików, które mają być kompresowane je za pomocą narzędzia gzip i szyfrować je przy użyciu algorytmu AES-256. Jest to szybkość i najlepsze zabezpieczenia w przesłanie pakietu dla pomocy technicznej za pośrednictwem sieci. Do kompresowania i szyfrowania plików, wprowadź następujące informacje:
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Edytuj pakiet dla pomocy technicznej](./media/storsimple-8000-create-manage-support-package/IC750707.png)
8. Po wyświetleniu monitu podaj hasło szyfrowania dla pakietu dla pomocy technicznej modyfikacji.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Zapisz nowe hasło, aby udostępnić go innym Microsoft Support żądanie.

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Przykład: Edytowanie plików pakietu dla pomocy technicznej w udziale chronionym hasłem

Poniższy przykład pokazuje, jak można odszyfrować, edytować i ponownie zaszyfrować pakiet dla pomocy technicznej.

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

* Dowiedz się więcej o [informacje zebrane w pakietu dla pomocy technicznej](https://support.microsoft.com/help/3193606/storsimple-support-packages-and-device-logs)
* Dowiedz się, jak [Rozwiązywanie problemów z wdrożenia urządzenia przy użyciu pakietów pomocy technicznej i dzienniki urządzeń](storsimple-8000-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

