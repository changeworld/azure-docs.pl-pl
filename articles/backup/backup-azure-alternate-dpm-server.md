---
title: Odzyskiwanie danych z usługi Azure Backup Server
description: Odzyskaj dane, które użytkownik został objęty ochroną w magazynie usługi Recovery Services z dowolnego serwera usługi Azure Backup zarejestrowanych w tym magazynie.
services: backup
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: 770baeeacb5f3808eba05f9e262bcbca75c6baad
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705225"
---
# <a name="recover-data-from-azure-backup-server"></a>Odzyskiwanie danych z usługi Azure Backup Server
Można użyć usługi Azure Backup Server, aby odzyskać dane, które zostały kopii zapasowej w magazynie usługi Recovery Services. Proces może więc jest zintegrowana z konsoli zarządzania usługi Azure Backup Server i jest podobna do przepływu pracy odzyskiwania dla innych składników usługi Azure Backup.

> [!NOTE]
> Ten artykuł dotyczy [System Center Data Protection Manager 2012 R2 z pakietem zbiorczym aktualizacji 7 lub nowszym](https://support.microsoft.com/en-us/kb/3065246), połączone z [najnowszą wersję agenta usługi Kopia zapasowa Azure](https://aka.ms/azurebackup_agent).
>
>

Aby odzyskać dane z usługi Azure Backup Server:

1. Z **odzyskiwania** karty konsoli zarządzania usługi Azure Backup Server kliknij **"Dodaj zewnętrzny program DPM"** (w lewym górnym rogu ekranu).   
    ![Dodaj zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Pobierz nowy **magazynu poświadczeń** z magazynu skojarzonego z **usługi Azure Backup Server** w przypadku, gdy dane są odzyskiwane, wybierz zarejestrowany serwera usługi Azure Backup z listy serwerów kopia zapasowa Azure za pomocą usług Recovery Services magazynu, a następnie podaj **hasło szyfrowania** skojarzone z serwerem, którego dane są odzyskiwane.

    ![Poświadczenia zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Tylko serwery kopii zapasowej platformy Azure skojarzone z tym samym magazynie rejestracji można odzyskać dane siebie nawzajem.
   >
   >

    Po pomyślnym dodaniu zewnętrznych usługi Azure Backup Server, można przeglądać dane z serwera zewnętrznego i lokalne usługi Azure Backup Server z **odzyskiwania** kartę.
3. Przeglądaj listę dostępnych serwerów produkcyjnych chronione przez zewnętrzne usługi Azure Backup Server i wybierz odpowiednie źródło danych.

    ![Przeglądaj serwer zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wybierz **w miesiącu i roku** z **punktów odzyskiwania** listę rozwijaną, wybierz wymagane **Data Recovery** punkt odzyskiwania został utworzony, i wybierz **Czas odzyskiwania**.

    W dolnym okienku, który może być przeglądany i odzyskać do dowolnego miejsca i zostanie wyświetlona lista plików i folderów.

    ![Punkty odzyskiwania serwera programu DPM zewnętrzne](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kliknij prawym przyciskiem myszy odpowiedni element, a następnie kliknij przycisk **odzyskać**.

    ![Zewnętrzne odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Przegląd **odzyskać wybór**. Sprawdź dane i czas ich odzyskania kopii zapasowej, a także źródła, z której została utworzona kopia zapasowa. Jeśli zaznaczenie jest nieprawidłowa, kliknij przycisk **anulować** można przejść z powrotem do odzyskiwania kartę, aby wybrać punkt odzyskiwania odpowiednie. Jeśli zaznaczenie jest poprawny, kliknij przycisk **dalej**.

    ![Podsumowanie zewnętrzne odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wybierz **odzyskiwanie do lokalizacji alternatywnej**. **Przeglądaj** do poprawnej lokalizacji dla odzyskiwania.

    ![Zewnętrznej lokalizacji alternatywnej odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wybierz opcję związane z **Utwórz kopię**, **Pomiń**, lub **Zastąp**.

   * **Utwórz kopię** — tworzy kopię pliku, w przypadku kolizji nazw.
   * **Pomiń** — w przypadku kolizji nazw, nie może odzyskać pliku, co pozostawia oryginalnego pliku.
   * **Zastąp** — w przypadku kolizji nazw, zastępuje istniejącą kopię pliku.

     Wybierz odpowiednią opcję, aby **Przywróć zabezpieczenia**. Można zastosować ustawienia zabezpieczeń komputera docelowego, gdzie są odzyskiwane dane lub ustawienia zabezpieczeń, które były stosowane do produktu w momencie utworzenia punktu odzyskiwania.

     Określenie czy **powiadomień** jest wysyłana, po pomyślnym zakończeniu odzyskiwania.

     ![Zewnętrzny program DPM odzyskiwania powiadomienia](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Podsumowanie** ekranu zawiera listę opcji wybranych do tej pory. Po kliknięciu **"Odzyskiwanie"** , dane są odzyskiwane do lokalizacji odpowiednie w środowisku lokalnym.

    ![Podsumowanie opcji odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Zadania odzyskiwania mogą być monitorowane w **monitorowanie** kartę usługi Azure Backup Server.
   >
   >

    ![Monitorowanie odzyskiwania](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Możesz kliknąć pozycję **wyczyść zewnętrzny program DPM** na **odzyskiwania** kartę serwer DPM, aby usunąć widok zewnętrznego serwera programu DPM.

    ![Wyczyść zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Rozwiązywanie problemów z komunikatów o błędach
| Nie. | Komunikat o błędzie | Kroki rozwiązywania problemów |
|:---:|:--- |:--- |
| 1. |Ten serwer nie jest zarejestrowany w magazynie określonym przez poświadczenia magazynu. |**Przyczyna:** Ten błąd jest wyświetlany, gdy plik poświadczeń magazynu, które są wybrane nie należy do magazynu usługi Recovery Services, skojarzone z usługi Azure Backup Server, na którym jest podejmowana próba odzyskania. <br> **Rozwiązanie:** Pobierz plik poświadczeń magazynu z magazynu usługi Recovery Services, w której jest zarejestrowana serwera usługi Azure Backup. |
| 2. |Dane możliwe do odzyskania są niedostępne lub wybrany serwer nie jest serwerem DPM. |**Przyczyna:** Istnieją nie inne serwery kopia zapasowa Azure zarejestrowany w magazynie usługi Recovery Services lub serwery nie została jeszcze przesłana metadanych lub wybrany serwer nie jest usługi Azure Backup Server (przy użyciu systemu Windows Server lub klienta Windows). <br> **Rozwiązanie:** Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usługi Recovery Services, upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure. <br>Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usługi Recovery Services, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekaże metadane dla wszystkich chronionych kopii zapasowych w chmurze. Dane będą dostępne do odzyskania. |
| 3. |Żaden inny serwer DPM jest zarejestrowany w tym magazynie. |**Przyczyna:** Nie ma żadnych innych serwerów kopia zapasowa Azure, zarejestrowanych w magazynie, z którego są podejmowane próby odzyskiwania.<br>**Rozwiązanie:** Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usługi Recovery Services, upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure.<br>Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usługi Recovery Services, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekazuje metadane dla wszystkich chronionych kopii zapasowych w chmurze. Dane będą dostępne do odzyskania. |
| 4. |Podane hasło szyfrowania jest niezgodny z hasłem skojarzonym z następującym serwerem:  **\<nazwa serwera >** |**Przyczyna:** Hasło szyfrowania używany przy wysyłaniu szyfrowania danych, dane serwera Azure Backup, który jest odzyskiwany jest niezgodna z podane hasło szyfrowania. Agent nie może odszyfrować danych. Dlatego odzyskiwanie zakończy się niepowodzeniem.<br>**Rozwiązanie:** Podaj dokładnie tego samego hasła szyfrowania skojarzone z usługi Azure Backup Server, którego dane są odzyskiwane. |

## <a name="next-steps"></a>Następne kroki

Przeczytaj inne — często zadawane pytania:

- [Często zadawane pytania dotyczące](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure
- [Często zadawane pytania dotyczące](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
