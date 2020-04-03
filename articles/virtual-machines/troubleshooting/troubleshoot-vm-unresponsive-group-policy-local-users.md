---
title: Maszyna wirtualna nie odpowiada podczas stosowania zasad "Użytkownicy lokalni zasad grupy & grupy"
description: Ten artykuł zawiera kroki, aby rozwiązać problemy, w przypadku gdy ekran ładowania jest zablokowany stosowania zasad podczas rozruchu na maszynie wirtualnej platformy Azure (VM).
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620859"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>Maszyna wirtualna nie odpowiada podczas stosowania zasad "Użytkownicy lokalni zasad grupy & grupy"

Ten artykuł zawiera kroki, aby rozwiązać problemy, w przypadku gdy ekran ładowania jest zablokowany stosowania zasad, podczas rozruchu, w maszynie wirtualnej platformy Azure (VM).

## <a name="symptom"></a>Objaw

Podczas korzystania z [diagnostyki rozruchu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) do wyświetlania zrzutu ekranu maszyny Wirtualnej, zobaczysz, że ekran jest zablokowany ładowania z komunikatem: *Stosowanie zasad użytkowników lokalnych i grup lokalnych zasad zasad zasad lokalnych zasad zasad zasad.*

![Tekst alternatywny: ekran z funkcją ładowania zasad Użytkownicy i grupy lokalne dotyczące stosowania zasad grupy (Windows Server 2012).](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Tekst alternatywny: ekran z funkcją ładowania zasad Użytkowników i grup lokalnych zasad stosowania zasad grupy (Windows Server 2012 R2).](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>Przyczyna

Objawy tego zamrożenia są spowodowane wadą kodu w bibliotece łącza dynamicznego usługi profilu systemu Windows (*profsvc.dll*).

> [!NOTE]
> Ta wada dotyczy tylko systemów Windows Server 2012 i Windows Server 2012 R2.

### <a name="the-policy-in-question"></a>Omawiana polityka

Stosowane zasady, które nie zakończą swoich procesów, to:

* *Konfiguracja komputera\Zasady\Szablony administracyjne\Profile systemowe/użytkownika\Usuń profile użytkowników starsze niż określona liczba dni po ponownym uruchomieniu systemu*

Ta zasada zostanie zawiesina tylko wtedy, gdy spełnione jest sześć następujących warunków:

* Usuń *profile użytkowników starsze niż określona liczba dni w zasadach ponownego uruchamiania systemu* jest włączona.
* Masz profile, które spełniły wymagania wiekowe, aby wymagać czyszczenia.
* Masz składniki, które zostały zarejestrowane do usuwania powiadomień o profilach.
* Składniki dokonują wywołań (bezpośrednich lub pośrednich), które muszą uzyskać dane ze składników Programu Kontroli usług (SCM) systemu Windows, takich jak Start, Stop lub Query informacje o usłudze.
* Masz usługę skonfigurowaną do uruchamiania jako *automatyczna*.
* Ta usługa jest ustawiona do uruchamiania w kontekście konta domeny (w przeciwieństwie do korzystania z wbudowanego konta, takiego jak system lokalny).

### <a name="the-code-defect"></a>Wada kodu

Wada kodu jest spowodowana przez Service Control Manager (SCM) i usługi profilu, które próbują jednocześnie zastosować blokady. Blokady istnieją, aby zapobiec wielu usługom wprowadzania zmian na tych samych danych w tym samym czasie, co mogłoby spowodować uszkodzenie. Zwykle wiele żądań blokady nie spowoduje problemu. Jednak ponieważ dzieje się to podczas rozruchu, żadna z usług nie może ukończyć swoich procesów, ponieważ utknęły czekając na siebie nawzajem.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>Błąd systemu operacyjnego 5880648 — zakleszczenia menedżera sterowania usługami z zasadami "Usuwanie profili użytkowników przy ponownym uruchomieniu"

Istnieją dwie akcje, które nakładają się tak, że:

* Akcja 1 przejmuje blokadę profilu, ale nie nabyła jeszcze blokady SCM.

  **I**

* Akcja 2 przejmuje blokadę SCM, ale nie nabyła jeszcze blokady profilu.

Po wystąpieniu tego zakleszczenia próba uzyskania drugiej wymaganej blokady zawiesza akcję.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>Akcja 1 - Powiadomienie o usunięciu starego profilu (ma **blokadę profilu,** wymaga **blokady SCM)**

1. Najpierw zasady ustawione na usuwanie starych profili uzyskuje blokadę usługi profilu wewnętrznego.

   * Ta blokada jest tam, aby zapobiec dwa wątki interakcji z profilami, podczas gdy *operacja usuwania* jest postęp.

2. Zasady znajdują profile, które są wystarczająco stare, aby można je było usunąć.
3. W ramach usuwania profilu składnik, który został zarejestrowany dla powiadomień o usunięciach profilu, próbuje **uruchomić usługę**.
4. Przed uruchomieniem usługi Service Control Manager (SCM) musi uzyskać **wewnętrzną blokadę SCM** utrzymywaną przez wątki w **akcji 2**.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>Akcja 2 - Obciążenie/tworzenie profilu dla danych specyficznych dla użytkownika (ma **blokadę SCM,** wymaga **blokady profilu)**

1. Przy starcie SCM musi zamówić wszystkie usługi *automatycznego uruchamiania* przez ich grupę, a także wszelkie usługi, od których te usługi są zależne.

2. **SCM uzyskuje wewnętrzną blokadę SCM służącą** do kontrolowania dostępu do uruchamiania, zatrzymywania lub konfigurowania usług podczas zamawiania usług.

3. Gdy usługi są w porządku, SCM pętli za pośrednictwem każdej usługi i uruchamia go.

4. Jeśli usługa jest uruchomiona w kontekście konta domeny, profil musi zostać załadowany lub utworzony dla konta domeny, aby mógł przechowywać dane określone użytkownika.

5. To żądanie jest wysyłane do **usługi profilu**.

6. Usługa profilu wymaga dostępu do **blokady wewnętrznej** nabytej w **akcji 1**.

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Omówienie procesu

1. Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu
2. Włącz kolekcję konsoli szeregowej i zrzutu pamięci
3. Odbuduj maszynę wirtualną
4. Zbieranie pliku zrzutu pamięci

   > [!NOTE]
   > W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Rozwiązywanie tego problemu będzie rozwiązywać problemy w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Użyj [kroków 1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z usługi Podłączanie pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Włącz kolekcję konsoli szeregowej i zrzutu pamięci

Aby włączyć zbieranie zrzutów pamięci i konsolę szeregową, uruchom poniższy skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   * Włącz konsolę szeregową:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Sprawdź, czy wolne miejsce na dysku systemu operacyjnego jest tak samo jak rozmiar pamięci (RAM) na maszynie wirtualnej.

   * Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać się do dowolnego dysku danych dołączonego do maszyny Wirtualnej, który ma wystarczającą ilość wolnego miejsca. Aby zmienić lokalizację, zastąp `%SystemRoot%` literą dysku (na przykład "F:") dysku danych w poniższych poleceniach.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Sugerowana konfiguracja umożliwiająca wysyp systemu operacyjnego

**Obciążenie uszkodzonego dysku systemu operacyjnego:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Włącz na ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Włącz na ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>Odbuduj maszynę wirtualną

Użyj [kroku 5 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby ponownie zmontować maszynę wirtualną.

### <a name="collect-the-memory-dump-file"></a>Zbieranie pliku zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii i skontaktować się z pomocą techniczną z plikiem zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Podłączanie dysku systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Użyj kroków [1-3 poleceń naprawy maszyny Wirtualnej,](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) aby przygotować nową maszynę wirtualną naprawy.

2. Korzystanie z usługi Podłączanie pulpitu zdalnego połącz się z maszyną wirtualną naprawy.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Znajdź plik zrzutu i prześlij zgłoszenie pomocy technicznej

1. Na maszynie wirtualnej naprawy przejdź do folderu systemu Windows na podłączonym dysku systemu operacyjnego. Jeśli literą sterownika przypisaną do dołączonego dysku systemu operacyjnego jest F, należy przejść do pliku F:\Windows.

2. Zlokalizuj plik memory.dmp, a następnie [prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) z plikiem zrzutu pamięci.

3. Jeśli masz problemy ze zlokalizowaniem pliku memory.dmp, możesz użyć [niemaskowanych wywołań przerwania (NMI) w konsoli szeregowej.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) Możesz postępować zgodnie z przewodnikiem, aby [wygenerować plik zrzutu jądra lub kompletnego zrzutu awaryjnego](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) przy użyciu wywołań NMI.
