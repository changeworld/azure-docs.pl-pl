---
title: Odzyskiwanie danych z serwera usługi Kopia zapasowa Azure
description: Odzyskiwanie danych, który był chroniony w magazynie usług odzyskiwania z dowolnego serwera kopii zapasowej Azure zarejestrowany do tego magazynu.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 8559532f873e8073e736f881374fec1c080d08c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604407"
---
# <a name="recover-data-from-azure-backup-server"></a>Odzyskiwanie danych z usługi Azure Backup Server
Możesz użyć serwera usługi Kopia zapasowa Azure, aby odzyskać dane, które zostały dodane do kopii w magazynie usług odzyskiwania. Proces może więc jest zintegrowana w konsoli zarządzania serwerem kopia zapasowa Azure i jest podobny do przepływu pracy odzyskiwania dla innych składników usługi Kopia zapasowa Azure.

> [!NOTE]
> Ten artykuł dotyczy [System Center Data Protection Manager 2012 R2 z pakietem UR7 lub nowszej] (https://support.microsoft.com/en-us/kb/3065246), połączeniu z [najnowsza wersja agenta usługi Kopia zapasowa Azure](http://aka.ms/azurebackup_agent).
>
>

Aby odzyskać dane z serwera usługi Kopia zapasowa Azure:

1. Z **odzyskiwania** karty konsoli zarządzania serwera usługi Kopia zapasowa Azure kliknij **"Dodaj zewnętrzny program DPM"** (w lewym górnym rogu ekranu).   
    ![Dodaj zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Pobieranie nowych **magazynu poświadczeń** z magazynu skojarzone z **serwer kopii zapasowej Azure** w przypadku, gdy dane są odzyskiwane, wybierz serwer kopii zapasowej Azure z listy serwerów kopia zapasowa Azure zarejestrowany z usług odzyskiwania magazynu i podaj **hasło szyfrowania** skojarzone z serwerem, którego dane są odzyskiwane.

    ![Poświadczenia zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Tylko serwery kopia zapasowa Azure skojarzony z tym samym magazynem rejestracji można odzyskać dane innych osób.
   >
   >

    Po pomyślnym dodaniu zewnętrznego serwera kopii zapasowej Azure można przeglądać dane z serwera zewnętrznego i serwera lokalnego kopia zapasowa Azure z **odzyskiwania** kartę.
3. Przeglądaj listę dostępnych serwerów produkcyjnych chronione przez serwer zewnętrzny kopia zapasowa Azure i wybierz odpowiednie źródło danych.

    ![Przeglądaj serwera zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wybierz **miesiąc i rok** z **punktów odzyskiwania** listy rozwijanej, wybierz wymagane **Data Recovery** gdy punkt odzyskiwania został utworzony i wybierz **Czasu odzyskiwania**.

    Zostanie wyświetlona lista plików i folderów w okienku u dołu, który może być przeglądany i odzyskać w dowolnej lokalizacji.

    ![Punkty odzyskiwania serwera DPM zewnętrzne](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kliknij prawym przyciskiem myszy odpowiedni element, a następnie kliknij przycisk **odzyskać**.

    ![Zewnętrzne odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Przegląd **odzyskać wybór**. Sprawdź dane i czas kopia zapasowa ma zostać przeprowadzone odzyskiwanie, a także źródła, z którego została utworzona kopia zapasowa. Jeśli zaznaczenie jest nieprawidłowa, kliknij przycisk **anulować** aby wrócić do karty odzyskiwanie wybierz punkt odzyskiwania odpowiednie. Jeśli zaznaczenie jest poprawny, kliknij przycisk **dalej**.

    ![Podsumowanie zewnętrznych odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wybierz **Odzyskaj do lokalizacji alternatywnej**. **Przeglądaj** do poprawnej lokalizacji dla odzyskiwania.

    ![Zewnętrznej lokalizacji alternatywnej odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wybierz opcję związane z **utworzyć kopię**, **Pomiń**, lub **Zastąp**.

   * **Utwórz kopię** — tworzy kopię pliku, jeśli istnieje kolizję nazw.
   * **Pomiń** — Jeśli istnieje kolizję nazw nie odzyskać pliku, co pozostawia oryginalnego pliku.
   * **Zastąp** — Jeśli istnieje kolizję nazw zastępuje istniejącą kopię pliku.

     Wybierz odpowiednią opcję, aby **Przywróć zabezpieczenia**. Możesz zastosować ustawienia zabezpieczeń komputera docelowego, w którym dane są odzyskiwane lub ustawienia zabezpieczeń, które były stosowane do produktu w chwili tworzenia punktu odzyskiwania.

     Zidentyfikuj czy **powiadomień** jest wysyłana, po pomyślnym zakończeniu odzyskiwania.

     ![Powiadomienia odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Podsumowanie** ekranu wymieniono opcje wybrane do tej pory. Po kliknięciu **"Odzyskać"**, dane zostaną odzyskane do lokalizacji lokalnego odpowiednie.

    ![Podsumowanie opcji odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Zadania odzyskiwania mogą być monitorowane w **monitorowanie** kartę Serwer kopii zapasowej Azure.
   >
   >

    ![Monitorowanie odzyskiwania](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Możesz kliknąć **wyczyść zewnętrzny program DPM** na **odzyskiwania** kartę serwer DPM, aby usunąć widok zewnętrznego serwera programu DPM.

    ![Wyczyść zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Rozwiązywanie problemów z komunikatów o błędach
| Nie. | Komunikat o błędzie | Kroki rozwiązywania problemów |
|:---:|:--- |:--- |
| 1. |Ten serwer nie jest zarejestrowany w magazynie określonym przez poświadczenie magazynu. |**Przyczyna:** ten błąd jest wyświetlany, gdy plik poświadczeń magazynu, które są wybrane nie należy do magazynu usług odzyskiwania skojarzonego z serwerem kopii zapasowej Azure, na którym nastąpiła odzyskiwania. <br> **Rozwiązanie:** Pobierz plik poświadczeń magazynu usług odzyskiwania magazynu jest zarejestrowana serwer kopii zapasowej Azure. |
| 2. |Dane możliwe do odzyskania są niedostępne albo wybrany serwer nie jest serwerem DPM. |**Przyczyna:** ma żadnych innych serwerów kopia zapasowa Azure zarejestrowany w magazynie usług odzyskiwania serwerów nie zostały jeszcze przekazane metadanych lub wybrany serwer nie jest serwerem kopia zapasowa Azure (alias systemu Windows Server lub klienta systemu Windows). <br> **Rozwiązanie:** Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowany w magazynie usług odzyskiwania, upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure. <br>Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usług odzyskiwania, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekaże metadanych dla wszystkich chronionych kopii zapasowych w chmurze. Dane będą dostępne do odzyskania. |
| 3. |Żaden inny serwer DPM jest zarejestrowany w tym magazynie. |**Przyczyna:** Brak innych Azure kopii zapasowej serwerów zarejestrowanych w magazynie, z którego jest próby odzyskiwania.<br>**Rozwiązanie:** Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowany w magazynie usług odzyskiwania, upewnij się, że zainstalowano najnowszą wersję agenta usługi Kopia zapasowa Azure.<br>Jeśli istnieją inne serwery kopia zapasowa Azure zarejestrowanych w magazynie usług odzyskiwania, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekazuje metadane dla wszystkich chronionych kopii zapasowych w chmurze. Dane będą dostępne do odzyskania. |
| 4. |Podane hasło szyfrowania nie jest zgodne z hasłem skojarzonym z następującym serwerem: **<server name>** |**Przyczyna:** używany w procesie szyfrowania danych z serwera kopii zapasowej Azure danych są odzyskiwane hasło szyfrowania jest niezgodny z podane hasło szyfrowania. Agent jest do odszyfrowania danych. Dlatego odzyskiwanie zakończy się niepowodzeniem.<br>**Rozwiązanie:** Podaj dokładnie tego samego hasła szyfrowania skojarzonego z serwerem kopia zapasowa Azure, którego dane są odzyskiwane. |

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Dlaczego nie można dodać zewnętrznego serwera programu DPM po zainstalowaniu pakietu zbiorczego aktualizacji 7 i najnowsza wersja agenta usługi Kopia zapasowa Azure?

Dla serwerów programu DPM ze źródłami danych, które są chronione w chmurze (za pomocą pakietu zbiorczego aktualizacji starsze niż pakiet zbiorczy aktualizacji 7), musisz poczekać co najmniej jeden dzień po zainstalowaniu pakietu zbiorczego aktualizacji 7 i najnowsza wersja agenta usługi Kopia zapasowa Azure, aby uruchomić **dodawania zewnętrznego programu DPM serwera**. Okres czasu jeden dzień jest potrzebny do przekazywania metadanych grup ochrony programu DPM na platformie Azure. Po raz pierwszy przez nocne zadanie przekazaniu metadanych grupy ochrony.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Co to jest minimalna wersja agenta usług odzyskiwania Microsoft Azure potrzebne?

Minimalna wersja agenta usług odzyskiwania Microsoft Azure lub agenta usługi Kopia zapasowa Azure, wymagane do włączenia tej funkcji to 2.0.8719.0.  Aby wyświetlić wersję agenta: Otwórz Panel sterowania **>** elementy Panelu sterowania wszystkie **>** programy i funkcje **>** Agent usług odzyskiwania Microsoft Azure. Jeśli wersja jest mniejsza niż 2.0.8719.0, Pobierz i zainstaluj [najnowsza wersja agenta usługi Kopia zapasowa Azure](https://go.microsoft.com/fwLink/?LinkID=288905).

![Wyczyść zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Następne kroki:
• [Azure często zadawane pytania dotyczące tworzenia kopii zapasowej](backup-azure-backup-faq.md)
