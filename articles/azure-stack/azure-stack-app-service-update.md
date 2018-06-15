---
title: Aktualizacja usługi aplikacji Azure na stosie Azure | Dokumentacja firmy Microsoft
description: Szczegółowe wskazówki dotyczące aktualizacji w usłudze Azure App Service na stosie Azure
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
ms.openlocfilehash: c822f25e25953b8709f481e51d6a63e6a912a60a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358816"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aktualizacja usługi aplikacji Azure na stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

> [!IMPORTANT]
> Zastosowanie aktualizacji 1804 systemu Azure stosu zintegrowane lub wdrożyć najnowszy zestaw deweloperski stosu Azure przed wdrożeniem usługi Azure App Service 1.2.
>
>

Postępując zgodnie z instrukcjami w tym artykule, możesz uaktualnić [dostawcy zasobów usługi aplikacji](azure-stack-app-service-overview.md) wdrożony w środowisku Azure stosu, w którym jest połączony z Internetem.

> [!IMPORTANT]
> Przed uruchomieniem uaktualnienia, upewnij się, że zostały już wykonane [wdrożenia usługi Azure App Service na dostawcy zasobów Azure stosu](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Uruchom Instalatora dostawcy zasobów usługi aplikacji

W trakcie tego procesu uaktualniania zostanie:

* Wykryj poprzedniego wdrożenia usługi App Service
* Przygotowanie wszystkich pakietów aktualizacji i nowe wersje wszystkich bibliotek OSS do wdrożenia
* Przekaż do magazynu
* Uaktualnij wszystkie role usługi aplikacji (kontrolerów, zarządzania, frontonu, wydawcy i proces roboczy ról)
* Zaktualizuj definicje zestawów skalowania usługi App Service
* Aktualizowanie manifestu dostawcy zasobów usługi aplikacji

> [!IMPORTANT]
> Instalator usługi aplikacji, należy uruchomić na komputerze, który można osiągnąć punktu końcowego Azure stosu administratora usługi Azure Resource Manager.
>
>

Aby uaktualnić wdrożenie usługi App Service na stosie Azure, wykonaj następujące kroki:

1. Pobierz [Instalatora usługi aplikacji](https://aka.ms/appsvcupdate2installer)

2. Uruchom jako administrator appservice.exe

    ![Instalator usługi aplikacji][1]

3. Kliknij przycisk **wdrażania usługi aplikacji lub uaktualnienia do najnowszej wersji.**

4. Przejrzyj i zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie kliknij przycisk **dalej**.

5. Przejrzyj i zaakceptuj postanowienia licencyjne innych firm, a następnie kliknij przycisk **dalej**.

6. Upewnij się, że punkt końcowy Azure stosu usługi Azure Resource Manager i dzierżawy usługi Active Directory informacje są poprawne. Jeśli domyślne ustawienia są używane podczas wdrażania usługi Azure stosu Development Kit, można zaakceptować wartości domyślne w tym miejscu. Jednak po wdrożeniu stosu Azure po dostosowaniu opcji, należy edytować wartości w tym oknie, aby odzwierciedlić który. Na przykład, jeśli korzystasz z sufiksem domeny *mycloud.com*, punkt końcowy Azure stosu usługi Azure Resource Manager, należy zmienić na *management.region.mycloud.com*. Po upewnieniu się informacje, kliknij przycisk **dalej**.

    ![Informacje o chmurze Azure stosu][2]

7. Na następnej stronie:

   1. Kliknij przycisk **Connect** znajdujący się obok **subskrypcji platformy Azure stosu** pole.
        * Jeśli używasz usługi Azure Active Directory (Azure AD), wprowadź konto administratora usługi Azure AD i hasło podane podczas wdrażania usługi Azure stosu. Kliknij przycisk **Zaloguj**.
        * Jeśli używasz programu Active Directory Federation Services (AD FS), podaj konto administratora. Na przykład *cloudadmin@azurestack.local*. Wprowadź hasło, a następnie kliknij przycisk **logowania**.
   2. W **subskrypcji platformy Azure stosu** wybierz opcję **domyślny dostawca subskrypcji**.
   3. W **lokalizacji stosu Azure** wybierz lokalizację, do której odnosi się do regionu jest wdrażany do. Na przykład wybierz **lokalnego** Jeśli wdrażanie Azure stosu Development Kit.
   4. Jeśli istniejące wdrożenie usługi aplikacji został odnaleziony, następnie zasobów grupy i konto magazynu zostanie wypełnione i nieaktywna.
   5. Kliknij przycisk **dalej** do Przejrzyj podsumowanie uaktualnienia.

    ![Wykryto instalacji usługi aplikacji][3]

8. Na stronie Podsumowanie:
   1. Sprawdź wybrane opcje wprowadzone. Aby wprowadzić zmiany, należy użyć **Wstecz** przycisków, aby przejść do poprzedniej strony.
   2. Jeśli konfiguracje są poprawne, zaznacz pole wyboru.
   3. Aby rozpocząć uaktualnianie, kliknij przycisk **dalej**.

       ![Podsumowanie uaktualnienia usługi aplikacji][4]

9. Na stronie Postęp uaktualniania:
    1. Śledź postęp uaktualnienia. Czas trwania uaktualniania z usługi aplikacji Azure stosu zależy zależy od liczby wystąpień roli wdrożone.
    2. Po pomyślnym zakończeniu uaktualniania kliknij **zakończenia**.

        ![Postęp uaktualniania usługi aplikacji][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Kolejne kroki

Możesz również wypróbować innych [platforma jako usługa (PaaS) usługi](azure-stack-tools-paas-services.md).

* [Dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Dostawca zasobów MySQL](azure-stack-mysql-resource-provider-deploy.md)
