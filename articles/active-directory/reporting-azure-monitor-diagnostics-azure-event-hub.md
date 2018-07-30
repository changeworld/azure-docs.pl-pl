---
title: Samouczek — przesyłanie strumieniowe dzienników usługi Azure Active Directory do centrum zdarzeń platformy Azure (wersja zapoznawcza) | Microsoft Docs
description: Dowiedz się, jak skonfigurować Diagnostykę Azure w celu wypychania dzienników usługi Azure Active Directory do centrum zdarzeń (wersja zapoznawcza)
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
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240221"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Samouczek: przesyłanie strumieniowe dzienników usługi Azure Active Directory loguje się do centrum zdarzeń platformy Azure (wersja zapoznawcza)

W tym samouczku przedstawiono informacje na temat sposobu konfigurowania ustawień diagnostycznych usługi Azure Monitor w celu strumieniowego przesyłania dzienników usługi Azure Active Directory do centrum zdarzeń platformy Azure. Ten mechanizm służy do integrowania dzienników z narzędziami SIEM innych firm, takich jak Splunk i QRadar.

## <a name="prerequisites"></a>Wymagania wstępne 

Potrzebne elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure Active Directory
* Użytkownik będący administratorem globalnym lub administratorem zabezpieczeń dla tej dzierżawy
* Przestrzeń nazw usługi Event Hubs i centrum zdarzeń w ramach subskrypcji platformy Azure. Dowiedz się, jak je utworzyć [tutaj](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Archiwizowanie dzienników w centrum zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Kliknij pozycje **Azure Active Directory** -> **Aktywność** -> **Dzienniki inspekcji**. 
3. Kliknij pozycję **Eksportuj ustawienia**, aby otworzyć blok Ustawienia diagnostyczne. Kliknij pozycję **Edytuj ustawienie**, jeśli chcesz zmienić istniejące ustawienia, lub kliknij pozycję **Dodaj ustawienie diagnostyczne**, aby dodać nowe ustawienie. Możesz mieć maksymalnie trzy ustawienia. 
    ![Eksportowanie ustawień](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Eksportowanie ustawień")

4. Zaznacz pole wyboru **Przesyłaj strumieniowo do centrum zdarzeń** i kliknij pozycję **Centrum zdarzeń/Konfigurowanie**.
5. Wybierz subskrypcję platformy Azure i przestrzeń nazw usługi Event Hubs, do której chcesz kierować dzienniki. Subskrypcja i przestrzeń nazw usługi Event Hubs muszą być skojarzone z dzierżawą usługi Active Directory, z której są przesyłane strumieniowo dzienniki. Możesz również określić centrum zdarzeń w przestrzeni nazw usługi Event Hubs, do której mają być wysyłane dzienniki. Jeśli nie określono żadnego centrum zdarzeń, centrum zdarzeń zostanie utworzone w przestrzeni nazw z domyślną nazwą **insights-logs-audit**.
6. Kliknij przycisk **OK** aby zakończyć konfigurację centrum zdarzeń.
7. Zaznacz pole wyboru **AuditLogs** (Dzienniki inspekcji), aby wysłać dzienniki inspekcji na konto magazynu. 
8. Zaznacz pole wyboru **SignInLogs** (Dzienniki logowania), aby wysłać dzienniki logowania na konto magazynu.
9. Kliknij pozycję **Zapisz**, aby zapisać ustawienie.
    ![Ustawienia diagnostyczne](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Ustawienia diagnostyczne")

10. Po około 15 minutach sprawdź, czy zdarzenia są wyświetlane w centrum zdarzeń. Aby to zrobić, przejdź do centrum zdarzeń w portalu i sprawdź, czy liczba **komunikatów przychodzących** jest większa od zera. 
    ![Dzienniki inspekcji](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Dzienniki inspekcji")


## <a name="access-data-from-event-hubs"></a>Uzyskiwanie dostępu do danych z usługi Event Hubs

Kiedy dane pojawią się w centrum zdarzeń, można uzyskać do nich dostęp na dwa sposoby.

* **Konfigurowanie obsługiwanych narzędziem SIEM do odczytywania danych**: większość narzędzi wymaga parametrów połączenia centrum zdarzeń i pewnych uprawnień do Twojej subskrypcji platformy Azure w celu odczytywania danych z centrum zdarzeń. Tutaj znajduje się niepełna lista narzędzi obsługujących integrację usługi Azure Monitor:
    1. **Splunk**: aby uzyskać więcej informacji na temat sposobu integracji dzienników usługi Azure AD za pomocą platformy Splunk, zobacz [jak zintegrować dzienniki usługi Azure Active Directory z platformą Splunk za pomocą usługi Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar**: Składniki Microsoft Azure DSM i Microsoft Azure Event Hub Protocol są dostępne do pobrania w [witrynie pomocy technicznej firmy IBM](http://www.ibm.com/support). Możesz [dowiedzieć się więcej o integracji z platformą Azure w tym miejscu](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: wykonaj [te instrukcje](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub), aby skonfigurować SumoLogic do pracy z danymi z centrum zdarzeń. 

* **Konfigurowanie niestandardowych narzędzi do odczytu danych**: jeśli bieżący system SIEM nie jest jeszcze obsługiwane w diagnostyce usługi Azure Monitor, możesz skonfigurować narzędzia niestandardowe przy użyciu interfejsów API centrum zdarzeń. Aby dowiedzieć się więcej, zobacz [interfejsy API centrum zdarzeń](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Następne kroki

* [Integrowanie dzienników usługi Azure Active Directory z platformą Splunk za pomocą diagnostyki usługi Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpretowanie schematu dzienników inspekcji w diagnostyce usługi Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretowanie schematu dzienników logowania w diagnostyce usługi Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)