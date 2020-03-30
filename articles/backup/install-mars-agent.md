---
title: Instalowanie agenta usług odzyskiwania platformy Microsoft Azure (MARS)
description: Dowiedz się, jak zainstalować agenta usług odzyskiwania platformy Microsoft Azure (MARS) w celu utworzenia kopii zapasowej maszyn z systemem Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247763"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instalowanie agenta marsjańskiej kopii zapasowej platformy Azure

W tym artykule wyjaśniono, jak zainstalować agenta usług odzyskiwania platformy Microsoft Azure (MARS). MARS jest również znany jako agent usługi Azure Backup.

## <a name="about-the-mars-agent"></a>Informacje o agencie MARS

Usługa Azure Backup używa agenta MARS do tworzenia kopii zapasowych plików, folderów i stanu systemu z maszyn lokalnych i maszyn wirtualnych platformy Azure. Te kopie zapasowe są przechowywane w magazynie usług odzyskiwania na platformie Azure. Agenta można uruchomić:

* Bezpośrednio na lokalnych komputerach z systemem Windows. Te maszyny można kopii zapasowej bezpośrednio do magazynu usług odzyskiwania na platformie Azure.
* Na maszynach wirtualnych platformy Azure, które uruchamiają system Windows obok siebie z rozszerzeniem kopii zapasowej maszyny Wirtualnej platformy Azure. Agent kopie zapasowe określonych plików i folderów na maszynie Wirtualnej.
* W wystąpieniu serwera kopii zapasowych platformy Microsoft Azure (MABS) lub na serwerze Menedżera ochrony danych (System Center Data Protection Manager) (DPM). W tym scenariuszu maszyny i obciążenia kopii zapasowej do MABS lub Data Protection Manager. Następnie MABS lub Data Protection Manager używa agenta MARS do utworzenia kopii zapasowej do magazynu na platformie Azure.

Dane, które są dostępne do tworzenia kopii zapasowych zależy od tego, gdzie agent jest zainstalowany.

> [!NOTE]
> Ogólnie rzecz biorąc, tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure przy użyciu rozszerzenia kopii zapasowej platformy Azure na maszynie wirtualnej. Ta metoda jest kopii zapasowej całej maszyny Wirtualnej. Jeśli chcesz zrobić kopie zapasowe określonych plików i folderów na maszynie Wirtualnej, zainstaluj i użyj agenta MARS obok rozszerzenia. Aby uzyskać więcej informacji, zobacz [Architektura wbudowanej kopii zapasowej maszyny Wirtualnej platformy Azure.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Kroki procesu tworzenia kopii zapasowej](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Przed rozpoczęciem

* Dowiedz się, jak [usługa Kopia zapasowa platformy Azure używa agenta MARS do tworzenia kopii zapasowych maszyn z systemem Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Dowiedz się więcej o [architekturze kopii zapasowej,](backup-architecture.md#architecture-back-up-to-dpmmabs) która uruchamia agenta MARS na pomocniczym serwerze MABS lub Data Protection Manager.
* Sprawdź, [co jest obsługiwane i co możesz zrobić kopii zapasowej](backup-support-matrix-mars-agent.md) przez agenta MARS.
* Upewnij się, że masz konto platformy Azure, jeśli chcesz wykonać zapasową serwera lub klienta na platformie Azure. Jeśli nie masz konta, możesz utworzyć [bezpłatne](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.
* Sprawdź dostęp do Internetu na komputerach, których chcesz zrobić, aby uzyskać jego utworzenie.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modyfikowanie replikacji magazynu

Domyślnie przechowalnia używają [magazynu geograficznie nadmiarowego (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Jeśli magazyn jest podstawowym mechanizmem tworzenia kopii zapasowych, zaleca się użycie GRS.
* Aby zmniejszyć koszty magazynu platformy Azure, można użyć [lokalnie nadmiarowego magazynu (LRS).](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Aby zmodyfikować typ replikacji magazynu:

1. W nowym przechowalni wybierz **pozycję Właściwości** w sekcji **Ustawienia.**

1. Na stronie **Właściwości** w obszarze **Konfiguracja kopii zapasowej**wybierz pozycję **Aktualizuj**.

1. Wybierz typ replikacji magazynu i wybierz pozycję **Zapisz**.

    ![Aktualizowanie konfiguracji kopii zapasowej](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Nie można zmodyfikować typu replikacji magazynu po skonfigurowaniu przechowalni i zawiera elementy kopii zapasowej. Jeśli chcesz to zrobić, musisz ponownie utworzyć przechowalnię.
>

### <a name="verify-internet-access"></a>Weryfikowanie dostępu do Internetu

Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na następujące adresy URL i adresy IP:

* adresy URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Korzystanie z usługi Azure ExpressRoute

Można wywrzeć zapas y danych za pośrednictwem usługi Azure ExpressRoute przy użyciu publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowych za pomocą prywatnej komunikacji równorzędnej nie jest obsługiwane.

Aby korzystać z komunikacji równorzędnej publicznej, najpierw upewnij się, że dostęp do następujących domen i adresów:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Aby korzystać z komunikacji równorzędnej firmy Microsoft, wybierz następujące usługi, regiony i odpowiednie wartości społeczności:

* Usługa Azure Active Directory (12076:5060)
* Region platformy Azure, w zależności od lokalizacji magazynu usług odzyskiwania
* Usługa Azure Storage, w zależności od lokalizacji magazynu usług odzyskiwania

Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące routingu usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> Publiczne komunikacji równorzędnej jest przestarzałe dla nowych obwodów.

Wszystkie poprzednie adresy URL i adresy IP używają protokołu HTTPS na porcie 443.

### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Pobierz agenta MARS

Pobierz agenta MARS, aby można go było zainstalować na komputerach, których chcesz symtować.

Jeśli agent został już zainstalowany na wszystkich komputerach, upewnij się, że jest uruchomiona najnowsza wersja agenta. Znajdź najnowszą wersję w portalu lub przejdź bezpośrednio do [pliku do pobrania](https://aka.ms/azurebackup_agent).

1. W przechowalni w obszarze **Wprowadzenie**wybierz pozycję **Kopia zapasowa**.

    ![Otwieranie celu tworzenia kopii zapasowej](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. W obszarze Gdzie jest uruchomione **On-premises** **obciążenie?** Wybierz tę opcję, nawet jeśli chcesz zainstalować agenta MARS na maszynie Wirtualnej platformy Azure.
1. W obszarze Co chcesz wykonać kopii **Files and folders** **zapasowej?** Można również wybrać **stan systemu**. Dostępnych jest wiele innych opcji, ale te opcje są obsługiwane tylko wtedy, gdy używasz pomocniczego serwera kopii zapasowej. Wybierz **opcję Przygotuj infrastrukturę**.

    ![Konfigurowanie plików i folderów](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. W przypadku **instalacji infrastruktury**w obszarze **Zainstaluj agenta usług odzyskiwania**pobierz agenta MARS.

    ![Przygotowywanie infrastruktury](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. W menu pobierania wybierz polecenie **Zapisz**. Domyślnie plik *MARSagentinstaller.exe* jest zapisywany w folderze Pobrane.

1. Wybierz **pozycję Już pobierz lub przy użyciu najnowszego agenta usług odzyskiwania,** a następnie pobierz poświadczenia magazynu.

    ![Pobieranie poświadczeń magazynu](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Wybierz **pozycję Zapisz**. Plik zostanie pobrany do folderu Pobrane. Nie można otworzyć pliku poświadczeń przechowalni.

## <a name="install-and-register-the-agent"></a>Instalowanie i rejestrowanie agenta

1. Uruchom plik *MARSagentinstaller.exe* na komputerach, których chcesz skonfekcjować.
1. W Kreatorze instalacji agenta MARS wybierz pozycję **Ustawienia instalacji**. Tam wybierz miejsce instalacji agenta i wybierz lokalizację pamięci podręcznej. Następnie wybierz **przycisk Dalej**.
   * Usługa Azure Backup używa pamięci podręcznej do przechowywania migawek danych przed wysłaniem ich na platformę Azure.
   * Lokalizacja pamięci podręcznej powinna mieć wolne miejsce równe co najmniej 5 procentom rozmiaru danych, które będą korzystać z kopii zapasowej.

    ![Wybieranie ustawień instalacji w Kreatorze instalacji agenta MARS](./media/backup-configure-vault/mars1.png)

1. W przypadku **konfiguracji serwera proxy**określ, w jaki sposób agent uruchamiany na komputerze z systemem Windows połączy się z Internetem. Następnie wybierz **przycisk Dalej**.

   * Jeśli używasz niestandardowego serwera proxy, określ wszystkie niezbędne ustawienia serwera proxy i poświadczenia.
   * Pamiętaj, że agent potrzebuje dostępu do [określonych adresów URL.](#before-you-start)

    ![Konfigurowanie dostępu do Internetu w kreatorze MARS](./media/backup-configure-vault/mars2.png)

1. W przypadku **instalacji**przejrzyj wymagania wstępne i wybierz pozycję **Zainstaluj**.
1. Po zainstalowaniu agenta wybierz **pozycję Przejdź do rejestracji**.
1. W obszarze**Zarejestruj identyfikator przechowalni** **Kreatora** > serwera przejdź do pobranego pliku poświadczeń i wybierz go. Następnie wybierz **przycisk Dalej**.

    ![Dodawanie poświadczeń przechowalni przy użyciu Kreatora rejestru serwera](./media/backup-configure-vault/register1.png)

1. Na stronie **Ustawienia szyfrowania** określ hasło, które będzie używane do szyfrowania i odszyfrowywania kopii zapasowych dla komputera.

    * Zapisz hasło w bezpiecznym miejscu. Jest potrzebny do przywrócenia kopii zapasowej.
    * Jeśli zgubisz lub zapomnisz hasła, firma Microsoft nie pomoże ci odzyskać danych kopii zapasowej.

1. Wybierz **pozycję Zakończ**. Agent jest teraz zainstalowany, a komputer jest zarejestrowany w przechowalni. Wszystko jest gotowe do skonfigurowania kopii zapasowej i zaplanowania jej tworzenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wykonać [kopię zapasową maszyn z systemem Windows przy użyciu agenta marsjańskiej kopii zapasowej azure](backup-windows-with-mars-agent.md)
