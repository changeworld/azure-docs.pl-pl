---
title: Dziele zapasowe farmy programu SharePoint na platformie Azure za pomocą programu DPM
description: Ten artykuł zawiera omówienie ochrony serwera usługi DPM/Azure Backup farmy programu SharePoint na platformie Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054112"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Dziele zapasowe farmy programu SharePoint na platformie Azure za pomocą programu DPM

Tworzenie kopii zapasowych farmy programu SharePoint na platformie Microsoft Azure przy użyciu programu System Center Data Protection Manager (DPM) w taki sam sposób, jak tworzenie kopii zapasowych innych źródeł danych. Usługa Azure Backup zapewnia elastyczność w harmonogramie tworzenia kopii zapasowych w celu tworzenia dziennych, tygodniowych, miesięcznych lub 5 000 punktów kopii zapasowych i udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Program DPM umożliwia przechowywanie kopii dysków lokalnych w celu uzyskania celów szybkiego odzyskiwania (RTO) i przechowywanie kopii na platformie Azure w celu uzyskania ekonomicznego, długoterminowego przechowywania.

Tworzenie kopii zapasowej programu SharePoint na platformie Azure za pomocą programu DPM jest bardzo podobny proces do tworzenia kopii zapasowej programu SharePoint do programu DPM lokalnie. W tym artykule zostaną odnotowane szczegółowe zagadnienia dotyczące platformy Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane scenariusze ochrony

Aby uzyskać listę obsługiwanych wersji programu SharePoint i wersji programu DPM wymaganych do wykonywania kopii zapasowych poszczególnych wersji programu SharePoint, zobacz artykuł [What can DPM back up?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup) (Kopie zapasowe wykonywane przez program DPM).

## <a name="before-you-start"></a>Przed rozpoczęciem

Istnieje kilka rzeczy, które należy potwierdzić przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że spełniasz wszystkie [wymagania wstępne dotyczące korzystania z usługi Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) w celu ochrony obciążeń. Niektóre zadania dotyczące wymagań wstępnych obejmują: tworzenie magazynu kopii zapasowych, pobieranie poświadczeń magazynu, instalowanie agenta azure backup agent i rejestrowanie programu DPM/Azure Backup Server w przechowalni.

Dodatkowe wymagania wstępne i ograniczenia można znaleźć w artykule [Kopii zapasowej programu SharePoint z programem DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Aby utworzyć kopię zapasową farmy programu SharePoint, skonfiguruj ochronę programu SharePoint przy użyciu pliku ConfigureSharePoint.exe, a następnie utwórz grupę ochrony w programie DPM. Aby uzyskać instrukcje, zobacz [Konfigurowanie kopii zapasowej](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) w dokumentacji programu DPM.

## <a name="monitoring"></a>Monitorowanie

Aby monitorować zadanie tworzenia kopii zapasowej, postępuj zgodnie z instrukcjami w [obszarze Monitorowanie kopii zapasowej programu DPM](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)

## <a name="restore-sharepoint-data"></a>Przywracanie danych programu SharePoint

Aby dowiedzieć się, jak przywrócić element programu SharePoint z dysku za pomocą programu DPM, zobacz [Przywracanie danych programu SharePoint](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM

1. Aby odzyskać bazę danych zawartości programu SharePoint, przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![Ochrona programu SharePoint programu DPM8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje o wykazie programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona pod kątem długoterminowego przechowywania na platformie Azure, na serwerze programu DPM nie są dostępne żadne informacje o katalogu (metadane). W rezultacie za każdym razem, gdy baza danych zawartości programu SharePoint w programie SharePoint musi zostać odzyskana, należy ponownie skatalogować farmę programu SharePoint.
   >
   >
3. Kliknij **pozycję Ponownie kataloguj**.

    ![Ochrona programu DPM programu SharePoint10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu **Recatalog** w chmurze.

    ![Ochrona programu SharePoint programu DPM11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu katalogowania stan zmienia się na *Sukces*. Kliknij przycisk **Zamknij**.

    ![Ochrona programu SharePoint programu DPM12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint pokazany na karcie **Odzyskiwanie** programu DPM, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij polecenie **Odzyskaj**.

    ![Ochrona programu SharePoint programu DPM13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="switching-the-front-end-web-server"></a>Przełączanie frontowego serwera sieci Web

Jeśli masz więcej niż jeden serwer sieci Web frontu i chcesz przełączyć serwer używany przez program DPM do ochrony farmy, postępuj zgodnie z instrukcjami w [aplikacji Przełączanie frontowego serwera sieci Web](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Następne kroki

* [Serwer kopii zapasowych platformy Azure i program DPM — często zadawane pytania](backup-azure-dpm-azure-server-faq.md)
* [Rozwiązywanie problemów z programem System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
