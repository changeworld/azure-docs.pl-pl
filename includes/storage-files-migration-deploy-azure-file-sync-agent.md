---
title: Wdrażanie agenta synchronizacji plików platformy Azure
description: Wdrażanie agenta synchronizacji plików platformy Azure. Wspólny blok tekstu, współużytkowane przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209417"
---
W tej sekcji należy zainstalować agenta usługi Azure File Sync na serwerze Windows Server.
[Przewodnik wdrażania](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustruje, że należy wyłączyć **ie zwiększonych zabezpieczeń**. IE zwiększonych zabezpieczeń jest środkiem zabezpieczeń, który nie ma zastosowania z synchronizacji plików platformy Azure i wyłączenie go umożliwia uwierzytelnianie na platformie Azure bez żadnych problemów.

Otwórz program PowerShell i zainstaluj wymagane moduły programu PowerShell za pomocą następujących poleceń. Upewnij się, aby zainstalować pełny moduł i dostawcy NuGet po wyświetleniu monitu:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Jeśli masz jakieś problemy z dotarciem do Internetu z serwera, teraz jest czas, aby je rozwiązać. Usługa Azure File Sync używa dowolnego dostępnego połączenia sieciowego z Internetem.
Obsługiwane jest również wymaganie serwera proxy, aby dotrzeć do Internetu. Można skonfigurować serwer proxy całego komputera teraz lub określić serwer proxy, który po prostu synchronizacji plików będzie używany podczas instalacji agenta.

Jeśli oznacza to, że musisz otworzyć zapory dla tego serwera, może to być akceptowalne podejście do Ciebie. Po zakończeniu instalacji serwera po zakończeniu rejestracji serwera pojawi się raport łączności sieciowej pokazujący dokładne adresy URL punktów końcowych na platformie Azure, z którym synchronizacja plików musi komunikować się z wybranym regionem. Raport zawiera również powód, dla którego potrzebna jest komunikacja. Za pomocą raportu można zablokować zapory wokół tego serwera, aby przejść do określonych adresów URL.

Można również wykonać bardziej konserwatywne podejście, w którym nie otwierasz szerokich zapór, ale zamiast tego ograniczasz serwer do komunikowania się z przestrzeniami nazw DNS wyższego poziomu — w artykule [ustawienia serwera proxy i zapory usługi Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) jest więcej dokumentacji i szczegółów. Postępuj zgodnie z własnymi najlepszymi praktykami sieciowymi.

Po zakończeniu pracy *kreatora instalacji* serwera pojawi się kreator *rejestracji* serwera.
Zarejestruj serwer do zasobu platformy Azure usługi synchronizacji magazynu z wcześniej.

Te kroki są opisane bardziej szczegółowo w przewodniku wdrażania, w tym powyższe moduły programu PowerShell, które należy zainstalować najpierw: [zainstalowanie agenta usługi Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md).

Najnowszy agent powinien być używany i można go pobrać z Centrum pobierania Microsoft: [Azure File Sync - agent](https://aka.ms/AFS/agent "Pobieranie agenta usługi Azure File Sync").

Po pomyślnej instalacji i rejestracji serwera, można sprawdzić, czy pomyślnie wykonano ten krok: Przejdź do zasobu usługi synchronizacji magazynu w witrynie Azure portal, a następnie wykonaj menu po lewej stronie do "Zarejestrowane serwery". Zobaczysz tam swój serwer od razu.
