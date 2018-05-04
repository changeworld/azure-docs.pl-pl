---
title: Dodaj lub Usuń punkt końcowy serwera synchronizacji plików Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy wziąć pod uwagę podczas planowania wdrożenia usługi pliki Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 26e4af814bad988da02d4e0cf36f17e1beec872e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Dodaj lub Usuń punkt końcowy serwera synchronizacji plików Azure (wersja zapoznawcza)
Usługa Azure File Sync (wersja zapoznawcza) umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files bez rezygnacji z elastyczności, wydajności i zgodności lokalnego serwera plików. Jest to realizowane poprzez przekształcanie systemów Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS) i możesz mieć dowolną potrzebną Ci liczbę pamięci podręcznych na całym świecie.

A *punkt końcowy serwera* reprezentuje określonej lokalizacji na *zarejestrowanego serwera*, takie jak folder na wolumin serwera lub w katalogu głównym woluminu. Wiele punktów końcowych serwera może istnieć na tym samym woluminie, jeśli nakładania ich przestrzenie nazw (na przykład F:\sync1 i F:\sync2). Można skonfigurować zasady warstw chmury indywidualnie dla każdego punktu końcowego serwera. Po dodaniu do grupy synchronizacji na serwerze z istniejącego zestawu plików jako punktu końcowego serwera, te pliki zostaną scalone z innymi plikami już dla innych punktów końcowych w grupie synchronizacji.

Zobacz [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md) informacji na temat wdrażania synchronizacji plików Azure end-to-end.

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć punktu końcowego serwera, należy najpierw upewnić, że są spełnione poniższe kryteria: 
- Serwer z zainstalowanym agentem synchronizacji plików Azure i został zarejestrowany. Instrukcje dotyczące instalowania agenta synchronizacji plików Azure można znaleźć w [zarejestrować/wyrejestrować na serwerze z funkcją synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-server-registration.md) artykułu. 
- Upewnij się, że wdrożono usługę synchronizacji magazynu. Zobacz [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md) szczegółowe informacje na temat wdrażania usługi synchronizacji magazynu. 
- Upewnij się, że grupy synchronizacji został wdrożony. Dowiedz się, jak [Utwórz grupę synchronizacji](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Upewnij się, że serwer ma połączenie z Internetem i czy Azure jest dostępna. Firma Microsoft korzysta z portu 443 dla całej komunikacji między serwerem a naszej usługi.

## <a name="add-a-server-endpoint"></a>Dodawanie punktu końcowego serwera
Aby dodać punktu końcowego serwera, przejdź do żądanego synchronizacji grupy i wybierz opcję "Dodaj punkt końcowy serwera".

![Dodaj nowy punkt końcowy serwera w okienku grupy synchronizacji](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

Następujące informacje są wymagane w obszarze **dodać punkt końcowy serwera**:

- **Zarejestrowanego serwera**: Nazwa serwera lub klastra, aby utworzyć punkt końcowy serwera na.
- **Ścieżka**: ścieżka w systemie Windows Server mają być synchronizowane w ramach grupy synchronizacji.
- **Obsługa poziomów w chmurze**: przełącznik Aby włączyć lub wyłączyć chmury, Obsługa poziomów w chmurze, które umożliwia rzadko używane lub dostęp do plików do należeć do warstwy do usługi pliki Azure.
- **Wolne miejsce w woluminie**: ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce w woluminie jest ustawiona na 50% na woluminie z punktem końcowym pojedynczego serwera, około połowie wielkości danych będzie należeć do warstwy do usługi pliki Azure. Niezależnie od tego, czy w chmurze, dodając funkcje warstw jest włączona, udziału plików Azure ma zawsze pełną kopię danych do grupy synchronizacji.

Wybierz **Utwórz** można dodać punktu końcowego serwera. Pliki znajdujące się w przestrzeni nazw grupy synchronizacji będą teraz przechowywane w synchronizacji. 

## <a name="remove-a-server-endpoint"></a>Usuwanie punktu końcowego serwera
Po włączeniu dla serwera punktu końcowego w chmurze będzie warstw *warstwy* plików do sieci udziały plików platformy Azure. Dzięki temu lokalnymi udziały plików do działania jako pamięci podręcznej, zamiast pełną kopię zestawu danych, aby wydajnie korzystać z miejsca na serwerze plików. Jednak **punktu końcowego serwera zostanie usunięty z plikami warstwowych nadal lokalnie na serwerze, pliki staną się niedostępne**. W związku z tym dalsze potrzeby uzyskiwania dostępu do plików w udziałach plików lokalnych, najpierw odwołać wszystkie pliki warstwowy z plików Azure przed kontynuowaniem usuwanie punktu końcowego serwera. 

Można to zrobić za pomocą polecenia cmdlet programu PowerShell w sposób przedstawiony poniżej:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> W przypadku woluminu obsługującego serwer nie ma wystarczającej ilości wolnego miejsca, aby odwołać wszystkie dane warstwowych `Invoke-StorageSyncFileRecall` polecenie cmdlet nie powiedzie się.  

Aby usunąć punkt końcowy serwera:

1. Przejdź do magazynu usługi synchronizacji, gdy serwer jest zarejestrowany.
2. Przejdź do żądanego synchronizacji grupy.
3. Usuń punkt końcowy serwera, który chcesz w grupie synchronizacji przez usługę synchronizacji magazynu. Można to zrobić, klikając prawym przyciskiem myszy odpowiedni serwer punktu końcowego w okienku grupy synchronizacji.

    ![Usuwanie punktu końcowego serwera z grupy synchronizacji](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Kolejne kroki
- [Register/unregister serwer synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-server-registration.md)
- [Planowanie wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md)
