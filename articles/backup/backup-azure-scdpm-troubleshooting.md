---
title: Rozwiązywanie problemów z programu System Center Data Protection Manager w usłudze Azure Backup | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów w System Center Data Protection Manager.
services: backup
documentationcenter: ''
author: adigan
manager: shreeshd
editor: ''
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;adigan
ms.openlocfilehash: 8f8117b216dcbda217bc433e643090e8eb47cf57
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-system-center-data-protection-manager"></a>Rozwiązywanie problemów z programem System Center Data Protection Manager

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z programu Data Protection Manager.

Najnowsze informacje o wersji programu System Center Data Protection Manager, zobacz [dokumentacji programu System Center](https://docs.microsoft.com/system-center/dpm/dpm-release-notes?view=sc-dpm-2016). Dowiedz się więcej o obsłudze programu Data Protection Manager w [tej macierzy](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2016).


## <a name="error-replica-is-inconsistent"></a>Błąd: Replika jest niespójna

Repliki mogą być niezgodne z następujących powodów:
- Zadanie tworzenia repliki zakończy się niepowodzeniem.
- Występują problemy z dziennika zmian.
- Mapy bitowej filtru poziomu wolumin zawiera błędy.
- Nieoczekiwane zamknięcie maszyny źródłowej.
- Przepełnienie w dzienniku synchronizacji.
- Replika jest niespójna naprawdę.

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Aby usunąć stan niespójna, uruchom sprawdzanie spójności ręcznie lub zaplanować codzienne sprawdzanie spójności.
- Upewnij się, że używasz najnowszej wersji programu Microsoft Azure Backup Server i programu Data Protection Manager.
- Upewnij się, że **automatycznej kontroli spójności** ustawienie jest włączone.
- Spróbuj ponownie uruchomić usługi z wiersza polecenia. Użyj `net stop dpmra` polecenia następuje `net start dpmra`.
- Upewnij się, że realizację wymagania dotyczące łączności i przepustowości sieci.
- Sprawdź, czy maszyna źródłowa została nieoczekiwanie wyłączony.
- Upewnij się, że dysk jest w dobrej kondycji i że istnieje wystarczająca ilość miejsca dla replik.
- Upewnij się, że nie istnieją zduplikowane zadania tworzenia kopii zapasowych uruchomionych współbieżnie.

## <a name="error-online-recovery-point-creation-failed"></a>Błąd: Tworzenie punktu odzyskiwania w trybie Online nie powiodło się.

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Upewnij się, że używasz najnowszej wersji agenta usługi Kopia zapasowa Azure.
- Spróbuj ręcznie utworzyć punkt odzyskiwania w obszarze zadanie ochrony.
- Upewnij się, że Uruchom sprawdzanie spójności w źródle danych.
- Upewnij się, że realizację wymagania dotyczące łączności i przepustowości sieci.
- Gdy dane repliki są w stanie niespójnym, Utwórz punkt odzyskiwania dysku tego źródła danych.
- Upewnij się, że replika jest obecny i nie brakuje.
- Upewnij się, że replika jest wystarczająca ilość miejsca do utworzenia dziennika (USN) numer sekwencji aktualizacji.

## <a name="error-unable-to-configure-protection"></a>Błąd: Nie można skonfigurować ochrony

Ten błąd występuje, gdy serwer programu Data Protection Manager nie może skontaktować się z serwera chronionego. 

Aby rozwiązać ten problem, wykonaj następujące czynności:
- Upewnij się, że używasz najnowszej wersji agenta usługi Kopia zapasowa Azure.
- Upewnij się, że istnieje łączność (/ zaporą/serwera proxy sieci) między serwerem programu Data Protection Manager i na serwerze chronionym.
- Jeśli chronisz serwer SQL, upewnij się, że **właściwości logowania** > **NT AUTHORITY\SYSTEM** pokazuje właściwości **sysadmin** ustawienie jest włączone.

## <a name="error-server-not-registered-as-specified-in-vault-credential-file"></a>Błąd: Serwer nie zarejestrowany jako określony w pliku poświadczeń magazynu

Ten błąd występuje podczas procesu odzyskiwania dla danych serwera kopii zapasowej Menedżera/Azure ochrony danych. Plik poświadczeń magazynu, który jest używany w procesie odzyskiwania nie należy do magazynu usług odzyskiwania dla serwera kopii zapasowej Menedżera/Azure ochrony danych.

Aby rozwiązać ten problem, wykonaj następujące kroki:
1. Pobierz plik poświadczeń magazynu z magazynu usług odzyskiwania, w której jest zarejestrowana serwera kopii zapasowej Menedżera/Azure ochrony danych.
2. Spróbuj zarejestrować serwer w magazynie przy użyciu najbardziej ostatnio pobrany plik poświadczeń magazynu.

## <a name="error-no-recoverable-data-or-selected-server-not-a-data-protection-manager-server"></a>Błąd: Nie dane możliwe do odzyskania lub wybrany serwer nie jest serwerem programu Data Protection Manager

Ten błąd występuje z następujących powodów:
- Nie ma innych serwerów kopii zapasowej Menedżera/Azure ochrony danych zarejestrowanych w magazynie usług odzyskiwania.
- Serwery jeszcze nie zostały jeszcze przekazane metadanych.
- Wybrany serwer nie jest serwerem kopii zapasowej Menedżera/Azure ochrony danych.

W przypadku innych serwerów kopii zapasowej Menedżera/Azure ochrony danych są zarejestrowane w magazynie usług odzyskiwania, wykonaj następujące kroki, aby rozwiązać ten problem:
1. Upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure.
2. Po upewnieniu się, że zainstalowano najnowszą wersję agenta, poczekaj jeden dzień przed rozpoczęciem procesu odzyskiwania. Zadanie tworzenia kopii zapasowej co noc przekazuje metadanych dla wszystkich chronionych kopii zapasowych w chmurze. Dane kopii zapasowej jest dostępna dla odzyskiwania.

## <a name="error-provided-encryption-passphrase-doesnt-match-passphrase-for-server"></a>Błąd: Hasło podane szyfrowania nie pasuje do hasła dla serwera

Ten błąd występuje podczas procesu szyfrowania, gdy odzyskiwanie danych serwera kopii zapasowej Menedżera/Azure ochrony danych. Hasło szyfrowania, który jest używany w procesie odzyskiwania nie jest zgodny hasło szyfrowania serwera. W związku z tym agentem nie może odszyfrować danych i odzyskiwanie nie powiedzie się.

> [!IMPORTANT]
> Jeśli zapomnisz lub utratę hasło szyfrowania, istnieją inne metody do odzyskiwania danych. Jest to jedyna opcja można ponownie wygenerować hasła. Użyj nowe hasło do szyfrowania danych kopii zapasowych w przyszłości.
>
> Podczas odzyskiwania danych zawsze podać to samo hasło szyfrowania skojarzoną z serwera kopii zapasowej Menedżera/Azure ochrony danych. 
>
