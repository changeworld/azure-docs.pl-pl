---
title: Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure z usługą Azure Backup
description: W tym artykule opisano sposób tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure przy użyciu usługi Azure Backup.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217046"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure

W tym artykule opisano sposób tworzenia kopii zapasowej i przywracanie maszyn wirtualnych Windows lub Linux, Azure (maszyny wirtualne) zaszyfrowanych dysków przy użyciu [kopia zapasowa Azure](backup-overview.md) usługi.

Jeśli chcesz dowiedzieć się więcej na temat usługi Azure Backup współdziałania z maszyn wirtualnych platformy Azure przed rozpoczęciem zawarte w tych zasobach:

- [Przegląd](backup-architecture.md#architecture-direct-backup-of-azure-vms) architektura kopii zapasowych maszyn wirtualnych platformy Azure.
- [Dowiedz się więcej o](backup-azure-vms-introduction.md) kopii zapasowych maszyn wirtualnych platformy Azure i rozszerzenia usługi Azure Backup.

## <a name="encryption-support"></a>Obsługa szyfrowania

Usługa Azure Backup obsługuje kopie zapasowe maszyn wirtualnych platformy Azure, które mają ich system operacyjny/dyski danych zaszyfrowanych za pomocą szyfrowania dysków Azure (ADE). ADE używa funkcji BitLocker do szyfrowania maszyn wirtualnych Windows oraz funkcji dm-crypt dla maszyn wirtualnych systemu Linux. ADE integruje się z usługą Azure Key Vault w celu zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków. Klucze szyfrowania klucza klucza magazynu (kluczy Kek) może służyć do dodać dodatkową warstwę zabezpieczeń, szyfrowanie kluczy szyfrowania tajnych przed zapisaniem ich do usługi Key Vault.

Usługa Azure Backup można kopii zapasowej i przywracania maszyn wirtualnych platformy Azure przy użyciu ADE z i bez niej aplikacji usługi Azure AD, zgodnie z opisem w poniższej tabeli.

**Typ dysku maszyny wirtualnej** | **ADE (klucz szyfrowania bloków dm-crypt)** | **ADE i KEK**
--- | --- | ---
**niezarządzane** | Yes | Yes
**Zarządzane**  | Tak | Yes

- Dowiedz się więcej o [ADE](../security/azure-security-disk-encryption-overview.md), [usługi Key Vault](../key-vault/key-vault-overview.md), i [kluczy Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/).
- Odczyt [— często zadawane pytania](../security/azure-security-disk-encryption-faq.md) dotyczące szyfrowania dysku maszyny Wirtualnej platformy Azure.



### <a name="limitations"></a>Ograniczenia

- Można tworzenie kopii zapasowej i przywracania zaszyfrowanych maszyn wirtualnych w ramach tej samej subskrypcji i regionu.
- Usługa Azure Backup obsługuje maszyny wirtualne szyfrowane przy użyciu kluczy autonomicznych. Dowolny klawisz, który jest częścią certyfikat używany do szyfrowania maszyny Wirtualnej nie jest obecnie obsługiwane.
- Można tworzenie kopii zapasowej i przywracania zaszyfrowanych maszyn wirtualnych w ramach tej samej subskrypcji i regionie co magazyn kopii zapasowych usług odzyskiwania.
- Nie można odzyskać zaszyfrowanych maszyn wirtualnych na poziomie plików/folderów. Musisz odzyskać całą maszynę Wirtualną można przywrócić pliki i foldery.
- Przywracanie maszyny Wirtualnej, nie można użyć [zastąpienia istniejącej maszyny Wirtualnej](backup-azure-arm-restore-vms.md#restore-options) opcji dla szyfrowanych maszyn wirtualnych. Ta opcja jest obsługiwana tylko dla niezaszyfrowane dyski zarządzane.




## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem należy wykonać następujące czynności:

1. Upewnij się, że masz co najmniej jeden [Windows](../security/azure-security-disk-encryption-windows.md) lub [Linux](../security/azure-security-disk-encryption-linux.md) maszyn wirtualnych przy użyciu ADE włączone.
2. [Przejrzyj macierz obsługi](backup-support-matrix-iaas.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure
3. [Utwórz](backup-azure-arm-vms-prepare.md#create-a-vault) magazynu kopii zapasowych usług odzyskiwania, jeśli nie masz.
4. Jeśli włączysz szyfrowanie dla maszyn wirtualnych, które już są włączone dla kopii zapasowej, należy po prostu podać kopii zapasowej z uprawnieniami dostępu usługi Key Vault, aby kontynuować tworzenie kopii zapasowych bez przerw w działaniu. [Dowiedz się więcej](#provide-permissions) o przypisanie tych uprawnień.

Ponadto istnieje kilka rzeczy, które może być konieczne w niektórych sytuacjach:

- **Zainstaluj agenta maszyny Wirtualnej na maszynie Wirtualnej**: Usługa Azure Backup tworzy kopie zapasowe maszyn wirtualnych platformy Azure, instalowanie rozszerzenia na maszynie agenta maszyny Wirtualnej platformy Azure. Jeśli maszyna wirtualna została utworzona z obrazu w witrynie Azure marketplace, agent jest zainstalowany i uruchomiony. Jeśli tworzysz niestandardową maszynę Wirtualną lub migrowania maszyny w środowisku lokalnym, może być konieczne [Zainstaluj agenta ręcznie w](backup-azure-arm-vms-prepare.md#install-the-vm-agent).
- **Jawnie zezwolić na dostęp ruchu wychodzącego**: Ogólnie rzecz biorąc nie musisz jawnie zezwolić na dostęp ruchu wychodzącego sieci maszyny wirtualnej platformy Azure w celu użycia go do komunikowania się z usługą Azure Backup. Jednak niektóre maszyny wirtualne mogą wystąpić problemy z połączeniem, przedstawiający **ExtensionSnapshotFailedNoNetwork** wystąpił błąd podczas próby połączenia. W takim przypadku należy [jawnie zezwolić na dostęp ruchu wychodzącego](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), więc rozszerzenie kopii zapasowej platformy Azure mogą komunikować się za pomocą platformy Azure publiczne adresy IP dla ruchu kopii zapasowej.



## <a name="configure-a-backup-policy"></a>Konfigurowanie zasad kopii zapasowych

1. Jeśli jeszcze nie utworzono kopii zapasowej magazynu usług Recovery Services, postępuj zgodnie z [te instrukcje](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Otwórz magazyn usługi w portalu, a następnie wybierz pozycję **kopii zapasowej** w **wprowadzenie** sekcji.

    ![Blok tworzenia kopii zapasowej](./media/backup-azure-vms-encryption/select-backup.png)

3. W **cel kopii zapasowej** > **gdzie jest uruchomione Twoje obciążenie?** wybierz **Azure**.
4. W **co chcesz utworzyć kopię zapasową?** wybierz **maszyny wirtualnej** > **OK**.

      ![Bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. W **zasady tworzenia kopii zapasowej** > **wybierz zasady tworzenia kopii zapasowej**, wybierz zasady, które chcesz skojarzyć z magazynem. Następnie kliknij przycisk **OK**.
    - Zasady tworzenia kopii zapasowych określa podczas tworzenia kopii zapasowych są pobierane i jak długo są przechowywane.
    - Szczegóły domyślnych zasad znajdują się w menu rozwijanym.

    ![Otwarcie bloku scenariusza](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Jeśli nie chcesz użyć domyślnych zasad, wybierz opcję **Utwórz nowy**, i [utworzyć niestandardowe zasady](backup-azure-arm-vms-prepare.md#create-a-custom-policy).


7. Kliknij zaszyfrowanych maszyn wirtualnych, aby utworzyć kopię zapasową za pomocą zasad wybierz opcję i wybierz **OK**.

      ![Wybierz zaszyfrowanych maszyn wirtualnych](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Jeśli używasz usługi Azure Key Vault, na stronie magazyn, zobaczysz komunikat, że usługa Azure Backup wymaga dostęp tylko do odczytu do kluczy i wpisów tajnych w usłudze Key Vault.

    - Jeśli ten komunikat jest wymagana żadna akcja.

        ![Access OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Jeśli zostanie wyświetlony ten komunikat, należy ustawić uprawnienia, zgodnie z opisem w [Poniższa procedura](#provide-permissions).

        ![Ostrzeżenie dostępu](./media/backup-azure-vms-encryption/access-warning.png)

9. Kliknij przycisk **Włącz kopię zapasową** Aby wdrożyć zasady kopii zapasowych w magazynie, a następnie włącz wykonywanie kopii zapasowej dla wybranych maszyn wirtualnych.


## <a name="trigger-a-backup-job"></a>Wyzwalanie zadania tworzenia kopii zapasowej

Tworzenie początkowej kopii zapasowej zostaną wykonane zgodnie z harmonogramem, ale możesz uruchomić ją od razu, w następujący sposób:

1. W menu magazynu kliknij **kopii zapasowych elementów**.
2. W **elementy kopii zapasowej** kliknij **maszyny wirtualnej platformy Azure**.
3. W **elementy kopii zapasowej** listy, kliknij przycisk z wielokropkiem (...).
4. Kliknij przycisk **Utwórz teraz kopię zapasową**.
5. W **Utwórz teraz kopię zapasową**, wybierz ostatni dzień, który ma być przechowywana punkt odzyskiwania przy użyciu kontrolki kalendarza. Następnie kliknij przycisk **OK**.
6. Monitoruj powiadomienia z portalu. Możesz monitorować postęp zadania na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej** > **w toku**. W zależności od rozmiaru maszyny wirtualnej tworzenie początkowej kopii zapasowej może potrwać pewien czas.


## <a name="provide-permissions"></a>Udostępnienie uprawnień

Maszyna wirtualna platformy Azure wymaga dostęp tylko do odczytu, aby utworzyć kopię zapasową kluczy i wpisów tajnych, wraz z skojarzonych maszynach wirtualnych.

- Usługi Key Vault jest skojarzona z dzierżawą usługi Azure AD subskrypcji platformy Azure. Jeśli jesteś **użytkownika elementu członkowskiego**, kopia zapasowa Azure uzyskuje dostęp do usługi Key Vault bez dalszych działań.
- Jeśli jesteś **użytkownik-Gość**, musisz udostępnić uprawnienia dla usługi Azure Backup do dostępu do magazynu kluczy.

Aby ustawić uprawnienia:

1. W witrynie Azure portal wybierz **wszystkich usług**i wyszukaj **klucza magazynów**.
2. Wybierz magazyn kluczy skojarzone z zaszyfrowanych maszyn wirtualnych, które wykonujesz kopie zapasowe.
3. Wybierz **zasady dostępu** > **Dodaj nowe**.
4. Wybierz **Wybierz podmiot zabezpieczeń**, a następnie wpisz **zarządzania kopią zapasową**.
5. Wybierz **Zarządzanie usługami kopii zapasowych** > **wybierz**.

    ![Wybieranie kopii zapasowej usługi](./media/backup-azure-vms-encryption/select-backup-service.png)

6. W **zasad dostępu Dodaj** > **Konfiguruj z szablonu (opcjonalnie)** , wybierz opcję **kopia zapasowa Azure**.
    - Wymagane uprawnienia są wstępnie dla **uprawnienia klucza** i **uprawnienia klucza tajnego**.
    - Jeśli maszyna wirtualna jest zaszyfrowana przy użyciu **tylko bloków**, usuń zaznaczenie dla **uprawnienia klucza** ponieważ dzięki temu wystarczy uprawnień dotyczących wpisów tajnych.

    ![Wybranej kopii zapasowej platformy Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

6. Kliknij przycisk **OK**. **Zarządzanie usługami kopii zapasowych** jest dodawany do **zasady dostępu**.

    ![Zasady dostępu](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. Kliknij przycisk **Zapisz** zapewnienie usługi Azure Backup przy użyciu uprawnień.

## <a name="restore-an-encrypted-vm"></a>Przywracanie zaszyfrowanych maszyn wirtualnych

Przywracanie zaszyfrowanych maszyn wirtualnych z w następujący sposób:

1. [Przywracanie dysku maszyny Wirtualnej](backup-azure-arm-restore-vms.md#restore-disks).
2. Następnie wykonaj jedną z następujących czynności:
    - Użyj szablonu, który jest generowany podczas operacji przywracania dostosowanie ustawień maszyny Wirtualnej w celu wyzwolenia wdrożenia maszyny Wirtualnej. [Dowiedz się więcej](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Tworzenie nowej maszyny Wirtualnej z przywróconych dysków przy użyciu programu Powershell. [Dowiedz się więcej](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>Kolejne kroki

Jeśli napotkasz problemy, zapoznaj się z

- [Typowe błędy](backup-azure-vms-troubleshoot.md) podczas tworzenia kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych platformy Azure.
- [Rozszerzenie agenta/kopia zapasowa maszyny Wirtualnej platformy Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) problemów.
