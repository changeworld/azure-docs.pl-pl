---
title: Aktualizowanie w tryb Offline usługa aplikacji Azure | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące aktualizacji usługi Azure App Service na stosie Azure w trybie offline
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
ms.date: 05/18/2018
ms.author: anwestg
ms.openlocfilehash: 8671cba484a779e8d7cd0172df141497bb396a97
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>W trybie offline aktualizacji usługi Azure App Service na stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!IMPORTANT]
> Zastosowanie aktualizacji 1802 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service.
>
>

Postępując zgodnie z instrukcjami w tym artykule, możesz uaktualnić [dostawcy zasobów usługi aplikacji](azure-stack-app-service-overview.md) wdrożony w środowisku Azure stosu, w którym jest:

* nie jest połączony z Internetem
* zabezpieczone przez usługi Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Przed uruchomieniem uaktualnienia, upewnij się, że zostały już wykonane [wdrożenia usługi Azure App Service na dostawcy zasobów Azure stosu](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi aplikacji

Aby uaktualnić dostawcę zasobów usługi aplikacji w środowisku Azure stosu, należy wykonać te zadania:

1. Pobierz [Instalatora usługi aplikacji](https://aka.ms/appsvcupdate2installer)
2. Tworzenie pakietu uaktualnienia w trybie offline.
3. Uruchom Instalatora programu App Service (appservice.exe) i ukończyć uaktualnianie.

W trakcie tego procesu uaktualniania zostanie:

* Wykryj poprzedniego wdrożenia usługi App Service
* Przekaż do magazynu
* Uaktualnij wszystkie role usługi aplikacji (kontrolerów, zarządzania, frontonu, wydawcy i proces roboczy ról)
* Zaktualizuj definicje zestawów skalowania usługi App Service
* Aktualizowanie manifestu dostawcy zasobów usługi aplikacji

## <a name="create-an-offline-upgrade-package"></a>Tworzenie pakietu uaktualnienia w trybie offline

Aby uaktualnić usługi aplikacji w środowisku bez połączenia, należy najpierw utworzyć pakietu uaktualnienia w trybie offline na komputerze, na którym jest połączony z Internetem.

1. Uruchom jako administrator appservice.exe

    ![Instalator usługi aplikacji][1]

2. Kliknij przycisk **zaawansowane** > **tworzenie pakietów w trybie offline**

    ![Instalator usługi aplikacji — zaawansowane][2]

3. Instalator usługi aplikacji — tworzy pakiet uaktualnienia w trybie offline i wyświetla ścieżkę do niego.  Możesz kliknąć **Otwórz folder** aby otworzyć folder w sieci Eksploratora plików.

4. Skopiuj Instalatora (AppService.exe) i w trybie offline pakiet uaktualnienia do komputera-hosta Azure stosu.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Dokończ uaktualnienie usługi aplikacji Azure stosu

> [!IMPORTANT]
> Instalator usługi aplikacji, należy uruchomić na komputerze, który można osiągnąć punktu końcowego platformy Azure stosu administratora usługi Azure Resource Manager.
>
>

1. Appservice.exe Uruchom jako administrator.

    ![Instalator usługi aplikacji][1]

2. Kliknij przycisk **zaawansowane** > **ukończyć instalację w trybie offline lub uaktualnienie**.

    ![Instalator usługi aplikacji — zaawansowane][2]

3. Przejdź do lokalizacji w trybie offline wcześniej utworzony pakiet uaktualnienia, a następnie kliknij przycisk **dalej**.

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

6. Upewnij się, że punkt końcowy Azure stosu usługi Azure Resource Manager i dzierżawy usługi Active Directory informacje są poprawne. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure stosu Development Kit, można zaakceptować wartości domyślne w tym miejscu. Jednak po wdrożeniu stosu Azure po dostosowaniu opcji, należy edytować wartości w tym oknie, aby odzwierciedlić który. Na przykład, jeśli korzystasz z sufiksem domeny *mycloud.com*, punkt końcowy Azure stosu usługi Azure Resource Manager, należy zmienić na *management.region.mycloud.com*. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Informacje o chmurze Azure stosu][3]

7. Na następnej stronie:

   1. Kliknij przycisk **Connect** znajdujący się obok **subskrypcji platformy Azure stosu** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure stosu. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz programu Active Directory Federation Services (AD FS), podaj konto administratora. Na przykład *cloudadmin@azurestack.local*. Wprowadź hasło, a następnie kliknij przycisk **logowania**.
   2. W **subskrypcji platformy Azure stosu** wybierz opcję **domyślny dostawca subskrypcji**.
   3. W **lokalizacji stosu Azure** wybierz lokalizację, do której odnosi się do regionu jest wdrażany do. Na przykład wybierz **lokalnego** Jeśli wdrażanie Azure stosu Development Kit.
   4. Jeśli istniejące wdrożenie usługi aplikacji został odnaleziony, następnie zasobów grupy i konto magazynu zostanie wypełnione i nieaktywna.
   5. Kliknij przycisk **dalej** do Przejrzyj podsumowanie uaktualnienia.

    ![Wykryto instalacji usługi aplikacji][4]

8. Na stronie Podsumowanie:
   1. Sprawdź wybrane opcje wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
   2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
   3. Aby rozpocząć uaktualnianie, kliknij przycisk **dalej**.

       ![Podsumowanie uaktualnienia usługi aplikacji][5]

9. Na stronie Postęp uaktualniania:
    1. Śledź postęp uaktualnienia. Czas trwania uaktualniania z usługi aplikacji Azure stosu zależy zależy od liczby wystąpień roli wdrożone.
    2. Po pomyślnym zakończeniu uaktualniania kliknij **zakończenia**.

        ![Postęp uaktualniania usługi aplikacji][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować innych [platforma jako usługa (PaaS) usługi](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawca zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md)
