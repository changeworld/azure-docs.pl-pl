---
title: Magazyn plików Azure instalacji z maszyny Wirtualnej systemu Windows Azure | Dokumentacja firmy Microsoft
description: Przechowywanie plików w chmurze za pomocą usługi Azure file storage i instalowanie udziału plików w chmurze z maszyny wirtualnej platformy Azure (VM).
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
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27577555"
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Udziały plików platformy Azure za pomocą maszyn wirtualnych systemu Windows 

Korzystać z udziałów plików na platformę Azure, jako sposób przechowywania i uzyskać dostęp do plików z maszyny Wirtualnej. Na przykład można przechowywać skryptu lub pliku konfiguracji aplikacji, które wszystkich maszyn wirtualnych do udziału. W tym artykule zostanie przedstawiony zostanie sposób tworzenia i instalowanie udziału plików na platformę Azure oraz sposób przekazywania i pobierania plików.

## <a name="connect-to-a-file-share-from-a-vm"></a>Połączenia z udziałem plików z maszyny Wirtualnej

W tej sekcji założono, że masz już udział plików, który chcesz nawiązać połączenie. Jeśli potrzebujesz go utworzyć, zobacz [utworzyć udział plików](#create-a-file-share) dalszej części tego artykułu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **kont magazynu**.
3. Wybierz konto magazynu.
4. W **omówienie** w obszarze **usług**, wybierz pozycję **pliki**.
5. Wybierz udział plików lub kliknij przycisk **+ udział pliku** do tworzenia nowego udziału plików do użycia.
6. Kliknij przycisk **Connect** aby otworzyć stronę przedstawiono składnię wiersza polecenia instalowanie udziału plików z systemem Windows lub Linux.
7. W **literę dysku**, wybierz list, którego chcesz użyć, aby zidentyfikować dysku.
8. Wybierz, które składni i kliknij przycisk Kopiuj po prawej stronie, aby skopiować go do Schowka. Wklej miejscu, w którym można z łatwością. 
8. Połącz się z maszyną Wirtualną i otwórz wiersz polecenia.
9. Wklej w składni edytowanych połączenia i trafień **Enter**.
10. Po utworzeniu połączenia jest wyświetlany komunikat **polecenie zostało wykonane pomyślnie.**
11. Sprawdzanie połączenia, wpisując w literę dysku, aby przełączyć się do tego dysku, a następnie wpisz **dir** Aby wyświetlić zawartość udziału plików.



## <a name="create-a-file-share"></a>Tworzenie udziału plików 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **kont magazynu**.
3. Wybierz konto magazynu.
4. W **omówienie** w obszarze **usług**, wybierz pozycję **pliki**.
5. Na stronie usługi plików, kliknij przycisk **+ udział pliku**.
6. Wprowadź nazwę udziału plików. Nazwy udziałów plików można użyć, małe litery, cyfry i łączniki pojedynczego. Nazwa nie może rozpoczynać się od łącznika i nie można używać wielu łączników obok siebie. 
7. Wprowadź limit na rozmiar, jaki może to być plik, do 5120 GB.
8. Kliknij przycisk **OK** można utworzyć udziału plików.
   
## <a name="upload-files"></a>Przekazywanie plików
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **kont magazynu**.
3. Wybierz konto magazynu.
4. W **omówienie** w obszarze **usług**, wybierz pozycję **pliki**.
5. Wybierz udział plików.
6. Kliknij przycisk **przekazać** otworzyć **przekazać pliki** strony.
7. Kliknij ikonę do przeglądania lokalnego systemu plików dla pliku do przekazania.   
8. Kliknij przycisk **przekazać** można przekazać pliku do udziału plików.

## <a name="download-files"></a>Pobieranie plików
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu po lewej stronie kliknij **kont magazynu**.
3. Wybierz konto magazynu.
4. W **omówienie** w obszarze **usług**, wybierz pozycję **pliki**.
5. Wybierz udział plików.
6. Kliknij prawym przyciskiem myszy plik i wybierz polecenie **Pobierz** można pobrać go na komputerze lokalnym.
   

## <a name="next-steps"></a>Kolejne kroki

Można również tworzyć i Zarządzanie udziałami plików przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z magazynem plików Azure w systemie Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
