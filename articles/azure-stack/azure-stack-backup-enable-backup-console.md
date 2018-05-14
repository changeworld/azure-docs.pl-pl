---
title: Włączenia kopii zapasowej Azure stosu z portalu administracyjnego | Dokumentacja firmy Microsoft
description: Infrastruktura usługi kopii zapasowej za pośrednictwem portalu administracyjnego należy włączyć, dzięki czemu stosu Azure można przywrócić po awarii.
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
ms.openlocfilehash: 0ef8247eba4605d3c8e5ef0992ce97bce989002e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Włączenia kopii zapasowej Azure stosu z portalu administracyjnego
Infrastruktura usługi kopii zapasowej za pośrednictwem portalu administracyjnego należy włączyć, dzięki czemu stosu Azure mogą generować kopii zapasowych. Te kopie zapasowe można użyć do przywrócenia środowiska przy użyciu chmury odzyskiwania w przypadku [poważnej awarii](.\azure-stack-backup-recover-data.md). Celem chmura odzyskiwania jest zapewnienie, że operatorów, a użytkownicy mogą Zaloguj się do portalu po zakończeniu odzyskiwania. Użytkownicy mają swoje subskrypcje przywrócone w tym uprawnienia dostępu opartej na rolach i ról, oryginalnego planów, oferty i obliczeń uprzednio zdefiniowanej, magazynu i limitów przydziałów sieci.

Jednak usługi tworzenia kopii zapasowej infrastruktury wykonywania kopii zapasowych maszyn wirtualnych IaaS, konfiguracji sieci i zasobów magazynu, takich jak konta magazynu obiektów blob, tabel i itd., więc zakończeniu użytkowników logowania po odzyskaniu chmury nie będą widzieć żadnego ich wcześniej istniejących zasoby. Platforma jako usługa (PaaS) zasobów i danych także nie jest wykonywana przez usługę. 

Administratorzy i użytkownicy są odpowiedzialne za tworzenie kopii zapasowych i przywracanie zasobów IaaS i PaaS oddzielnie od infrastruktury procesów tworzenia kopii zapasowej. Informacje o tworzeniu kopii zapasowych zasobów IaaS i PaaS zobacz następujące linki:

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> Przed włączeniem kopii zapasowej za pomocą konsoli, należy skonfigurować usługi tworzenia kopii zapasowej. Można skonfigurować usługi tworzenia kopii zapasowej przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

## <a name="enable-backup"></a>Włączenia kopii zapasowej

1. Otwieranie portalu administracyjnego platformy Azure stosu w [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Wybierz **więcej usług** > **infrastruktura kopii zapasowej**. Wybierz **konfiguracji** w **infrastruktura kopii zapasowej** bloku.

    ![Azure stosu — ustawienia kontrolera kopii zapasowej](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Wpisz ścieżkę do **lokalizacja magazynu kopii zapasowej**. Użyj ciągu konwencji UNC (Universal Naming) dla ścieżki do udziału plików hostowane na osobnych urządzenia. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń. Dla usługi używając adresu IP. Aby zapewnić dostępność kopii zapasowej danych po awarii, urządzenie powinno być w innej lokalizacji.
    > [!Note]  
    > Jeśli środowisko obsługuje rozpoznawania nazw z siecią infrastruktury Azure stosu w środowisku przedsiębiorstwa, można użyć nazwy FQDN, a nie adres IP.
4. Typ **Username** przy użyciu domena i nazwa użytkownika. Na przykład `Contoso\administrator`.
5. Typ **hasło** dla użytkownika.
5. Wpisz hasło ponownie **Potwierdź hasło**.
6. Podaj klucz wstępny w **klucza szyfrowania** pole. Pliki kopii zapasowej są szyfrowane za pomocą tego klucza. Upewnij się, że przechowywanie tego klucza w bezpiecznej lokalizacji. Po ustawić ten klucz po raz pierwszy lub Obróć klucz w przyszłości, nie można wyświetlić tego klucza z tego interfejsu. Więcej instrukcji do wygenerowania klucza wstępnego, wykonaj skrypty w [włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Wybierz **OK** Aby zapisać ustawienia kopii zapasowej kontrolera.

Do wykonania kopii zapasowej, należy pobrać narzędzia stosu Azure, a następnie uruchom polecenie cmdlet programu PowerShell **Start AzSBackup** w węźle Administracja stosu Azure. Aby uzyskać więcej informacji, zobacz [kopia zapasowa Azure stosu](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się wykonać kopię zapasową. Zobacz [kopia zapasowa Azure stosu](azure-stack-backup-back-up-azure-stack.md ).
- Dowiedz się zweryfikować kopii zapasowej. Zobacz [Potwierdź kopii zapasowej zostało ukończone w portalu administracyjnym](azure-stack-backup-back-up-azure-stack.md).
