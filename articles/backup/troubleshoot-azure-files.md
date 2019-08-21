---
title: Rozwiązywanie problemów dotyczących tworzenia kopii zapasowej udziałów plików platformy Azure
description: W tym artykule znajdują się informacje dotyczące rozwiązywania problemów występujących podczas ochrony udziałów plików platformy Azure.
ms.service: backup
author: dcurwin
ms.author: dacurwin
ms.date: 08/20/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 1182c7d4ac9a103e752a8cd0c392c5e57f1eebd0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637580"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Rozwiązywanie problemów związanych z tworzeniem kopii zapasowej udziałów plików platformy Azure
Korzystając z informacji znajdujących się w poniższych tabelach możesz rozwiązywać problemy i usuwać błędy napotkane podczas używania funkcji tworzenia kopii zapasowej udziałów plików platformy Azure.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Ograniczenia dotyczące tworzenia kopii zapasowej udziału plików platformy Azure w okresie korzystania z wersji zapoznawczej
Funkcja tworzenia kopii zapasowych udziałów plików platformy Azure jest dostępna w wersji zapoznawczej. Obsługiwane są udziały plików platformy Azure na kontach magazynu ogólnego przeznaczenia w wersji 1 i 2. Następujące scenariusze tworzenia kopii zapasowej nie są obsługiwane w przypadku udziałów plików platformy Azure:
- Nie ma dostępnego interfejsu wiersza polecenia do ochrony usługi Azure Files z poziomu usługi Azure Backup.
- Maksymalna liczba zaplanowanych kopii zapasowych to jedna dziennie.
- Maksymalna liczba kopii zapasowych na żądanie to cztery dziennie.
- Aby zapobiec przypadkowemu usunięciu kopii zapasowych z magazynu usługi Recovery Services, użyj [blokad zasobów](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na koncie magazynu.
- Nie usuwaj migawek utworzonych przy użyciu usługi Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.
- Nie usuwaj udziałów plików, które są chronione przez usługę Azure Backup. Bieżące rozwiązanie usunie wszystkie migawki utworzone przez usługę Azure Backup, gdy udział plików zostanie usunięty, i tym samym utraci wszystkie punkty przywracania.

Tworzenie kopii zapasowej udziałów plików platformy Azure w ramach kont magazynu za pomocą replikacji [magazynu strefowo nadmiarowego](../storage/common/storage-redundancy-zrs.md) (ZRS) jest obecnie dostępne tylko w regionach Środkowe stany USA (CUS), Wschodnie stany USA (EUS), Wschodnie stany USA 2 (EUS2), Europa Północna (NE), Azja Południowo-Wschodniej (SEA), Europa Zachodnia (WE) i Zachodnie stany USA 2 (WUS2).

## <a name="configuring-backup"></a>Konfigurowanie kopii zapasowej
Poniższa tabela dotyczy konfigurowania kopii zapasowej:

| Komunikaty o błędach | Wskazówki dotyczące obejść lub rozwiązań |
| ------------------ | ----------------------------- |
| Nie można odnaleźć konta magazynu w celu skonfigurowania kopii zapasowej na potrzeby udziału plików platformy Azure | <ul><li>Poczekaj na zakończenie odnajdywania. <li>Sprawdź, czy żaden udział plików z konta magazynu nie jest już chroniony w ramach innego magazynu usługi Recovery Services. **Uwaga**: wszystkie udziały plików konta magazynu mogą być chronione w ramach jednego magazynu usługi Recovery Services. <li>Upewnij się, że udział plików nie istnieje w żadnym z nieobsługiwanych kont magazynu.|
| Błąd w portalu z informacją o niepowodzeniu odnajdywania kont magazynu. | Jeśli subskrypcja jest partnerska (z obsługą dostawcy usług kryptograficznych), zignoruj ten błąd. Jeśli Twoja subskrypcja nie obsługuje dostawcy usług kryptograficznych i nie można odnaleźć kont magazynu, skontaktuj się z pomocą techniczną.|
| Wybrana weryfikacja lub rejestracja konta magazynu nie powiodła się.| Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.|
| Nie można wyświetlić lub odnaleźć udziałów plików w ramach wybranego konta magazynu. | <ul><li> Sprawdź, czy konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione po ostatniej weryfikacji lub rejestracji w magazynie.<li>Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty. <li>Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików.<li>Sprawdź, czy udział plików nie jest już chroniony w tym samym magazynie usługi Recovery Services.|
| Konfiguracja kopii zapasowej udziału plików (lub konfiguracja zasad ochrony) kończy się niepowodzeniem. | <ul><li>Spróbuj ponownie wykonać operację, aby sprawdzić, czy problem nadal występuje. <li> Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty. <li> Jeśli próbujesz chronić wiele udziałów plików jednocześnie, a niektóre udziały plików kończą się niepowodzeniem, ponów próbę skonfigurowania kopii zapasowej dla udziałów plików zakończonych niepowodzeniem. |
| Nie można usunąć magazynu usługi Recovery Services po wyłączeniu ochrony udziału plików. | W witrynie Azure Portal otwórz swój magazyn, wybierz pozycję **Infrastruktura kopii zapasowych** > **Konta magazynu**, a następnie kliknij pozycję **Wyrejestruj**, aby usunąć konto magazynu z magazynu usługi Recovery Services.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Komunikaty o błędach dotyczących zadań tworzenia kopii zapasowej lub przywracania

| Komunikaty o błędach | Wskazówki dotyczące obejść lub rozwiązań |
| -------------- | ----------------------------- |
| Operacja nie powiodła się, ponieważ nie odnaleziono udziału plików. | Sprawdź, czy udział plików, który chcesz chronić, nie został usunięty.|
| Nie odnaleziono konta magazynu lub nie jest ono obsługiwane. | <ul><li>Sprawdź, czy konto magazynu istnieje w grupie zasobów i nie zostało usunięte ani przeniesione z grupy zasobów po ostatniej weryfikacji. <li> Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików.|
| Osiągnięto maksymalną liczbę migawek dla tego udziału plików. Tworzenie kolejnych migawek będzie możliwe po wygaśnięciu starszych. | <ul><li> Ten błąd może wystąpić podczas tworzenia wielu kopii zapasowych na żądanie dla pliku. <li> Istnieje limit 200 migawek na udział plików łącznie z migawkami tworzonymi w ramach usługi Azure Backup. Starsze zaplanowane kopie zapasowych (lub migawki) są czyszczone automatycznie. Kopie zapasowe na żądanie (lub migawki) muszą zostać usunięte po osiągnięciu maksymalnego limitu.<li> Usuń kopie zapasowe na żądanie (migawki udziałów plików platformy Azure) z portalu usługi Azure Files. **Uwaga**: punkty odzyskiwania zostaną utracone w przypadku usunięcia migawek utworzonych za pomocą usługi Azure Backup. |
| Tworzenie kopii zapasowej udziału plików lub jego przywracanie nie powiodło się z powodu ograniczania usługi magazynu. Może to być spowodowane tym, że usługa magazynu jest zajęta przetwarzaniem innych żądań dla danego konta magazynu.| Spróbuj ponownie wykonać operację po pewnym czasie. |
| Przywracanie nie powiodło się, ponieważ docelowy udziału plików nie został odnaleziony. | <ul><li>Sprawdź, czy wybrane konto magazynu istnieje, a docelowy udziału plików nie został usunięty. <li> Sprawdź, czy dla konta magazynu obsługiwane jest tworzenie kopii zapasowej udziału plików. |
| Zadania tworzenia kopii zapasowej lub przywracania nie powiodły się, ponieważ konto magazynu jest w stanie zablokowanym. | Usuń blokadę konta magazynu lub użyj opcji usunięcia blokady zamiast odczytu blokady, a następnie spróbuj ponownie wykonać operację. |
| Odzyskiwanie nie powiodło się, ponieważ liczba plików zakończonych niepowodzeniem jest większa niż wartość progowa. | <ul><li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie ponów próbę wykonania operacji przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> -pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |
| Odzyskiwanie nie powiodło się, ponieważ żaden plik nie mógł zostać odzyskany. | <ul><li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> -pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |
| Przywracanie nie powiodło się, ponieważ jeden z plików w źródle nie istnieje. | <ul><li> Wybrane elementy nie istnieją w danych punktu odzyskiwania. Aby odzyskać pliki, udostępnij poprawną listę plików. <li> Migawka udziału plików odpowiadająca punktowi odzyskiwania została ręcznie usunięta. Wybierz inny punkt odzyskiwania, a następnie spróbuj ponownie wykonać operację przywracania. |
| Zadanie odzyskiwania jest w trakcie przetwarzania do tego samego miejsca docelowego. | <ul><li>Kopia zapasowa udziału plików nie obsługuje odzyskiwania równoległego do tego samego docelowego udziału plików. <li>Poczekaj na zakończenie trwającej operacji odzyskiwania, a następnie spróbuj ponownie. Jeśli zadanie odzyskiwania nie zostanie odnalezione w magazynie usługi Recovery Services, wybierz inne magazyny usługi Recovery Services w ramach tej samej subskrypcji. |
| Operacja przywracania nie powiodła się, ponieważ docelowy udział plików jest pełny. | Zwiększ limit przydziału rozmiaru docelowego udziału plików w celu pomieszczenia przywracanych danych, a następnie spróbuj ponownie wykonać operację. |
| Operacja przywrócenia nie powiodła się, ponieważ wystąpił błąd podczas wykonywania operacji przywracania wstępnego dla zasobów usługi File Sync skojarzonych z docelowym udziałem plików. | Spróbuj ponownie za jakiś czas. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |
| Odzyskanie co najmniej jednego pliku zakończyło się niepowodzeniem. Aby uzyskać więcej informacji, sprawdź listę plików zakończonych niepowodzeniem w ramach podanej powyżej ścieżki. | <ul> <li> Przyczyny niepowodzenia odzyskiwania są wymienione w pliku (ścieżka jest podana w szczegółach zadania). Usuń przyczyny niepowodzenia, a następnie spróbuj ponownie wykonać operację przywracania tylko dla plików zakończonych niepowodzeniem. <li> Typowe przyczyny błędów przywracania plików: <br/> - pliki zakończone niepowodzeniem są używane <br/> -w katalogu nadrzędnym istnieje katalog o tej samej nazwie, co plik zakończony niepowodzeniem |


## <a name="modify-policy"></a>Modyfikowanie zasad
| Komunikaty o błędach | Wskazówki dotyczące obejść lub rozwiązań |
| ------------------ | ----------------------------- |
| Inna operacja konfiguracji ochrony jest w toku dla tego elementu. | Poczekaj na zakończenie poprzedniej operacji modyfikowania zasad, a następnie spróbuj ponownie po pewnym czasie.|
| Inna operacja jest w toku dla wybranego elementu. | Poczekaj na ukończenie innej operacji w toku, a następnie spróbuj ponownie po pewnym czasie |


## <a name="see-also"></a>Zobacz też
Aby uzyskać dodatkowe informacje na temat tworzenia kopii zapasowej udziałów plików platformy Azure, zobacz:
- [Tworzenie kopii zapasowej udziałów plików platformy Azure](backup-azure-files.md)
- [Tworzenie kopii zapasowej udziałów plików platformy Azure — często zadawane pytania](backup-azure-files-faq.md)
