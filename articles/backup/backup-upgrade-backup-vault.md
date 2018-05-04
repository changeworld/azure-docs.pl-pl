---
title: Uaktualnienia magazynu kopii zapasowych w magazynie usług odzyskiwania usługi Azure Backup | Dokumentacja firmy Microsoft
description: Uaktualnienia magazynu kopii zapasowych w magazynie usług odzyskiwania, aby uzyskać nowe funkcje, takie jak kopii zapasowej Menedżera zasobów maszyn wirtualnych, zwiększone zabezpieczenia, kopii zapasowej maszyny Wirtualnej VMware i kopii zapasowej stanu systemu dla serwerów systemu Windows
services: backup
documentationcenter: ''
author: trinadhk
manager: vijayts
editor: ''
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: ffc5f11a324b5ac65c872ca2c033f039c129c5f8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Magazyn kopii zapasowych uaktualnione do magazynu usług odzyskiwania
Ten artykuł zawiera omówienie magazynu usług odzyskiwania, jakie udostępnia, często zadawane pytania na temat uaktualniania istniejącej kopii zapasowej magazynu do magazynu usług odzyskiwania i czynności po uaktualnieniu. Magazyn usług odzyskiwania jest odpowiednikiem usługi Azure Resource Manager w magazynie kopii zapasowej, która przechowuje dane kopii zapasowej. Dane są zwykle kopii danych lub informacje o konfiguracji dla maszyn wirtualnych (VM), obciążenia, serwerach lub stacjach roboczych, czy lokalnie lub na platformie Azure.

## <a name="what-is-a-recovery-services-vault"></a>Co to jest magazyn usługi Recovery Services?
Magazyn usługi Recovery Services jest jednostką magazynu online na platformie Azure używaną do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych. Magazyny usług odzyskiwania służy do przechowywania danych kopii zapasowej dla różnych usług Azure, takich jak maszyny wirtualne IaaS (Linux lub Windows) oraz baz danych Azure SQL. System obsługi magazynów usług odzyskiwania Centrum programu DPM, Windows Server, serwer kopii zapasowej Azure i więcej. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Magazyny usług odzyskiwania porównaniem i magazynami kopii zapasowych
Magazyny usług odzyskiwania są oparte na modelu usługi Azure Resource Manager platformy Azure, magazyny kopii zapasowych są oparte na modelu Azure Service Manager. Po uaktualnieniu magazynu kopii zapasowych do magazynu usług odzyskiwania dane kopii zapasowej pozostanie niezmieniona podczas i po zakończeniu procesu uaktualniania. Magazyny usług odzyskiwania zapewniają funkcje nie są dostępne dla magazyny kopii zapasowych, takich jak:

- **Ulepszonych funkcji ułatwia zabezpieczanie danych kopii zapasowej**: magazynów z usług odzyskiwania, kopia zapasowa Azure udostępnia funkcje zabezpieczeń w celu ochrony kopii zapasowych w chmurze. Te funkcje zabezpieczeń sprawdź secure kopii zapasowych i bezpiecznie odzyskać dane z kopii zapasowych w chmurze, nawet jeśli serwerów produkcyjnych i tworzenia kopii zapasowej są uszkodzone. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie sieci hybrydowe środowiska IT**: magazynów z usług odzyskiwania, można monitorować nie tylko z [maszyn wirtualnych IaaS platformy Azure](backup-azure-manage-vms.md) , ale także z [zasoby lokalne](backup-azure-manage-windows-server.md#manage-backup-items) z centralnej portalu. [Dowiedz się więcej](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Kontrola dostępu oparta na rolach (RBAC)**: RBAC zapewnia szczegółowej zarządzania kontroli dostępu na platformie Azure. [Platforma Azure oferuje różne role wbudowane](../role-based-access-control/built-in-roles.md), a kopia zapasowa Azure ma trzy [wbudowane role, aby zarządzać punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny usług odzyskiwania są zgodne z RBAC, który ogranicza możliwość użycia kopii zapasowej i przywracania dostępu do zdefiniowanego zestawu ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Chroń wszystkie konfiguracje maszyn wirtualnych Azure**: Magazyny usług odzyskiwania chronić maszyny wirtualne oparte na Menedżera zasobów, w tym dyski Premium, dysków zarządzanych i szyfrowane maszyn wirtualnych. Uaktualnianie magazynu kopii zapasowych do magazynu usług odzyskiwania daje możliwość uaktualnienia programu Service Manager na podstawie maszyn wirtualnych do maszyn wirtualnych w Menedżerze zasobów. Podczas uaktualniania magazynu, można zachować punktów odzyskiwania maszyny Wirtualnej opartej na programu Service Manager i skonfiguruj ochronę uaktualnionego maszyn wirtualnych (włączone Resource Manager). [Dowiedz się więcej](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowego przywracania dla maszyn wirtualnych IaaS**: Magazyny usług odzyskiwania przy użyciu, można przywrócić pliki i foldery z maszyn wirtualnych IaaS bez przywracania całą maszynę Wirtualną, która umożliwia szybsze przywracania. Natychmiastowego przywracania dla maszyn wirtualnych IaaS jest dostępna dla systemów Windows i maszyn wirtualnych systemu Linux. [Dowiedz się więcej](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Jeśli masz elementy zarejestrowane do magazynu kopii zapasowej z agenta MARS starszych niż 2.0.9083.0, [Pobierz najnowszą wersję agenta MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) wersji, aby z zalet funkcji magazynu usług odzyskiwania. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Zarządzanie z Magazyny usług odzyskiwania
Następujące ekrany Pokaż nowy magazyn usług odzyskiwania uaktualnione z magazynu kopii zapasowych w portalu Azure. Uaktualnionego magazynu będzie obecne w domyślnej grupie zasobów o nazwie "Domyślna-RecoveryServices-grupa zasobów — geo". Przykład: Jeśli magazynu kopii zapasowej znajduje się w zachodnie stany USA, jego będzie znajdować się w domyślnej grupy zasobów o nazwie domyślnej-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> Klienci CPS Standard grupy zasobów nie ulega zmianie po uaktualnieniu magazynu i jest taka sama, jak przed uaktualnieniem.

Pierwszy ekran pokazuje pulpitu nawigacyjnego magazynu, który wyświetla jednostek klucza magazynu.
![przykład uaktualnienia z magazynu kopii zapasowych magazynu usług odzyskiwania](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Drugi ekran pokazuje pomocy łącza może pomóc Ci rozpocząć korzystanie z magazynu usług odzyskiwania.

![łącza pomocy w bloku Szybki Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Czynności po uaktualnieniu
Magazyn usług odzyskiwania obsługują określania informacji o strefie czasowej zasad tworzenia kopii zapasowej. Po pomyślnym uaktualnieniu magazynu, przejdź do zasady tworzenia kopii zapasowej z menu ustawień magazynu i zaktualizować informacje o strefie czasowej dla każdej zasady skonfigurowane w magazynie. Ten ekran pokazuje już czas harmonogram tworzenia kopii zapasowych określony na lokalnej strefie czasowej używane podczas tworzenia zasad. 

## <a name="enhanced-security"></a>Większe bezpieczeństwo
Po uaktualnieniu magazynu kopii zapasowych do magazynu usług odzyskiwania, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Gdy ustawienia zabezpieczeń znajdują się na niektórych operacji, takich jak usuwanie kopii zapasowych lub zmiana hasła wymagają [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) numeru PIN. Aby uzyskać więcej informacji na zwiększonych zabezpieczeń, zobacz artykuł [funkcji zabezpieczeń do ochrony kopii zapasowych hybrydowego](backup-azure-security-feature.md). Po włączeniu zwiększonych zabezpieczeń danych jest zachowywana się do 14 dni, po usunięciu informacji punktu odzyskiwania z magazynu. Klienci są rozliczane do przechowywania tych danych zabezpieczeń. Przechowywanie danych zabezpieczeń ma zastosowanie do punktów odzyskiwania dla agenta usługi Kopia zapasowa Azure, serwer usługi Kopia zapasowa Azure i System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Zbieranie danych dotyczących magazynu
Raz uaktualnienia do magazynu usług odzyskiwania, skonfiguruj raporty dla usługi Kopia zapasowa Azure (dla agenta maszynom wirtualnym IaaS i usług odzyskiwania Microsoft Azure) i umożliwia dostęp do raportów usługi Power BI. Aby uzyskać dodatkowe informacje dotyczące zbierania danych, zobacz artykuł, [raporty skonfigurować kopia zapasowa Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Plan uaktualniania wpływa na Moje bieżące kopie zapasowe?**</br>
Nie. Kopie zapasowe trwającą nadal przerwana podczas i po uaktualnieniu.

**Jaki jest średnia ta uaktualnienia do mojej istniejącej oprzyrządowania?**</br>
Musisz zaktualizować narzędzi lub z istniejącej automatyzacji do modelu wdrażania usługi Resource Manager, aby upewnić się, że nadal działać po uaktualnieniu. Zapoznaj się z odwołań do poleceń cmdlet programu PowerShell dla [modelu wdrażania usługi Resource Manager](backup-client-automation.md).

**I przywrócić po uaktualnieniu?**</br>
Nie. ROLLBACK nie jest obsługiwana, gdy zasoby zostały pomyślnie uaktualnione.

**Może wyświetlać Mój po uaktualnieniu magazynu classic?**</br>
Nie. Nie można wyświetlić lub zarządzaj nimi po uaktualnieniu magazynu klasycznego. Tylko będzie mógł używać nowego portalu Azure do wszystkich akcji zarządzania w magazynie.

**Dlaczego nie widzę serwerów chronionych przez agenta MARS w mojej uaktualnionego magazynu?**</br>
Musisz zainstalować najnowszą wersję agenta MARS można zobaczyć wszystkie serwery chronione przez agenta MARS w magazynie. Możesz pobrać najnowszą wersję agenta z [tutaj]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Nie widzę zasad tworzenia kopii zapasowej dla serwerów chronionych przez agenta MARS po uaktualnieniu**</br>
Zasady tworzenia kopii zapasowej na magazyn może być nieaktualna i w związku z tym nie może zostać zsynchronizowany w magazynie uaktualniony. Zaktualizuj zasady upewnij się, że będzie się zasadom w uaktualnionym magazynu.
Aby móc aktualizować zasady, przejdź do agenta MARS i zaktualizuj skonfigurowanych zasad tworzenia kopii zapasowej.

**Dlaczego nie można zaktualizować Moje zasad tworzenia kopii zapasowej po uaktualnieniu?**</br>
Dzieje się tak podczas starego agenta kopii zapasowej i wybierz minimalny okres przechowywania jest mniejsza niż wartość minimalna dozwolonych. Po uaktualnieniu magazynu kopii zapasowych do magazynu usług odzyskiwania, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Aby upewnić się, że jest zawsze prawidłową liczbę punktów odzyskiwania dostępnych, brak niektórych okres przechowywania minimalnej, które należy aktualizować zgodnie z funkcji zabezpieczeń. Aby uzyskać więcej informacji, zobacz [tutaj](backup-azure-security-feature.md).
Ponadto należy zaktualizować agentów programu Kopia zapasowa Azure do najnowszej wersji, aby z zalet najnowszych funkcji Kopia zapasowa Azure.

**Zaktualizowano Moje agenta, ale nadal nie jest widoczne wszystkie obiekty synchronizowanego nawet przez kilka dni po uaktualnieniu**</br>
Sprawdź, czy zarejestrowano tej samej maszyny do wielu magazynów. Upewnij się, że poszukujesz tego samego magazynu, w której agenta MARS jest zarejestrowana. Aby dowiedzieć się, które magazynu agenta MARS jest zarejestrowany, otwórz rejestru systemu Windows i sprawdź wartość dla klucza ServiceResourceName w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config magazynu zarejestrowany pojawi agenta MARS Brak. Jeśli klucz ServiceResourceName nie jest widoczna w systemie, dotrzeć do nas o wartości kluczy ResourceId i MachineId w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config, a pomożemy Ci rozwiązać ten problem.

**Dlaczego nie widzę informacji zadania dla mojej zasobów po uaktualnieniu?**</br>
Monitorowanie kopii zapasowych (IaaS i MARS agent) jest nową funkcją, która pojawia się po uaktualnieniu magazynu kopii zapasowej w magazynie usług odzyskiwania. Dane monitorowania ma 12 godzin do synchronizacji z usługą.

**Jak zgłosić problem?**</br>
W przypadku niepowodzenia jakiejkolwiek jego części uaktualnienia magazynu należy pamiętać, że OperationId na liście w dzienniku błędów. Aby rozwiązać ten problem aktywnie działa Support firmy Microsoft. Może dotrzeć do pomocy technicznej lub wiadomość e-mail na adres rsvaultupgrade@service.microsoft.com Twojego Identyfikatora subskrypcji, nazwę magazynu i OperationId. Firma Microsoft podejmie próbę rozwiązania problemu tak szybko jak to możliwe. Nie próbuj ponownie wykonać operację, chyba że jawnie instrukcją w tym celu przez firmę Microsoft.

## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższych artykułów dla:</br>
[Tworzenie kopii zapasowej maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Utwórz kopię zapasową serwera kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md)
