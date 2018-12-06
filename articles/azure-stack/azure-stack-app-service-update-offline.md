---
title: Aktualizacja usługi Azure App Service w trybie Offline | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące aktualizacji usługi Azure App Service w usłudze Azure Stack w trybie offline
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: anwestg
ms.openlocfilehash: 740a3ce36dde953cb92c52a10cbebf12544adae0
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968156"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>W trybie offline aktualizacji usługi Azure App Service w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

> [!IMPORTANT]
> Dotyczą aktualizacji 1809 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service 1.4.
>
>

Postępując zgodnie z instrukcjami w tym artykule, możesz uaktualnić [dostawcy zasobów usługi App Service](azure-stack-app-service-overview.md) wdrożone w środowisku usługi Azure Stack, który jest:

* nie jest połączony z Internetem
* zabezpieczone przez usługi Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Przed uruchomieniem uaktualniania, upewnij się, że zostały już wykonane [wdrożenia usługi Azure App Service dla dostawcy zasobów usługi Azure Stack](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi App Service

Aby uaktualnić dostawcy zasobów usługi App Service w środowisku usługi Azure Stack, należy wykonać następujące zadania:

1. Pobierz [Instalatora usługi App Service](https://aka.ms/appsvcupdate4installer)
2. Utwórz pakiet uaktualnienia w trybie offline.
3. Uruchom Instalatora usługi App Service (appservice.exe), a następnie zakończyć uaktualnianie.

W trakcie tego procesu uaktualniania wykonują następujące czynności:

* Wykrywanie poprzedniego wdrożenia usługi App Service
* Przekaż do magazynu
* Uaktualnij wszystkie role usługi App Service (kontrolerów, zarządzania i frontonu, wydawcy i proces roboczy role)
* Zaktualizuj definicje zestawów skalowania usługi App Service
* Aktualizuj Manifest dostawcy zasobów usługi App Service

## <a name="create-an-offline-upgrade-package"></a>Tworzenie pakietu uaktualnienia w trybie offline

Aby uaktualnić usługi App Service w środowisku bez połączenia, należy najpierw utworzyć pakietu uaktualnienia w trybie offline na komputerze, na którym jest połączony z Internetem.

1. Uruchom appservice.exe jako administrator

    ![Instalator usługi App Service][1]

2. Kliknij przycisk **zaawansowane** > **Utwórz pakiet offline**

    ![Instalator usługi App Service, zaawansowane][2]

3. Instalator usługi App Service tworzy pakiet uaktualnienia w trybie offline i zawiera ścieżkę do niego.  Możesz kliknąć pozycję **Otwórz folder** aby otworzyć folder w Eksploratorze plików.

4. Skopiuj pliki Instalatora (AppService.exe) i pakiet uaktualnienia w trybie offline do komputera-hosta usługi Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Wykonaj uaktualnienie usługi App Service w usłudze Azure Stack

> [!IMPORTANT]
> Instalator usługi App Service, należy uruchomić na komputerze, na którym można osiągnąć punktu końcowego usługi Azure Stack administratora usługi Azure Resource Manager.
>
>

1. Appservice.exe Uruchom jako administrator.

    ![Instalator usługi App Service][1]

2. Kliknij przycisk **zaawansowane** > **ukończenie instalacji w trybie offline lub uaktualnienie**.

    ![Instalator usługi App Service, zaawansowane][2]

3. Przejdź do lokalizacji pakietu uaktualniającego w trybie offline, została wcześniej utworzona, a następnie kliknij przycisk **dalej**.

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

6. Upewnij się, że punkt końcowy usługi Azure Stack usługi Azure Resource Manager i dzierżawy usługi Active Directory informacje są poprawne. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure Stack Development Kit, możesz zaakceptować wartości domyślne w tym miejscu. Jednak jeśli dostosowano opcji podczas wdrażania usługi Azure Stack, możesz edytować wartości w tym oknie. Na przykład, jeśli używasz sufiks domeny *mycloud.com*, należy zmienić punkt końcowy usługi Azure Stack usługi Azure Resource Manager *management.region.mycloud.com*. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Informacje o chmurze usługi Azure Stack][3]

7. Na następnej stronie:

   1. Kliknij przycisk **Connect** znajdujący się obok **usługi Azure Stack subskrypcje** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure Stack. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz usługi Active Directory Federation Services (AD FS) zapewniają konta administratora. Na przykład _cloudadmin@azurestack.local_. Wprowadź hasło, a następnie kliknij przycisk **Sign In**.
   2. W **usługi Azure Stack subskrypcje** wybierz opcję **domyślne subskrypcję dostawcy**.
   3. W **lokalizacje usługi Azure Stack** wybierz lokalizację, do której odnosi się do regionu jest wdrażany na. Na przykład wybierz **lokalnego** Jeśli Twoje wdrożenie usługi Azure Stack Development Kit.
   4. W przypadku wykrycia istniejącego wdrożenia usługi App Service następnie zasobów grupy i konta magazynu będzie być wypełnione i wyszarzone.
   5. Kliknij przycisk **dalej** do Przejrzyj podsumowanie uaktualnienia.

    ![Wykryto instalacji usługi App Service][4]

8. Na stronie podsumowania:
   1. Sprawdź wybrane opcje, które zostały wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
   2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
   3. Aby rozpocząć uaktualnianie, kliknij przycisk **dalej**.

       ![Podsumowanie uaktualnienia usługi App Service][5]

9. Na stronie Postęp uaktualniania:
    1. Śledź postęp uaktualniania. Czas trwania uaktualniania usługi App Service w usłudze Azure Stack zmienia się zależne od liczby wystąpień roli wdrożone.
    2. Po pomyślnym zakończeniu uaktualniania kliknij **zakończenia**.

        ![Postęp uaktualniania usługi App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-deploy.md)
