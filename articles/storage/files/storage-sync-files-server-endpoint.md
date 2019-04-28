---
title: Dodaj/Usuń punkt końcowy serwera usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 31bb71f016dd7f9dd37c766ece25caf8f300754b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766956"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Dodaj/Usuń punkt końcowy serwera usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Robi to poprzez przekształcenie serwerów Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

A *punkt końcowy serwera* reprezentuje konkretnej lokalizacji na *zarejestrowanego serwera*, takie jak folder na wolumin serwera lub w katalogu głównym woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli nakładania ich przestrzenie nazw (na przykład F:\sync1 i F:\sync2). Można skonfigurować zasad obsługi warstw chmury osobno dla każdego punktu końcowego serwera. Jeśli dodasz lokalizacji przy użyciu istniejącego zestawu plików jako punkt końcowy serwera z grupą synchronizacji te pliki zostaną scalone z innymi plikami, znajdujące się na inne punkty końcowe w grupie synchronizacji.

Zobacz [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md) informacji na temat wdrażania usługi Azure File Sync end-to-end.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć punkt końcowy serwera, najpierw upewnij się, że są spełnione poniższe kryteria: 
- Serwer został zainstalowany agent usługi Azure File Sync i został zarejestrowany. Instrukcje dotyczące instalowania agenta synchronizacji plików platformy Azure można znaleźć w [zarejestrować/wyrejestrować danego serwera za pomocą usługi Azure File Sync](storage-sync-files-server-registration.md) artykułu. 
- Upewnij się, że wdrożono usługę synchronizacji magazynu. Zobacz [sposób wdrażania usługi Azure File Sync](storage-sync-files-deployment-guide.md) Aby uzyskać szczegółowe informacje na temat wdrażania usługi synchronizacji magazynu. 
- Upewnij się, czy grupa synchronizacji został pomyślnie wdrożony. Dowiedz się, jak [Tworzenie grupy synchronizacji](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Upewnij się, że serwer jest połączony z Internetem i czy Azure jest dostępna. Firma Microsoft korzysta z portu 443 dla całej komunikacji między serwerem a naszej usługi.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Aby dodać punkt końcowy serwera, przejdź do grupy synchronizacji żądany, a następnie wybierz pozycję "Dodaj punkt końcowy serwera".

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Poniższe informacje są wymagane w ramach **Dodaj punkt końcowy serwera**:

- **Zarejestrowany serwer**: Nazwa serwera lub klastra, aby utworzyć punkt końcowy serwera na.
- **Ścieżka**: Ścieżka w systemie Windows Server mają być synchronizowane w ramach grupy synchronizacji.
- **Obsługa warstw w chmurze**: Przełącznik, aby włączyć lub wyłączyć chmury warstw. Po włączeniu będzie warstw w chmurze *warstwy* pliki do udziałów plików platformy Azure. Spowoduje to konwersję lokalnych udziałów plików do pamięci podręcznej zamiast pełną kopię zestawu danych, ułatwiające zarządzanie wydajności miejsca na serwerze.
- **Wolne miejsce w woluminie**: ilość wolnego miejsca na dysku zarezerwowane na wolumin, który znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce w woluminie wynosi 50% na woluminie z punktem końcowym pojedynczego serwera, około połowie wielkości danych będą umieszczane do usługi Azure Files. Niezależnie od tego, czy w chmurze warstw jest włączona, udziału plików platformy Azure ma zawsze pełne kopii danych w grupie synchronizacji.

Wybierz **Utwórz** Dodawanie punktu końcowego serwera. Pliki znajdujące się w przestrzeni nazw grupy synchronizacji będą teraz przechowywane w synchronizacji. 

## <a name="remove-a-server-endpoint"></a>Usuwanie punktu końcowego serwera
Jeśli chcesz zrezygnować z używania usługi Azure File Sync dla punktu końcowego na danym serwerze, można usunąć punktu końcowego serwera. 

> [!Warning]  
> Nie należy próbować Rozwiązywanie problemów z usługą synchronizacji, obsługi warstw w chmurze lub dowolnego aspektu usługi Azure File Sync przez usunięcie i ponowne utworzenie punktu końcowego serwera, chyba że jawnie zalecił inżynier z firmy Microsoft. Usuwanie punktu końcowego serwera jest operacją destrukcyjną, a pliki warstwowe w ramach punktu końcowego serwera zostanie nie "ponownie połączony" do ich lokalizacji w udziale plików platformy Azure po punkt końcowy serwera są odtwarzane, co spowoduje, że błędy synchronizacji. Należy również zauważyć, pliki warstwowe, które znajdują się poza przestrzeni nazw punktu końcowego serwera może być trwale utracone. Pliki warstwowe mogą istnieć w ramach Twojej serwerze punktu końcowego, nawet jeśli chmury nigdy nie włączono obsługi warstw.

Aby upewnić się, że wszystkie pliki warstwowe zostaną odwołane przed usunięciem punktu końcowego serwera, wyłącz obsługi warstw w punkcie końcowym serwera w chmurze, a następnie wykonaj następujące polecenie cmdlet programu PowerShell, aby odwołać wszystkie pliki warstwowe w przestrzeni nazw punktu końcowego serwera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Jeśli wolumin lokalny, który jest hostem serwera nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowe `Invoke-StorageSyncFileRecall` polecenie cmdlet nie powiedzie się.  

Aby usunąć punkt końcowy serwera:

1. Przejdź do usługi synchronizacji magazynu, gdy serwer jest zarejestrowany.
2. Przejdź do grupy synchronizacji żądaną.
3. Usuń punkt końcowy serwera, której wymagasz w grupie synchronizacji w usłudze synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni serwer punktu końcowego w okienku grup synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupą synchronizacji](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Kolejne kroki
- [Zarejestrować/wyrejestrować serwer usługi Azure File Sync](storage-sync-files-server-registration.md)
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
