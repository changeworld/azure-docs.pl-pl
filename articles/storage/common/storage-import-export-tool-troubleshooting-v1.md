---
title: Rozwiązywanie problemów z narzędzia Azure importu/eksportu | Dokumentacja firmy Microsoft
description: Poznaj typowe problemy występuje w przypadku za pomocą narzędzia importu/eksportu Azure i sposobu ich obsługi.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873695"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Rozwiązywanie problemów z narzędziem Azure Import/Export
Narzędzie importu/eksportu Microsoft Azure zwraca komunikaty o błędach, gdy jest ono uruchomione na problemy. W tym temacie wymieniono niektóre typowe problemy, które użytkownicy mogą napotkać.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Kopiuj sesja nie powiedzie się, co należy zrobić?  
 W przypadku awarii sesji kopiowania dostępne są dwie opcje:  
  
 Jeśli błąd powtarzający operację, na przykład jeśli udział sieciowy był w trybie offline przez krótki czas, a teraz jest znowu w trybie online, można wznowić sesji kopiowania. Błąd nie jest powtarzający operację, na przykład, jeśli określono nieprawidłowe źródło pliku katalogu w parametry wiersza polecenia, musisz przerwać sesję kopiowania. Zobacz [przygotowywanie dyski twarde dla zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) uzyskać więcej informacji o wznawianie i przerywanie sesji kopiowania.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>I nie można wznowić lub przerwania sesji kopiowania.  
 Jeśli sesja kopiowania jest pierwsza sesja kopii dysku, a następnie należy podać komunikat o błędzie: "pierwszej sesji kopiowania nie może być wznowione ani przerwane". W takim przypadku można usunąć starego pliku dziennika i ponownie uruchom polecenie.  
  
 Jeśli sesja kopiowania nie jest pierwszego dysku, można go zawsze wznowione lub zostało przerwane.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Utratą pliku dziennika, może nadal utworzyć zadanie?  
 Plik dziennika dla dysku zawiera pełne informacje kopiowania danych do tego dysku, i jest wymagana, aby dodać więcej plików na dysku i będzie używany do utworzenia zadania importu. Jeśli plik dziennika zostanie utracony, konieczne będzie wykonaj ponownie wszystkie sesje kopii dysku.  
  
## <a name="next-steps"></a>Następne kroki
 
* [Trwa konfigurowanie narzędzia azure import/eksport](../storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
