---
title: Instalowanie usługi Azure file storage w Maszynie wirtualnej platformy Azure Windows | Dokumentacja firmy Microsoft
description: Store plików w chmurze za pomocą usługi Azure file storage i instalowanie udziału plików w chmurze z maszyny wirtualnej (VM) platformy Azure.
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.subservice: files
ms.openlocfilehash: d79a2d33157d9e2a6f56eb18d061f71a7dcd635c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462243"
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Używanie udziałów plików platformy Azure z maszynami wirtualnymi Windows 

Korzystać z udziałów plików platformy Azure, jako sposób na przechowywanie i dostęp do plików z maszyny Wirtualnej. Na przykład można przechowywać skrypt lub plik konfiguracji aplikacji, które wszystkich maszyn wirtualnych do udostępniania. W tym artykule pokazujemy, jak utworzyć i zainstalować udział plików platformy Azure oraz sposób przekazywania i pobierania plików.

## <a name="connect-to-a-file-share-from-a-vm"></a>Łączenie z udziałem plików z maszyny Wirtualnej

W tej sekcji założono, że masz już udział plików, który chcesz się połączyć. Jeśli potrzebujesz go utworzyć, zobacz [Utwórz udział plików](#create-a-file-share) w dalszej części tego artykułu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie, kliknij polecenie **kont magazynu**.
3. Wybierz konto magazynu.
4. W **Przegląd** w obszarze **usług**, wybierz opcję **pliki**.
5. Wybierz udział plików lub kliknij przycisk **+ udział plików** do tworzenia nowego udziału plików do użycia.
6. Kliknij przycisk **Connect** aby otworzyć stronę, który pokazuje składni wiersza polecenia umożliwiający zainstalowanie udziału plików w Windows lub Linux.
7. W **literę**, wybierz literę, którego chcesz użyć, aby zidentyfikować na dysku.
8. Wybierz które składnię i wybierz przycisku kopiowania po prawej stronie, aby skopiować go do Schowka. Wklej niektóre miejsca, w którym można łatwo do niego dostęp. 
8. Łączenie z maszyną wirtualną, a następnie otwórz okno wiersza polecenia.
9. Wklej składni edycji połączenia, a następnie kliknij przycisk **Enter**.
10. Po utworzeniu połączenia zostanie wyświetlony komunikat o **polecenie zostało wykonane pomyślnie.**
11. Sprawdzanie połączenia, wpisując literę dysku, aby przełączyć się do tego dysku, a następnie wpisz **dir** Aby wyświetlić zawartość udziału plików.



## <a name="create-a-file-share"></a>Tworzenie udziału plików 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie, kliknij polecenie **kont magazynu**.
3. Wybierz konto magazynu.
4. W **Przegląd** w obszarze **usług**, wybierz opcję **pliki**.
5. Na stronie usługi plików, kliknij przycisk **+ udział plików**.
6. Wprowadź nazwę udziału plików. Nazwy udziałów plików można użyć, małe litery, cyfry i pojedyncze łączniki. Nazwa nie może zaczynać się łącznikiem i nie można użyć wielu następujących po sobie łączników. 
7. Wprowadź limit na rozmiar, jaki może to być plik, do 5120 GB.
8. Kliknij przycisk **OK** można utworzyć udziału plików.
   
## <a name="upload-files"></a>Przekazywanie plików
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie, kliknij polecenie **kont magazynu**.
3. Wybierz konto magazynu.
4. W **Przegląd** w obszarze **usług**, wybierz opcję **pliki**.
5. Wybierz udział plików.
6. Kliknij przycisk **przekazywanie** otworzyć **przekazywanie plików** strony.
7. Kliknij ikonę folderu i przejdź do lokalnego systemu plików dla pliku do przekazania.   
8. Kliknij przycisk **przekazywanie** można przekazać pliku do udziału plików.

## <a name="download-files"></a>Pobieranie plików
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie, kliknij polecenie **kont magazynu**.
3. Wybierz konto magazynu.
4. W **Przegląd** w obszarze **usług**, wybierz opcję **pliki**.
5. Wybierz udział plików.
6. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz** aby ją pobrać na komputer lokalny.
   

## <a name="next-steps"></a>Kolejne kroki

Można również tworzyć i zarządzać udziałami plików przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą Azure File storage w Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
