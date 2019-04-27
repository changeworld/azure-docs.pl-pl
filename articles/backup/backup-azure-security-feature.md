---
title: Funkcje zabezpieczeń, aby pomóc w ochronie hybrydowych kopii zapasowych, które używają usługi Azure Backup
description: Dowiedz się, jak używać funkcji zabezpieczeń w usłudze Azure Backup, aby zabezpieczyć kopie zapasowe
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: 2ba94963238cd5ee96df5c178a072addc5ddd75e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620691"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkcje zabezpieczeń, aby pomóc w ochronie hybrydowych kopii zapasowych, które używają usługi Azure Backup
Coraz więcej wątpliwości dotyczących problemów z zabezpieczeniami, takich jak złośliwe oprogramowanie, przed oprogramowaniem wymuszającym okup i nieautoryzowanego dostępu. Te problemy dotyczące zabezpieczeń może być kosztowna, pod względem zarówno pieniądze, jak i dane. Aby zabezpieczyć się przed takimi atakami, kopia zapasowa Azure zapewnia funkcje zabezpieczeń, aby pomóc w ochronie hybrydowych kopii zapasowych. W tym artykule opisano, jak włączanie i używanie tych funkcji przy użyciu agenta usługi Azure Recovery Services i serwera usługi Azure Backup. Te funkcje obejmują:

- **Zapobieganie**. Zawsze wtedy, gdy jest wykonywana krytycznej operacji, takich jak zmiana hasła, zostanie dodany dodatkową warstwę uwierzytelniania. Tej weryfikacji jest zapewnienie, że takie operacje mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure.
- **Alerty**. Wiadomość e-mail z powiadomieniem są wysyłane do administratora subskrypcji, w każdym przypadku, gdy krytycznej operacji, takich jak usuwanie danych kopii zapasowej jest wykonywane. Tę wiadomość e-mail gwarantuje, że użytkownik jest powiadamiany szybko o takich czynności.
- **Odzyskiwanie**. Usunięte dane kopii zapasowej są przechowywane dodatkowe 14 dni od daty usuwania. Zapewnia to możliwość odzyskania danych w danym okresie czasu, więc nie ma bez utraty danych, nawet jeśli się dzieje w przypadku ataków. Ponadto większa liczba punktów odzyskiwania minimalne są przechowywane w celu ochrony przed uszkodzone dane.

> [!NOTE]
> Funkcje zabezpieczeń nie można włączyć, korzystając z infrastruktury jako usługi (IaaS) kopii zapasowej maszyny Wirtualnej. Te funkcje nie są jeszcze dostępne dla kopii zapasowych maszyn wirtualnych IaaS, aby umożliwić im korzystanie z nie ma żadnego wpływu. Funkcje zabezpieczeń powinny być włączone, tylko wtedy, gdy używane są: <br/>
>  * **Agent usługi Azure Backup**. Minimalna wersja agenta: 2.0.9052. Po włączeniu tych funkcji, należy uaktualnić do tej wersji agenta, aby wykonywać krytyczne operacje. <br/>
>  * **Usługa Azure Backup Server**. Minimalna usługi Azure Backup agent w wersji 2.0.9052 z usługi Azure Backup Server update 1. <br/>
>  * **System Center Data Protection Manager**. Minimalna usługi Azure Backup agent w wersji 2.0.9052 z programu Data Protection Manager 2012 R2 UR12 lub Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Te funkcje są dostępne tylko w przypadku magazynu usługi Recovery Services. Wszystkie nowo utworzone magazynów usługi Recovery Services te funkcje są włączone domyślnie. Dla istniejących magazynów usługi Recovery Services użytkowników, należy włączyć te funkcje wykonując kroki opisane w poniższej sekcji. Po funkcji są włączone, odnoszą się do wszystkich usług Recovery Services agent komputerów, wystąpienia usługi Azure Backup Server i serwery programu Data Protection Manager zarejestrowane w usłudze magazynu. Włączenie tego ustawienia jest to akcja jednorazowa, a po ich włączeniu nie można wyłączyć te funkcje.
>

## <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.
W przypadku tworzenia magazynu usługi Recovery Services, można użyć wszystkich funkcji zabezpieczeń. Jeśli pracujesz z istniejącego magazynu, włączyć funkcje zabezpieczeń, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal przy użyciu swoich poświadczeń platformy Azure.
2. Wybierz **Przeglądaj**i wpisz **usługi Recovery Services**.

    ![Zrzut ekranu usługi Azure portal opcja przeglądania](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services. Wybierz magazyn z tej listy. Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
3. Z listy elementów, który pojawia się w obszarze magazynu, w obszarze **ustawienia**, kliknij przycisk **właściwości**.

    ![Zrzut ekranu usługi Recovery Services vault opcje](./media/backup-azure-security-feature/vault-list-properties.png)
4. W obszarze **ustawienia zabezpieczeń**, kliknij przycisk **aktualizacji**.

    ![Zrzut ekranu usługi Recovery Services vault właściwości](./media/backup-azure-security-feature/security-settings-update.png)

    Uaktualnij łącze otwiera **ustawienia zabezpieczeń** bloku, który zawiera podsumowanie funkcji i można je włączyć.
5. Z listy rozwijanej **skonfigurowano usługę Azure Multi-Factor Authentication?**, wybierz wartość, aby upewnić się, jeśli włączono [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Jeśli jest włączona, zostanie wyświetlony monit do uwierzytelnienia za pomocą innego urządzenia (na przykład telefon komórkowy) podczas logowania się do witryny Azure portal.

   Podczas wykonywania krytycznej operacji w usłudze Kopia zapasowa, należy wprowadzić zabezpieczający numer PIN, dostępne w witrynie Azure portal. Włączanie usługi Azure Multi-Factor Authentication dodaje warstwę zabezpieczeń. Tylko autoryzowanym użytkownikom za pomocą prawidłowych poświadczeń platformy Azure i uwierzytelniony z drugiego urządzenia, może uzyskać dostęp do witryny Azure portal.
6. Aby zapisać ustawienia zabezpieczeń, wybierz pozycję **Włącz** i kliknij przycisk **Zapisz**. Możesz wybrać **Włącz** tylko wtedy, gdy wybierz wartość z zakresu od **skonfigurowano usługę Azure Multi-Factor Authentication?** listy w poprzednim kroku.

    ![Zrzut ekranu przedstawiający ustawienia zabezpieczeń](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Odzyskiwanie usuniętych danych kopii zapasowej
Kopia zapasowa zachowuje usunięte dane kopii zapasowej dodatkowych 14 dni i nie powoduje usunięcia natychmiast, jeśli **zatrzymywanie tworzenia kopii zapasowych i Usuń dane kopii zapasowej** operacja została wykonana. Aby przywrócić te dane w 14-dniowy okres, wykonaj następujące kroki w zależności od tego, czego używasz:

Aby uzyskać **agenta usług odzyskiwania Azure** użytkowników:

1. Jeśli komputer, na których były wykonywane kopie zapasowe jest nadal dostępna, ponownie włączyć ochronę źródeł danych usuniętych i użyj [odzyskiwanie danych na tym samym komputerze](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) w usłudze Azure Recovery Services można odzyskać stare punkty odzyskiwania.
2. Jeśli ten komputer nie jest dostępna, należy użyć [odzyskiwanie do alternatywnej maszynie](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) do pobrania tych danych za pomocą innego komputera usług Azure Recovery Services.

Aby uzyskać **usługi Azure Backup Server** użytkowników:

1. Jeśli serwer, na których były wykonywane kopie zapasowe jest nadal dostępna, ponownie włączyć ochronę źródeł danych usuniętych i użyj **Odzyskaj dane** funkcję, aby odzyskać wszystkie stare punkty odzyskiwania.
2. Jeśli ten serwer jest niedostępny, użyj [odzyskać dane z innego serwera usługi Azure Backup](backup-azure-alternate-dpm-server.md) używać innego wystąpienia usługi Azure Backup Server do pobrania tych danych.

Aby uzyskać **programu Data Protection Manager** użytkowników:

1. Jeśli serwer, na których były wykonywane kopie zapasowe jest nadal dostępna, ponownie włączyć ochronę źródeł danych usuniętych i użyj **Odzyskaj dane** funkcję, aby odzyskać wszystkie stare punkty odzyskiwania.
2. Jeśli ten serwer jest niedostępny, użyj [Dodaj zewnętrzny program DPM](backup-azure-alternate-dpm-server.md) pobrania tych danych przy użyciu innego serwera programu Data Protection Manager.

## <a name="prevent-attacks"></a>Zapobieganie atakom
Testy zostały dodane do upewnij się, że tylko uprawnieni użytkownicy mogą wykonywać różne operacje. Obejmują one dodanie dodatkowej warstwy uwierzytelniania i pozostawienie co najmniej zakres na potrzeby odzyskiwania.

### <a name="authentication-to-perform-critical-operations"></a>Uwierzytelnianie, aby wykonywać krytyczne operacje
W ramach dodanie dodatkowej warstwy uwierzytelniania krytyczne operacje, zostanie wyświetlony monit o wprowadź zabezpieczający numer PIN, gdy wykonujesz **Zatrzymaj ochronę za pomocą usuwania danych** i **zmiany hasła** operacji.

> [!NOTE]
> 
> Obecnie zabezpieczający numer pin nie jest obsługiwana dla **Zatrzymaj ochronę za pomocą usuwania danych** programu DPM i serwera usługi MAB.

Aby otrzymać ten kod PIN:

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazyn usługi Recovery Services** > **ustawienia** > **właściwości**.
3. W obszarze **zabezpieczający numer PIN**, kliknij przycisk **Generuj**. Spowoduje to otwarcie bloku, który zawiera numer PIN, aby wpisać w interfejsie użytkownika agenta usługi Azure Recovery Services.
    Ten numer PIN jest prawidłowy tylko pięć minut, a następnie pobiera wygenerowany automatycznie po upływie tego czasu.

### <a name="maintain-a-minimum-retention-range"></a>Obsługa zakresu przechowywania minimalnej
Aby upewnić się, że jest zawsze prawidłowe liczby punktów odzyskiwania dostępnych, zostały dodane następujące testy:

- Do przechowywania codziennych, co najmniej **siedem** dni przechowywania powinna być podejmowana z.
- Do przechowywania danych co tydzień, co najmniej **cztery** tygodni przechowywania powinna być podejmowana z.
- Do przechowywania miesięczne, co najmniej **trzy** miesięcy przechowywania powinna być podejmowana z.
- Do przechowywania roczne, co najmniej **jeden** powinna być podejmowana rok przechowywania.

## <a name="notifications-for-critical-operations"></a>Powiadomienia krytyczne operacje
Zazwyczaj podczas wykonywania krytycznej operacji administratora subskrypcji jest wysyłana wiadomość e-mail z powiadomieniem ze szczegółowymi informacjami na temat operacji. Dodatkowi adresaci wiadomości e-mail dla powiadomień można skonfigurować za pomocą witryny Azure portal.

Funkcje zabezpieczeń, wymienione w niniejszym artykule zapewniają mechanizmy ochrony przed atakami ukierunkowanymi. Co ważniejsze, jeśli wystąpi atak, te funkcje zapewniają możliwość odzyskania danych.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
| Zmiany zasad |Nie można modyfikować zasad tworzenia kopii zapasowej. Błąd: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj wykonać ponownie operację za jakiś czas. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |**Przyczyna:**<br/>Ten błąd jest dostarczany, gdy ustawienia zabezpieczeń są włączone, użytkownik próbuje zmniejszyć zakres przechowywania, poniżej wartości minimalnej wymienione powyżej i korzystasz z nieobsługiwanej wersji (obsługiwane wersje są określone w pierwszym Uwaga części tego artykułu). <br/>**Zalecana akcja:**<br/> W takim przypadku należy ustawić okres przechowywania danych powyżej co najmniej, podanego okresu (7 dni w przypadku codziennie, cztery tygodnie, co tydzień, trzy tygodnie, co miesiąc lub rok dla corocznych) aby kontynuować za pomocą zasad aktualizacji dotyczących. Opcjonalnie aktualizacji agenta kopii zapasowej serwera usługi Azure Backup i/lub programu DPM z pakietem zbiorczym aktualizacji wykorzystać wszystkie aktualizacje zabezpieczeń jest preferowanym podejściem. |
| Zmień hasło |Wprowadzony zabezpieczający numer PIN jest niepoprawny. (ID: 100130) Podaj poprawny zabezpieczający numer PIN do ukończenia tej operacji. |**Przyczyna:**<br/> Ten błąd jest dostarczany, po wprowadzeniu nieprawidłowe lub wygasłe zabezpieczający numer PIN podczas wykonywania krytycznej operacji (np. zmianą hasła). <br/>**Zalecana akcja:**<br/> Aby zakończyć operację, należy wprowadzić prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services > Ustawienia > Właściwości > Generuj zabezpieczający numer PIN. Ten numer PIN umożliwia zmianę hasła. |
| Zmień hasło |Operacja nie powiodła się. Identyfikator: 120002 |**Przyczyna:**<br/>Ten błąd jest dostarczany, gdy ustawienia zabezpieczeń są włączone, próbuje zmienić hasło i korzystasz z nieobsługiwanej wersji (prawidłowe wersje określone w pierwszym Uwaga części tego artykułu).<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do minimalnej wersji 2.0.9052 minimalne, serwer usługi Azure Backup do minimalnej update 1, i/lub serwer programu DPM ma minimalne programu DPM 2012 R2 UR12 lub DPM 2016 UR2 (Poniższe łącza pobierania), a następnie wprowadź prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usługi Recovery Services > Ustawienia > Właściwości > Generuj zabezpieczający numer PIN. Ten numer PIN umożliwia zmianę hasła. |

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Recovery Services vault](backup-azure-vms-first-look-arm.md) Aby włączyć te funkcje.
* [Pobierz najnowszą wersję agenta usług odzyskiwania Azure](https://aka.ms/azurebackup_agent) pomagające chronić komputery Windows i ochrony danych przed atakami.
* [Pobieranie najnowszych kopii zapasowych serwera Azure](https://aka.ms/latest_azurebackupserver) pomagające chronić obciążenia i ochrony danych przed atakami.
* [Pobierz UR12 for System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) lub [Pobierz UR2 dla programu System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) pomagające chronić obciążenia i ochrony danych przed atakami.
