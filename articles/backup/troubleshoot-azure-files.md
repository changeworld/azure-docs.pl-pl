---
title: Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej udziału plików platformy Azure
description: W tym artykule znajdują się informacje dotyczące rozwiązywania problemów występujących podczas ochrony udziałów plików platformy Azure.
services: backup
ms.service: backup
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 225d11c8609c81ed7877283e8dc0fd920b14d838
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-problems-backing-up-azure-files"></a>Rozwiązywanie problemów związanych z tworzeniem kopii zapasowej w usłudze Azure Files
Korzystając z informacji znajdujących się w poniższych tabelach możesz rozwiązywać problemy i usuwać błędy napotkane podczas używania funkcji tworzenia kopii zapasowej w usłudze Azure Files.

## <a name="preview-boundaries"></a>Granice wersji zapoznawczej
Usługa Azure Files jest w wersji zapoznawczej. Następujące scenariusze tworzenia kopii zapasowej nie są obsługiwane w przypadku udziałów plików platformy Azure:
- Ochrona udziałów plików platformy Azure w ramach kont magazynu przy użyciu replikacji [magazynu strefowo nadmiarowego (ZRS)](../storage/common/storage-redundancy-zrs.md) lub [magazynu geograficznie nadmiarowego dostępnego do odczytu (RA-GRS)](../storage/common/storage-redundancy-grs.md).
- Ochrona udziałów plików platformy Azure w ramach kont magazynu, które mają włączone sieci wirtualne.
- Tworzenie kopii zapasowej udziałów plików platformy Azure za pomocą programu PowerShell lub interfejsu wiersza polecenia.

### <a name="limitations"></a>Ograniczenia
- Maksymalna liczba zaplanowanych kopii zapasowych na dzień wynosi 1.
- Maksymalna liczba kopii zapasowych na żądanie na dzień wynosi 4.
- Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj blokad zasobów na koncie magazynu.
- Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania

## <a name="configuring-backup"></a>Konfigurowanie kopii zapasowej
Poniższa tabela dotyczy konfigurowania kopii zapasowej:

| Konfigurowanie kopii zapasowej | Wskazówki dotyczące obejść lub rozwiązań |
| ------------------ | ----------------------------- |
| Nie można odnaleźć konta magazynu w celu skonfigurowania kopii zapasowej na potrzeby udziału plików platformy Azure | <ul><li>Poczekaj na zakończenie odnajdywania. <li>Sprawdź, czy żaden udział plików z konta magazynu nie jest już chroniony w ramach innego magazynu usługi Recovery Services. **Uwaga**: wszystkie udziały plików konta magazynu mogą być chronione w ramach jednego magazynu usługi Recovery Services. <li>Upewnij się, że udział plików nie istnieje w żadnym z nieobsługiwanych kont magazynu.|
| Błąd w portalu z informacją o niepowodzeniu odnajdywania kont magazynu. | Jeśli subskrypcja jest partnerska (z obsługą dostawcy usług kryptograficznych), zignoruj ten błąd. Jeśli Twoja subskrypcja nie obsługuje dostawcy usług kryptograficznych i nie można odnaleźć kont magazynu, skontaktuj się z pomocą techniczną.|
| Wybrana weryfikacja lub rejestracja konta magazynu nie powiodła się.| Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.|
| Nie można wyświetlić lub odnaleźć udziałów plików w ramach wybranego konta magazynu. | <ul><li> Sprawdź, czy konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione po ostatniej weryfikacji lub rejestracji w magazynie.<li>Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty. <li>Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików.<li>Sprawdź, czy udział plików nie jest już chroniony w tym samym magazynie usługi Recovery Services.|
| Konfiguracja kopii zapasowej udziału plików (lub konfiguracja zasad ochrony) kończy się niepowodzeniem. | <ul><li>Spróbuj ponownie wykonać operację, aby sprawdzić, czy problem nadal występuje. <li> Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty. <li> Jeśli próbujesz chronić wiele udziałów plików jednocześnie, a niektóre udziały plików kończą się niepowodzeniem, ponów próbę skonfigurowania kopii zapasowej dla udziałów plików zakończonych niepowodzeniem. |
| Nie można usunąć magazynu usługi Recovery Services po wyłączeniu ochrony udziału plików. | W witrynie Azure Portal otwórz pozycję **Infrastruktura kopii zapasowych** > **Konta magazynu**, a następnie kliknij pozycję **Wyrejestruj**, aby usunąć konto magazynu z magazynu usługi Recovery Services.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Komunikaty o błędach dotyczących zadań tworzenia kopii zapasowej lub przywracania

| Komunikaty o błędach | Wskazówki dotyczące obejść lub rozwiązań |
| -------------- | ----------------------------- |
| Operacja nie powiodła się, ponieważ nie odnaleziono udziału plików. | Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty.|
| Nie odnaleziono konta magazynu lub nie jest ono obsługiwane. | <ul><li>Sprawdź, czy konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione z grupy zasobów po ostatniej weryfikacji. <li> Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików.|
| Osiągnięto maksymalną liczbę migawek dla tego udziału plików. Tworzenie kolejnych migawek będzie możliwe po wygaśnięciu starszych. | <ul><li> Ten błąd może wystąpić podczas tworzenia wielu kopii zapasowych na żądanie dla pliku. <li> Istnieje limit 200 migawek na udziałów plików łącznie z migawkami tworzonymi w ramach usługi Azure Backup. Starsze zaplanowane kopie zapasowych (lub migawki) są czyszczone automatycznie. Kopie zapasowe na żądanie (lub migawki) muszą zostać usunięte po osiągnięciu maksymalnego limitu.<li> Usuń kopie zapasowe na żądanie (migawki udziałów plików platformy Azure) z portalu usługi Azure Files. **Uwaga**: punkty odzyskiwania zostaną utracone w przypadku usunięcia migawek utworzonych za pomocą usługi Azure Backup. |
| Tworzenie kopii zapasowej udziału plików lub jego przywracanie nie powiodło się z powodu ograniczania usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu.| Spróbuj ponownie wykonać operację po pewnym czasie. |
| Przywracanie nie powiodło się, ponieważ docelowy udziału plików nie został odnaleziony. | <ul><li>Sprawdź, czy wybrane konto magazynu istnieje, a docelowy udziału plików nie został usunięty. <li> Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików. |
| Kopie zapasowe usługi Azure Backup nie są obecnie obsługiwane dla usługi Azure Files w ramach kont magazynu, które mają włączone sieci wirtualne. | Wyłącz sieci wirtualne w ramach konta magazynu, aby zapewnić powodzenie operacji tworzenia kopii zapasowych i przywracania. |
| Zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym. | Usuń blokadę konta magazynu lub użyj opcji usunięcia blokady zamiast odczytu blokady, a następnie spróbuj ponownie wykonać operację. |
| Odzyskiwanie nie powiodło się, ponieważ liczba plików zakończonych niepowodzeniem jest większa niż wartość progowa. | <ul><li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie ponów próbę wykonania operacji przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> -pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |
| Odzyskiwanie nie powiodło się, ponieważ żaden plik nie mógł zostać odzyskany. | <ul><li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> -pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |
| Przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje. | <ul><li> Wybrane elementy nie istnieją w danych punktu odzyskiwania. Aby odzyskać pliki, udostępnij poprawną listę plików. <li> Migawka udziału plików odpowiadająca punktowi odzyskiwania została ręcznie usunięta. Wybierz inny punkt odzyskiwania, a następnie spróbuj ponownie wykonać operację przywracania. |
| Zadanie odzyskiwania jest w trakcie przetwarzania do tego samego miejsca docelowego. | <ul><li>Kopia zapasowa udziału plików nie obsługuje odzyskiwania równoległego do tego samego docelowego udziału plików. <li>Poczekaj na zakończenie trwającej operacji odzyskiwania, a następnie spróbuj ponownie. Jeśli zadanie odzyskiwania nie zostanie odnalezione w magazynie usługi Recovery Services, wybierz inne magazyny usługi Recovery Services w ramach tej samej subskrypcji. |
| Operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny. | Zwiększ limit przydziału rozmiaru docelowego udziału plików w celu pomieszczenia przywracanych danych, a następnie spróbuj ponownie wykonać operację. |
| Operacja przywrócenia nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przywracania wstępnego dla zasobów usługi File Sync skojarzonych z docelowym udziałem plików. | Spróbuj ponownie za jakiś czas. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Odzyskanie co najmniej jednego pliku zakończyło się niepowodzeniem. Aby uzyskać więcej informacji, sprawdź listę plików zakończonych niepowodzeniem w ramach podanej powyżej ścieżki. | <ul> <li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> - pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |

## <a name="see-also"></a>Zobacz też
Aby uzyskać dodatkowe informacje na temat tworzenia kopii zapasowej udziałów plików platformy Azure, zobacz:
- [Tworzenie kopii zapasowej udziałów plików platformy Azure](backup-azure-files.md)
- [Tworzenie kopii zapasowej udziałów plików platformy Azure — często zadawane pytania](backup-azure-files-faq.md)
