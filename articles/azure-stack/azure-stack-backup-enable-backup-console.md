---
title: Włączanie tworzenia kopii zapasowej z portalu administratora usługi Azure Stack | Dokumentacja firmy Microsoft
description: Włącz usługę infrastruktury kopii zapasowych za pośrednictwem portalu administracyjnego usługi Azure Stack mogą zostać przywrócone, jeśli wystąpi awaria.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: db2c55ec30e766496b98ef66b584df26f2dfe116
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239285"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Włączanie tworzenia kopii zapasowej z portalu administratora usługi Azure Stack
Włącz usługi Backup infrastrukturę, za pośrednictwem portalu administracyjnego usługi Azure Stack mogą generować kopii zapasowych. Te kopie zapasowe można użyć do przywrócenia środowiska za pomocą odzyskiwania w chmurze w przypadku powstania [poważnej awarii](./azure-stack-backup-recover-data.md). Cel odzyskiwania w chmurze jest zapewnienie, że Twoje operatory i użytkownicy mogą Zaloguj się do portalu po ukończeniu odzyskiwania. Użytkownicy mają swoje subskrypcje przywrócone w tym uprawnienia dostępu opartej na rolach i ról, oryginalnym planów, ofert i obliczeń wcześniej zdefiniowane, magazynu i limitów przydziałów sieci.

Jednak infrastruktury usługi kopii zapasowej wykonywania kopii zapasowych maszyn wirtualnych IaaS, konfiguracje sieci i zasobów magazynu, takich jak konta magazynu obiektów blob, tabel i tak dalej, dzięki czemu użytkownicy logujący się po odzyskaniu chmurze kończy nie będą widzieć żadnego z wcześniej istniejącej zasoby. Platforma jako usługa (PaaS) zasobów i danych także nie jest wykonywana przez usługę. 

Administratorzy i użytkownicy są odpowiedzialne za tworzenie kopii zapasowych i przywracanie zasobów IaaS i PaaS oddzielnie od procesów tworzenia kopii zapasowej infrastruktury. Aby uzyskać informacje o tworzeniu kopii zapasowej zasobów IaaS i PaaS zobacz następujące linki:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Włącz lub zmienić konfigurację kopii zapasowej

1. Otwórz [portalu administratora usługi Azure Stack](azure-stack-manage-portals.md).
2. Wybierz **wszystkich usług**, a następnie w obszarze **administracji** wybierz kategorię **tworzenie kopii zapasowych**. Wybierz **konfiguracji** w **tworzenie kopii zapasowych** bloku.
3. Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Dla ścieżki do udziału plików hostowane na oddzielnym urządzeniu, należy użyć ciągu Universal Naming Convention (UNC). Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. W przypadku usługi można użyć adresu IP. Aby zapewnić dostępność kopii zapasowych danych po awarii, urządzenia powinny być w innej lokalizacji.

    > [!Note]  
    > Jeśli Twoje środowisko obsługuje rozpoznawanie nazw z siecią infrastruktury Azure Stack w środowisku przedsiębiorstwa, używając nazwy FQDN, a nie adres IP.
    
4. Typ **Username** przy domena i nazwa użytkownika wystarczające uprawnienia dostępu do odczytu i zapisu plików. Na przykład `Contoso\backupshareuser`.
5. Typ **hasło** dla użytkownika.
6. Wpisz hasło ponownie **Potwierdź hasło**.
7. **Częstotliwości w godzinach** Określa, jak często kopie zapasowe są tworzone. Wartością domyślną jest 12. Usługa Scheduler obsługuje maksymalnie 12 i co najmniej 4. 
8. **Czas przechowywania w dniach** Określa, ile dni kopii zapasowych zostaną zachowane w lokalizacji zewnętrznej. Wartość domyślna to 7. Usługa Scheduler obsługuje maksymalnie 14 i co najmniej 2. Starszy niż okres przechowywania kopii zapasowych są automatycznie usuwane z lokalizacji zewnętrznej.

    > [!Note]  
    > Jeśli chcesz zarchiwizować starszy niż okres przechowywania kopii zapasowych, upewnij się utworzyć kopię zapasową plików przed harmonogram usuwa kopie zapasowe. W przypadku skrócenia okresu przechowywania kopii zapasowych (np. z 7 dni do 5 dni) harmonogramu spowoduje usunięcie wszystkich kopii zapasowych, które są starsze niż nowy okres przechowywania. Upewnij się, że jesteś ok z kopiami zapasowymi wprowadzenie usunięte przed uaktualnieniem tej wartości. 

9. Podaj klucz wstępny w **klucza szyfrowania** pole. Pliki kopii zapasowej są szyfrowane przy użyciu tego klucza. Upewnij się, że przechowywanie tego klucza w bezpiecznej lokalizacji. Po wprowadzeniu tego klucza po raz pierwszy lub wymienić główny klucz w przyszłości, nie można wyświetlić klucz, w tym interfejsie. Aby utworzyć klucz, uruchom następujące polecenia programu Azure Stack PowerShell:
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. Wybierz **OK** można zapisać ustawień kopii zapasowej kontrolera.

    ![Usługa Azure Stack — ustawienia kontrolera kopii zapasowej](media/azure-stack-backup/backup-controller-settings.png)

## <a name="start-backup"></a>Rozpocznij wykonywanie kopii zapasowej
Aby rozpocząć tworzenie kopii zapasowej, kliknij pozycję **Utwórz teraz kopię zapasową** aby rozpocząć tworzenie kopii zapasowej na żądanie. Kopii zapasowej na żądanie nie zmodyfikuje podczas następnej zaplanowanej kopii zapasowej. Po zakończeniu zadania, można potwierdzić ustawień w **Essentials**:

![Usługa Azure Stack — kopii zapasowej na żądanie](media/azure-stack-backup/scheduled-backup.png)

Można również uruchomić polecenie cmdlet programu PowerShell **Start AzsBackup** na tym komputerze administracji usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Włącza lub wyłącza automatyczne tworzenie kopii zapasowych
Po włączeniu kopii zapasowej, kopii zapasowych są planowane automatycznie. Następny zaplanowany termin tworzenia kopii zapasowej można sprawdzić **Essentials**. 

![Usługa Azure Stack — kopii zapasowej na żądanie](media/azure-stack-backup/on-demand-backup.png)

Jeśli zachodzi potrzeba wyłączenia przyszłych zaplanowanych kopii zapasowych, kliknij polecenie **wyłączyć automatyczne kopie zapasowe**. Wyłączanie automatycznego tworzenia kopii zapasowej zachowa ustawienia kopii zapasowej skonfigurowane i zachowa harmonogram tworzenia kopii zapasowych. Ta akcja po prostu informuje harmonogramu, aby pominąć tworzenie kopii zapasowych w przyszłości. 

![Usługa Azure Stack — Wyłącz zaplanowane tworzenie kopii zapasowych](media/azure-stack-backup/disable-auto-backup.png)

Upewnij się, że przyszłych zaplanowanych kopii zapasowych zostały wyłączone w **Essentials**:

![Usługa Azure Stack — upewnij się, tworzenie kopii zapasowych zostały wyłączone.](media/azure-stack-backup/confirm-disable.png)

Kliknij pozycję **włączyć automatyczne tworzenie kopii zapasowych** poinformować harmonogramu, aby rozpocząć wykonywanie kolejnych kopii zapasowych w zaplanowanym czasie. 

![Usługa Azure Stack — Włącz zaplanowane tworzenie kopii zapasowych](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Jeśli skonfigurowano tworzenie kopii zapasowych przed zaktualizowaniem do 1807 automatycznych kopii zapasowych zostaną wyłączone. W ten sposób kopie zapasowe uruchomione przez usługę Azure Stack nie powodują konfliktów z kopiami zapasowymi, uruchomione przez zadanie zewnętrzne aparat planowania. Po wyłączeniu wszelkie harmonogram zadań zewnętrznych kliknij **włączyć automatyczne tworzenie kopii zapasowych**.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się wykonać kopię zapasową. Zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Dowiedz się zweryfikować kopii zapasowej. Zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md).
