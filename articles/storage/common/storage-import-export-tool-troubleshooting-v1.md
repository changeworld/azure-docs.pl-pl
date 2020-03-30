---
title: Rozwiązywanie problemów z narzędziem importu/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o niektórych typowych problemach, które można znaleźć podczas korzystania z narzędzia Import/Eksport platformy Azure oraz o tym, jak sobie z nimi radzić.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978402"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Rozwiązywanie problemów z narzędziem Azure Import/Export
Narzędzie importu/eksportu platformy Microsoft Azure zwraca komunikaty o błędach, jeśli występują problemy. W tym temacie wymieniono niektóre typowe problemy, na które użytkownicy mogą napotkać.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Sesja kopiowania kończy się niepowodzeniem, co powinienem zrobić?  
 Gdy sesja kopiowania nie powiedzie się, dostępne są dwie opcje:  

 Jeśli błąd jest ponawiany, na przykład jeśli udział sieciowy był w trybie offline przez krótki okres, a teraz jest z powrotem w trybie online, można wznowić sesję kopiowania. Jeśli błąd nie jest ponawiany, na przykład jeśli określono niewłaściwy katalog plików źródłowych w parametrach wiersza polecenia, należy przerwać sesję kopiowania. Aby uzyskać więcej informacji na temat wznawiania i przerywania sesji kopiowania, zobacz [Przygotowywanie dysków twardych do zadania importu.](../storage-import-export-tool-preparing-hard-drives-import-v1.md)  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nie mogę wznowić ani przerwać sesji kopiowania.  
 Jeśli sesja kopiowania jest pierwszą sesją kopiowania dysku, komunikat o błędzie powinien być wyświetlany: "Nie można wznowić ani przerwać pierwszej sesji kopiowania". W takim przypadku można usunąć stary plik dziennika i ponownie uruchomić polecenie.  

 Jeśli sesja kopiowania nie jest pierwszą dla dysku, zawsze można ją wznowić lub przerwać.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Zgubiłem plik dziennika, czy nadal mogę utworzyć zadanie?  
 Plik dziennika dysku zawiera pełne informacje o kopiowaniu danych na ten dysk i jest potrzebny do dodania większej liczby plików do dysku i będzie używany do tworzenia zadania importu. Jeśli plik dziennika zostanie utracony, trzeba będzie ponownie ponawiać wszystkie sesje kopiowania dysku.  

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia importu/eksportu platformy Azure](../storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
