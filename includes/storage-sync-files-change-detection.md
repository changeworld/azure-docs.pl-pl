---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 55456a6be938411d3c08a0eaa8fdbfb0844e7129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391734"
---
Zmiany wprowadzone w udziale plików platformy Azure przy użyciu witryny Azure portal lub SMB nie są natychmiast wykrywane i replikowane jak zmiany w punkcie końcowym serwera. Usługa Azure Files nie ma jeszcze powiadomień o zmianach ani rejestrowania, więc nie ma możliwości automatycznego inicjowania sesji synchronizacji po zmianie plików. W systemie Windows Server usługa Azure File Sync używa [dziennika USN systemu Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) do automatycznego inicjowania sesji synchronizacji po zmianie plików.

Aby wykryć zmiany w udziale plików platformy Azure, usługa Azure File Sync ma zaplanowane zadanie o nazwie *zadanie wykrywania zmian*. Zadanie wykrywania zmian wylicza każdy plik w udziale plików, a następnie porównuje go z wersją synchronizacji dla tego pliku. Gdy zadanie wykrywania zmian określa, że pliki zostały zmienione, usługa Azure File Sync inicjuje sesję synchronizacji. Zadanie wykrywania zmian jest inicjowane co 24 godziny. Ponieważ zadanie wykrywania zmian działa przez wyliczenie każdego pliku w udziale plików platformy Azure, wykrywanie zmian trwa dłużej w większych przestrzeniach nazw niż w mniejszych obszarach nazw. W przypadku dużych obszarów nazw może upłynąć więcej niż raz na 24 godziny, aby określić, które pliki zostały zmienione.

Aby natychmiast zsynchronizować pliki, które zostały zmienione w udziale plików platformy Azure, polecenie cmdlet **invoke-AzStorageSyncChangeDetection** programu PowerShell może służyć do ręcznego inicjowania wykrywania zmian w udziale plików platformy Azure. To polecenie cmdlet jest przeznaczone dla scenariuszy, w których jakiś typ zautomatyzowanego procesu wprowadza zmiany w udziale plików platformy Azure lub zmiany są wykonywane przez administratora (na przykład przenoszenie plików i katalogów do udziału). W przypadku zmian użytkownika końcowego zaleca się zainstalowanie agenta usługi Azure File Sync na maszynie Wirtualnej IaaS i udostępnienie udziału plików przez użytkowników końcowych za pośrednictwem maszyny Wirtualnej IaaS. W ten sposób wszystkie zmiany zostaną szybko zsynchronizowane z innymi agentami bez konieczności używania polecenia cmdlet Invoke-AzStorageSyncChangeDetection. Aby dowiedzieć się więcej, zobacz [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentacji.

>[!NOTE]
>Zmiany wprowadzone w udziale plików platformy Azure przy użyciu REST nie aktualizuje czasu ostatniej modyfikacji SMB i nie będą postrzegane jako zmiana według synchronizacji.

Badamy dodawanie wykrywania zmian dla udziału plików platformy Azure podobne do usn dla woluminów w systemie Windows Server. Pomóż nam ustalić priorytety tej funkcji dla przyszłego rozwoju, głosując na nią w [usłudze Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
