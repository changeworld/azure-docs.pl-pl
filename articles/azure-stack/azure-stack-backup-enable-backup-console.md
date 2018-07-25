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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: 08bce6284b672ae092e2cee3c26140e8c6049a34
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242856"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Włączanie tworzenia kopii zapasowej z portalu administratora usługi Azure Stack
Włącz usługi Backup infrastrukturę, za pośrednictwem portalu administracyjnego usługi Azure Stack mogą generować kopii zapasowych. Te kopie zapasowe można użyć do przywrócenia środowiska za pomocą odzyskiwania w chmurze w przypadku powstania [poważnej awarii](.\azure-stack-backup-recover-data.md). Cel odzyskiwania w chmurze jest zapewnienie, że Twoje operatory i użytkownicy mogą Zaloguj się do portalu po ukończeniu odzyskiwania. Użytkownicy mają swoje subskrypcje przywrócone w tym uprawnienia dostępu opartej na rolach i ról, oryginalnym planów, ofert i obliczeń wcześniej zdefiniowane, magazynu i limitów przydziałów sieci.

Jednak infrastruktury usługi kopii zapasowej wykonywania kopii zapasowych maszyn wirtualnych IaaS, konfiguracje sieci i zasobów magazynu, takich jak konta magazynu obiektów blob, tabel i tak dalej, dzięki czemu użytkownicy logujący się po odzyskaniu chmurze kończy nie będą widzieć żadnego z wcześniej istniejącej zasoby. Platforma jako usługa (PaaS) zasobów i danych także nie jest wykonywana przez usługę. 

Administratorzy i użytkownicy są odpowiedzialne za tworzenie kopii zapasowych i przywracanie zasobów IaaS i PaaS oddzielnie od procesów tworzenia kopii zapasowej infrastruktury. Aby uzyskać informacje o tworzeniu kopii zapasowej zasobów IaaS i PaaS zobacz następujące linki:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Przed skonfigurowaniem kopii zapasowej za pomocą konsoli, należy skonfigurować usługi kopii zapasowej. Można skonfigurować usługi kopii zapasowej przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Włącz wykonywanie kopii zapasowej

1. Otwórz w portalu administratora usługi Azure Stack w [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Wybierz **więcej usług** > **tworzenie kopii zapasowych**. Wybierz **konfiguracji** w **tworzenie kopii zapasowych** bloku.

    ![Usługa Azure Stack — ustawienia kontrolera kopii zapasowej](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Dla ścieżki do udziału plików hostowane na oddzielnym urządzeniu, należy użyć ciągu Universal Naming Convention (UNC). Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. W przypadku usługi można użyć adresu IP. Aby zapewnić dostępność kopii zapasowych danych po awarii, urządzenia powinny być w innej lokalizacji.
    > [!Note]  
    > Jeśli Twoje środowisko obsługuje rozpoznawanie nazw z siecią infrastruktury Azure Stack w środowisku przedsiębiorstwa, używając nazwy FQDN, a nie adres IP.
4. Typ **Username** przy domena i nazwa użytkownika wystarczające uprawnienia dostępu do odczytu i zapisu plików. Na przykład `Contoso\backupshareuser`.
5. Typ **hasło** dla użytkownika.
5. Wpisz hasło ponownie **Potwierdź hasło**.
6. Podaj klucz wstępny w **klucza szyfrowania** pole. Pliki kopii zapasowej są szyfrowane przy użyciu tego klucza. Upewnij się, że przechowywanie tego klucza w bezpiecznej lokalizacji. Po wprowadzeniu tego klucza po raz pierwszy lub wymienić główny klucz w przyszłości, nie można wyświetlić tego klucza, w tym interfejsie. Więcej instrukcji do generowania klucza wstępnego, należy wykonać skryptów w [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).
7. Wybierz **OK** można zapisać ustawień kopii zapasowej kontrolera.

Aby wykonać kopię zapasową, należy pobrać narzędzia usługi Azure Stack, a następnie uruchom polecenie cmdlet programu PowerShell **Start AzSBackup** w węźle Administracja usługi Azure Stack. Aby uzyskać więcej informacji, zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się wykonać kopię zapasową. Zobacz [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Dowiedz się zweryfikować kopii zapasowej. Zobacz [Potwierdź kopia zapasowa została wykonana w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md).