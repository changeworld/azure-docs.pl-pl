---
title: Aktualizacja usługi Azure App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące aktualizacji usługi Azure App Service w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 1ea079373edc9b9f1dde6038f1e02e3d7036e052
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890495"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aktualizacja usługi Azure App Service w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!IMPORTANT]  
> Dotyczą aktualizacji 1901 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem Azure App Service w wersji 1.5.

Postępując zgodnie z instrukcjami w tym artykule, możesz uaktualnić [dostawcy zasobów usługi App Service](azure-stack-app-service-overview.md) wdrożone w środowisku usługi Azure Stack, która jest połączona z Internetem.

> [!IMPORTANT]  
> Przed uruchomieniem uaktualniania, upewnij się, że zostały już wykonane [wdrożenia usługi Azure App Service dla dostawcy zasobów usługi Azure Stack](azure-stack-app-service-deploy.md) oraz że znasz [informacje o wersji](azure-stack-app-service-release-notes-update-five.md) którym towarzyszą wersję 1.5 informacje na temat nowych funkcji, poprawek i znanych problemach, które mogą wpłynąć na wdrożenie.

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi App Service

W trakcie tego procesu uaktualniania wykonują następujące czynności:

* Wykrywanie poprzedniego wdrożenia usługi App Service
* Przygotowanie wszystkich pakietów aktualizacji i nowe wersje wszystkich bibliotek OSS do wdrożenia
* Przekaż do magazynu
* Uaktualnij wszystkie role usługi App Service (kontrolerów, zarządzania i frontonu, wydawcy i proces roboczy role)
* Zaktualizuj definicje zestawów skalowania usługi App Service
* Aktualizuj Manifest dostawcy zasobów usługi App Service

> [!IMPORTANT]
> Instalator usługi App Service, należy uruchomić na komputerze, na którym można osiągnąć punktu końcowego usługi Azure Stack administratora usługi Azure Resource Manager.
>
>

Aby uaktualnić wdrożenie usługi App Service w usłudze Azure Stack, wykonaj następujące kroki:

1. Pobierz [Instalatora usługi App Service](https://aka.ms/appsvcupdate5installer)

2. Uruchom appservice.exe jako administrator

    ![Instalator usługi App Service][1]

3. Kliknij przycisk **wdrażanie usługi App Service lub uaktualnienia do najnowszej wersji.**

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

6. Upewnij się, że punkt końcowy usługi Azure Stack usługi Azure Resource Manager i dzierżawy usługi Active Directory informacje są poprawne. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure Stack Development Kit, możesz zaakceptować wartości domyślne w tym miejscu. Jednak jeśli dostosowano opcji podczas wdrażania usługi Azure Stack, możesz edytować wartości w tym oknie. Na przykład, jeśli używasz sufiks domeny *mycloud.com*, należy zmienić punkt końcowy usługi Azure Stack usługi Azure Resource Manager *management.region.mycloud.com*. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Informacje o chmurze usługi Azure Stack][2]

7. Na następnej stronie:

   1. Kliknij przycisk **Connect** znajdujący się obok **usługi Azure Stack subskrypcje** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure Stack. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz usługi Active Directory Federation Services (AD FS) zapewniają konta administratora. Na przykład *cloudadmin\@azurestack.local*. Wprowadź hasło, a następnie kliknij przycisk **Sign In**.
   2. W **usługi Azure Stack subskrypcje** wybierz opcję **domyślne subskrypcję dostawcy**.
   3. W **lokalizacje usługi Azure Stack** wybierz lokalizację, do której odnosi się do regionu jest wdrażany na. Na przykład wybierz **lokalnego** Jeśli Twoje wdrożenie usługi Azure Stack Development Kit.
   4. W przypadku wykrycia istniejącego wdrożenia usługi App Service następnie zasobów grupy i konta magazynu będzie być wypełnione i wyszarzone.
   5. Kliknij przycisk **dalej** do Przejrzyj podsumowanie uaktualnienia.

      ![Wykryto instalacji usługi App Service][3]

8. Na stronie podsumowania:
   1. Sprawdź wybrane opcje, które zostały wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
   2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
   3. Aby rozpocząć uaktualnianie, kliknij przycisk **dalej**.

       ![Podsumowanie uaktualnienia usługi App Service][4]

9. Na stronie Postęp uaktualniania:
    1. Śledź postęp uaktualniania. Czas trwania uaktualniania usługi App Service w usłudze Azure Stack zmienia się zależne od liczby wystąpień roli wdrożone.
    2. Po pomyślnym zakończeniu uaktualniania kliknij **zakończenia**.

        ![Postęp uaktualniania usługi App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować inne [platformy jako usługi (PaaS)](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawcy zasobów bazy danych MySQL](azure-stack-mysql-resource-provider-deploy.md)
