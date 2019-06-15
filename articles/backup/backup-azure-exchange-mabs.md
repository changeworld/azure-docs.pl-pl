---
title: Tworzenie kopii zapasowej programu Exchange server w usłudze Azure Backup przy użyciu usługi Azure Backup Server
description: Dowiedz się, jak wykonać kopię zapasową programu Exchange server w usłudze Azure Backup przy użyciu usługi Azure Backup Server
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641480"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Tworzenie kopii zapasowej programu Exchange server na platformę Azure za pomocą usługi Azure Backup Server
W tym artykule opisano sposób konfigurowania Microsoft Azure Backup Server (MABS) do utworzenia kopii zapasowej serwera Microsoft Exchange na platformie Azure.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że serwer usługi Azure Backup jest [zainstalowane i przygotowywane](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent ochrony serwera usługi Mab
Aby zainstalować agenta ochrony serwera usługi Mab na serwerze programu Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [skonfigurowania wyjątków zapory dla agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Zainstalować agenta na serwerze programu Exchange, klikając pozycję **zarządzania > agenci > Zainstaluj** w konsoli administratora serwera usługi MAB. Zobacz [Zainstaluj agenta ochrony serwera usługi Mab](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) szczegółowy opis kroków.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Utwórz grupę ochrony dla programu Exchange server
1. Kliknij w konsoli administratora serwera usługi Mab **ochrony**, a następnie kliknij przycisk **New** na wstążce narzędzi, aby otworzyć **Utwórz nową grupę ochrony** kreatora.
2. Na **powitalnej** ekranu kliknij polecenie Kreator **dalej**.
3. Na **wybierz typ grupy ochrony** ekranu, wybierz **serwerów** i kliknij przycisk **dalej**.
4. Wybierz bazy danych serwera Exchange, który chcesz chronić, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > W przypadku ochrony programu Exchange 2013 Sprawdź [wymagania wstępne dotyczące programu Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    W poniższym przykładzie wybrano bazy danych programu Exchange 2010.

    ![Wybierz członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Określ nazwę grupy ochrony, a następnie wybierz oba z następujących opcji:

   * Chcę uzyskać krótkoterminową ochronę za pomocą dysku.
   * Chcę uzyskać ochronę online.
6. Kliknij przycisk **Dalej**.
7. Wybierz **Uruchom program Eseutil, aby sprawdzić spójność danych** opcji, jeśli chcesz sprawdzić integralność bazy danych programu Exchange Server.

    Po zaznaczeniu tej opcji, sprawdzanie spójności kopii zapasowej będzie uruchamiana zgodnie z serwera usługi Mab, aby uniknąć ruch we/wy, który jest generowany przez uruchomienie **eseutil** polecenia na serwerze programu Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serwera usługi MAB. W przeciwnym razie zostanie wywołany następujący błąd:  
   > ![Błąd Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kliknij przycisk **Dalej**.
9. Wybierz bazę danych dla **kopii zapasowej**, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Jeśli nie zaznaczysz "Pełna kopia zapasowa" związanym z kopiowaniem co najmniej jednej grupy DAG bazy danych, dzienników nie zostaną obcięte.
   >
   >
10. Konfigurowanie celów dla **krótkoterminowych kopii zapasowych**, a następnie kliknij przycisk **dalej**.
11. Przejrzyj dostępne miejsce na dysku, a następnie kliknij przycisk **dalej**.
12. Wybierz godzinę, jaką serwera usługi MAB będzie replikacji początkowej, a następnie kliknij polecenie **dalej**.
13. Wybierz opcje sprawdzania spójności, a następnie kliknij przycisk **dalej**.
14. Wybierz bazę danych, który chcesz utworzyć kopię zapasową na platformie Azure, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ dane chronione w trybie online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **kopia zapasowa Azure**, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ harmonogram tworzenia kopii zapasowej online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Należy pamiętać, punkty odzyskiwania Online są na podstawie ekspresowego pełnego punktów odzyskiwania. W związku z tym należy zaplanować punktu odzyskiwania online po punktu odzyskiwania w czasie określonym dla ekspresowej pełnej.
    >
    >
16. Konfigurowanie zasad przechowywania dla **kopia zapasowa Azure**, a następnie kliknij przycisk **dalej**.
17. Wybierz opcję replikacji online, a następnie kliknij przycisk **dalej**.

    W przypadku dużych baz danych może potrwać długo początkowa kopia zapasowa ma zostać utworzony za pośrednictwem sieci. Aby uniknąć tego problemu, można utworzyć kopii zapasowej offline.  

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie kliknij przycisk **Utwórz grupę**.
19. Kliknij przycisk **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange
1. Aby odzyskać bazę danych programu Exchange, kliknij przycisk **odzyskiwania** w konsoli administratora serwera usługi MAB.
2. Znajdź bazę danych programu Exchange, który chcesz odzyskać.
3. Wybierz punkt odzyskiwania w trybie online z *czas odzyskiwania* listy rozwijanej.
4. Kliknij przycisk **odzyskać** można uruchomić **Kreatora odzyskiwania**.

Punkty odzyskiwania online są pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera programu Exchange.
* **Odzyskaj do innej bazy danych programu Exchange Server:** Dane zostaną odzyskane do innej bazy danych na innym serwerze programu Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczna stacja taśm dołączonych i skonfigurowane na serwera usługi Mab, punkt odzyskiwania zostaną skopiowane do wolnej taśmy.

    ![Wybierz replikację online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Kolejne kroki
* [Usługa Azure Backup — często zadawane pytania](backup-azure-backup-faq.md)
