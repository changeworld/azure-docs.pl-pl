---
title: Odzyskaj usługi App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące odzyskiwania po awarii w usłudze Azure Stack App Service
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340245"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Odzyskiwanie usługi App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*  

Niniejszy dokument zawiera instrukcje dotyczące akcji do wykonania odzyskiwania po awarii usługi App Service.

Odzyskanie usługi App Service w usłudze Azure Stack z kopii zapasowej należy podjąć następujące działania:
1.  Przywracanie baz danych usługi App Service
2.  Przywróć zawartość udziału serwera plików
3.  Przywrócić role usługi App Service i usługi

Jeśli magazyn usługi Azure Stack był używany do przechowywania aplikacji funkcji, należy również wziąć kroki w celu przywrócenia aplikacji funkcji.

## <a name="restore-the-app-service-databases"></a>Przywracanie baz danych usługi App Service
Powinny zostać przywrócone baz danych aplikacji usługi SQL Server w wystąpieniu programu SQL Server z gotowe produkcji. 

Po [przygotowywanie wystąpienia programu SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) do hostowania baz danych usługi App Service, wykonaj następujące czynności, aby przywrócić bazy danych z kopii zapasowej:

1. Zaloguj się do programu SQL Server, który będzie hostował odzyskanej bazy danych usługi App Service z uprawnieniami administratora.
2. Aby przywrócić bazy danych usługi App Service z poziomu wiersza polecenia z uprawnieniami administratora, użyj następujących poleceń:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Sprawdź, czy obu bazach danych usługi App Service zostały pomyślnie przywrócone, a następnie zamknij program SQL Server Management Studio.

> [!NOTE]
> Aby odzyskiwanie po awarii wystąpienia klastra trybu failover, [w instrukcjach](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Przywróć zawartość udziału plików usługi App Service
Po [Przygotowanie serwera plików](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) do hostowania udziału plików w usłudze App Service, trzeba przywrócić dzierżawy zawartości udziału plików z kopii zapasowej. Można użyć dowolnej metody dostępne skopiować pliki do nowo utworzonej lokalizacji udziału plików usługi App Service. Uruchomione w tym przykładzie na serwerze plików będzie używać programu PowerShell i narzędzia robocopy Aby podłączyć się do udziału zdalnego i skopiuj pliki do udziału:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Oprócz kopiowania zawartości udziału plików, możesz także zresetować uprawnienia dotyczące udziału plików. Aby to zrobić, otwórz administracyjny wiersz polecenia na komputerze serwera plików i uruchom **ReACL.cmd** pliku. **ReACL.cmd** plik znajduje się w plikach instalacyjnych usługi App Service w **BCDR** katalogu.

## <a name="restore-app-service-roles-and-services"></a>Przywrócić role usługi App Service i usługi
Po przywróceniu bazy danych usługi App Service i zawartości udziału plików, należy następnie przywrócić role usługi App Service i usługi za pomocą programu PowerShell. Te kroki spowoduje przywrócenie wpisy tajne usługi App Service i usługi konfiguracji.  

1. Zaloguj się do kontrolera usługi App Service **maszyny Wirtualnej CN0** maszynę Wirtualną jako **roleadmin** przy użyciu hasła podanego podczas instalacji usługi App Service. 
    > [!TIP]
    > Należy zmodyfikować grupy zabezpieczeń sieci maszyny Wirtualnej, aby zezwolić na połączenia RDP. 
2. Kopiuj **SystemSecrets.JSON** plik lokalnie do maszyny Wirtualnej kontrolera. Należy podać ścieżkę do tego pliku jako `$pathToExportedSecretFile` parametru w następnym kroku. 
3. Użyj następujących poleceń w oknie konsoli programu PowerShell z podwyższonym poziomem uprawnień, aby przywrócić role usługi App Service i usługi:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Zdecydowanie zaleca się zamknięcie tej sesji programu PowerShell, po zakończeniu działania polecenia.

## <a name="restore-function-apps"></a>Przywracanie aplikacji funkcji 
App Service do usługi Azure Stack nie obsługuje przywracania aplikacji użytkownika dzierżawy lub danych innych niż zawartości udziału plików. Dlatego te musi być kopii zapasowej i odzyskać poza usługi App Service i operacje przywracania kopii zapasowych. Jeśli magazyn usługi Azure Stack był używany do przechowywania aplikacji funkcji, odzyskać utracone dane należy podjąć następujące kroki:

1. Utwórz nowe konto magazynu, który ma być używany przez aplikację funkcji. Ten magazyn może być magazyn usługi Azure Stack, usługi Azure storage lub dowolnym zgodną z magazynem.
2. Pobierz parametry połączenia magazynu.
3. Otwórz portal funkcji, przejdź do aplikacji funkcji.
4. Przejdź do **funkcje platformy** kartę, a następnie kliknij przycisk **ustawienia aplikacji**.
5. Zmiana **AzureWebJobsDashboard** i **AzureWebJobsStorage** nowe parametry połączenia oraz kliknięcie przycisku **Zapisz**.
6. Przełącz się do **Przegląd**.
7. Uruchom ponownie aplikację. Może upłynąć kilka prób, aby wyczyścić wszystkie błędy.

## <a name="next-steps"></a>Kolejne kroki
[Omówienie usługi App Service w usłudze Azure Stack](azure-stack-app-service-overview.md)