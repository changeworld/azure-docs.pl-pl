---
title: Wykonywanie kopii zapasowej serwera Exchange kopia zapasowa Azure z serwerem kopia zapasowa Azure
description: Dowiedz się, jak wykonać kopię zapasową serwera Exchange, kopia zapasowa Azure za pomocą serwera usługi Kopia zapasowa Azure
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: d64c273a189b1fe2337c4430b156874e0adf54b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605964"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-azure-backup-server"></a>Wykonywanie kopii zapasowej serwera Exchange kopia zapasowa Azure z serwerem kopia zapasowa Azure
W tym artykule opisano sposób konfigurowania programu Microsoft Azure kopii zapasowej serwera (MABS) aby utworzyć kopię zapasową Microsoft Exchange server na platformie Azure.  

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że serwer kopii zapasowej Azure jest [zainstalowany i przygotowane](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agent ochrony MABS
Aby zainstalować agenta ochrony MABS na serwerze programu Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [skonfigurowania wyjątków zapory dla agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Zainstaluj agenta na serwerze programu Exchange, klikając **zarządzania > agentów > Zainstaluj** w konsoli administratora MABS. Zobacz [zainstalować agenta ochrony MABS](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) szczegółowy opis kroków.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Utwórz grupę ochrony dla serwera Exchange
1. W konsoli administratora MABS kliknij **ochrony**, a następnie kliknij przycisk **nowy** na wstążce narzędzi, aby otworzyć **tworzenia nowej grupy ochrony** kreatora.
2. Na **powitalnej** ekranu kliknij kreatora **dalej**.
3. Na **wybierz typ grupy ochrony** wybierz **serwerów** i kliknij przycisk **dalej**.
4. Wybierz bazy danych serwera Exchange, który chcesz chronić, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > W przypadku ochrony programu Exchange 2013 Sprawdź [wymagania wstępne programu Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    W poniższym przykładzie wybrano bazy danych programu Exchange 2010.

    ![Wybierz członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Określ nazwę grupy ochrony, a następnie wybierz oba z następujących opcji:

   * Chcę uzyskać krótkoterminową ochronę za pomocą dysku.
   * Chcę uzyskać ochronę online.
6. Kliknij przycisk **Dalej**.
7. Wybierz **Uruchom program Eseutil, aby sprawdzić spójność danych** opcję, jeśli chcesz sprawdzić spójność baz danych programu Exchange Server.

    Po wybraniu tej opcji sprawdzania spójności kopii zapasowej jest uruchamiane MABS, aby uniknąć ruch we/wy, który jest generowany przez uruchomienie **eseutil** polecenia na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serwerze MAB. W przeciwnym razie zostanie wywołany następujący błąd:  
   > ![Błąd Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kliknij przycisk **Dalej**.
9. Wybierz bazę danych dla **kopii zapasowej**, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Jeśli nie zostanie wybrana opcja "Pełnej kopii zapasowej" dla co najmniej jednej grupy DAG kopii bazy danych, dzienniki nie zostaną obcięte.
   >
   >
10. Konfigurowanie celów dla **krótkoterminowych kopii zapasowych**, a następnie kliknij przycisk **dalej**.
11. Przejrzyj dostępne miejsce na dysku, a następnie kliknij przycisk **dalej**.
12. Określ czas, jaką serwera MAB będzie utworzyć replikacji początkowej, a następnie kliknij **dalej**.
13. Wybierz opcje sprawdzania spójności, a następnie kliknij przycisk **dalej**.
14. Wybierz bazę danych, który chcesz utworzyć kopię zapasową na platformie Azure, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ dane chronione w trybie online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **kopia zapasowa Azure**, a następnie kliknij przycisk **dalej**. Na przykład:

    ![Określ harmonogram tworzenia kopii zapasowej online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Należy pamiętać, punkty odzyskiwania Online są na podstawie ekspresowego pełnego punktów odzyskiwania. W związku z tym musisz zaplanować punktu odzyskiwania online po punktu odzyskiwania w czasie określonym dla ekspresowej pełnej.
    >
    >
16. Konfigurowanie zasad przechowywania dla **kopia zapasowa Azure**, a następnie kliknij przycisk **dalej**.
17. Wybierz opcję replikacji online, a następnie kliknij przycisk **dalej**.

    Jeśli masz dużą bazy danych, może upłynąć długo początkowa kopia zapasowa ma zostać utworzony za pośrednictwem sieci. Aby uniknąć tego problemu, można utworzyć kopię zapasową offline.  

    ![Określ zasady przechowywania danych online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie kliknij przycisk **Utwórz grupę**.
19. Kliknij przycisk **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange
1. Aby odzyskać bazy danych programu Exchange, kliknij **odzyskiwania** w konsoli administratora MABS.
2. Zlokalizuj bazy danych programu Exchange, który chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z *czasu odzyskiwania* listy rozwijanej.
4. Kliknij przycisk **odzyskać** uruchomić **Kreatora odzyskiwania**.

Punkty odzyskiwania online są pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** dane zostaną odzyskane na oryginalnym serwerze Exchange.
* **Odzyskaj do innej bazy danych serwera Exchange:** dane zostaną odzyskane do innej bazy danych na innym serwerze programu Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli biblioteka taśm lub autonomiczna stacja taśm dołączonych i skonfigurowane na MABS, punkt odzyskiwania zostanie skopiowana do wolnej taśmy.

    ![Wybierz opcję replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Kolejne kroki
* [Azure — często zadawane pytania kopii zapasowej](backup-azure-backup-faq.md)
