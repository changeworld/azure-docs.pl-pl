---
title: Utwórz kopię zapasową plików stosu Azure i aplikacji | Dokumentacja firmy Microsoft
description: Kopia zapasowa Azure umożliwia tworzenie kopii zapasowej i odzyskiwanie plików stosu Azure i aplikacji do środowiska Azure stosu.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: adigan,markgal
ms.openlocfilehash: 905f6b13928d11243202059af0ad255971102da8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Kopie zapasowe plików i aplikacji na stosie Azure
Kopia zapasowa Azure umożliwia ochronę (lub utworzyć kopię zapasową) plików i aplikacji na stosie Azure. Aby utworzyć kopię zapasową plików i aplikacji, należy zainstalować serwer kopii zapasowej Microsoft Azure jako maszynę wirtualną działającą na stosie Azure. Po zainstalowaniu serwera kopii zapasowej Azure, Dodawanie dysku systemu Azure, aby zwiększyć magazynu lokalnego, która jest dostępna dla krótkoterminowej kopii zapasowej danych. Serwer kopii zapasowej systemu Azure korzysta z magazynu Azure w celu przechowywania długoterminowego.

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server
Serwer kopii zapasowej systemu Azure chroni następujące obciążenia maszyny wirtualnej Azure stosu.

| Chronionego źródła danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Windows Server częściowej roczna kanału - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Entprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | Database (Baza danych) |
| SQL Server 2014 | Database (Baza danych) |
| SQL Server 2012 z dodatkiem SP1 | Database (Baza danych) |
| SharePoint 2013 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2010 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| Stan systemu | Stan systemu |
| Odzyskiwania kompletnego stanu systemu od zera (BMR) | Odzyskiwania systemu od ZERA, stan systemu plików, folderów | 

## <a name="install-azure-backup-server"></a>Zainstaluj serwer kopii zapasowej systemu Azure
Aby zainstalować serwer kopii zapasowej Azure na maszynie wirtualnej platformy Azure stosu, zapoznaj się z artykułem [przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu serwera usługi Kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md). Przed zainstalowaniem i skonfigurowaniem serwera usługi Kopia zapasowa Azure, należy pamiętać o następujących czynności:

### <a name="determining-size-of-virtual-machine"></a>Ustalanie rozmiaru maszyny wirtualnej
Aby uruchomić serwera usługi Kopia zapasowa Azure na maszynie wirtualnej platformy Azure stosu, użyj rozmiaru A2 lub większym. Aby uzyskać pomoc przy wyborze rozmiaru maszyny wirtualnej, należy pobrać [kalkulatora rozmiaru maszyny Wirtualnej Azure stosu](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na maszynach wirtualnych Azure stosu
Wszystkie maszyny wirtualne używane w stos Azure obciążenia muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcję Azure. 

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym oraz na platformie Azure
Serwer kopii zapasowej systemu Azure przechowuje dane kopii zapasowej na dyskach platformy Azure dołączonych do maszyny wirtualnej dla operacyjnych dotyczących odzyskiwania. Gdy dyski i miejsca do magazynowania są dołączone do maszyny wirtualnej, serwer kopii zapasowej Azure zarządza magazynu. Ilość miejsca w magazynie danych kopii zapasowych zależy od liczby i rozmiaru dysków dołączonych do każdego [maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-storage-overview.md). Każdego rozmiaru maszyny Wirtualnej Azure stosu ma maksymalną liczbę dysków, które może zostać dołączony do maszyny wirtualnej. Na przykład A2 jest cztery dyski. A3 jest ośmiu dysków. A4 jest 16 dysków. Ponownie rozmiaru i liczby dysków określa puli całkowita magazynu kopii zapasowych.

> [!IMPORTANT]
> Należy **nie** zachowania danych operacyjnych dotyczących odzyskiwania (kopia zapasowa) na dyskach dołączone do serwera kopii zapasowej Azure przez więcej niż pięć dni.
>

Przechowywania danych kopii zapasowej na platformie Azure zmniejsza infrastruktury kopii zapasowych na stosie Azure. Jeśli dane są więcej niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, należy utworzyć lub użyć magazynu usług odzyskiwania. Podczas przygotowania do wykonania kopii zapasowej obciążenie serwera kopii zapasowej Azure, będzie [skonfigurować magazyn usług odzyskiwania](backup-azure-microsoft-azure-backup.md#recovery-services-vault). Po skonfigurowaniu każdym uruchomieniu zadania tworzenia kopii zapasowej, punkt odzyskiwania jest tworzony w magazynie. Każdy magazyn usług odzyskiwania zawiera punkty odzyskiwania do 9999. W zależności od liczby punktów odzyskiwania utworzonych i jak długo są przechowywane przez wiele lat można zachować dane kopii zapasowej. Na przykład można utworzyć miesięczne punktów odzyskiwania i zachować je do pięciu lat.
 
### <a name="using-sql-server"></a>Za pomocą programu SQL Server
Jeśli chcesz użyć zdalnego serwera SQL dla bazy danych serwera usługi Kopia zapasowa Azure, wybierz tylko stosu maszyny Wirtualnej platformy Azure działa program SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Wydajność maszyny Wirtualnej serwera kopii zapasowej platformy Azure
Jeśli udostępniony innym maszynom wirtualnym, rozmiar konta magazynu i limity liczby mogą obniżyć wydajność maszyny wirtualnej serwera usługi Kopia zapasowa Azure. Z tego powodu należy użyć oddzielnego konta magazynu dla maszyny wirtualnej Azure Utwórz kopię zapasową serwera. Agent usługi Kopia zapasowa Azure uruchomionych na serwerze kopii zapasowej Azure wymaga tymczasowego magazynu dla:
    - własnego użytku (lokalizacja pamięci podręcznej),
    - danych przywróconych z chmury (lokalny obszar przemieszczania)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie magazynu dysku tymczasowym kopia zapasowa Azure
Każda maszyna wirtualna Azure stosu jest dostarczany z magazynu na dysku tymczasowym, który jest dostępny dla użytkownika jako wolumin D:`\`. Lokalny obszar przemieszczania wymagany dla usługi Kopia zapasowa Azure można skonfigurować na D:`\`, i lokalizację pamięci podręcznej można umieścić na dysku C:`\`. W ten sposób magazynu nie trzeba używać przeciwną na dyskach danych dołączonych do maszyny wirtualnej serwera usługi Kopia zapasowa Azure.

### <a name="scaling-deployment"></a>Skalowania wdrożenia
Jeśli chcesz skalowania wdrożenia, dostępne są następujące opcje:
  - Skalowanie w górę — zwiększenie rozmiaru maszyny wirtualnej serwera usługi Kopia zapasowa Azure z serii D serii i zwiększyć Magazyn lokalny [zgodnie z instrukcjami maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Korzystaj — wysyłanie starszych danych do serwera usługi Kopia zapasowa Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do serwera kopii zapasowej Azure.
  - Skalowanie w poziomie — dodawanie kolejnych serwerów kopia zapasowa Azure w celu włączenia ochrony obciążeń.


## <a name="bare-metal-recovery-for-azure-stack-vm"></a>Odzyskiwanie stosu Azure maszyny Wirtualnej

Kopia zapasowa kompletnego stanu odzyskiwania systemu od zera (BMR) chroni pliki systemu operacyjnego i wszystkich danych wolumin krytyczny, z wyjątkiem danych użytkownika. Kopia zapasowa BMR obejmuje kopię zapasową stanu systemu. Poniższe procedury wyjaśniają, jak przywrócić dane odzyskiwania systemu od ZERA. 

### <a name="run-recovery-on-the-azure-backup-server"></a>Uruchamianie odzyskiwania na serwer kopii zapasowej systemu Azure 

Otwórz konsolę serwer kopii zapasowej Azure.

1. W konsoli, kliknij przycisk **odzyskiwania**, Znajdź maszyny, której chcesz odzyskać, a następnie kliknij przycisk **odzyskiwaniu bez systemu operacyjnego**.
2. Dostępne punkty odzyskiwania są wyświetlane w kalendarzu pogrubioną czcionką. Wybierz datę i godzinę dla punktu odzyskiwania, który ma być używany.
3. W **Wybieranie typu odzyskiwania**, wybierz pozycję **Kopiuj do folderu sieciowego**.
4. W **określ miejsce docelowe**, wybierz opcję, jeśli chcesz skopiować dane. Należy pamiętać, że wybrany lokalizacja docelowa musi mieć wystarczającej ilości miejsca dla punktu odzyskiwania. Zalecane jest utworzenie nowego folderu.
5. W **Określanie opcji odzyskiwania**, wybierz ustawienia zabezpieczeń, aby zastosować i określ, czy szybciej odzyskiwać dane przy użyciu migawek sprzętowych opartych na sieci SAN.     Migawek sprzętowych opartych na sieci SAN są opcję tylko wtedy, gdy sieć SAN z tej funkcji, włączona i możliwość tworzenia i podziałem jako możliwego do zapisu. Również migawek sprzętowych opartych na sieci SAN do pracy, chronionej maszyny i serwer kopii zapasowej Azure musi być podłączony do tej samej sieci.
6. Ustaw opcje powiadamiania, a następnie kliknij przycisk **odzyskać** na **Podsumowanie** strony.

### <a name="set-up-the-share-location"></a>Skonfiguruj lokalizację udziału
W konsoli serwera usługi Kopia zapasowa Azure:
1. W lokalizacji przywracania przejdź do folderu zawierającego kopię zapasową.
2. Udostępnij folder powyżej WindowsImageBackup, tak aby głównym folderu udostępnionego jest WindowsImageBackup folder. Jeśli nie jest udostępniony WindowsImageBackup folder, operacja przywracania nie odnajdzie kopii zapasowej. Nawiązywanie połączeń za pomocą środowiska WinRE, konieczne jest dostępny WinRE udziału i poprawny adres IP i poświadczeń.

### <a name="restore-the-machine"></a>Przywrócenie maszyny

1. Na maszynie wirtualnej, której chcesz przywrócić odzyskiwania systemu od ZERA Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i wpisz następujące polecenia. **/bootore** Określa, czy środowisko odzyskiwania systemu Windows jest uruchamiany automatycznie podczas następnego uruchomienia systemu.
```
Reagent /boottore
shutdown /r /t 0
```

2. W oknie dialogowym Otwieranie wybierz język i ustawienia regionalne. Na **zainstalować** ekranu wybierz **Napraw komputer**.
3. Na **opcje odzyskiwania systemu** wybierz pozycję **Przywróć komputer przy użyciu utworzonego wcześniej obrazu systemu**.
4. Na **Wybierz kopię zapasową obrazu systemu** wybierz pozycję **wybierz obraz systemu** > **zaawansowane** > **Wyszukaj obraz systemu w sieci**. Jeśli zostanie wyświetlone ostrzeżenie, wybierz **tak**. Aby wybrać obraz, przejdź do udziału sieciowego, wprowadź poświadczenia, a następnie wybierz punkt odzyskiwania. To skanowanie w poszukiwaniu określonych kopii zapasowych, które są dostępne w tym punkcie odzyskiwania. Wybierz punkt odzyskiwania.
5. W **wybierz sposób przywracania kopii zapasowej**, wybierz pozycję **Format i ponownie podziel na partycje dysków**. Na następnym ekranie sprawdź ustawienia i kliknij przycisk **Zakończ** do rozpoczęcia zadania przywracania. Ponowne uruchomienie zgodnie z potrzebami.

## <a name="see-also"></a>Zobacz także
Uzyskać na ochrona innych obciążeń za pomocą serwera usługi Kopia zapasowa Azure zobacz następujące artykuły:
- [Tworzenie kopii zapasowej farmy programu SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Tworzenie kopii zapasowych programu SQL server](backup-azure-sql-mabs.md)
