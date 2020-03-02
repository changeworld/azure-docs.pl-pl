---
title: Wdrażanie agenta Azure File Sync
description: Wdrażanie agenta Azure File Sync. Wspólny blok tekstu współużytkowany przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209417"
---
W tej sekcji instalujesz agenta Azure File Sync na serwerze z systemem Windows.
[Przewodnik wdrażania](../articles/storage/files/storage-sync-files-deployment-guide.md) pokazuje, że należy wyłączyć **ulepszone zabezpieczenia programu IE**. Ulepszone zabezpieczenia programu Internet Explorer to środki bezpieczeństwa, które nie mają zastosowania do Azure File Sync i jego wyłączenia umożliwiają uwierzytelnianie na platformie Azure bez jakichkolwiek problemów.

Otwórz program PowerShell i zainstaluj wymagane moduły programu PowerShell z następującymi poleceniami. Po wyświetleniu monitu upewnij się, że zainstalowano pełny moduł i dostawcę NuGet:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Jeśli masz problemy z nawiązaniem połączenia z Internetem z serwera, możesz teraz rozwiązać ten problem. Azure File Sync korzysta z dowolnego dostępnego połączenia sieciowego z Internetem.
Obsługiwane jest również wymaganie, aby serwer proxy mógł nawiązać połączenie z Internetem. Można skonfigurować serwer proxy dla całego komputera lub określić serwer proxy, który będzie używany do synchronizacji plików podczas instalacji agenta.

Jeśli to konieczne, należy otworzyć zapory dla tego serwera, co może być rozwiązaniem możliwym do zaakceptowania. Po zakończeniu instalacji serwera po zakończeniu rejestracji serwera zostanie wyświetlony raport o łączności sieciowej z dokładnymi adresami URL punktów końcowych na platformie Azure, których synchronizacja plików musi nawiązać połączenie z wybranym regionem. Raport informuje również o przyczynach niepotrzebnej komunikacji. Możesz użyć raportu, aby następnie zablokować zapory otaczające ten serwer do określonych adresów URL.

Można również zastosować bardziej rozpuszczalną metodę, w której nie można otwierać zapór, ale zamiast tego ograniczyć serwer do komunikacji z nazwami DNS wyższego poziomu — w artykule [Azure File Sync serwera proxy i ustawień zapory](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) znajduje się więcej szczegółów. Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi sieci.

Po zakończeniu działania kreatora *instalacji* serwera zostanie wyświetlony Kreator *rejestracji* serwera.
Zarejestruj serwer w usłudze synchronizacji magazynu na podstawie wcześniejszego zasobu platformy Azure.

Te kroki są opisane bardziej szczegółowo w Podręczniku wdrażania, w tym w powyższym module programu PowerShell, należy najpierw zainstalować: [Azure File Sync instalacji agenta](../articles/storage/files/storage-sync-files-deployment-guide.md).

Należy użyć najnowszego agenta i można go pobrać z centrum pobierania Microsoft: [Azure File Sync-Agent](https://aka.ms/AFS/agent "Pobieranie agenta Azure File Sync").

Po pomyślnej instalacji i rejestracji serwera można sprawdzić, czy pomyślnie wykonano ten krok: Przejdź do zasobu usługi synchronizacji magazynu w Azure Portal, a następnie postępuj zgodnie z menu po lewej stronie, aby "zarejestrowane serwery". Serwer będzie tam tam widoczny.
