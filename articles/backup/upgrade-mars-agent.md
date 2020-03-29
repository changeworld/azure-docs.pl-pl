---
title: Uaktualnianie agenta usług odzyskiwania platformy Microsoft Azure (MARS)
description: Dowiedz się, jak uaktualnić agenta usług odzyskiwania platformy Microsoft Azure (MARS).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672897"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>Uaktualnianie agenta usług odzyskiwania platformy Microsoft Azure (MARS)

W tym artykule dowiesz się, jak:

* Identyfikowanie serwerów z wcześniejszymi wersjami agenta MARS
* Aktualizowanie instalacji MARS na tych serwerach

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Identyfikowanie serwerów z wcześniejszymi wersjami agenta MARS

W przypadku instalacji agenta usługi Azure Backup i serwera kopii zapasowej platformy Azure:

1. Przejdź do magazynu usług odzyskiwania, gdzie masz zarejestrowane serwery, które mogą być archiwizowane przez starsze wersje agenta. W alertach usługi Azure Backup Update z platformy Azure można znaleźć reprezentatywną listę magazynów ze starszymi agentami usługi Azure Backup Update.
1. W sekcji **Ustawienia** po lewej stronie magazynu usług odzyskiwania wybierz pozycję **Infrastruktura kopii zapasowych** w sekcji **Zarządzanie.**
1. Aby odnajdować agentów kopii zapasowych platformy Azure zainstalowanych w ramach instalacji serwera usługi Azure Backup, przejdź do **witryny Serwery zarządzania kopiami zapasowymi** w obszarze **Serwery zarządzania**. Spowoduje to wyświetlone listę serwerów, które mają instalacje serwera usługi Azure Backup wraz z numerem wersji skojarzonego agenta usługi Azure Backup.

    ![Lista agentów MARS zainstalowanych w ramach instalacji serwera usługi Azure Backup](./media/upgrade-mars-agent/backup-management-servers.png)

1. Aby sprawdzić wersje agentów dla instalacji agenta usług Microsoft Azure Recovery Services (MARS) lub agenta usługi Azure Backup, przejdź do **witryny Serwery chronione** w obszarze **Serwery zarządzania**. Następnie wybierz **agenta usługi Azure Backup** w obszarze Typ zarządzania kopiami zapasowymi. Spowoduje to wyświetle listę serwerów, które mają instalacje agenta usługi Azure Backup wraz z numerem wersji instalacji.

    ![Lista serwerów z zainstalowanym agentem MARS](./media/upgrade-mars-agent/protected-servers.png)

1. Posortuj kolumnę Wersja agenta usługi Azure Backup Agent, klikając **kolumnę Wersja agenta** dla instalacji agenta MARS lub **kolumnę Wersja agenta usługi Azure Backup Agent** dla instalacji serwera kopii zapasowych platformy Azure.

1. W poprzednim kroku zostanie wyświetlona lista serwerów z agentami usługi Azure Backup, które mają wersje niższe niż 2.0.9083.0 lub wersje agenta wymienione jako puste miejsca. Są to serwery, na których muszą zostać zaktualizowane agenci usługi Azure Backup.

## <a name="update-the-mars-agent-installation-on-the-server"></a>Aktualizowanie instalacji agenta MARS na serwerze

Po zidentyfikowaniu serwerów, które wymagają aktualizacji agenta usługi Azure Backup, wykonaj następujące kroki dla każdego zidentyfikowanego serwera (przy użyciu serwera kopii zapasowej platformy Azure lub agenta MARS). [Pobierz najnowszą wersję agenta kopii zapasowej platformy Azure,](https://aka.ms/azurebackup_agent) zanim wykonaj poniższe czynności.

1. Kliknij wiersz, który ma agenta usługi Azure Backup poniżej 2.0.9083.0 lub puste. Spowoduje to otwarcie ekranu szczegółów serwera.

    ![Chronione serwery z nieaktualnymi wersjami agenta](./media/upgrade-mars-agent/old-agent-version.png)

    ![Serwery kopii zapasowych platformy Azure z nieaktualnymi wersjami agenta](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Kliknij przycisk **Połącz,** aby otrzymać plik połączenia pulpitu zdalnego, aby połączyć się z serwerem lub bezpośrednio połączyć się z serwerem za pośrednictwem połączenia pulpitu zdalnego na serwerze.

    ![Łączenie się z serwerem za pośrednictwem połączenia pulpitu zdalnego](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Jeśli wymieniony serwer nie istnieje lub został wycofany z eksploatacji, można zignorować pozostałe kroki poniżej i przejść do następnego serwera.

1. Wprowadź dane logowania administracyjnego i zaloguj się.

1. Jeśli serwer proxy serwera lub serwera ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na serwerze/serwerze proxy są skonfigurowane tak, aby zezwalały na adres URL odpowiedni dla używanej chmury azure:

    Chmura platformy Azure | Adres URL
    -- | ---
    Chmura Azure (publiczna) |   `https://login.windows.net`
    Chmura Azure China 21Vianet   | `https://login.chinacloudapi.cn`
    Chmura dla instytucji rządowych platformy Azure w USA |   `https://login.microsoftonline.us`
    Niemiecka chmura platformy Azure  |  `https://login.microsoftonline.de`

1. Skopiuj instalator aktualizacji agenta kopii zapasowej platformy Azure na serwer.

    ![Kopiowanie instalatora aktualizacji agenta kopii zapasowej platformy Azure na serwer](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Uruchom instalatora. Zostanie otwarty Kreator uaktualniania agenta usług odzyskiwania platformy Microsoft Azure.

    ![Kreator uaktualniania agenta usług odzyskiwania platformy Microsoft Azure](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Kliknij przycisk **alej**.

1. Kliknij przycisk **Uaktualnij**.

    ![Instalacja agenta usług odzyskiwania platformy Microsoft Azure](./media/upgrade-mars-agent/upgrade-installation.png)

1. Końcowy ekran potwierdzenia wskazuje, że agent usługi Azure Backup został pomyślnie zaktualizowany.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Dla klientów Programu System Center Data Protection Manager (SC DPM)

Jeśli na serwerach Menedżera ochrony danych systemu Center (SC DPM) zainstalowano agentów tworzenia kopii zapasowych platformy Azure, należy wykonać poniższe czynności, aby określić, czy serwery programu DPM wymagają aktualizacji agenta kopii zapasowej platformy Azure:

1. Zaloguj się do serwera SC DPM jako administrator.
2. Otwórz konsolę programu DPM.
3. Kliknij pozycję **Zarządzanie** w lewym dolnym lewym przycisku myszy na konsoli.
4. W informacjach wyświetlanych w lewej nawigacji poszukaj informacji o wersji agenta usługi Azure Backup.
5. Jeśli wersja jest niższa niż 2.0.9083.0, pobierz najnowszy instalator agenta kopii zapasowej platformy Azure i uruchom instalatora na serwerze programu DPM, aby zaktualizować agenta usługi Azure Backup.

Powtórz powyższe kroki dla wszystkich serwerów programu DPM w twoim środowisku.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wykonać [kopię zapasową maszyn z systemem Windows przy użyciu agenta marsjańskiej kopii zapasowej azure](backup-windows-with-mars-agent.md)
