---
title: Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM
description: Dowiedz się, jak wykonać kopię zapasową serwera Exchange, kopia zapasowa Azure za pomocą programu System Center 2012 R2 DPM
services: backup
author: MaanasSaran
manager: NKolli1
ms.service: backup
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: adigan
ms.openlocfilehash: 4edec499d12261add398e5a9297f039ecfb252e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605104"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM
W tym artykule opisano sposób konfigurowania serwera System Center 2012 R2 danych ochrony Manager (DPM), aby utworzyć kopię zapasową serwera Microsoft Exchange do usługi Kopia zapasowa Azure.  

## <a name="updates"></a>Aktualizacje
Aby pomyślnie rejestrowania serwera DPM w usłudze Kopia zapasowa Azure, należy zainstalować najnowszego pakietu zbiorczego aktualizacji dla programu System Center 2012 R2 DPM i najnowszą wersję agenta kopii zapasowej Azure. Pobierz najnowszy pakiet zbiorczy aktualizacji z [Microsoft Catalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Przykłady w tym artykule jest zainstalowana wersja 2.0.8719.0 agenta kopii zapasowej Azure i pakietu zbiorczego aktualizacji 6 jest zainstalowany na System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Wymagania wstępne
Przed kontynuowaniem upewnij się, że wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites) narzędzia Kopia zapasowa Microsoft Azure do ochrony obciążeń zostały spełnione. Te wymagania wstępne są następujące:

* Magazyn kopii zapasowych w witrynie Azure została utworzona.
* Poświadczenia magazynu i agenta zostały pobrane na serwer programu DPM.
* Agent jest zainstalowany na serwerze programu DPM.
* Poświadczenia magazynu były używane do rejestrowania serwera DPM.
* W przypadku ochrony programu Exchange 2016, przeprowadź uaktualnienie do wersji DPM 2012 R2 UR9 lub nowszej

## <a name="dpm-protection-agent"></a>Agent ochrony programu DPM
Aby zainstalować agenta ochrony DPM na serwerze programu Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [skonfigurowania wyjątków zapory dla agenta](https://technet.microsoft.com/library/Hh758204.aspx).
2. Zainstaluj agenta na serwerze programu Exchange, klikając **zarządzania > agentów > Zainstaluj** w konsoli administratora programu DPM. Zobacz [zainstalować agenta ochrony DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) szczegółowy opis kroków.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Utwórz grupę ochrony dla serwera Exchange
1. W konsoli administratora programu DPM, kliknij przycisk **ochrony**, a następnie kliknij przycisk **nowy** na wstążce narzędzi, aby otworzyć **tworzenia nowej grupy ochrony** kreatora.
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

    Po wybraniu tej opcji sprawdzania spójności kopii zapasowej zostanie uruchomione na serwerze programu DPM, aby uniknąć ruch we/wy, który jest generowany przez uruchomienie **eseutil** polecenia na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin na serwerze programu DPM. W przeciwnym razie zostanie wywołany następujący błąd:  
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
12. Określ czas, co serwer programu DPM będzie utworzyć replikacji początkowej, a następnie kliknij **dalej**.
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
1. Aby odzyskać bazy danych programu Exchange, kliknij **odzyskiwania** w konsoli administratora programu DPM.
2. Zlokalizuj bazy danych programu Exchange, który chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z *czasu odzyskiwania* listy rozwijanej.
4. Kliknij przycisk **odzyskać** uruchomić **Kreatora odzyskiwania**.

Punkty odzyskiwania online są pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** dane zostaną odzyskane na oryginalnym serwerze Exchange.
* **Odzyskaj do innej bazy danych serwera Exchange:** dane zostaną odzyskane do innej bazy danych na innym serwerze programu Exchange.
* **Odzyskaj do bazy danych odzyskiwania:** dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiuj do folderu sieciowego:** dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli biblioteka taśm lub autonomiczna stacja taśm dołączonych i skonfigurowane na serwerze programu DPM, punkt odzyskiwania zostanie skopiowana do wolnej taśmy.

    ![Wybierz opcję replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Kolejne kroki
* [Azure — często zadawane pytania kopii zapasowej](backup-azure-backup-faq.md)
