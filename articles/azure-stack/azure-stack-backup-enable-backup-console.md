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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 1585eb460cc5f8ae437ee59a596dc7a854a108e7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995734"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Włączanie tworzenia kopii zapasowej z portalu administratora usługi Azure Stack
Włącz usługę infrastruktury kopii zapasowych za pośrednictwem portalu administracyjnego usługi Azure Stack mogą generować infrastruktury kopii zapasowych. Partner sprzętu można użyć tych kopii zapasowych do przywrócenia środowiska za pomocą odzyskiwania w chmurze w przypadku powstania [poważnej awarii](./azure-stack-backup-recover-data.md). Cel odzyskiwania w chmurze jest zapewnienie, że Twoje operatory i użytkownicy mogą Zaloguj się do portalu po ukończeniu odzyskiwania. Użytkownicy mają swoje subskrypcje przywrócone w tym uprawnienia dostępu opartej na rolach i ról, oryginalnym planów, ofert i obliczeń wcześniej zdefiniowane, magazynu, limitów przydziałów sieci i wpisy tajne usługi Key Vault.

Jednak infrastruktura usługa Kopia zapasowa nie jest w stanie tworzenie kopii zapasowych maszyn wirtualnych IaaS, konfiguracji sieci i zasobów magazynu, takich jak konta magazynu obiektów blob, tabele i tak dalej, dzięki czemu użytkownicy logujący się po zakończeniu odzyskiwania w chmurze nie zobaczą żadnego z wcześniej istniejącej zasoby. Platforma jako usługa (PaaS) zasobów i danych także nie jest wykonywana przez usługę. 

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

9. W ustawieniach szyfrowania należy podać certyfikat w polu plik certyfikatu cer. Pliki kopii zapasowej są szyfrowane przy użyciu tego klucza publicznego w certyfikacie. Należy podać certyfikat, który zawiera tylko część z kluczem publicznym, konfigurując ustawienia kopii zapasowej. Po wprowadzeniu tego certyfikatu po raz pierwszy lub Obróć certyfikatu w przyszłości, możesz tylko wyświetlać odcisk palca certyfikatu. Nie można pobrać lub wyświetlić plik przekazano certyfikat. Aby utworzyć plik certyfikatu, uruchom następujące polecenie programu PowerShell, aby utworzyć certyfikat z podpisem własnym za pomocą kluczy publicznych i prywatnych i wyeksportować certyfikat z tylko część z kluczem publicznym.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

    > [!Note]  
    > **1901 i nowsze wersje**: Usługa Azure Stack akceptuje certyfikatu do szyfrowania danych kopii zapasowej infrastruktury. Upewnij się, że z certyfikatem przy użyciu klucza publicznego i prywatnego w bezpiecznej lokalizacji. Ze względów bezpieczeństwa nie zaleca się używać certyfikatu przy użyciu kluczy publicznych i prywatnych do konfigurowania ustawień kopii zapasowej. Aby uzyskać więcej informacji na temat zarządzania cyklem życia certyfikatu, zobacz [infrastruktura kopii zapasowej usługa najlepszych rozwiązań](azure-stack-backup-best-practices.md).

10. Wybierz **OK** można zapisać ustawień kopii zapasowej kontrolera.

![Usługa Azure Stack — ustawienia kontrolera kopii zapasowej](media/azure-stack-backup/backup-controller-settings-certificate.png)

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

## <a name="update-backup-settings"></a>Aktualizowanie ustawień kopii zapasowej
Począwszy od 1901 pomoc techniczna dla klucza szyfrowania jest przestarzały. W przypadku konfigurowania kopii zapasowej po raz pierwszy w 1901, należy użyć certyfikatu. Usługa Azure Stack obsługuje klucz szyfrowania, tylko wtedy, gdy klucz jest skonfigurowana przed zaktualizowaniem do 1901. Tryb zgodności z poprzednimi wersjami będą nadal w trzech wersjach. Po tym klucze szyfrowania już nie będą obsługiwane. 

### <a name="default-mode"></a>Tryb domyślny
Ustawienia szyfrowania w przypadku konfigurowania infrastruktury kopii zapasowej po raz pierwszy po zainstalowaniu lub zaktualizowaniu do 1901, trzeba skonfigurować kopię zapasową przy użyciu certyfikatu. Za pomocą klucza szyfrowania nie jest już obsługiwana. 

Aby zaktualizować certyfikat używany do szyfrowania danych kopii zapasowych, możesz przekazać nowy. CER plików za pomocą część z kluczem publicznym i wybierz przycisk OK, aby zapisać ustawienia. 

Aby użyć klucza publicznego w certyfikacie nowe rozpocznie się nowych kopii zapasowych. Nie ma to wpływu na wszystkie istniejące kopie zapasowe utworzone za pomocą poprzedni certyfikat. Upewnij się zachować starszych certyfikatów wokół w bezpiecznej lokalizacji, w razie potrzeby do odzyskiwania w chmurze.

![Usługa Azure Stack — odcisk palca certyfikatu widoku](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Wstecz w trybie zgodności
Jeśli skonfigurowano kopię zapasową przed aktualizacją do 1901 ustawienia zostaną przeniesione bez zmian w zachowaniu. W tym przypadku klucz szyfrowania jest obsługiwana w przypadku zapewnienia zgodności. Istnieje możliwość aktualizowania klucza szyfrowania lub przełączanie do korzystania z certyfikatu. Będziesz mieć trzy wersje, aby kontynuować aktualizację klucza szyfrowania. Użyj tego czasu, do którego nastąpi przejście do certyfikatu. 

![Usługa Azure Stack — przy użyciu klucza szyfrowania w trybie zgodności z poprzednimi wersjami](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Aktualizowanie z szyfrowania klucza certyfikatu jest Operacja jednokierunkowa. Po wprowadzeniu tej zmiany, nie można przełączyć się do klucza szyfrowania. Wszystkie istniejące kopie zapasowe pozostaną zaszyfrowane przy użyciu poprzedniego klucza szyfrowania. 

![Usługa Azure Stack — Użyj certyfikatu szyfrowania w trybie zgodności z poprzednimi wersjami](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się wykonać kopię zapasową. Zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Dowiedz się zweryfikować kopii zapasowej. Zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md)