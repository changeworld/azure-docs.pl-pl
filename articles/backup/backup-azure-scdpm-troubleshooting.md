---
title: Rozwiązywanie problemów z programu System Center Data Protection Manager z usługą Azure Backup
description: Rozwiązywanie problemów w programie System Center Data Protection Manager.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 4108616e3ae41e2c88b74bb08d5f846c0035101f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236212"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Rozwiązywanie problemów z programem System Center Data Protection Manager

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z programu Data Protection Manager.

Aby uzyskać najnowsze informacje o wersji programu System Center Data Protection Manager, zobacz [dokumentacja programu System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Dowiedz się więcej na temat obsługi programu Data Protection Manager w [tej macierzy](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Błąd: Replika jest niespójna

Replika może być niezgodne z następujących powodów:
- Zadanie tworzenia repliki zakończy się niepowodzeniem.
- Występują problemy z dziennika zmian.
- Mapy bitowej filtr na poziomie woluminu zawiera błędy.
- Nieoczekiwane zamknięcie maszyny źródłowej.
- Dziennik synchronizacji przepełnienia.
- Replika jest niespójna naprawdę.

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Aby usunąć stan niespójna, uruchom sprawdzanie spójności ręcznie lub zaplanować codzienne sprawdzanie spójności.
- Upewnij się, że używasz najnowszej wersji programu Microsoft Azure Backup Server i programu Data Protection Manager.
- Upewnij się, że **wykonywanie automatycznej kontroli spójności** ustawienie jest włączone.
- Spróbuj ponownie uruchomić usługi z poziomu wiersza polecenia. Użyj `net stop dpmra` polecenia, a następnie `net start dpmra`.
- Upewnij się, że jesteś spełniających wymagania dotyczące łączności i przepustowości sieci.
- Sprawdź, jeśli maszyna źródłowa została nieoczekiwanie wyłączony.
- Upewnij się, że dysk jest w dobrej kondycji i czy jest za mało miejsca dla replik.
- Upewnij się, że nie istnieją zduplikowane zadania tworzenia kopii zapasowych uruchomionych jednocześnie.

## <a name="error-online-recovery-point-creation-failed"></a>Błąd: Tworzenie punktu odzyskiwania w trybie online nie powiodło się

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Upewnij się, że używasz najnowszej wersji agenta usługi Azure Backup.
- Spróbuj ręcznie utworzyć punkt odzyskiwania, w obszarze zadanie ochrony.
- Upewnij się, że Uruchom sprawdzanie spójności w źródle danych.
- Upewnij się, że jesteś spełniających wymagania dotyczące łączności i przepustowości sieci.
- Gdy dane repliki jest w niespójnym stanie, należy utworzyć punkt odzyskiwania dysku tego źródła danych.
- Upewnij się, że replika jest obecna, a nie Brak.
- Upewnij się, że repliki ma wystarczająco dużo miejsca, aby utworzyć arkusz (USN) numer sekwencji aktualizacji.

## <a name="error-unable-to-configure-protection"></a>Błąd: Nie można skonfigurować ochrony

Ten błąd występuje, gdy serwer programu Data Protection Manager nie może skontaktować się z chronionym serwerem. 

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Upewnij się, że używasz najnowszej wersji agenta usługi Azure Backup.
- Upewnij się, że istnieje łączność (/ Zapora/serwer proxy sieci) między serwerem programu Data Protection Manager i na serwerze chronionym.
- W przypadku ochrony programu SQL server, upewnij się, że **właściwości logowania** > **NT AUTHORITY\SYSTEM** pokazuje właściwości **sysadmin** ustawienie jest włączone.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Błąd: Serwer nie zarejestrowany jako określony w pliku poświadczeń magazynu

Ten błąd występuje podczas procesu odzyskiwania dla ochrony Menedżera/Azure tworzenia kopii zapasowych danych serwera. Plik poświadczeń magazynu, który jest używany w procesie odzyskiwania nie należy do magazynu usługi Recovery Services pod kątem Data Protection Manager/usługi Azure Backup server.

Aby rozwiązać ten problem, wykonaj następujące kroki:
1. Pobierz plik poświadczeń magazynu z magazynu usługi Recovery Services, w której jest zarejestrowana Data Protection Manager/usługi Azure Backup server.
2. Spróbuj zarejestrować serwer w magazynie przy użyciu najbardziej niedawno pobranego pliku poświadczeń magazynu.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Błąd: Nie dane możliwe do odzyskania lub wybrany serwer nie jest serwerem programu Data Protection Manager

Ten błąd występuje w następujących sytuacjach:
- Inne serwery kopii zapasowej Menedżera/Azure ochrony danych są rejestrowane w magazynie usługi Recovery Services.
- Serwery nie zostały jeszcze załadowane metadanych.
- Wybrany serwer nie jest Data Protection Manager/usługi Azure Backup server.

Gdy inne serwery kopii zapasowej Menedżera/Azure ochrony danych są zarejestrowane w magazynie usługi Recovery Services, wykonaj następujące kroki, aby rozwiązać ten problem:
1. Upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure.
2. Po upewnieniu się, że zainstalowano najnowszą wersję agenta, należy poczekać jeden dzień przed rozpoczęciem procesu odzyskiwania. Nocne zadanie tworzenia kopii zapasowej przekazuje metadane dla wszystkich chronionych kopii zapasowych w chmurze. Dane kopii zapasowej będzie dostępne do odzyskania.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Błąd: Podane hasło szyfrowania nie jest zgodna hasło dla serwera

Ten błąd występuje podczas procesu szyfrowania podczas odzyskiwania danych serwera kopii zapasowej Menedżera/Azure ochrony danych. Hasło szyfrowania, który jest używany w procesie odzyskiwania nie jest zgodna hasło szyfrowania serwera. W rezultacie agent nie może odszyfrować danych i odzyskiwania kończy się niepowodzeniem.

> [!IMPORTANT]
> Jeśli zapomnisz lub utraty hasła szyfrowania, istnieją inne metody odzyskiwania danych. Jedyną opcją jest ponownie wygenerować hasło. Użyj nowe hasło do szyfrowania danych kopii zapasowych w przyszłości.
>
> W przypadku odzyskiwania danych, należy zawsze podawać tego samego hasła szyfrowania, która jest skojarzona z Data Protection Manager/usługi Azure Backup server. 
>
