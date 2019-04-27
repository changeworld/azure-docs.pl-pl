---
title: Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM
description: Dowiedz się, jak wykonać kopię zapasową programu Exchange server w usłudze Azure Backup przy użyciu programu System Center 2012 R2 DPM
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: kasinh
ms.openlocfilehash: ef976667ec580ea75dd1b8566c7bdddf35eeb0fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647267"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM
W tym artykule opisano sposób konfigurowania serwera System Center 2012 R2 Data Protection Manager (DPM), aby utworzyć kopię zapasową serwera Microsoft Exchange do usługi Azure Backup.  

## <a name="updates"></a>Aktualizacje
Aby pomyślnie zarejestrować serwer programu DPM za pomocą usługi Azure Backup, należy zainstalować najnowszego pakietu zbiorczego aktualizacji dla programu System Center 2012 R2 DPM oraz najnowszą wersję agenta usługi Azure Backup. Pobierz najnowszy pakiet zbiorczy aktualizacji z [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> W przykładach w tym artykule jest zainstalowana wersja 2.0.8719.0 agenta usługi Azure Backup, a na System Center 2012 R2 DPM zainstalowano pakietu zbiorczego aktualizacji 6.
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites-and-limitations) narzędzia Kopia zapasowa Microsoft Azure do ochrony obciążeń zostały spełnione. Te wymagania wstępne są następujące:

* Magazyn kopii zapasowych w witrynie platformy Azure została utworzona.
* Poświadczenia magazynu i agenta zostały pobrane na serwer programu DPM.
* Agent jest zainstalowany na serwerze programu DPM.
* Poświadczenia magazynu zostały użyte do zarejestrowania serwera programu DPM.
* W przypadku ochrony programu Exchange 2016, przeprowadź uaktualnienie do wersji DPM 2012 R2 pakietem zbiorczym aktualizacji 9 lub nowszej

## <a name="dpm-protection-agent"></a>Agent ochrony programu DPM
Aby zainstalować agenta ochrony programu DPM na serwerze programu Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [skonfigurowania wyjątków zapory dla agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Zainstalować agenta na serwerze programu Exchange, klikając pozycję **zarządzania > agenci > Zainstaluj** w konsoli administratora programu DPM. Zobacz [Zainstaluj agenta ochrony DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) szczegółowy opis kroków.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Utwórz grupę ochrony dla programu Exchange server
1. W konsoli administratora programu DPM kliknij **ochrony**, a następnie kliknij przycisk **New** na wstążce narzędzi, aby otworzyć **Utwórz nową grupę ochrony** kreatora.
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

    Po zaznaczeniu tej opcji, sprawdzanie spójności kopii zapasowej będzie uruchamiana na serwerze programu DPM, aby uniknąć ruch we/wy, który jest generowany przez uruchomienie **eseutil** polecenia na serwerze programu Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serwerze programu DPM. W przeciwnym razie zostanie wywołany następujący błąd:  
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
12. Wybierz czas, w którym serwer programu DPM będzie tworzenie początkowej replikacji, a następnie kliknij **dalej**.
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
1. Aby odzyskać bazę danych programu Exchange, kliknij przycisk **odzyskiwania** w konsoli administratora programu DPM.
2. Znajdź bazę danych programu Exchange, który chcesz odzyskać.
3. Wybierz punkt odzyskiwania w trybie online z *czas odzyskiwania* listy rozwijanej.
4. Kliknij przycisk **odzyskać** można uruchomić **Kreatora odzyskiwania**.

Punkty odzyskiwania online są pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera programu Exchange.
* **Odzyskaj do innej bazy danych programu Exchange Server:** Dane zostaną odzyskane do innej bazy danych na innym serwerze programu Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczna stacja taśm dołączonych i skonfigurowane na serwerze programu DPM, punkt odzyskiwania zostaną skopiowane do wolnej taśmy.

    ![Wybierz replikację online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Kolejne kroki
* [Usługa Azure Backup — często zadawane pytania](backup-azure-backup-faq.md)
