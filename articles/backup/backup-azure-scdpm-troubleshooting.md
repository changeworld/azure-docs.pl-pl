---
title: Rozwiązywanie problemów z programem System Center Data Protection Manager
description: W tym artykule odnajdujesz rozwiązania problemów, które mogą wystąpić podczas korzystania z Menedżera ochrony danych w centrum systemu.
ms.topic: troubleshooting
ms.date: 01/30/2019
ms.openlocfilehash: bcb30fa7eb3e05099761fc751b09a9fb16134e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75664746"
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Rozwiązywanie problemów z programem System Center Data Protection Manager

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z Menedżera ochrony danych.

Najnowsze informacje o wersji programu System Center Data Protection Manager można znaleźć w [dokumentacji programu System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Więcej informacji na temat pomocy technicznej dla Programu Data Protection Manager można uzyskać w [tej matrycy](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).

## <a name="error-replica-is-inconsistent"></a>Błąd: replika jest niespójna

Replika może być niespójna z następujących powodów:

- Zadanie tworzenia repliki kończy się niepowodzeniem.
- Występują problemy z arkuszem zmian.
- Mapa bitowa filtru poziomu woluminu zawiera błędy.
- Maszyna źródłowa wyłącza się nieoczekiwanie.
- Rejestr synchronizacji przepełnia się.
- Replika jest naprawdę niespójna.

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Aby usunąć niespójny stan, uruchom sprawdzanie spójności ręcznie lub zaplanuj codzienne sprawdzanie spójności.
- Upewnij się, że używasz najnowszej wersji programu Microsoft Azure Backup Server i Menedżera ochrony danych.
- Upewnij się, że ustawienie **Automatyczna spójność** jest włączone.
- Spróbuj ponownie uruchomić usługi z wiersza polecenia. Użyj `net stop dpmra` polecenia, `net start dpmra`po którym następuje .
- Upewnij się, że spełniasz wymagania dotyczące łączności sieciowej i przepustowości.
- Sprawdź, czy komputer źródłowy został nieoczekiwanie wyłączony.
- Upewnij się, że dysk jest w dobrej kondycji i że jest wystarczająco dużo miejsca dla repliki.
- Upewnij się, że nie ma żadnych zduplikowanych zadań tworzenia kopii zapasowych, które są uruchomione jednocześnie.

## <a name="error-online-recovery-point-creation-failed"></a>Błąd: tworzenie punktu odzyskiwania online nie powiodło się

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Upewnij się, że używasz najnowszej wersji agenta usługi Azure Backup.
- Spróbuj ręcznie utworzyć punkt odzyskiwania w obszarze zadań ochrony.
- Upewnij się, że uruchomisz sprawdzanie spójności w źródle danych.
- Upewnij się, że spełniasz wymagania dotyczące łączności sieciowej i przepustowości.
- Gdy dane repliki są w niespójnym stanie, utwórz punkt odzyskiwania dysku tego źródła danych.
- Upewnij się, że replika jest obecny i nie brakuje.
- Upewnij się, że replika ma wystarczająco dużo miejsca, aby utworzyć arkusz numeru sekwencyjny aktualizacji (USN).

## <a name="error-unable-to-configure-protection"></a>Błąd: nie można skonfigurować ochrony

Ten błąd występuje, gdy serwer Menedżera ochrony danych nie może skontaktować się z serwerem chronionym.

Aby rozwiązać ten problem, wykonaj następujące czynności:

- Upewnij się, że używasz najnowszej wersji agenta usługi Azure Backup.
- Upewnij się, że między serwerem Programu Data Protection Manager a serwerem chronionym istnieje łączność (sieć/zapora/serwer proxy).
- Jeśli chronisz serwer SQL, upewnij się, że **właściwość Właściwości logowania** > **NT AUTHORITY\SYSTEM** jest wyświetlana jako włączone ustawienie **sysadmin.**

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Błąd: serwer nie zarejestrowany jako określony w pliku poświadczeń przechowalni

Ten błąd występuje podczas procesu odzyskiwania danych serwera usługi Data Protection Manager/Azure Backup. Plik poświadczeń magazynu, który jest używany w procesie odzyskiwania nie należy do magazynu usług odzyskiwania dla serwera Data Protection Manager/Azure Backup.

Aby rozwiązać ten problem, wykonaj następujące kroki:

1. Pobierz plik poświadczeń magazynu z magazynu usług odzyskiwania, do którego jest zarejestrowany serwer Data Protection Manager/Azure Backup.
2. Spróbuj zarejestrować serwer w przechowalni przy użyciu ostatnio pobranego pliku poświadczeń przechowalni.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Błąd: Brak danych do odzyskania lub wybrany serwer nie jest serwerem Menedżera ochrony danych

Ten błąd występuje z następujących powodów:

- Żadne inne serwery Usługi Data Protection Manager/Azure Backup nie są zarejestrowane w magazynie usług odzyskiwania.
- Serwery nie przekazały jeszcze metadanych.
- Wybrany serwer nie jest serwerem Usługi Data Protection Manager/Azure Backup.

Gdy inne serwery Usługi Data Protection Manager/Azure Backup są zarejestrowane w magazynie usług odzyskiwania, wykonaj następujące kroki, aby rozwiązać ten problem:

1. Upewnij się, że jest zainstalowany najnowszy agent usługi Azure Backup.
2. Po upewnienie się, że najnowszy agent jest zainstalowany, poczekaj jeden dzień przed rozpoczęciem procesu odzyskiwania. Zadanie nocnej kopii zapasowej przekazuje metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane kopii zapasowej są następnie dostępne do odzyskiwania.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Błąd: pod warunkiem, że hasło szyfrowania nie pasuje do hasła dla serwera

Ten błąd występuje podczas procesu szyfrowania podczas odzyskiwania danych serwera usługi Data Protection Manager/Azure Backup. Hasło szyfrowania używane w procesie odzyskiwania nie jest zgodne z hasłem szyfrowania serwera. W rezultacie agent nie można odszyfrować danych i odzyskiwanie kończy się niepowodzeniem.

> [!IMPORTANT]
> Jeśli zapomnisz lub utracisz hasło szyfrowania, nie ma żadnych innych metod odzyskiwania danych. Jedyną opcją jest ponowne wygenerowanie hasła. Użyj nowego hasła, aby zaszyfrować przyszłe dane kopii zapasowej.
>
> Podczas odzyskiwania danych zawsze podaj ten sam hasło szyfrowania, które jest skojarzone z usługą Data Protection Manager/Azure Backup server.
>
