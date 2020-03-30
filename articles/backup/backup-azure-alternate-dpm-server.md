---
title: Odzyskiwanie danych z serwera kopii zapasowych platformy Azure
description: Odzyskaj chronione dane do magazynu usług odzyskiwania z dowolnego serwera kopii zapasowych platformy Azure zarejestrowanego w tym magazynie.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173497"
---
# <a name="recover-data-from-azure-backup-server"></a>Odzyskiwanie danych z usługi Azure Backup Server

Za pomocą usługi Azure Backup Server można odzyskać dane, których kopię zapasową wykonaliśmy w magazynie usług odzyskiwania. Proces w tym celu jest zintegrowany z konsolą zarządzania usługi Azure Backup Server i jest podobny do przepływu pracy odzyskiwania dla innych składników usługi Azure Backup.

> [!NOTE]
> Ten artykuł dotyczy [programu System Center Data Protection Manager 2012 R2 z ur7 lub nowszym](https://support.microsoft.com/kb/3065246)w połączeniu z najnowszym [agentem usługi Azure Backup.](https://aka.ms/azurebackup_agent)
>
>

Aby odzyskać dane z serwera kopii zapasowych platformy Azure:

1. Na karcie **Odzyskiwanie** konsoli zarządzania serwera kopii zapasowych platformy Azure kliknij przycisk **"Dodaj zewnętrzny program DPM"** (w lewym górnym rogu ekranu).

    ![Dodawanie zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Pobierz nowe poświadczenia magazynu z magazynu **skojarzonego** z **serwerem kopii zapasowej platformy Azure,** w którym są odzyskiwane dane, wybierz serwer kopii zapasowej platformy Azure z listy serwerów kopii zapasowych platformy Azure zarejestrowanych w magazynie usług odzyskiwania i podaj **hasło szyfrowania skojarzone** z serwerem, którego dane są odzyskiwane.

    ![Zewnętrzne poświadczenia programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Tylko serwery kopii zapasowej platformy Azure skojarzone z tym samym magazynem rejestracji mogą odzyskać swoje dane.
   >
   >

    Po pomyślnym dodaniu zewnętrznego serwera kopii zapasowej platformy Azure można przeglądać dane serwera zewnętrznego i lokalnego serwera kopii zapasowej platformy Azure na karcie **Odzyskiwanie.**
3. Przejrzyj dostępną listę serwerów produkcyjnych chronionych przez zewnętrzny serwer kopii zapasowych platformy Azure i wybierz odpowiednie źródło danych.

    ![Przeglądaj zewnętrzny serwer DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wybierz **miesiąc i rok** z listy rozwijanej Punkty **odzyskiwania,** wybierz wymaganą datę **odzyskiwania** dla momentu utworzenia punktu odzyskiwania i wybierz **czas odzyskiwania**.

    W dolnym okienku zostanie wyświetlona lista plików i folderów, które można przeglądać i odzyskiwać w dowolnej lokalizacji.

    ![Zewnętrzne punkty odzyskiwania serwera programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kliknij prawym przyciskiem myszy odpowiedni element i kliknij polecenie **Odzyskaj**.

    ![Zewnętrzne odzyskiwanie programu DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Przejrzyj **wybór Odzyskiwanie**. Sprawdź dane i czas odzyskiwanej kopii zapasowej, a także źródło, z którego utworzono kopię zapasową. Jeśli zaznaczenie jest nieprawidłowe, kliknij przycisk **Anuluj,** aby powrócić do karty odzyskiwania, aby wybrać odpowiedni punkt odzyskiwania. Jeśli zaznaczenie jest poprawne, kliknij przycisk **Dalej**.

    ![Podsumowanie odzyskiwania programu DPM zewnętrznego](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wybierz **pozycję Odzyskaj do alternatywnej lokalizacji**. **Przejdź** do właściwej lokalizacji odzyskiwania.

    ![Zewnętrzna alternatywna lokalizacja odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wybierz opcję związaną z **tworzeniem kopii**, **Pomiń**lub **Zastąpienie**.

   * **Utwórz kopię** - tworzy kopię pliku, jeśli występuje kolizja nazw.
   * **Pomiń** - jeśli występuje kolizja nazw, nie odzyskuje pliku, który opuszcza oryginalny plik.
   * **Zastąp** — jeśli występuje kolizja nazw, zastępuje istniejącą kopię pliku.

     Wybierz odpowiednią opcję **przywracania zabezpieczeń**. Można zastosować ustawienia zabezpieczeń komputera docelowego, na którym dane są odzyskiwane, lub ustawienia zabezpieczeń, które miały zastosowanie do produktu w momencie utworzenia punktu odzyskiwania.

     Określ, czy **powiadomienie** jest wysyłane po pomyślnym zakończeniu odzyskiwania.

     ![Powiadomienia o odzyskiwaniu programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Na ekranie **Podsumowanie** wymieniono opcje wybrane do tej pory. Po kliknięciu **przycisku "Odzyskaj"** dane są odzyskiwane do odpowiedniej lokalizacji lokalnej.

    ![Podsumowanie opcji odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Zadanie odzyskiwania można monitorować na karcie **Monitorowanie** serwera kopii zapasowej platformy Azure.
   >
   >

    ![Monitorowanie odzyskiwania](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Aby usunąć widok zewnętrznego serwera **Recovery** programu DPM, można kliknąć przycisk **Wyczyść program DPM** zewnętrzny.

    ![Wyczyść zewnętrzny moduł DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Rozwiązywanie komunikatów o błędach

| Nie. | Komunikat o błędzie | Kroki rozwiązywania problemów |
|:---:|:--- |:--- |
| 1. |Ten serwer nie jest zarejestrowany w przechowalni określonej przez poświadczenia magazynu. |**Przyczyna:** Ten błąd pojawia się, gdy wybrany plik poświadczeń przechowalni nie należy do magazynu usług odzyskiwania skojarzonego z serwerem kopii zapasowych platformy Azure, na którym podejmowana jest próba odzyskania. <br> **Rozdzielczość:** Pobierz plik poświadczeń magazynu z magazynu usług odzyskiwania, do którego jest zarejestrowany serwer kopii zapasowej platformy Azure. |
| 2. |Dane odzyskiwalne nie są dostępne lub wybrany serwer nie jest serwerem programu DPM. |**Przyczyna:** Nie ma żadnych innych serwerów kopii zapasowej platformy Azure zarejestrowanych w magazynie usług odzyskiwania lub serwery nie przekazały jeszcze metadanych lub wybrany serwer nie jest serwerem kopii zapasowej platformy Azure (przy użyciu systemu Windows Server lub klienta systemu Windows). <br> **Rozdzielczość:** Jeśli istnieją inne serwery kopii zapasowych platformy Azure zarejestrowane w magazynie usług odzyskiwania, upewnij się, że jest zainstalowany najnowszy agent usługi Azure Backup. <br>Jeśli istnieją inne serwery kopii zapasowych platformy Azure zarejestrowane w magazynie usług odzyskiwania, poczekaj na dzień po instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekaże metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 3. |Żaden inny serwer programu DPM nie jest zarejestrowany w tym magazynie. |**Przyczyna:** Nie ma żadnych innych serwerów kopii zapasowych platformy Azure, które są zarejestrowane w magazynie, z którego jest podejmowana próba odzyskiwania.<br>**Rozdzielczość:** Jeśli istnieją inne serwery kopii zapasowych platformy Azure zarejestrowane w magazynie usług odzyskiwania, upewnij się, że jest zainstalowany najnowszy agent usługi Azure Backup.<br>Jeśli istnieją inne serwery kopii zapasowych platformy Azure zarejestrowane w magazynie usług odzyskiwania, poczekaj na dzień po instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekazuje metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 4. |Podane hasło szyfrowania nie jest zgodne z hasłem powiązanym z następującym serwerem: ** \<nazwa serwera>** |**Przyczyna:** Hasło szyfrowania używane w procesie szyfrowania danych z danych serwera kopii zapasowej platformy Azure, które są odzyskiwane, nie jest zgodne z dostarczonym hasłem szyfrowania. Agent nie może odszyfrować danych. W związku z tym odzyskiwanie nie powiedzie się.<br>**Rozdzielczość:** Podaj dokładnie ten sam hasło szyfrowania skojarzone z serwerem kopii zapasowej platformy Azure, którego dane są odzyskiwane. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Typowe pytania](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
