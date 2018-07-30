---
title: Samouczek — archiwizowanie dzienników usługi Azure Active Directory na koncie usługi Azure Storage (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak skonfigurować diagnostykę Azure w celu wypychania dzienników usługi Azure Active Directory na konto magazynu (wersja zapoznawcza)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240247"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Samouczek: archiwizowanie dzienników usługi Azure Active Directory na koncie usługi Azure Storage (wersja zapoznawcza)

W tym samouczku dowiesz się, jak skonfigurować ustawienia diagnostyki usługi Azure Monitor w celu kierowania dzienników usługi Azure Active Directory do konta usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne 

Potrzebne elementy:

* Subskrypcja platformy Azure z kontem usługi Azure Storage. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure Active Directory.
* Użytkownik będący administratorem globalnym lub administratorem zabezpieczeń dla tej dzierżawy.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archiwizowanie dzienników na koncie usługi Azure Storage

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Kliknij pozycje **Azure Active Directory** -> **Aktywność** -> **Dzienniki inspekcji**. 
3. Kliknij pozycję **Eksportuj ustawienia**, aby otworzyć blok Ustawienia diagnostyczne. Kliknij pozycję **Edytuj ustawienie**, jeśli chcesz zmienić istniejące ustawienia, lub kliknij pozycję **Dodaj ustawienie diagnostyczne**, aby dodać nowe ustawienie. Możesz mieć maksymalnie trzy ustawienia. 
    ![Eksportowanie ustawień](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Eksportowanie ustawień")

4. Dodaj przyjazną nazwę ustawienia, która będzie przypominała o jego przeznaczenie. Na przykład „Wysyłanie na konto usługi Azure Storage”. 
5. Zaznacz pole wyboru **Zarchiwizuj na koncie magazynu** i kliknij pozycję **Konto magazynu**, aby wybrać konto usługi Azure Storage. 
6. Wybierz subskrypcję platformy Azure i konto magazynu, na które chcesz przekierowywać dzienniki, a następnie kliknij przycisk **OK**, aby zamknąć konfigurację.
7. Zaznacz pole wyboru **AuditLogs** (Dzienniki inspekcji), aby wysłać dzienniki inspekcji na konto magazynu. 
8. Zaznacz pole wyboru **SignInLogs** (Dzienniki logowania), aby wysłać dzienniki logowania na konto magazynu.
9. Za pomocą suwaka ustaw czas przechowywania danych dziennika. Domyślnie ta wartość wynosi „0” i dzienniki będą przechowywane na koncie magazynu przez czas nieokreślony. Można także ustawić inną wartość, aby zdarzenia starsze niż ta liczba dni były automatycznie czyszczone.
10. Kliknij pozycję **Zapisz**, aby zapisać ustawienie.
    ![Ustawienia diagnostyczne](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Ustawienia diagnostyczne")

11. Po około 15 minutach sprawdź, czy dzienniki są wypychane na konto magazynu. Przejdź do witryny Azure Portal, kliknij pozycję **Konta magazynu**, wybierz konto magazynu, które było używane wcześniej, i kliknij pozycję **Obiekty blob**. 
12. W przypadku **dzienników inspekcji** kliknij pozycję **insights-log-audit**. W przypadku **dzienników logowania** kliknij pozycję **insights-logs-signin**.
    ![Konto magazynu](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Konto magazynu")

## <a name="next-steps"></a>Następne kroki

* [Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretowanie schematu dzienników logowania w usłudze Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Często zadawane pytania i znane problemy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)