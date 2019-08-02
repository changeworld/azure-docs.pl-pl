---
title: Instalowanie udziału plików platformy Azure za pomocą protokołu SMB w systemie macOS | Microsoft Docs
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pomocą protokołu SMB w systemie macOS.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bd696ce5a314b0c849256311d0629b917036ea2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699550"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Instalowanie udziału plików platformy Azure za pomocą protokołu SMB w systemie macOS
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować przy użyciu standardowego w branży protokołu SMB 3 w systemie macOS El Capitan w wersji 10.11 lub nowszej. W tym artykule przedstawiono dwa różne sposoby instalowania udziału plików platformy Azure w systemie macOS: za pomocą interfejsu użytkownika programu Finder i Terminalu.

> [!Note]  
> Przed instalowaniem udziału plików platformy Azure przy użyciu protokołu SMB zaleca się wyłączenie podpisywanie pakietów SMB. Niezastosowanie się do tego zalecenia może spowodować zmniejszenie wydajności podczas uzyskiwania dostępu do udziału plików platformy Azure z systemu macOS. Połączenie SMB będzie szyfrowane, więc nie zostanie obniżone bezpieczeństwo Twojego połączenia. Następujące polecenia wykonane z poziomu terminala spowodują wyłączenie podpisywania pakietów SMB, jak opisano w tym [artykule pomocy technicznej firmy Apple dotyczącym wyłączania podpisywania pakietów SMB](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Wymagania wstępne dotyczące instalowania udziału plików platformy Azure w systemie macOS
* **Nazwa konta magazynu**: aby zainstalować udział plików platformy Azure, konieczne będzie podanie nazwy konta magazynu.

* **Klucz konta magazynu**: aby zainstalować udział plików platformy Azure, konieczne będzie posiadanie podstawowego (lub dodatkowego) klucza magazynu. Klucze sygnatur dostępu współdzielonego nie są aktualnie obsługiwane na potrzeby instalowania.

* **Otwarty port 445**: Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Na komputerze klienckim (komputerze Mac) upewnij się, że zapora nie blokuje portu TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Instalowanie udziału plików platformy Azure za pomocą programu Finder
1. **Otwórz program Finder**: Program Finder jest domyślnie otwarty w systemie macOS, ale możesz się upewnić, że jest on aktualnie wybraną aplikacją, klikając „ikonę twarzy systemu macOS” w Docku:  
    ![Ikona twarzy systemu macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **W menu „Idź” wybierz pozycję „Połącz z serwerem”** : W ścieżce UNC z wymagań wstępnych przekształć początkowy podwójny ukośnik odwrotny (`\\`) w ciąg `smb://`, a wszystkie pozostałe ukośniki odwrotne (`\`) w ukośniki (`/`). Link powinien wyglądać następująco: ![Okno dialogowe „Łączenie z serwerem”](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Podaj nazwę konta magazynu i klucza konta magazynu w wyświetlonym monicie o podanie nazwy użytkownika i hasła**: Po kliknięciu przycisku „Połącz” w oknie dialogowym „Łączenie z serwerem” zostanie wyświetlony monit o podanie nazwy użytkownika i hasła (pole zostanie automatycznie wypełnione przy użyciu Twojej nazwy użytkownika systemu macOS). Istnieje możliwość umieszczenia nazwy konta magazynu / klucza konta magazynu w pęku kluczy systemu macOS.

4. **Użyj udziału plików platformy Azure zgodnie z potrzebami**: Udział zostanie zainstalowany po podstawieniu nazwy udziału oraz klucza konta magazynu w polach nazwy użytkownika i hasła. Udziału można używać tak samo jak lokalnego folderu / udziału plików. Obsługiwane jest też przeciąganie i upuszczanie plików do udziału plików:

    ![Migawka przedstawiająca zainstalowany udział plików platformy Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Instalowanie udziału plików platformy Azure za pomocą Terminalu
1. Zastąp zmienną  `<storage-account-name>`  nazwą swojego konta magazynu. Jeśli zostanie wyświetlony monit, podaj klucz konta magazynu jako hasło. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Użyj udziału plików platformy Azure zgodnie z potrzebami**: Udział plików platformy Azure zostanie zainstalowany w punkcie instalacji określonym w poprzednim poleceniu.  

    ![Migawka przedstawiająca zainstalowany udział plików platformy Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Kolejne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

* [Artykuł pomocy technicznej firmy Apple — Łączenie z funkcją Udostępnianie plików na komputerze Mac](https://support.apple.com/HT204445)
* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Rozwiązywanie problemów w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)    
