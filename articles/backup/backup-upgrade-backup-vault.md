---
title: Uaktualnianie magazynu kopii zapasowych w magazynie usługi Recovery Services, usługi Azure Backup "
description: Uaktualnianie magazynu kopii zapasowych w magazynie usługi Recovery Services, aby uzyskać nowe funkcje takie jak Kopia zapasowa usługi Resource manager VMs, zwiększone zabezpieczenia, kopia zapasowa maszyny Wirtualnej VMware i kopia zapasowa stanu systemu na serwerach Windows
services: backup
author: raynew
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: b7671271e569802311884861265a7825404c9c75
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490349"
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Magazyn kopii zapasowych uaktualniony do magazynu usługi Recovery Services
Ten artykuł zawiera omówienie magazynu usługi Recovery Services, które zawiera, często zadawane pytania na temat uaktualniania istniejącej kopii zapasowej magazynu do magazynu usługi Recovery Services i czynności po uaktualnieniu. Magazyn usługi Recovery Services jest odpowiednikiem usługi Azure Resource Manager magazynu kopii zapasowych, która przechowuje dane kopii zapasowej. Dane są zwykle kopii danych lub informacje o konfiguracji dla maszyn wirtualnych (VM), obciążenia, serwerach lub stacjach roboczych, czy w środowisku lokalnym lub na platformie Azure.

## <a name="what-is-a-recovery-services-vault"></a>Co to jest magazyn usługi Recovery Services?
Magazyn usługi Recovery Services jest jednostką magazynu online na platformie Azure używaną do przechowywania danych, takich jak kopie zapasowe, punkty odzyskiwania i zasady tworzenia kopii zapasowych. Magazyny usługi Recovery Services służy do przechowywania danych kopii zapasowych dla różnych usług platformy Azure, takie jak maszyny wirtualne IaaS (Linux lub Windows) i bazy danych Azure SQL. System pomocy technicznej Magazyny usług odzyskiwania Centrum programu DPM, Windows Server, usługi Azure Backup Server i więcej. Magazyny usługi Recovery Services ułatwiają organizowanie danych kopii zapasowych przy jednoczesnym zmniejszeniu nakładów pracy związanych z zarządzaniem.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Magazyny usług odzyskiwania porównywanie i magazynami kopii zapasowych
Magazyny usługi Recovery Services są oparte na modelu usługi Azure Resource Manager platformy Azure, magazyny kopii zapasowych są oparte na modelu usługi Azure Service Manager. Po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services dane kopii zapasowej pozostaje bez zmian, podczas i po zakończeniu procesu uaktualniania. Magazyny usługi Recovery Services zapewniają funkcje nie są dostępne dla magazynów kopii zapasowych, takie jak:

- **Rozszerzone możliwości, aby ułatwić zabezpieczanie danych kopii zapasowej**: Magazyny usługi Recovery Services usługi Azure Backup zapewnia funkcje zabezpieczeń, aby chronić kopie zapasowe w chmurze. Te funkcje zabezpieczeń zagwarantować, że można zabezpieczyć kopie zapasowe i bezpiecznie odzyskać dane z kopiami zapasowymi w chmurze, nawet wtedy, gdy serwery produkcyjne i kopii zapasowej są uszkodzone. [Dowiedz się więcej](backup-azure-security-feature.md)

- **Centralne monitorowanie hybrydowe środowiska IT**: Magazyny usługi Recovery Services, umożliwia monitorowanie nie tylko usługi [maszyn wirtualnych IaaS platformy Azure](backup-azure-manage-vms.md) , ale także usługi [zasobach lokalnych](backup-azure-manage-windows-server.md#manage-backup-items) centralnej portalu. [Dowiedz się więcej](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Kontrola dostępu oparta na rolach (RBAC)**: RBAC zapewnia szczegółowej kontroli dostępu do zarządzania na platformie Azure. [System Azure oferuje różne role wbudowane](../role-based-access-control/built-in-roles.md), a usługa Azure Backup ma trzy [role wbudowane, umożliwiające zarządzanie punktami odzyskiwania](backup-rbac-rs-vault.md). Magazyny usługi Recovery Services są zgodne z RBAC, który ogranicza możliwość użycia kopia zapasowa i przywrócenia dostępu do określonych ról użytkownika. [Dowiedz się więcej](backup-rbac-rs-vault.md)

- **Chroń wszystkie konfiguracje maszyn wirtualnych platformy Azure**: Magazyny usług odzyskiwania chronią maszyn wirtualnych opartych na usłudze Resource Manager, m.in. dyski Premium Managed Disks i szyfrowanych maszyn wirtualnych. Uaktualnianie magazynu usługi Backup do magazynu usługi Recovery Services daje możliwość uaktualnienia maszyny wirtualne z opartych na programie Service Manager do maszyn wirtualnych opartych na programie Resource Manager. Podczas uaktualniania magazynu, można zachować punkty odzyskiwania maszyny Wirtualnej opartej na programie Service Manager i skonfigurować ochronę uaktualnionych maszyn wirtualnych (korzystających z Menedżera zasobów). [Dowiedz się więcej](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Natychmiastowe przywracanie dla maszyn wirtualnych IaaS**: Przy użyciu magazynów usługi Recovery Services, można przywrócić pliki i foldery z maszyny Wirtualnej IaaS bez przywracania całej maszyny Wirtualnej, która umożliwia skraca czas ich przywracania. Natychmiastowe przywracanie dla maszyn wirtualnych IaaS jest dostępna dla maszyn wirtualnych systemu Linux i Windows. [Dowiedz się więcej](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Jeśli masz elementów zarejestrowanych w magazynie kopii zapasowych za pomocą agenta usług MARS starszych niż 2.0.9083.0, [Pobierz najnowszą wersję agenta usług MARS]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) wersji, aby korzystać z zalet funkcji magazynu usługi Recovery Services. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Zarządzanie Magazyny usługi Recovery Services
Następującym zrzucie ekranu przedstawiono nowy magazyn usługi Recovery Services uaktualniony z magazynu usługi Backup, która znajduje się w witrynie Azure portal. Uaktualniony magazyn będą obecne w domyślnej grupie zasobów o nazwie "Domyślna RecoveryServices-ResourceGroup geograficzna". Przykład: Jeśli magazyn kopii zapasowych znajdują się w regionie zachodnie stany USA, będzie być umieszczane w domyślną grupą zasobów o nazwie domyślnej-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> W przypadku klientów standardu CPS grupa zasobów nie ulegną zmianie po uaktualnieniu magazynu i pozostaje taki sam, jak przed uaktualnieniem.

Pierwszy ekran przedstawia pulpit nawigacyjny magazynu, który wyświetla kluczowych jednostek dla magazynu.
![przykład magazynu usług Recovery Services uaktualniony z magazynu usługi Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Drugi ekran przedstawia pomocy łącza, które ułatwiają rozpoczęcie korzystania z magazynu usługi Recovery Services.

![linki pomocy w bloku Szybki Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Czynności po uaktualnieniu
Magazyn usługi Recovery Services obsługuje określanie informacji o strefie czasowej w zasadach kopii zapasowych. Po pomyślnym uaktualnieniu magazynu przejdź do pozycji zasady tworzenia kopii zapasowych z menu Ustawienia magazynu i zaktualizuj informacje o strefie czasowej dla każdej zasady skonfigurowane w magazynie. Ten ekran pokazuje już czas harmonogram tworzenia kopii zapasowych określony dla lokalnej strefy czasowej używane podczas tworzenia zasad. 

## <a name="enhanced-security"></a>Ulepszone zabezpieczenia
Po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Gdy ustawienia zabezpieczeń znajdują się w niektórych operacji, takich jak usuwanie kopii zapasowych lub zmiana hasła wymagać [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) numeru PIN. Aby uzyskać więcej informacji o zwiększonych zabezpieczeń, zobacz artykuł [funkcje zabezpieczeń, aby chronić hybrydowych kopii zapasowych](backup-azure-security-feature.md). Zwiększonych zabezpieczeń jest włączone, dane są przechowywane się 14 dni, po usunięciu informacji punktu odzyskiwania z magazynu. Klienci są obciążani za magazynowanie tych danych zabezpieczeń. Przechowywanie danych zabezpieczeń mają zastosowanie do punktów odzyskiwania dla agenta usługi Kopia zapasowa Azure, usługi Azure Backup Server i System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Zbieranie danych dotyczących magazynu
Raz uaktualnienie do magazynu usługi Recovery Services, skonfigurować raporty usługi Azure Backup (maszynom wirtualnym IaaS i usług Microsoft Azure Recovery Services agent), a dostęp do raportów przy użyciu usługi Power BI. Aby uzyskać dodatkowe informacje dotyczące zbierania danych, zapoznaj się z artykułem [raporty Konfigurowanie usługi Azure Backup](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Uaktualnij plan wpływa na Moje bieżące kopie zapasowe?**</br>
Nie. Trwającą kopii zapasowych bezproblemowo kontynuują podczas i po uaktualnieniu.

**Ile kosztuje to oznacza, że uaktualnienie dla moich istniejących narzędzi?**</br>
W modelu wdrażania usługi Resource Manager, aby upewnić się, że będzie on nadal działać po uaktualnieniu należy zaktualizować istniejące automatyzacje lub narzędzia. Zapoznaj się z odwołań do poleceń cmdlet programu PowerShell dla [modelu wdrażania usługi Resource Manager](backup-client-automation.md).

**Czy mogę wycofać po uaktualnieniu?**</br>
Nie. Wycofanie nie jest obsługiwane po pomyślnym uaktualnieniu zasobów.

**Mogę wyświetlić mojej klasycznego magazynu po uaktualnieniu**</br>
Nie. Nie można wyświetlić ani zarządzania magazynem klasycznego po uaktualnieniu. Tylko można użyć nowej witryny Azure portal dla wszystkich akcji zarządzania w magazynie.

**Dlaczego nie widzę serwerów chronionych przez agenta usług MARS w mojej uaktualnionego magazynie?**</br>
Musisz zainstalować najnowszą wersję agenta MARS, aby wyświetlić wszystkie serwery, które są chronione przez agenta usług MARS w magazynie. Możesz pobrać najnowszą wersję agenta z [tutaj]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**Nie widzę zasady tworzenia kopii zapasowych serwerów chronionych przez agenta usług MARS po uaktualnieniu**</br>
Zasady tworzenia kopii zapasowej dla magazynu może być nieaktualna i w związku z tym nie może zostać zsynchronizowany w magazynie uaktualniony. Zaktualizuj zasady aby upewnić się, że jest nadal wyświetlany zasad w magazynie uaktualniony.
Aby zaktualizować zasady, przejdź do agenta usług MARS i aktualizacja skonfigurowanych zasad kopii zapasowych.

**Dlaczego nie można zaktualizować Moje zasad tworzenia kopii zapasowej po uaktualnieniu?**</br>
Dzieje się tak, gdy są w ramach starego agenta kopii zapasowej i wybrać minimalny okres przechowywania jest mniejsza niż minimalna dozwolona wartość. Po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Aby upewnić się, że jest zawsze prawidłowe liczby punktów odzyskiwania dostępnych, istnieje kilka minimalny okres przechowywania, musi być zachowana zgodnie z funkcji zabezpieczeń. Aby uzyskać więcej informacji można znaleźć [tutaj](backup-azure-security-feature.md).
Ponadto należy zaktualizować agentów programu Azure Backup do najnowszej wersji, aby móc z zalet najnowszych funkcji usługi Azure Backup.

**Zaktualizowano agent, ale nadal nie są wyświetlane wszystkie obiekty synchronizowanego nawet przez kilka dni po uaktualnieniu**</br>
Sprawdź, czy Jeśli zarejestrowano tym samym komputerze w wielu magazynach. Upewnij się, że szukasz na tym samym magazynie, do którego agenta usług MARS jest zarejestrowany. Aby dowiedzieć się, które magazynu agenta usług MARS jest zarejestrowany, otwórz Rejestr Windows i sprawdź wartość klucza ServiceResourceName pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config magazynu zarejestrowany pojawi agenta usług MARS . Jeśli klucz ServiceResourceName nie jest widoczny w systemie, się z nami za pomocą wartości kluczy ResourceId i MachineId w obszarze HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config, a my pomożemy Ci rozwiązać problem.

**Dlaczego nie widzę informacji zadania dla moich zasobów po uaktualnieniu?**</br>
Monitorowanie kopii zapasowych (IaaS i agenta MARS) to nowa funkcja, która pojawia się po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services. Informacje monitorowania trwa maksymalnie 12 godzin do synchronizacji z usługą.

**Jak zgłosić problem?**</br>
W przypadku niepowodzenia jakiejkolwiek jego części Uaktualnienie magazynu należy pamiętać, że identyfikatorowi OperationId na liście błędów. Microsoft Support aktywnie będzie działać w celu rozwiązania problemu. Można Dotrzyj do działu pomocy technicznej lub Wyślij wiadomość e-mail rsvaultupgrade@service.microsoft.com z Identyfikatora subskrypcji, nazwę magazynu i OperationId. Firma Microsoft podejmie możliwie jak najszybciej rozwiązać problem. Nie należy wykonywać ponowień operacji, chyba że jawnie zgodnie z instrukcjami otrzymywanymi Aby to zrobić przez firmę Microsoft.

## <a name="next-steps"></a>Kolejne kroki
Użyj następujących artykułach:</br>
[Tworzenie kopii zapasowych maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowych usługi Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md)
