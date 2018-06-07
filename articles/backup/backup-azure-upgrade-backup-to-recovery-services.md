---
title: Uaktualnij magazynu kopii zapasowych do magazynu usług odzyskiwania
description: Instrukcje i informacje pomocy technicznej w celu uaktualnienia magazynu usługi Kopia zapasowa Azure do magazynu usług odzyskiwania.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: 551bc2aa4ff80feb3f28b5698e25dfd1b03dc870
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607341"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Uaktualnij magazynu kopii zapasowych do magazynu usług odzyskiwania

W tym artykule opisano sposób uaktualniania magazynu kopii zapasowych do magazynu usług odzyskiwania. Proces uaktualniania bez wpływu na wszystkie zadania uruchomione w kopii zapasowej, a nie zostały utracone nie dane kopii zapasowej. Głównych powodów uaktualniania magazynu kopii zapasowych do magazynu usług odzyskiwania:
- Wszystkie funkcje magazynu kopii zapasowych są przechowywane w magazynie usług odzyskiwania.
- Magazyny usług odzyskiwania mają więcej funkcji niż magazyny kopii zapasowych, w tym: ze względów bezpieczeństwa zintegrowane monitorowanie, szybciej przeprowadzać operacje przywracania i na poziomie pozycji.
- Zarządzanie elementów kopii zapasowych z ulepszonych, uproszczone portalu.
- Nowe funkcje dotyczą tylko Magazyny usług odzyskiwania.

## <a name="impact-to-operations-during-upgrade"></a>Wpływ na operacje podczas uaktualniania

Podczas uaktualniania magazynu kopii zapasowych do magazynu usług odzyskiwania, nie ma żadnych wpływu na operacje płaszczyzna danych. Wszystkie zadania tworzenia kopii zapasowych nadal normalnie, a wszystkie zadania przywracania active nadal bez przeszkód. Podczas uaktualniania operacji zarządzania pociągnąć za sobą krótki czas przestoju i nie można chronić nowych elementów lub utworzyć zadania tworzenia kopii zapasowych ad hoc. Zadania przywracania dla maszyn wirtualnych IaaS nie działają podczas uaktualniania. Uaktualnienie magazynu trwa krótszym niż jedna godzina ukończenia. Po skończeniu magazynu usług odzyskiwania zastępuje magazyn kopii zapasowych.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Zmiany do automatyzacji i narzędzia po uaktualnieniu

Podczas przygotowywania infrastruktury do uaktualnienia magazynu, należy zaktualizować narzędzi, aby upewnić się, że nadal działać po uaktualnieniu lub z istniejącej automatyzacji.
Zapoznaj się z odwołań do poleceń cmdlet programu PowerShell dla [modelu wdrażania usługi Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Przed uaktualnieniem

Sprawdź następujące problemy przed uaktualnieniem programu magazyny kopii zapasowych do magazynów usług odzyskiwania.

- **Wersja agenta minimalna**: Aby uaktualnić magazynu, upewnij się, agent usług odzyskiwania Azure firmy Microsoft (MARS) jest co najmniej wersji 2.0.9083.0. Jeśli agenta MARS jest starsza niż 2.0.9083.0, zaktualizuj agenta, przed rozpoczęciem procesu uaktualniania.
- **Metoda oparta na modelu rozliczeń**: Magazyny usług odzyskiwania obsługują tylko na podstawie wystąpienia modelu rozliczeń. Jeśli masz magazynu kopii zapasowej, który używa starszej modelu rozliczeń na podstawie magazynu, należy przekonwertować modelu rozliczenia podczas uaktualniania.
- **Żadne operacje konfiguracji kopii zapasowej w toku**: podczas uaktualniania jest ograniczony dostęp do płaszczyzny zarządzania. Wykonanie wszystkich akcji płaszczyzny zarządzania, a następnie uruchom uaktualnianie.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Aby uaktualnić Twoje magazynów za pomocą skryptów programu PowerShell

Skrypty programu PowerShell służą do uaktualnienia programu magazyny kopii zapasowych do magazynów usług odzyskiwania. Sprawdź, czy masz wymagane składniki programu PowerShell w celu wyzwolenia aktualizacji magazynu. Skrypty programu PowerShell dla magazyny kopii zapasowych nie działają w przypadku magazynów usług odzyskiwania. Przygotuj środowisko do uaktualnienia z magazynów:

1. Zainstaluj lub Uaktualnij [Windows Management Framework (WMF) w wersji 5](https://www.microsoft.com/download/details.aspx?id=50395) lub nowszej.
2. [Zainstaluj Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Pobierz [skrypt programu PowerShell](https://aka.ms/vaultupgradescript2) do uaktualnienia z magazynów.

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell

Użyj następującego skryptu, aby uaktualnić Twoje magazynów. Poniższy przykładowy skrypt zawiera wyjaśnienia dotyczące parametrów.

RecoveryServicesVaultUpgrade 1.0.2.ps1 **- SubscriptionID** `<subscriptionID>` **- VaultName** `<vaultname>` **-lokalizacji** `<location>` **- ResourceType** `BackupVault` **- TargetResourceGroupName** `<rgname>`

**Identyfikator subskrypcji** — identyfikator subskrypcji magazynu, który jest uaktualniany.<br/>
**VaultName** -nazwę magazynu kopii zapasowych, który jest uaktualniany.<br/>
**Lokalizacja** — lokalizacja magazynu uaktualniany.<br/>
**Typ zasobu** -Użyj BackupVault.<br/>
**TargetResourceGroupName** — ponieważ w przypadku uaktualniania magazynu do wdrożenia na podstawie Resource Manager, określ grupę zasobów. Można użyć istniejącej grupy zasobów lub utwórz go, podając nową nazwę. Jeśli wpiszesz nazwę grupy zasobów może utworzyć nową grupę zasobów. Aby dowiedzieć się więcej na temat grup zasobów, przeczytaj to [informacje na temat grup zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Nazwy grup zasobów ma ograniczenia. Należy postępować zgodnie ze wskazówkami; Błąd w tym celu może spowodować uaktualnienia magazynu się niepowodzeniem.
>
>**Azure instytucji rządowych Stanów Zjednoczonych** klientów należy ustawić w środowisku "AzureUSGovernment" podczas uruchamiania skryptu.
>**Azure Chin** klientów należy ustawić w środowisku "AzureChinaCloud" podczas uruchamiania skryptu.

Poniższy fragment kodu jest przykładem z polecenia programu PowerShell powinien wyglądać następująco:

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Można również uruchomić skrypt bez żadnych parametrów i zostanie wyświetlona prośba o podanie danych wejściowych wszystkich wymaganych parametrów.

Skrypt programu PowerShell wyświetli monit o podanie poświadczeń. Wprowadź swoje poświadczenia dwa razy: raz dla konta programu Service Manager, a na drugim dla konta usługi Resource Manager.

### <a name="pre-requisites-checking"></a>Wstępne sprawdzanie
Po wprowadzeniu poświadczeń platformy Azure, Azure sprawdza, czy dane środowisko spełnia następujące wymagania wstępne:

- **Wersja agenta minimalna** -magazyny kopii zapasowych magazynów usług odzyskiwania uaktualnianą agenta MARS na co najmniej wersji 2.0.9083.0. Jeśli masz elementy zarejestrowane do magazynu kopii zapasowej z agentem starszych niż 2.0.9083.0 sprawdzanie wymagań wstępnych kończy się niepowodzeniem. Jeśli wymagania wstępne nie powiedzie się, zaktualizuj agenta i ponowić próbę uaktualnienia magazynu. Możesz pobrać najnowszą wersję agenta z [ http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe ](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Zadania konfiguracji i nieustanne**: Jeśli ktoś konfiguruje zadania dla magazynu kopii zapasowych ustawić do uaktualnienia, rejestrowanie elementu, sprawdzanie wymagań wstępnych kończy się lub. Zakończ konfigurację, lub zakończenie rejestracji elementu, a następnie uruchom proces uaktualniania magazynu.
- **Oparty na magazynie modelu rozliczeń**: Magazyny usług odzyskiwania obsługują metoda oparta na modelu rozliczeń. Po uruchomieniu uaktualniania magazynu w magazynie kopii zapasowych, który korzysta z magazynu na podstawie modelu rozliczeń monit o uaktualnienie modelu rozliczeń wraz z magazynem. W przeciwnym razie można zaktualizować modelu rozliczeń najpierw, a następnie uruchom Uaktualnianie magazynu.
- Określ grupę zasobów dla magazynu usług odzyskiwania. Aby móc korzystać z funkcji wdrażania usługi Resource Manager, należy przełączyć magazyn usług odzyskiwania w grupie zasobów. Jeśli nie wiadomo, które grupy zasobów, aby użyć, podaj nazwę i procesu uaktualniania zostanie utworzona grupa zasobów dla Ciebie. Proces uaktualniania powoduje również skojarzenie magazynu z nową grupę zasobów.

Po zakończeniu procesu uaktualniania sprawdzanie wstępne proces wyświetli monit o uruchom operację uaktualniania magazynu. Po upewnieniu się, proces uaktualniania zwykle trwa około 15-20 minut do wykonania, w zależności od rozmiaru magazynu. Jeśli masz dużą magazynu Uaktualnianie może potrwać do 90 minut.

## <a name="managing-your-recovery-services-vaults"></a>Zarządzanie z Magazyny usług odzyskiwania

Następujące ekrany Pokaż nowy magazyn usług odzyskiwania uaktualnione z magazynu kopii zapasowych w portalu Azure. Pierwszy ekran pokazuje pulpitu nawigacyjnego magazynu, który wyświetla jednostek klucza magazynu.

![przykład uaktualnienia z magazynu kopii zapasowych magazynu usług odzyskiwania](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Drugi ekran pokazuje pomocy łącza może pomóc Ci rozpocząć korzystanie z magazynu usług odzyskiwania.

![łącza pomocy w bloku Szybki Start](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Czynności po uaktualnieniu
Magazyn usług odzyskiwania obsługują określania informacji o strefie czasowej zasad tworzenia kopii zapasowej. Po pomyślnym uaktualnieniu magazynu, przejdź do zasady tworzenia kopii zapasowej z menu ustawień magazynu i zaktualizować informacje o strefie czasowej dla każdej zasady skonfigurowane w magazynie. Ten ekran pokazuje już czas harmonogram tworzenia kopii zapasowych określony na lokalnej strefie czasowej używane podczas tworzenia zasad. 

## <a name="enhanced-security"></a>Większe bezpieczeństwo

Po uaktualnieniu magazynu kopii zapasowych do magazynu usług odzyskiwania, ustawienia zabezpieczeń dla tego magazynu są automatycznie włączone. Gdy ustawienia zabezpieczeń znajdują się na niektórych operacji, takich jak usuwanie kopii zapasowych lub zmiana hasła wymagają [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) numeru PIN. Aby uzyskać więcej informacji na zwiększonych zabezpieczeń, zobacz artykuł [funkcji zabezpieczeń do ochrony kopii zapasowych hybrydowego](backup-azure-security-feature.md). 

Po włączeniu zwiększonych zabezpieczeń danych jest zachowywana się do 14 dni, po usunięciu informacji punktu odzyskiwania z magazynu. Klienci są rozliczane do przechowywania tych danych zabezpieczeń. Przechowywanie danych zabezpieczeń ma zastosowanie do punktów odzyskiwania dla agenta usługi Kopia zapasowa Azure, serwer usługi Kopia zapasowa Azure i System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Zbieranie danych dotyczących magazynu

Po uaktualnieniu do magazynu usług odzyskiwania, skonfiguruj raporty dla usługi Kopia zapasowa Azure (w przypadku maszyn wirtualnych IaaS i usług odzyskiwania Azure firmy Microsoft (MARS)) i umożliwia dostęp do raportów usługi Power BI. Aby uzyskać dodatkowe informacje dotyczące zbierania danych, zobacz artykuł, [raporty skonfigurować kopia zapasowa Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Plan uaktualniania wpływa na Moje bieżące kopie zapasowe?**</br>
Nie. Kopie zapasowe trwającą nadal przerwana podczas i po uaktualnieniu.

**Jeśli nie należy zaplanować na temat uaktualniania wkrótce, co się dzieje z mojej magazynów?**</br>
Ponieważ wszystkie nowe funkcje dotyczą tylko Magazyny usług odzyskiwania, zalecamy uaktualnienie z magazynów. Uruchamianie 1 września 2017 Microsoft rozpocznie uaktualniania automatycznie magazynów kopii zapasowych do magazynów usług odzyskiwania. Od listopada 30,2017, możesz już tworzyć magazyny kopii zapasowych przy użyciu programu PowerShell. Magazyn można automatycznie uaktualnić wszystkie czas między. Firma Microsoft zaleca się, że jak najszybciej Uaktualnij magazynu.

**Jaki jest średnia ta uaktualnienia do mojej istniejącej oprzyrządowania?**</br>
Aktualizacja z narzędziami do modelu wdrażania usługi Resource Manager. Usługi odzyskiwania, który magazynów zostały utworzone dla używać w modelu wdrażania usługi Resource Manager. Planowanie modelu wdrażania usługi Resource Manager i ewidencjonowanie aktywności różnicy z magazynów jest ważne. 

**Podczas uaktualniania jest znacznie Przestój?**</br>
To zależy od liczby zasobów, które jest uaktualniany. Przy mniejszych wdrożeniach (dziesiątki kilka wystąpień chronionym) uaktualnienie całego powinny zająć mniej niż 20 minut. W przypadku większych wdrożeń powinien upłynąć maksymalnie godziny.

**I przywrócić po uaktualnieniu?**</br>
Nie. ROLLBACK nie jest obsługiwana, gdy zasoby zostały pomyślnie uaktualnione.

**Moja subskrypcja lub zasobów, aby sprawdzić, czy są one w stanie uaktualnienia I zweryfikować?**</br>
Tak. Pierwszym krokiem podczas uaktualnienia weryfikuje, czy zasoby są w stanie uaktualnienia. W przypadku niepowodzenia weryfikacji wymagań wstępnych komunikaty z przyczyn, którego nie można ukończyć uaktualnianie.

**Można uaktualnić mój opartych na dostawcy usług Kryptograficznych magazynu kopii zapasowej?**</br>
Nie. Obecnie nie można uaktualnić magazynów kopii zapasowych opartych na dostawcy usług Kryptograficznych. Firma Microsoft zostanie dodana obsługa uaktualniania opartych na dostawcy usług Kryptograficznych magazyny kopii zapasowych w następnej wersji.

**Może wyświetlać Mój po uaktualnieniu magazynu classic?**</br>
Nie. Nie można wyświetlić lub zarządzaj nimi po uaktualnieniu magazynu klasycznego. Tylko będzie mógł używać nowego portalu Azure do wszystkich akcji zarządzania w magazynie.

**Moje uaktualnienie nie powiodło się, ale komputera, na którym przechowywane agenta wymagające aktualizacji, już nie istnieje. Co zrobić w takim przypadku?**</br>
Chcąc korzystać z magazynu kopii zapasowych z tego komputera w celu przechowywania długoterminowego, możesz nie będzie można uaktualnić w magazynie. W przyszłych wersjach dodamy obsługę uaktualnianie takiego magazynu.
Jeśli nie trzeba już przechowywania kopii zapasowych tego komputera, następnie sprawdź wyrejestrować tej maszyny z magazynu i ponów uaktualnienie.

**Dlaczego nie widzę informacji zadania dla mojej zasobów po uaktualnieniu?**</br>
Monitorowanie kopii zapasowych (IaaS i MARS agent) jest nową funkcją, która pojawia się po uaktualnieniu magazynu kopii zapasowej w magazynie usług odzyskiwania. Dane monitorowania ma 12 godzin do synchronizacji z usługą.

**Jak zgłosić problem?**</br>
W przypadku niepowodzenia jakiejkolwiek jego części uaktualnienia magazynu należy pamiętać, że OperationId na liście w dzienniku błędów. Aby rozwiązać ten problem aktywnie działa Support firmy Microsoft. Może dotrzeć do pomocy technicznej lub wiadomość e-mail na adres rsvaultupgrade@service.microsoft.com Twojego Identyfikatora subskrypcji, nazwę magazynu i OperationId. Firma Microsoft podejmie próbę rozwiązania problemu tak szybko jak to możliwe. Nie próbuj ponownie wykonać operację, chyba że jawnie instrukcją w tym celu przez firmę Microsoft.


## <a name="next-steps"></a>Kolejne kroki
Użyj następującego artykułu, aby:</br>
[Tworzenie kopii zapasowej maszyn wirtualnych IaaS](backup-azure-arm-vms-prepare.md)</br>
[Utwórz kopię zapasową serwera kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md)</br>
[Tworzenie kopii zapasowej systemu Windows Server](backup-configure-vault.md).
