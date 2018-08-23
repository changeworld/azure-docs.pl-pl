---
title: Aktualizacja usługi Azure App Service w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące aktualizacji usługi Azure App Service w usłudze Azure Stack
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
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: f12eb7d74d9c47567c72cddda8d2813f394fb1de
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054173"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aktualizacja usługi Azure App Service w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

> [!IMPORTANT]
> Dotyczą aktualizacji 1807 system zintegrowany z usługi Azure Stack można też wdrażać najnowszy zestaw Azure Stack development kit przed wdrożeniem usługi Azure App Service 1.3.
>
>

Postępując zgodnie z instrukcjami w tym artykule, możesz uaktualnić [dostawcy zasobów usługi App Service](azure-stack-app-service-overview.md) wdrożone w środowisku usługi Azure Stack, która jest połączona z Internetem.

> [!IMPORTANT]
> Przed uruchomieniem uaktualniania, upewnij się, że zostały już wykonane [wdrożenia usługi Azure App Service dla dostawcy zasobów usługi Azure Stack](azure-stack-app-service-deploy.md)
>
>

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

1. Pobierz [Instalatora usługi App Service](https://aka.ms/appsvcupdate3installer)

2. Uruchom appservice.exe jako administrator

    ![Instalator usługi App Service][1]

3. Kliknij przycisk **wdrażanie usługi App Service lub uaktualnienia do najnowszej wersji.**

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

6. Upewnij się, że punkt końcowy usługi Azure Stack usługi Azure Resource Manager i dzierżawy usługi Active Directory informacje są poprawne. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure Stack Development Kit, możesz zaakceptować wartości domyślne w tym miejscu. Jednak jeśli dostosowano opcji podczas wdrażania usługi Azure Stack, możesz edytować wartości w tym oknie, w celu odzwierciedlenia zmiany. Na przykład, jeśli używasz sufiks domeny *mycloud.com*, należy zmienić punkt końcowy usługi Azure Stack usługi Azure Resource Manager *management.region.mycloud.com*. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Informacje o chmurze usługi Azure Stack][2]

7. Na następnej stronie:

   1. Kliknij przycisk **Connect** znajdujący się obok **usługi Azure Stack subskrypcje** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure Stack. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz usługi Active Directory Federation Services (AD FS) zapewniają konta administratora. Na przykład *cloudadmin@azurestack.local*. Wprowadź hasło, a następnie kliknij przycisk **Sign In**.
   2. W **usługi Azure Stack subskrypcje** wybierz opcję **domyślne subskrypcję dostawcy**.
   3. W **lokalizacje usługi Azure Stack** wybierz lokalizację, do której odnosi się do regionu jest wdrażany na. Na przykład wybierz **lokalnego** Jeśli Twoje wdrożenie usługi Azure Stack Development Kit.
   4. Jeśli istniejące wdrożenie usługi App Service zostanie odnalezione, następnie zasobów grupy i konta magazynu będzie być wypełnione i wyszarzone.
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
