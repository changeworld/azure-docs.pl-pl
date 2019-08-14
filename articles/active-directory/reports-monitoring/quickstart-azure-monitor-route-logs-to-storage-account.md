---
title: Samouczek — archiwizowanie dzienników Azure Active Directory na koncie magazynu | Microsoft Docs
description: Dowiedz się, jak skonfigurować Diagnostyka Azure do wypychania dzienników Azure Active Directory do konta magazynu
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98fb0677b864fccfb5abd2b08381db1bd1c9c8f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989737"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Samouczek: Archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage

W tym samouczku dowiesz się, jak skonfigurować ustawienia diagnostyki usługi Azure Monitor w celu kierowania dzienników usługi Azure Active Directory (Azure AD) do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne 

Aby używać tej funkcji, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure z kontem usługi Azure Storage. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiwizowanie dzienników na koncie usługi Azure Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz kolejno pozycje **Azure Active Directory** > **Aktywność** > **Dzienniki inspekcji**. 

3. Wybierz pozycję **Eksportuj ustawienia**. 

4. W okienku **Ustawienia diagnostyczne** wykonaj jedną z następujących czynności:
   * Aby zmienić istniejące ustawienia, wybierz polecenie **Edytuj ustawienie**.
   * Aby dodać nowe ustawienie, wybierz polecenie **Dodaj ustawienia diagnostyki**.  
     Możesz mieć maksymalnie trzy ustawienia. 

     ![Eksportuj ustawienia](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Wprowadź przyjazną nazwę ustawienia, która będzie przypominała o jego przeznaczeniu (na przykład *Wyślij na konto usługi Azure Storage*). 

6. Zaznacz pole wyboru **Zarchiwizuj na koncie magazynu**, a następnie wybierz pozycję **Konto magazynu**. 

7. Wybierz subskrypcję platformy Azure i konto magazynu, do którego chcesz kierować dzienniki.
 
8. Wybierz pozycję **OK**, aby zakończyć konfigurację.

9. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji na konto magazynu, zaznacz pole wyboru **AuditLogs** (Dzienniki inspekcji). 
    * Aby wysłać dzienniki logowania na konto magazynu, zaznacz pole wyboru **SignInLogs** (Dzienniki logowania).

10. Za pomocą suwaka ustaw czas przechowywania danych dziennika. Domyślnie ta wartość wynosi *0*, co oznacza że dzienniki będą przechowywane na koncie magazynu przez czas nieokreślony. Jeśli ustawisz inną wartość, zdarzenia starsze niż ta liczba dni zostaną automatycznie czyszczone.

11. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Po około 15 minutach sprawdź, czy dzienniki zostały wypchnięte na konto magazynu. Przejdź do witryny [Azure Portal](https://portal.azure.com), wybierz pozycję **Konta magazynu**, wybierz konto magazynu, które było używane wcześniej, i kliknij pozycję **Obiekty blob**. W przypadku **dzienników inspekcji** wybierz pozycję **insights-log-audit**. W przypadku **dzienników logowania** wybierz pozycję **insights-logs-signin**.

    ![Konto magazynu](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Następne kroki

* [Interpret audit logs schema in Azure Monitor (Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor)](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
* [Często zadawane pytania i znane problemy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)