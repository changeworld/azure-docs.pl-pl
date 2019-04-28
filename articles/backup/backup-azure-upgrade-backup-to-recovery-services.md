---
title: Uaktualnienie magazynu usługi Backup do magazynu usługi Recovery Services
description: Instrukcje i informacje pomocy technicznej do uaktualnienia magazynu usługi Azure Backup do magazynu usługi Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: b4ecebc6bef7f49a23455c7a85f25680df087a95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848371"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Uaktualnienie magazynu usługi Backup do magazynu usługi Recovery Services

W tym artykule opisano sposób uaktualniania magazynu kopii zapasowych w magazynie usługi Recovery Services. Proces uaktualniania nie ma wpływu na wszystkie zadania uruchomione kopii zapasowej i zostaną utracone żadne dane kopii zapasowej. Głównych powodów uaktualnienia magazynu usługi Backup do magazynu usługi Recovery Services:
- Wszystkie funkcje magazynu usługi Backup zostaną zachowane w magazynie usługi Recovery Services.
- Magazyny usługi Recovery Services ma więcej funkcji niż magazyny kopii zapasowych, w tym: większe bezpieczeństwo, zintegrowane monitorowanie, szybciej przeprowadzać operacje przywracania i na poziomie pozycji.
- Elementy kopii zapasowej można zarządzać w witrynie portal ulepszone i uproszczone.
- Nowe funkcje zastosowanie tylko do magazynów usługi Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Wpływ na operacje podczas uaktualniania

W przypadku uaktualniania magazynu kopii zapasowych do magazynu usługi Recovery Services, nie ma to wpływu na operacji płaszczyzny danych. Wszystkie zadania tworzenia kopii zapasowej nadal normalnie, a wszystkie zadania przywracania aktywnej nadal bez przeszkód korzystać z programu. Podczas uaktualniania operacji zarządzania pociągnąć za sobą krótki czas przestoju i nie można chronić nowych elementów lub utworzyć zadania tworzenia kopii zapasowych ad hoc. Zadania przywracania dla maszyn wirtualnych IaaS nie działają podczas uaktualniania. Uaktualnienie magazynu trwa mniej niż godzinę. Po zakończeniu magazyn usługi Recovery Services zastępuje magazynu kopii zapasowych.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Zmiany automatyzacji i narzędzi po uaktualnieniu

Podczas przygotowywania infrastruktury dla Uaktualnienie magazynu, należy zaktualizować istniejące automatyzacje lub narzędzia, aby zapewnić, że będzie on nadal działać po uaktualnieniu.
Zapoznaj się z odwołań do poleceń cmdlet programu PowerShell dla [modelu wdrażania usługi Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Przed uaktualnieniem

Sprawdź następujące problemy przed rozpoczęciem uaktualnienia magazynów kopii zapasowych do magazynów usług odzyskiwania.

- **Minimalna wersja agenta**: Aby przeprowadzić uaktualnienie magazynu, upewnij się, agent usługi Microsoft Azure Recovery Services (MARS) jest co najmniej wersji 2.0.9083.0. Jeśli agenta usług MARS jest starsza niż 2.0.9083.0, zaktualizuj agenta przed rozpoczęciem procesu uaktualniania.
- **Model rozliczeń oparty na wystąpienie**: Magazyny usług odzyskiwania obsługują tylko model rozliczeń oparty na wystąpienie. Jeśli masz magazyn kopii zapasowych, który używa starszego modelu rozliczeń na podstawie magazynu, należy przekonwertować model rozliczeń podczas uaktualniania.
- **Żadne operacje konfiguracji kopii zapasowej w toku**: Podczas uaktualniania dostęp do płaszczyzny zarządzania jest ograniczony. Wykonaj wszystkie operacje płaszczyzny zarządzania, a następnie uruchom uaktualnianie.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Za pomocą skryptów programu PowerShell do uaktualnienia Twoich magazynów

Skrypty programu PowerShell służy do uaktualnienia magazynów kopii zapasowych do magazynów usługi Recovery Services. Sprawdź, czy masz wymagane składniki programu PowerShell do wyzwolenia Uaktualnienie magazynu. Skrypty programu PowerShell dla magazynów kopii zapasowych nie działają w przypadku magazynów usługi Recovery Services. Przygotowywanie środowiska do uaktualnienia magazynów:

1. Zainstaluj lub Uaktualnij [Windows Management Framework (WMF) w wersji 5](https://www.microsoft.com/download/details.aspx?id=50395) lub nowszej.
2. [Instalowanie usługi Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Pobierz [skrypt programu PowerShell](https://aka.ms/vaultupgradescript2) uaktualnienie Twoich magazynów.

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell

Użyj następującego skryptu, aby uaktualnić Twoich magazynów. Poniższy przykładowy skrypt ma objaśnienia dotyczące parametrów.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** — numer Identyfikatora subskrypcji, magazynu, który jest uaktualniany.<br/>
**VaultName** — nazwę magazynu kopii zapasowych, który jest uaktualniany.<br/>
**Lokalizacja** — lokalizacja magazynu uaktualniana.<br/>
**Typ zasobu** — Użyj BackupVault.<br/>
**TargetResourceGroupName** — ponieważ w przypadku uaktualniania magazynu do wdrożenia opartego na usłudze Resource Manager, określ grupę zasobów. Możesz użyć istniejącej grupy zasobów lub utwórz ją, podając nową nazwę. W przypadku błędnego wpisania nazwy grupy zasobów może utworzyć nową grupę zasobów. Aby dowiedzieć się więcej na temat grup zasobów, przeczytaj ten [Przegląd informacji o grupach zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Nazwy grup zasobów mogą mieć ograniczenia. Należy postępować zgodnie ze wskazówkami; Niewykonanie tej czynności może spowodować uaktualnienia magazynu nie powiedzie się.
>
>**Azure dla administracji USA** klienci muszą ustawić środowisko do "AzureUSGovernment" podczas uruchamiania skryptu.
>**Chińska wersja platformy Azure** klienci muszą ustawić środowisko do "AzureChinaCloud" podczas uruchamiania skryptu.

Poniższy fragment kodu znajduje się przykład Twoje polecenie programu PowerShell powinien wyglądać następująco:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Można również uruchomić skrypt bez żadnych parametrów i zostanie wyświetlony monit podaj dane wejściowe dla wszystkich wymaganych parametrów.

Skrypt programu PowerShell wyświetli monit o podanie poświadczeń. Wprowadź swoje poświadczenia, dwa razy: jeden raz dla konta programu Service Manager, a drugi raz dla konta usługi Resource Manager.

### <a name="pre-requisites-checking"></a>Sprawdzanie wymagań wstępnych
Po wprowadzeniu poświadczeń platformy Azure, Azure sprawdza, czy dane środowisko spełnia następujące wymagania wstępne:

- **Minimalna wersja agenta** — uaktualnienie magazynów kopii zapasowych do magazynów usługi Recovery Services wymaga agenta usług MARS na co najmniej wersji 2.0.9083.0. W przypadku elementów zarejestrowanych w magazynie kopii zapasowych z agentem starszych niż 2.0.9083.0 sprawdzania wymagań wstępnych kończy się niepowodzeniem. Jeśli sprawdzanie wymagań wstępnych nie powiedzie się, zaktualizuj agenta, a następnie ponowić próbę uaktualnienia magazynu. Możesz pobrać najnowszą wersję agenta z [ https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Zadania konfiguracji i nieustanne**: Gdy osoba niepowołana jest równa Konfigurowanie zadania dla magazynu kopii zapasowych można uaktualnić, czy rejestrowanie elementu, niepowodzenia sprawdzania wymagań wstępnych. Zakończ konfigurację, lub Zakończ rejestrowanie elementu, a następnie uruchom proces uaktualniania magazynu.
- **Model rozliczeń oparty na magazyn**: Magazyny usługi Recovery Services obsługują model rozliczeń oparty na wystąpienie. Jeśli uruchamiasz Uaktualnienie magazynu na magazyn kopii zapasowych, która wykorzystuje model rozliczeń oparty na magazyn, pojawia się monit o uaktualnienie modelu rozliczeń wraz z magazynem. W przeciwnym razie można zaktualizować modelu rozliczeń, a następnie uruchom Uaktualnianie magazynu.
- Określ grupę zasobów dla magazynu usługi Recovery Services. Aby móc korzystać z funkcji wdrażania usługi Resource Manager, należy przełączyć magazyn usługi Recovery Services w grupie zasobów. Jeśli nie wiesz, które grupy zasobów, aby użyć, podaj nazwę i procesu uaktualniania zostanie utworzona grupa zasobów dla Ciebie. Proces uaktualniania powoduje również skojarzenie magazynie z nową grupę zasobów.

Po zakończeniu procesu uaktualniania Sprawdzanie wymagań wstępnych w proces zostanie wyświetlony monit z rozpoczęciem uaktualniania magazynu. Po upewnieniu się, proces uaktualniania zwykle trwa około 15 – 20 minut, w zależności od rozmiaru magazynu. Jeśli masz duży magazynu, uaktualnienie może potrwać do 90 minut.

## <a name="managing-your-recovery-services-vaults"></a>Zarządzanie Magazyny usługi Recovery Services

Następującym zrzucie ekranu przedstawiono nowy magazyn usługi Recovery Services uaktualniony z magazynu usługi Backup, która znajduje się w witrynie Azure portal. Pierwszy ekran przedstawia pulpit nawigacyjny magazynu, który wyświetla kluczowych jednostek dla magazynu.

![przykład magazynu usług Recovery Services uaktualniony z magazynu usługi Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Drugi ekran przedstawia pomocy łącza, które ułatwiają rozpoczęcie korzystania z magazynu usługi Recovery Services.

![linki pomocy w bloku Szybki Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Czynności po uaktualnieniu
Magazyn usługi Recovery Services obsługuje określanie informacji o strefie czasowej w zasadach kopii zapasowych. Po pomyślnym uaktualnieniu magazynu przejdź do pozycji zasady tworzenia kopii zapasowych z menu Ustawienia magazynu i zaktualizuj informacje o strefie czasowej dla każdej zasady skonfigurowane w magazynie. Ten ekran pokazuje już czas harmonogram tworzenia kopii zapasowych określony dla lokalnej strefy czasowej używane podczas tworzenia zasad.

## <a name="enhanced-security"></a>Ulepszone zabezpieczenia

Po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Gdy ustawienia zabezpieczeń znajdują się w niektórych operacji, takich jak usuwanie kopii zapasowych lub zmiana hasła wymagać [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) numeru PIN. Aby uzyskać więcej informacji o zwiększonych zabezpieczeń, zobacz artykuł [funkcje zabezpieczeń, aby chronić hybrydowych kopii zapasowych](backup-azure-security-feature.md).

Zwiększonych zabezpieczeń jest włączone, dane są przechowywane się 14 dni, po usunięciu informacji punktu odzyskiwania z magazynu. Klienci są obciążani za magazynowanie tych danych zabezpieczeń. Przechowywanie danych zabezpieczeń mają zastosowanie do punktów odzyskiwania dla agenta usługi Kopia zapasowa Azure, usługi Azure Backup Server i System Center Data Protection Manager.

## <a name="gather-data-on-your-vault"></a>Zbieranie danych dotyczących magazynu

Po uaktualnieniu do magazynu usługi Recovery Services, skonfigurować raporty usługi Azure Backup (dla maszyn wirtualnych IaaS i Microsoft Azure Recovery Services (MARS)) i umożliwia dostęp do raportów usługi Power BI. Aby uzyskać dodatkowe informacje dotyczące zbierania danych, zapoznaj się z artykułem [raporty Konfigurowanie usługi Azure Backup](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="does-the-upgrade-plan-affect-my-ongoing-backups"></a>Uaktualnij plan wpływa na Moje bieżące kopie zapasowe?
Nie. Trwającą kopii zapasowych bezproblemowo kontynuują podczas i po uaktualnieniu.

### <a name="if-i-dont-plan-on-upgrading-soon-what-happens-to-my-vaults"></a>Jeśli nie planuję wkrótce uaktualniania, co się dzieje Moje magazynów?
Ponieważ wszystkie nowe funkcje są stosowane tylko do magazynów usługi Recovery Services, zalecamy przeczytanie tematu do uaktualnienia Twoich magazynów. Od 1 września 2017 r. Firma Microsoft rozpocznie uaktualnianie automatycznie magazynów kopii zapasowych do magazynów usługi Recovery Services. Od listopada 30,2017, możesz już tworzyć magazynów kopii zapasowych przy użyciu programu PowerShell. Magazyn może zostać automatycznie uaktualniony ilekroć pomiędzy. Microsoft zaleca, aby jak najszybciej uaktualniania magazynu.

### <a name="what-does-this-upgrade-mean-for-my-existing-tooling"></a>Ile kosztuje to oznacza, że uaktualnienie dla moich istniejących narzędzi?
Aktualizacja narzędzi do modelu wdrażania usługi Resource Manager. Użyj zostały utworzone dla dla magazynów usługi Recovery Services w modelu wdrażania usługi Resource Manager. Planowanie modelu wdrażania usługi Resource Manager i ewidencjonowanie aktywności na różnicę w Twoich magazynów jest ważne.

### <a name="during-the-upgrade-is-there-much-downtime"></a>Podczas uaktualniania jest długi Przestój?
To zależy od liczby zasobów, które uaktualniania. W przypadku mniejszych wdrożeń (kilkadziesiąt chronionych wystąpień) całe uaktualnienie powinno zająć mniej niż 20 minut. W przypadku większych wdrożeń powinno zająć maksymalnie godzinę.

### <a name="can-i-roll-back-after-upgrading"></a>Czy mogę wycofać po uaktualnieniu?
Nie. Wycofanie nie jest obsługiwane po pomyślnym uaktualnieniu zasobów.

### <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-upgrade"></a>Można sprawdzić poprawność moją subskrypcję lub Moje zasoby, aby sprawdzić, jeśli są one zdolne do uaktualnienia?
Tak. Pierwszym krokiem podczas uaktualnienia weryfikuje, czy zasoby są zdolne do uaktualnienia. W przypadku, gdy weryfikacja wymagania wstępne nie powiedzie się, zostaną zgłoszone komunikaty dla wszystkich przyczyn, dla których nie można ukończyć uaktualnianie.

### <a name="can-i-upgrade-my-csp-based-backup-vault"></a>Czy mogę zmienić mój opartej na dostawcy usług Kryptograficznych magazynu kopii zapasowych
Nie. Obecnie nie można uaktualnić magazynów kopii zapasowych opartych na dostawcy usług Kryptograficznych. Dodamy obsługę uaktualnienia magazynów kopii zapasowej bazującej na dostawcy usług Kryptograficznych w następnej wersji.

### <a name="can-i-view-my-classic-vault-post-upgrade"></a>Mogę wyświetlić mojej klasycznego magazynu po uaktualnieniu
Nie. Nie można wyświetlić ani zarządzania magazynem klasycznego po uaktualnieniu. Tylko można użyć nowej witryny Azure portal dla wszystkich akcji zarządzania w magazynie.

### <a name="my-upgrade-failed-but-the-machine-that-held-the-agent-requiring-updating-doesnt-exist-anymore-what-do-i-do-in-such-a-case"></a>Moje uaktualnienie nie powiodło się, ale komputer, na którym przechowywane agenta wymagające aktualizacji i już nie istnieje. Co zrobić w takiej sytuacji?
Jeśli potrzebujesz używał magazynu, kopii zapasowych tego komputera w celu długoterminowego przechowywania, możesz nie będzie można uaktualnić magazynu. W przyszłych wydaniach dodamy obsługę dla Uaktualnienie magazynu usługi.
Jeśli nie musisz już przechowywać kopie zapasowe tego komputera, następnie sprawdź wyrejestrowanie tego komputera w magazynie i ponownie przeprowadź uaktualnienie.

### <a name="why-cant-i-see-the-jobs-information-for-my-resources-after-upgrade"></a>Dlaczego nie widzę informacji zadania dla moich zasobów po uaktualnieniu?
Monitorowanie kopii zapasowych (IaaS i agenta MARS) to nowa funkcja, która pojawia się po uaktualnieniu magazynu usługi Backup do magazynu usługi Recovery Services. Informacje monitorowania trwa maksymalnie 12 godzin do synchronizacji z usługą.

### <a name="how-do-i-report-an-issue"></a>Jak mogę zgłosić problem?
W przypadku niepowodzenia jakiejkolwiek jego części Uaktualnienie magazynu należy pamiętać, że identyfikatorowi OperationId na liście błędów. Microsoft Support aktywnie będzie działać w celu rozwiązania problemu. Można Dotrzyj do działu pomocy technicznej lub Wyślij wiadomość e-mail rsvaultupgrade@service.microsoft.com z Identyfikatora subskrypcji, nazwę magazynu i OperationId. Firma Microsoft podejmie możliwie jak najszybciej rozwiązać problem. Nie należy wykonywać ponowień operacji, chyba że jawnie zgodnie z instrukcjami otrzymywanymi Aby to zrobić przez firmę Microsoft.


## <a name="next-steps"></a>Kolejne kroki
Skorzystaj z poniższego artykułu, aby:</br>
[Tworzenie kopii zapasowych maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Tworzenie kopii zapasowych usługi Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md).
