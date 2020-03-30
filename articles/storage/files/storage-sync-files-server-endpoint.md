---
title: Dodawanie/usuwanie punktu końcowego serwera synchronizacji plików platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255108"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Dodawanie/usuwanie punktu końcowego serwera synchronizacji plików platformy Azure
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. W tym celu przekształcając serwery systemu Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

*Punkt końcowy serwera* reprezentuje określoną lokalizację na *zarejestrowanym serwerze,* taką jak folder na woluminie serwera lub katalog główny woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli ich przestrzenie nazw nie nakładają się na siebie (na przykład F:\sync1 i F:\sync2). Zasady warstw w chmurze można skonfigurować indywidualnie dla każdego punktu końcowego serwera. Jeśli dodasz lokalizację serwera z istniejącym zestawem plików jako punkt końcowy serwera do grupy synchronizacji, pliki te zostaną scalone z innymi plikami już w innych punktach końcowych w grupie synchronizacji.

Zobacz [Jak wdrożyć synchronizację plików platformy Azure,](storage-sync-files-deployment-guide.md) aby uzyskać informacje na temat wdrażania kompleksowej synchronizacji plików platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć punkt końcowy serwera, należy najpierw upewnić się, że spełnione są następujące kryteria: 
- Serwer ma agenta usługi Azure File Sync zainstalowany i został zarejestrowany. Instrukcje dotyczące instalowania agenta synchronizacji plików platformy Azure można znaleźć w artykule [Zarejestruj/wyrejestrować serwer za pomocą usługi Azure File Sync.](storage-sync-files-server-registration.md) 
- Upewnij się, że usługa synchronizacji magazynu została wdrożona. Zobacz [Jak wdrożyć synchronizację plików platformy Azure, aby](storage-sync-files-deployment-guide.md) uzyskać szczegółowe informacje na temat wdrażania usługi synchronizacji magazynu. 
- Upewnij się, że grupa synchronizacji została wdrożona. Dowiedz się, jak [utworzyć grupę synchronizacji](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Upewnij się, że serwer jest połączony z Internetem i że platforma Azure jest dostępna. Używamy portu 443 do komunikacji między serwerem a naszą usługą.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Aby dodać punkt końcowy serwera, przejdź do żądanej grupy synchronizacji i wybierz "Dodaj punkt końcowy serwera".

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

W obszarze **Dodaj punkt końcowy serwera**wymagane są następujące informacje:

- **Zarejestrowany serwer**: Nazwa serwera lub klastra do utworzenia punktu końcowego serwera.
- **Ścieżka:** Ścieżka w systemie Windows Server, która ma być zsynchronizowana jako część grupy synchronizacji.
- **Warstwowa chmura:** przełącznik, aby włączyć lub wyłączyć warstwy w chmurze. Po włączeniu warstwowe chmury będzie *warstwy* plików do udziałów plików platformy Azure. Spowoduje to konwersję lokalnych udziałów plików w pamięci podręcznej, a nie w pełną kopię zestawu danych, aby ułatwić zarządzanie wydajnością miejsca na serwerze.
- **Wolne miejsce na wolumin:** ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce na wolumin jest ustawiona na 50% na woluminie z jednym punktem końcowym serwera, mniej więcej połowa ilości danych będzie warstwowa do usługi Azure Files. Niezależnie od tego, czy warstwa w chmurze jest włączona, twój udział plików platformy Azure zawsze ma pełną kopię danych w grupie synchronizacji.

Wybierz **pozycję Utwórz,** aby dodać punkt końcowy serwera. Pliki w obszarze nazw grupy synchronizacji będą teraz synchronizowane. 

## <a name="remove-a-server-endpoint"></a>Usuwanie punktu końcowego serwera
Jeśli chcesz zaprzestać używania usługi Azure File Sync dla danego punktu końcowego serwera, możesz usunąć punkt końcowy serwera. 

> [!Warning]  
> Nie próbuj rozwiązywać problemów z synchronizacją, warstwą w chmurze lub innym aspektem synchronizacji plików platformy Azure, usuwając i odtwarzając punkt końcowy serwera, chyba że zostanie to wyraźnie poinstruowane przez inżyniera firmy Microsoft. Usunięcie punktu końcowego serwera jest operacją destrukcyjną, a pliki warstwowe w punkcie końcowym serwera nie zostaną "ponownie połączone" z ich lokalizacjami w udziale plików platformy Azure po odtworzeniu punktu końcowego serwera, co spowoduje błędy synchronizacji. Należy również zauważyć, warstwowe pliki, które istnieją poza obszarem nazw punktu końcowego serwera mogą zostać trwale utracone. Pliki warstwowe mogą istnieć w punkcie końcowym serwera, nawet jeśli warstwa w chmurze nigdy nie została włączona.

Aby upewnić się, że wszystkie pliki warstwowe są przywoływani przed usunięciem punktu końcowego serwera, należy wyłączyć warstwę chmury w punkcie końcowym serwera, a następnie wykonać następujące polecenie cmdlet programu PowerShell, aby odwołać wszystkie pliki warstwowe w obszarze nazw punktu końcowego serwera:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Określenie `-Order CloudTieringPolicy` spowoduje przywołanie ostatnio zmodyfikowanych plików.
Inne opcjonalne, ale przydatne parametry do rozważenia to:
* `-ThreadCount`określa, ile plików można przywoływania równolegle.
* `-PerFileRetryCount`określa, jak często będzie podejmowana próba wycofania pliku, który jest aktualnie zablokowany.
* `-PerFileRetryDelaySeconds`określa czas w sekundach między ponowną próbą odwołania i zawsze powinien być używany w połączeniu z poprzednim parametrem.

> [!Note]  
> Jeśli wolumin lokalny obsługujący serwer nie ma wystarczającej ilości wolnego `Invoke-StorageSyncFileRecall` miejsca, aby odwołać wszystkie dane warstwowe, polecenie cmdlet kończy się niepowodzeniem.  

Aby usunąć punkt końcowy serwera:

1. Przejdź do usługi synchronizacji magazynu, w której jest zarejestrowany serwer.
2. Przejdź do żądanej grupy synchronizacji.
3. Usuń punkt końcowy serwera, którego potrzebujesz w grupie synchronizacji w usłudze synchronizacji magazynu. Można to osiągnąć, klikając prawym przyciskiem myszy odpowiedni punkt końcowy serwera w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Następne kroki
- [Rejestrowanie/wyrejestrowywalnia serwera za pomocą usługi Azure File Sync](storage-sync-files-server-registration.md)
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
