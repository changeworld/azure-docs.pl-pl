---
title: Funkcje zabezpieczeń w celu ochrony kopii zapasowych hybrydowych, korzystających z usługi Kopia zapasowa Azure
description: Dowiedz się, jak za pomocą funkcji zabezpieczeń w usłudze Kopia zapasowa Azure można tworzyć kopie zapasowe są bardziej bezpieczne
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: pajosh
ms.openlocfilehash: 11483cd87600ef8b10c2c7492e2434b9ab97149e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606209"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Funkcje zabezpieczeń w celu ochrony kopii zapasowych hybrydowych, korzystających z usługi Kopia zapasowa Azure
Coraz więcej problemów dotyczących problemów z zabezpieczeniami, takich jak złośliwego oprogramowania, ransomware i nieautoryzowanego dostępu. Te problemy zabezpieczeń może być kosztowne zarówno oszczędność pieniędzy i danych. Ochrona przed takimi atakami, kopia zapasowa Azure udostępnia funkcje zabezpieczeń w celu ochrony kopii zapasowych hybrydowego. W tym artykule omówiono sposobu włączania i używania tych funkcji, za pomocą agenta usług odzyskiwania Azure i serwer kopii zapasowej Azure. Te funkcje obejmują:

- **Zapobieganie**. Zawsze, gdy jest wykonywana krytycznej operacji, takich jak zmiana hasła dodawany jest dodatkową warstwę uwierzytelniania. Upewnij się, że takie operacje mogą być wykonywane tylko przez użytkowników, którzy mają prawidłowe poświadczenia platformy Azure jest tej weryfikacji.
- **Alerty**. Powiadomienie e-mail są wysyłane do administratora subskrypcji zawsze, gdy jest wykonywana krytycznej operacji, takich jak usuwanie danych kopii zapasowej. Tę wiadomość e-mail gwarantuje, że użytkownik jest powiadamiany szybko o takich działań.
- **Odzyskiwanie**. Usunięte dane kopii zapasowej jest przechowywany dla kolejnych 14 dni od daty usunięcia. Zapewnia to możliwość odzyskania danych w danym okresie, więc nie ma bez utraty danych, nawet jeśli się stanie w przypadku ataków. Ponadto większa liczba punktów odzyskiwania minimalne są obsługiwane chronią przed uszkodzone dane.

> [!NOTE]
> Funkcje zabezpieczeń nie można włączyć, korzystając z infrastruktury jako usługi (IaaS) kopii zapasowej maszyny Wirtualnej. Te funkcje nie są jeszcze dostępne dla kopii zapasowych maszyn wirtualnych IaaS, dlatego włączenie ich nie będzie miało żadnych skutków. Funkcje zabezpieczeń powinny być włączone, tylko wtedy, gdy używane są: <br/>
>  * **Agent usługi Kopia zapasowa Azure**. Wersja agenta minimalna 2.0.9052. Po włączeniu tych funkcji, należy uaktualnić system do tej wersji agenta w celu wykonania operacji krytyczne. <br/>
>  * **Serwer kopii zapasowej systemu Azure**. Minimalna kopia zapasowa Azure agenta w wersji 1 aktualizacji 2.0.9052 serwer kopii zapasowej Azure. <br/>
>  * **Program System Center Data Protection Manager**. Minimalna Azure Backup agent wersja 2.0.9052 z programu Data Protection Manager 2012 R2 UR12 lub UR2 2016 programu Data Protection Manager. <br/> 


> [!NOTE]
> Te funkcje są dostępne tylko dla magazynu usług odzyskiwania. Wszystkie nowo utworzone magazyny usług odzyskiwania te funkcje są włączone domyślnie. Dla istniejących Magazyny usług odzyskiwania użytkowników, należy włączyć te funkcje przy użyciu czynności wymienionych w poniższej sekcji. Po funkcje są włączone, mają one zastosowanie do wszystkich usług odzyskiwania agenta komputerów, wystąpienia serwera usługi Kopia zapasowa Azure i serwery programu Data Protection Manager zarejestrowane na magazynie. Włączenie tego ustawienia to działanie jednorazowe i tych funkcji nie można wyłączyć po ich włączeniem.
>

## <a name="enable-security-features"></a>Funkcje zabezpieczeń
W przypadku tworzenia magazynu usług odzyskiwania, można użyć funkcji zabezpieczeń. Jeśli pracujesz z istniejącego magazynu, włączyć funkcje zabezpieczeń, wykonaj następujące czynności:

1. Zaloguj się do portalu Azure przy użyciu poświadczeń platformy Azure.
2. Wybierz **Przeglądaj**i wpisz **usług odzyskiwania**.

    ![Opcja przeglądania portalu zrzut ekranu Azure](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Zostanie wyświetlona lista magazynów usługi Recovery Services. Z tej listy wybierz magazyn. Zostanie otwarty pulpit nawigacyjny wybranego magazynu.
3. Z listy elementów, który pojawia się w magazynie, w obszarze **ustawienia**, kliknij przycisk **właściwości**.

    ![Opcje magazynu zrzut ekranu usług odzyskiwania](./media/backup-azure-security-feature/vault-list-properties.png)
4. W obszarze **ustawienia zabezpieczeń**, kliknij przycisk **aktualizacji**.

    ![Zrzut ekranu usług odzyskiwania Magazyn właściwości](./media/backup-azure-security-feature/security-settings-update.png)

    Aktualizacja łącze powoduje otwarcie **ustawienia zabezpieczeń** bloku, który zawiera podsumowanie funkcji i można je włączyć.
5. Z listy rozwijanej **zostały skonfigurowane uwierzytelnianie wieloskładnikowe Azure?**, wybierając odpowiednią wartość, aby upewnić się, jeśli włączono [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Jeśli jest włączona, zostanie wyświetlona prośba o uwierzytelniania za pomocą innego urządzenia (na przykład telefon komórkowy) podczas logowania do portalu Azure.

   Podczas wykonywania krytycznej operacji w usłudze Kopia zapasowa, należy wprowadzić zabezpieczający numer PIN, dostępne w portalu Azure. Włączanie usługi Azure Multi-Factor Authentication dodaje warstwy zabezpieczeń. Tylko przez autoryzowanych użytkowników z prawidłowymi poświadczeniami Azure i uwierzytelniony z drugiego urządzenia, można uzyskać dostępu do portalu Azure.
6. Aby zapisać ustawienia zabezpieczeń, wybierz **włączyć** i kliknij przycisk **zapisać**. Możesz wybrać **włączyć** tylko po wybraniu wartości od **zostały skonfigurowane uwierzytelnianie wieloskładnikowe Azure?** listy w poprzednim kroku.

    ![Zrzut ekranu przedstawiający ustawienia zabezpieczeń](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Odzyskiwanie usuniętych danych kopii zapasowej
Kopia zapasowa zachowuje usunięte dane kopii zapasowej dla dodatkowych 14 dni, a nie powoduje usunięcia natychmiast, jeśli **kopii zapasowej Zatrzymaj z usuwania danych kopii zapasowej** operacja została wykonana. Aby przywrócić te dane w okresie 14 dni, należy wykonać następujące czynności, w zależności od tego, czego używasz:

Aby uzyskać **agenta usług odzyskiwania Azure** użytkowników:

1. Jeśli komputer, na których były wykonywane kopie zapasowe jest nadal dostępne, użyj [odzyskać dane do tej samej maszynie](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) w usług odzyskiwania Azure, aby przywrócić wszystkie stare punkty odzyskiwania.
2. Jeśli ten komputer jest niedostępny, użyj [odzyskanie do alternatywnego maszyny](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) na innym komputerze usług odzyskiwania Azure tych danych.

Aby uzyskać **serwer kopii zapasowej Azure** użytkowników:

1. Jeśli nadal dostępny jest serwer, na których były wykonywane kopie zapasowe, ponownie włączyć ochronę źródeł danych usuniętych i użyj **odzyskać dane** funkcji pozwoli na odzyskanie wszystkich stare punkty odzyskiwania.
2. Jeśli ten serwer jest niedostępny, użyj [odzyskać dane z innego serwera kopii zapasowej Azure](backup-azure-alternate-dpm-server.md) do innego wystąpienia serwera kopii zapasowej Azure umożliwia pobrać te dane.

Aby uzyskać **programu Data Protection Manager** użytkowników:

1. Jeśli nadal dostępny jest serwer, na których były wykonywane kopie zapasowe, ponownie włączyć ochronę źródeł danych usuniętych i użyj **odzyskać dane** funkcji pozwoli na odzyskanie wszystkich stare punkty odzyskiwania.
2. Jeśli ten serwer jest niedostępny, użyj [Dodaj zewnętrzny program DPM](backup-azure-alternate-dpm-server.md) do Użyj innego serwera programu Data Protection Manager, aby pobrać te dane.

## <a name="prevent-attacks"></a>Zapobieganie atakom
Aby upewnić się, że tylko prawidłowych użytkowników można wykonywać różne operacje, zostały dodane kontroli. Obejmują one dodanie dodatkowej warstwy uwierzytelniania i utrzymywanie zakres przechowywania minimalnej na potrzeby odzyskiwania.

### <a name="authentication-to-perform-critical-operations"></a>Uwierzytelnianie w celu wykonania operacji krytyczne
W ramach dodanie dodatkowej warstwy uwierzytelniania na ważne operacje, zostanie wyświetlony monit o wprowadź zabezpieczający numer PIN, podczas wykonywania **Zatrzymaj ochronę danych Delete** i **Zmień hasło** operacji.

Aby otrzymać ten kod PIN:

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazyn usług odzyskiwania i** > **ustawienia** > **właściwości**.
3. W obszarze **kod PIN zabezpieczeń**, kliknij przycisk **Generuj**. Spowoduje to otwarcie bloku, który zawiera numer PIN, aby wpisać w interfejsie użytkownika agenta usług odzyskiwania Azure.
    Ten numer PIN jest prawidłowy tylko pięć minut, a następnie pobiera ona generowana automatycznie po upływie tego czasu.

### <a name="maintain-a-minimum-retention-range"></a>Obsługa zakresu przechowywania minimalnej
Aby upewnić się, że jest zawsze prawidłową liczbę punktów odzyskiwania dostępnych, zostały dodane następujące testy:

- Do przechowywania codziennie, co najmniej **siedmiu** ma się odbywać dni przechowywania.
- Do przechowywania co tydzień, co najmniej **cztery** ma się odbywać tygodni przechowywania.
- Miesięczne okresu przechowywania, co najmniej **trzy** ma się odbywać okresem przechowywania.
- Do przechowywania roczne, co najmniej **jeden** ma się odbywać roku przechowywania.

## <a name="notifications-for-critical-operations"></a>Powiadomienia dotyczące ważne operacje
Zwykle po wykonaniu operacji krytyczne administratora subskrypcji jest wysyłana wiadomość e-mail z powiadomieniem o szczegółowe informacje dotyczące operacji. Można skonfigurować dodatkowe e-mail adresatów powiadomień za pomocą portalu Azure.

Funkcje zabezpieczeń wymienione w niniejszym artykule zapewniają mechanizmy obrony przed atakami ukierunkowanymi. Co ważniejsze Jeśli wystąpi atak, funkcje te zapewniają możliwość odzyskania danych.

## <a name="troubleshooting-errors"></a>Rozwiązywanie problemów z błędami
| Operacja | Szczegóły błędu | Rozwiązanie |
| --- | --- | --- |
| Zmiana zasad |Nie można modyfikować zasady tworzenia kopii zapasowej. Błąd: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj wykonać ponownie operację za jakiś czas. Jeśli problem będzie nadal występować, skontaktuj się z pomocą techniczną firmy Microsoft. |**Przyczyna:**<br/>Ten błąd jest dostarczany podczas ustawienia zabezpieczeń zostały włączone, spróbuj zmniejszyć zakres przechowywania, poniżej wartości minimalnej wymienione powyżej i znajdują się na nieobsługiwaną wersję (obsługiwane wersje są określone w pierwszym należy wziąć pod uwagę w tym artykule). <br/>**Zalecana akcja:**<br/> W takim przypadku należy ustawić okresu przechowywania powyżej minimalne przechowywania podanego okresu (siedem dni codziennie, czterech tygodni, przez co tydzień, trzy tygodnie, co miesiąc lub rok dla corocznej) kontynuowanie zasady dotyczące aktualizacji. Opcjonalnie preferowane rozwiązanie byłoby zaktualizować agenta kopii zapasowej serwera kopii zapasowej Azure i/lub pakietem zbiorczym aktualizacji programu DPM wykorzystać wszystkie aktualizacje zabezpieczeń. |
| Zmień hasło |Zabezpieczenia wprowadzony numer PIN jest niepoprawny. (IDENTYFIKATOR: 100130) Podaj poprawny numer PIN zabezpieczeń, aby wykonać tę operację. |**Przyczyna:**<br/> Ten błąd jest dostarczany podczas wprowadzania numeru PIN zabezpieczeń nieważny lub wygasły podczas wykonywania krytycznej operacji (jak zmienić hasło). <br/>**Zalecana akcja:**<br/> Aby ukończyć operację, wprowadź prawidłowy kod PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania > Ustawienia > Właściwości > Generowanie kodu PIN zabezpieczeń. Ten numer PIN umożliwia zmianę hasła. |
| Zmień hasło |Operacja nie powiodła się. ID: 120002 |**Przyczyna:**<br/>Ten błąd jest dostarczany podczas próby zmiany hasła, ustawienia zabezpieczeń zostały włączone, a na nieobsługiwaną wersję (prawidłowe wersje określone w pierwszym należy wziąć pod uwagę w tym artykule).<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do minimalna wersja minimalna 2.0.9052, kopia zapasowa Azure serwerowi minimalnej aktualizacji 1, i/lub DPM ma minimalną programu DPM 2012 R2 UR12 lub UR2 2016 programu DPM (poniżej łącza pobierania), wprowadź prawidłowy kod PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do portalu Azure i przejdź do magazynu usług odzyskiwania > Ustawienia > Właściwości > Generowanie kodu PIN zabezpieczeń. Ten numer PIN umożliwia zmianę hasła. |

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do magazynu usług odzyskiwania Azure](backup-azure-vms-first-look-arm.md) Aby włączyć te funkcje.
* [Pobierz najnowszą wersję agenta usług odzyskiwania Azure](http://aka.ms/azurebackup_agent) Aby chronić komputery z systemem Windows i ochronę danych przed atakami.
* [Pobierz najnowsze serwera kopii zapasowej Azure](https://aka.ms/latest_azurebackupserver) ułatwia ochronę obciążeń i ochronę danych przed atakami.
* [Pobierz UR12 dla systemu System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) lub [Pobierz UR2 dla programu System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) ułatwia ochronę obciążeń i ochronę danych przed atakami.
