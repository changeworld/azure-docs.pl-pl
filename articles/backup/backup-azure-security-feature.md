---
title: Funkcje zabezpieczeń chroniące hybrydowe kopie zapasowe
description: Dowiedz się, jak korzystać z funkcji zabezpieczeń w usłudze Azure Backup w celu zwiększenia bezpieczeństwa kopii zapasowych
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586397"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkcje zabezpieczeń ułatwiające ochronę hybrydowych kopii zapasowych korzystających z usługi Azure Backup

Obawy dotyczące problemów z bezpieczeństwem, takich jak złośliwe oprogramowanie, ransomware i włamania, są coraz większe. Te problemy z bezpieczeństwem mogą być kosztowne, zarówno pod względem pieniędzy, jak i danych. Aby zabezpieczyć się przed takimi atakami, usługa Azure Backup udostępnia teraz funkcje zabezpieczeń ułatwiające ochronę hybrydowych kopii zapasowych. W tym artykule opisano, jak włączyć i korzystać z tych funkcji przy użyciu agenta usług Azure Recovery Services i usługi Azure Backup Server. Między innymi są to następujące funkcje:

- **Zapobieganie**. Dodatkowa warstwa uwierzytelniania jest dodawana za każdym razem, gdy wykonywana jest operacja krytyczna, taka jak zmiana hasła. Ta weryfikacja ma na celu zapewnienie, że takie operacje mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure.
- **Alerty**. Powiadomienie e-mail jest wysyłane do administratora subskrypcji za każdym razem, gdy wykonywana jest krytyczna operacja, taka jak usuwanie danych kopii zapasowej. Ten e-mail gwarantuje, że użytkownik zostanie szybko powiadomiony o takich działaniach.
- **Odzyskiwanie**. Usunięte dane kopii zapasowej są przechowywane przez dodatkowe 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie czasu, więc nie ma utraty danych, nawet jeśli dojdzie do ataku. Ponadto większa liczba minimalnych punktów odzyskiwania są utrzymywane w celu ochrony przed uszkodzonymi danymi.

> [!NOTE]
> Funkcje zabezpieczeń nie powinny być włączone, jeśli używasz infrastruktury jako usługi (IaaS) kopii zapasowej maszyny Wirtualnej. Te funkcje nie są jeszcze dostępne dla kopii zapasowej maszyny Wirtualnej IaaS, więc włączenie ich nie będzie miało żadnego wpływu. Funkcje zabezpieczeń powinny być włączone tylko wtedy, gdy używasz: <br/>
>  * **Agent kopii zapasowej platformy Azure**. Minimalna wersja agenta 2.0.9052. Po włączeniu tych funkcji należy uaktualnić do tej wersji agenta, aby wykonać operacje krytyczne. <br/>
>  * **Serwer kopii zapasowych platformy Azure**. Minimalny agent usługi Azure Backup w wersji 2.0.9052 z aktualizacją 1 serwera kopii zapasowych platformy Azure. <br/>
>  * **System Center Data Protection Manager**. Minimalny agent usługi Azure Backup w wersji 2.0.9052 z menedżerem ochrony danych 2012 R2 UR12 lub Menedżerem ochrony danych 2016 UR2. <br/>


> [!NOTE]
> Te funkcje są dostępne tylko dla magazynu usług odzyskiwania. Wszystkie nowo utworzone magazyny usług odzyskiwania mają te funkcje domyślnie włączone. W przypadku istniejących magazynów usług odzyskiwania użytkownicy włączają te funkcje, wykonując kroki wymienione w poniższej sekcji. Po włączeniu funkcji mają one zastosowanie do wszystkich komputerów agenta usług odzyskiwania, wystąpień serwera kopii zapasowych platformy Azure i serwerów Menedżera ochrony danych zarejestrowanych w przechowalni. Włączenie tego ustawienia jest działaniem jednorazowym i nie można wyłączyć tych funkcji po ich włączeniu.
>

## <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Jeśli tworzysz magazyn usług odzyskiwania, możesz użyć wszystkich funkcji zabezpieczeń. Jeśli pracujesz z istniejącym magazynem, włącz funkcje zabezpieczeń, wykonując następujące kroki:

1. Zaloguj się do witryny Azure portal przy użyciu poświadczeń platformy Azure.
2. Wybierz **pozycję Przeglądaj**i wpisz polecenie **Usługi odzyskiwania**.

    ![Zrzut ekranu przedstawiający opcję Przeglądania portalu Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services. Z tej listy wybierz przechowalnię. Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
3. Z listy elementów wyświetlanych pod przechowalnią w obszarze **Ustawienia**kliknij pozycję **Właściwości**.

    ![Zrzut ekranu przedstawiający opcje przechowalni usług odzyskiwania](./media/backup-azure-security-feature/vault-list-properties.png)
4. W obszarze **Ustawienia zabezpieczeń**kliknij pozycję **Aktualizuj**.

    ![Zrzut ekranu przedstawiający właściwości magazynu usług odzyskiwania](./media/backup-azure-security-feature/security-settings-update.png)

    Łącze aktualizacji otwiera blok **Ustawienia zabezpieczeń,** który zawiera podsumowanie funkcji i umożliwia ich włączenie.
5. Z listy **rozwijanej Czy skonfigurowano uwierzytelnianie wieloskładnikowe platformy Azure?**, wybierz wartość, aby potwierdzić, jeśli włączono [uwierzytelnianie wieloskładnikowe platformy Azure](../active-directory/authentication/multi-factor-authentication.md). Jeśli jest włączona, zostaniesz poproszony o uwierzytelnienie z innego urządzenia (na przykład telefonu komórkowego) podczas logowania się do witryny Azure portal.

   Podczas wykonywania operacji krytycznych w kopii zapasowej, należy wprowadzić kod PIN zabezpieczeń, dostępne w witrynie Azure portal. Włączenie uwierzytelniania wieloskładnikowego platformy Azure dodaje warstwę zabezpieczeń. Tylko autoryzowani użytkownicy z prawidłowymi poświadczeniami platformy Azure i uwierzytelnieni z drugiego urządzenia mogą uzyskać dostęp do witryny Azure portal.
6. Aby zapisać ustawienia zabezpieczeń, wybierz pozycję **Włącz** i kliknij przycisk **Zapisz**. Możesz wybrać **włącz** tylko po wybraniu wartości z listy **Czy skonfigurowano uwierzytelnianie wieloskładnikowe platformy Azure** w poprzednim kroku.

    ![Zrzut ekranu przedstawiający ustawienia zabezpieczeń](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Odzyskiwanie usuniętych danych kopii zapasowej

Kopia zapasowa zachowuje usunięte dane kopii zapasowej przez dodatkowe 14 dni i nie usuwa ich natychmiast po wykonaniu operacji **Zatrzymaj kopię zapasową z usunięciem danych kopii zapasowej.** Aby przywrócić te dane w okresie 14 dni, należy wykonać następujące kroki, w zależności od tego, czego używasz:

Dla użytkowników **agenta usług Azure Recovery Services:**

1. Jeśli komputer, na którym miały miejsce tworzenie kopii zapasowych, jest nadal dostępny, należy ponownie chronić usunięte źródła danych i użyć [odzyskiwanie danych na tym samym komputerze](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) w usłudze Azure Recovery Services, aby odzyskać dane ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten komputer nie jest dostępny, użyj [funkcji Odzyskaj na komputerze alternatywnym,](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) aby uzyskać te dane za pomocą innego komputera usług Azure Recovery Services.

Dla użytkowników **serwera kopii zapasowych platformy Azure:**

1. Jeśli serwer, na którym występowały kopie zapasowe, jest nadal dostępny, należy ponownie chronić usunięte źródła danych i użyć funkcji **Odzyskaj dane,** aby odzyskać dane ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten serwer nie jest dostępny, użyj [recover data z innego serwera kopii zapasowej platformy Azure,](backup-azure-alternate-dpm-server.md) aby użyć innego wystąpienia serwera kopii zapasowej platformy Azure, aby uzyskać te dane.

Dla użytkowników **Programu Data Protection Manager:**

1. Jeśli serwer, na którym występowały kopie zapasowe, jest nadal dostępny, należy ponownie chronić usunięte źródła danych i użyć funkcji **Odzyskaj dane,** aby odzyskać dane ze wszystkich starych punktów odzyskiwania.
2. Jeśli ten serwer nie jest dostępny, użyj [dodaj zewnętrznego programu DPM,](backup-azure-alternate-dpm-server.md) aby uzyskać te dane za pomocą innego serwera Menedżera ochrony danych.

## <a name="prevent-attacks"></a>Zapobieganie atakom

Dodano kontrole, aby upewnić się, że tylko prawidłowi użytkownicy mogą wykonywać różne operacje. Obejmują one dodanie dodatkowej warstwy uwierzytelniania i utrzymanie minimalnego zakresu przechowywania do celów odzyskiwania.

### <a name="authentication-to-perform-critical-operations"></a>Uwierzytelnianie w celu wykonywania operacji krytycznych

W ramach dodawania dodatkowej warstwy uwierzytelniania dla operacji krytycznych zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń podczas wykonywania **operacji Zatrzymaj ochronę za pomocą operacji Usuń dane** i Zmień **hasło.**

> [!NOTE]
>
> Obecnie pin zabezpieczeń nie jest obsługiwany dla ochrony zatrzymania z usuń dane dla **programów** DPM i MABS.

Aby otrzymać ten kod PIN:

1. Zaloguj się do Portalu Azure.
2. Przejdź do właściwości**ustawień** >  **magazynu** > usług odzyskiwania **.**
3. W obszarze **Kod PIN zabezpieczeń**kliknij pozycję **Generuj**. Spowoduje to otwarcie bloku zawierającego kod PIN, który ma zostać wprowadzony w interfejsie użytkownika agenta usług Azure Recovery Services.
    Ten kod PIN jest ważny tylko przez pięć minut i jest generowany automatycznie po tym okresie.

### <a name="maintain-a-minimum-retention-range"></a>Utrzymanie minimalnego zakresu retencji

Aby upewnić się, że zawsze dostępna jest prawidłowa liczba punktów odzyskiwania, dodano następujące kontrole:

- W przypadku dziennego przechowywania należy wykonać co najmniej **siedem** dni przechowywania.
- W przypadku tygodniowego przechowywania należy wykonać co najmniej **cztery** tygodnie retencji.
- W przypadku miesięcznego zatrzymania należy wykonać co najmniej **trzy** miesiące zatrzymania.
- W przypadku rocznego zatrzymania należy wykonać co najmniej **jeden** rok zatrzymania.

## <a name="notifications-for-critical-operations"></a>Powiadomienia o operacjach krytycznych

Zazwyczaj po wykonaniu operacji krytycznej administrator subskrypcji otrzymuje powiadomienie e-mail ze szczegółami dotyczącymi operacji. Dodatkowe adresaci wiadomości e-mail dla tych powiadomień można skonfigurować przy użyciu witryny Azure portal.

Funkcje zabezpieczeń wymienione w tym artykule zapewniają mechanizmy obronne przed atakami ukierunkowanymi. Co ważniejsze, jeśli dojdzie do ataku, te funkcje umożliwiają odzyskanie danych.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami

| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
| Zmiana zasad |Nie można zmodyfikować zasad tworzenia kopii zapasowej. Błąd: bieżąca operacja nie powiodła się z powodu błędu usługi wewnętrznej [0x29834]. Ponów próbę wykonania operacji po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft. |**Spowodować:**<br/>Ten błąd pojawia się, gdy ustawienia zabezpieczeń są włączone, spróbuj zmniejszyć zakres przechowywania poniżej minimalnych wartości określonych powyżej i jesteś na nieobsługiwaniu wersji (obsługiwane wersje są określone w pierwszej notatce tego artykułu). <br/>**Zalecane działanie:**<br/> W takim przypadku należy ustawić okres przechowywania powyżej określonego minimalnego okresu przechowywania (siedem dni dla dnia, cztery tygodnie w tygodniu, trzy tygodnie w ujęciu miesięcznym lub jeden rok w skali roku), aby kontynuować aktualizacje związane z zasadami. Opcjonalnie preferowanym podejściem byłoby zaktualizowanie agenta kopii zapasowej, serwera kopii zapasowych platformy Azure i/lub ur programu DPM w celu wykorzystania wszystkich aktualizacji zabezpieczeń. |
| Zmień hasło |Wprowadzony kod PIN zabezpieczeń jest nieprawidłowy. (ID: 100130) Podaj poprawny kod PIN zabezpieczeń, aby wykonać tę operację. |**Spowodować:**<br/> Ten błąd pojawia się po wprowadzeniu nieprawidłowego lub wygasłego numeru PIN zabezpieczeń podczas wykonywania operacji krytycznych (takich jak hasło zmiany). <br/>**Zalecane działanie:**<br/> Aby wykonać operację, należy wprowadzić prawidłowy kod PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do usługi Recovery Services > ustawienia > właściwości > generowania numeru PIN zabezpieczeń. Użyj tego numeru PIN, aby zmienić hasło. |
| Zmień hasło |Operacja nie powiodła się. Identyfikator: 120002 |**Spowodować:**<br/>Ten błąd pojawia się, gdy ustawienia zabezpieczeń są włączone, spróbuj zmienić hasło i jesteś na nieobsługiwce (prawidłowe wersje określone w pierwszej notatce tego artykułu).<br/>**Zalecane działanie:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do minimalnej wersji minimum 2.0.9052, serwera usługi Azure Backup do minimalnej aktualizacji 1 i/lub programu DPM do minimalnej wartości DPM 2012 R2 UR12 lub DPM 2016 UR2 (łącza pobierania poniżej), a następnie wprowadź prawidłowy numer PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do usługi Recovery Services > ustawienia > właściwości > generowania numeru PIN zabezpieczeń. Użyj tego numeru PIN, aby zmienić hasło. |

## <a name="next-steps"></a>Następne kroki

- [Wprowadzenie do magazynu usług Azure Recovery Services,](backup-azure-vms-first-look-arm.md) aby włączyć te funkcje.
- [Pobierz najnowszy agent usług Odzyskiwania platformy Azure,](https://aka.ms/azurebackup_agent) aby chronić komputery z systemem Windows i chronić dane kopii zapasowej przed atakami.
- [Pobierz najnowszy serwer kopii zapasowych platformy Azure,](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) aby chronić obciążenia i chronić dane kopii zapasowej przed atakami.
- [Pobierz program UR12 for System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) lub [pobierz program UR2 dla Menedżera ochrony danych w usłudze System Center 2016,](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) aby chronić obciążenia i chronić dane kopii zapasowych przed atakami.
