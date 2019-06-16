---
title: Rozwiązywanie problemów z narzędziem Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o niektórych typowych problemów, które są widoczne podczas korzystania z narzędzia Import/Eksport Azure i jak je obsłużyć.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 9a4e47143515c7f9c21d701809c35d61853d91ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60320452"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Rozwiązywanie problemów z narzędziem Azure Import/Export
Narzędzie importu/eksportu platformy Microsoft Azure zwraca komunikaty o błędach, jeśli działa problemy. Ten temat zawiera listę typowych problemów, które użytkownicy mogą uruchamiać w.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>Kopiowanie sesja nie powiedzie się, co należy zrobić?  
 Podczas sesji kopiowania zakończy się niepowodzeniem, dostępne są dwie opcje:  
  
 Jeśli błąd umożliwiający ponowienie próby, na przykład jeśli udział sieciowy była w trybie offline przez krótki okres, a teraz jest wróci do trybu online, można wznowić sesji kopiowania. Jeśli ten błąd nie jest powtarzający operację, na przykład, jeśli określono nieprawidłowe źródło pliku katalogu za pomocą parametrów wiersza polecenia, musisz przerwać sesję kopiowania. Zobacz [przygotowywania dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) Aby uzyskać więcej informacji na temat wznawianie i przerywanie sesji kopiowania.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nie można wznowić lub Przerwij sesję kopiowania.  
 Jeśli sesja kopiowania jest pierwsza sesja kopiowania dla dysku, powinien podać komunikat o błędzie: "Pierwsza sesja kopiowania nie może być wznowione lub zostało przerwane". W takim przypadku można usunąć starego pliku dziennika i ponownie uruchom polecenie.  
  
 Jeśli sesja kopiowania nie jest pierwszy z nich dla dysku, można go zawsze wznowione lub zostało przerwane.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Utratą pliku dziennika, mimo to mogę tworzyć zadania?  
 Plik dziennika dla dysku zawiera pełnych informacji kopiowania danych na tym dysku i potrzebny do dodania większej liczby plików na dysku i będzie używany do utworzenia zadania importu. Jeśli plik dziennika zostanie utracony, trzeba będzie ponownie wszystkie sesje kopii dysku.  
  
## <a name="next-steps"></a>Kolejne kroki
 
* [Konfigurowanie narzędzia azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
