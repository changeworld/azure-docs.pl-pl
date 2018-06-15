---
title: Tworzenie klasycznego Azure maszyny Wirtualnej z systemem MySQL | Dokumentacja firmy Microsoft
description: Utwórz maszynę wirtualną platformy Azure systemem Windows Server 2012 R2 i bazy danych MySQL, przy użyciu klasycznego modelu wdrażania.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: be2a4232fdb59deb87d6d9340fd22909707294b8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918119"
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Zainstaluj MySQL na maszyny wirtualnej utworzonej z klasycznym modelu wdrażania systemem Windows Server 2016
[MySQL](https://www.mysql.com) jest popularnych typu open source, bazy danych SQL. Ten samouczek pokazuje, jak instalować i uruchamiać **społecznościową wersję MySQL 5.7.18** jako serwer MySQL na maszynie wirtualnej z systemem **systemu Windows Server 2016**. Środowiska mogą być nieco inne w przypadku innych wersji MySQL lub Windows Server.

Aby uzyskać instrukcje dotyczące instalowania MySQL w systemie Linux, zapoznaj się: [sposób instalowania MySQL na platformie Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Utwórz maszynę wirtualną z systemem Windows Server 2016
Jeśli nie masz jeszcze maszyny Wirtualnej z systemem Windows Server 2016, możesz użyć tej funkcji [samouczek](./tutorial.md) można utworzyć maszyny wirtualnej.

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
Po utworzeniu maszyny wirtualnej, można opcjonalnie dołączyć dysku danych. Dodawanie dysku danych jest zalecane w przypadku obciążeń produkcyjnych i w celu uniknięcia brakiem miejsca na dysku systemu operacyjnego (C:), która obejmuje system operacyjny.

Zobacz [jak można dołączyć dysku danych do maszyny wirtualnej systemu Windows](../attach-managed-disk-portal.md) i postępuj zgodnie z instrukcjami dotyczącymi dołączanie pusty dysk. W ustawieniu hosta pamięci podręcznej **Brak** lub **tylko do odczytu**.

## <a name="log-on-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej
Następnie będzie [Zaloguj się do maszyny wirtualnej](./connect-logon.md) , można zainstalować MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Zainstaluj i uruchom program MySQL Community Server na maszynie wirtualnej
Wykonaj następujące kroki, aby zainstalować, skonfigurować i uruchomić społecznościową wersję serwera MySQL:

> [!NOTE]
> Podczas pobierania elementów za pomocą programu Internet Explorer, można ustawić IE **Konfiguracja zwiększonych zabezpieczeń** do wyłączone i uprościć proces pobierania. Z Start menu kliknij administracyjne serwera Narzędzia Menedżera/lokalnego serwera, a następnie IE **Konfiguracja zwiększonych zabezpieczeń** i konfiguracji na Off).
>
>

1. Po nawiązaniu połączenia z maszyną wirtualną przy użyciu pulpitu zdalnego, kliknij przycisk **programu Internet Explorer** na ekranie startowym.
2. Wybierz **narzędzia** przycisk w prawym górnym rogu (ikona kółka cogged), a następnie kliknij przycisk **Opcje internetowe**. Kliknij przycisk **zabezpieczeń** , kliknij pozycję **Zaufane witryny** ikonę, a następnie kliknij przycisk **witryny** przycisku. Dodaj http://*.mysql.com do listy zaufanych witryn. Kliknij przycisk **Zamknij**, a następnie kliknij przycisk **OK**.
3. Wpisz w adres pasek programu Internet Explorer, https://dev.mysql.com/downloads/mysql/.
4. Znajdź i Pobierz najnowszą wersję Instalatora MySQL dla systemu Windows za pomocą witryny MySQL. W przypadku wybrania Instalator MySQL, pobrać wersję z pełną plików zestawu (na przykład mysql — Instalator — społeczność 5.7.18.0.msi pliku o rozmiarze 352.8 MB) i Zapisz Instalator.
5. Gdy Instalator zakończył pobieranie, kliknij przycisk **Uruchom** można uruchomić Instalatora.
6. Na **umowy licencyjnej** , zaakceptuj umowę licencyjną i kliknij przycisk **dalej**.
7. Na **Wybieranie typu instalacji** kliknij typ instalacji, który, a następnie kliknij pozycję **dalej**. W następujących krokach założono wybór **tylko serwera** typ instalacji.
8. Jeśli **Sprawdź wymagania** wyświetlana, kliknij przycisk **Execute** aby umożliwić Instalator Zainstaluj brakujące składniki. Postępuj zgodnie z instrukcjami wyświetlanych, takich jak pakiet redystrybucyjny programu C++ środowiska wykonawczego.
9. Na **instalacji** kliknij przycisk **Execute**. Po zakończeniu instalacji kliknij przycisk **dalej**.

10. Na **konfiguracji produktu** kliknij przycisk **dalej**.

11. Na **typu i sieci** strony, określ odpowiednią konfigurację typu i łączności opcje, łącznie z portem TCP, w razie potrzeby. Wybierz **Pokaż opcje zaawansowane**, a następnie kliknij przycisk **dalej**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Na **kont i ról** Określ silne hasło główne MySQL. W razie potrzeby dodaj dodatkowe konta użytkowników MySQL, a następnie kliknij przycisk **dalej**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Na **usługi systemu Windows** określić zmiany ustawień domyślnych dla uruchomiony jako usługa systemu Windows MySQL Server, zgodnie z potrzebami, a następnie kliknij przycisk **dalej**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Opcje dostępne na **dodatki i rozszerzenia** strony są opcjonalne. Kliknij przycisk **dalej** aby kontynuować.
15. Na **zaawansowane opcje** określić zmiany opcji rejestrowania, w razie potrzeby, a następnie kliknij przycisk **dalej**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Na **zastosować konfiguracji serwera** kliknij przycisk **Execute**. Po zakończeniu kroków konfiguracji, kliknij przycisk **Zakończ**.
17. Na **konfiguracji produktu** kliknij przycisk **dalej**.
18. Na **zakończenia instalacji** kliknij przycisk **dziennik kopiowania do Schowka** aby ją później zbadać, a następnie kliknij przycisk **Zakończ**.
19. Na ekranie startowym wpisz **mysql**, a następnie kliknij przycisk **klient wiersza polecenia 5.7 MySQL**.
20. Wprowadź hasła głównego określonego w kroku 12, która jest wyświetlana wraz z monitem o których możesz wykonywać polecenia, aby skonfigurować MySQL. Aby uzyskać szczegóły polecenia i składnię, zobacz [instrukcje MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Można również skonfigurować ustawienia domyślne konfiguracji serwera, takich jak podstawowe i danych katalogów i dysków. Aby uzyskać więcej informacji, zobacz [6.1.2 domyślnie przyjmowana jest konfiguracja serwera](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Dla usługi MySQL, które mają być dostępne dla komputerów klienckich w Internecie należy skonfigurować punkt końcowy dla portu TCP i utworzyć regułę zapory systemu Windows. Domyślna wartość portu, na którym usługi MySQL serwer nasłuchuje klientów MySQL to 3306. Można określić innego portu, tak długo, jak port jest zgodna z wartością na **typu i sieci** (krok 11 poprzedniej procedury).

> [!NOTE]
> W środowisku produkcyjnym należy wziąć pod uwagę ryzyko związane z udostępnianie usługi MySQL serwer do wszystkich komputerów w Internecie. Możesz zdefiniować zestaw źródłowych adresów IP, które mogą używać punktu końcowego z listy kontroli dostępu (ACL). Aby uzyskać więcej informacji, zobacz [jak się punkty końcowe z maszyną wirtualną](setup-endpoints.md).
>
>

Aby skonfigurować punkt końcowy usługi MySQL serwera:

1. W portalu Azure kliknij **maszyn wirtualnych (klasyczne)**, kliknij nazwę maszyny wirtualnej MySQL, a następnie kliknij przycisk **punkty końcowe**.
2. Na pasku poleceń, kliknij przycisk **Dodaj**.
3. Na **dodać punkt końcowy** wpisz unikatową nazwę **nazwa**.
4. Wybierz **TCP** jako protokół.
5. Wpisz numer portu, na przykład **3306**, zarówno w **Port publiczny** i **Port prywatny**, a następnie kliknij przycisk **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Dodaj regułę zapory systemu Windows, aby zezwolić na ruch MySQL
Aby dodać regułę zapory systemu Windows, która zezwala na ruch MySQL z Internetu, uruchom następujące polecenie w _wiersza polecenia programu Windows PowerShell z podwyższonym poziomem uprawnień_ na maszynie wirtualnej serwera MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Testowanie połączenia zdalnego
Aby przetestować połączenia zdalnego do maszyny Wirtualnej Azure uruchomiona usługa serwera MySQL, podaj nazwę DNS usługi w chmurze zawierających VN.

1. W portalu Azure kliknij **maszyn wirtualnych (klasyczne)**, kliknij nazwę maszyny wirtualnej serwera MySQL, a następnie kliknij przycisk **omówienie**.
2. Na pulpicie nawigacyjnym maszyny wirtualnej, należy zwrócić uwagę **nazwy DNS** wartość. Oto przykład:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Z komputera lokalnego z systemem MySQL lub klienta MySQL uruchom następujące polecenie, aby zalogować się jako użytkownik MySQL.

     MySQL -u <yourMysqlUsername> - p -h <yourDNSname>

   Na przykład przy użyciu nazwy użytkownika MySQL _dbadmin3_ i _testmysql.cloudapp.net_ nazwy DNS dla maszyny wirtualnej, można uruchomić MySQL za pomocą następującego polecenia:

     testmysql.cloudapp.net -h -p dbadmin3 -u MySQL

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej o uruchamianiu MySQL, zobacz [dokumentacji MySQL](http://dev.mysql.com/doc/).
