---
title: "3."
description: Dowiedz się, jak wykonać kopię zapasową serwera Exchange w usłudze Azure Backup przy użyciu programu System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 389713767409ff49c6fc83a4d6e8dc3c87272fc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614368"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Tworzenie kopii zapasowych serwera programu Exchange do usługi Azure Backup przy użyciu programu System Center 2012 R2 DPM

W tym artykule opisano sposób konfigurowania serwera Programu DPM (System Center 2012 R2 Data Protection Manager) w celu utworzenia kopii zapasowej serwera microsoft exchange w usłudze Azure Backup.  

## <a name="updates"></a>Aktualizacje

Aby pomyślnie zarejestrować serwer programu DPM w usłudze Azure Backup, należy zainstalować najnowszy pakiet zbiorczy aktualizacji dla programu Us Program DPM programu System Center 2012 r2 i najnowszą wersję agenta azure backup. Pobierz najnowszy pakiet zbiorczy aktualizacji z [katalogu Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Przykłady w tym artykule jest zainstalowana wersja 2.0.8719.0 agenta tworzenia kopii zapasowych platformy Azure, a pakiet zbiorczy aktualizacji 6 jest zainstalowany w programie System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że wszystkie [wymagania wstępne](backup-azure-dpm-introduction.md#prerequisites-and-limitations) dotyczące korzystania z usługi Microsoft Azure Backup w celu ochrony obciążeń zostały spełnione. Te wymagania wstępne są następujące:

* Utworzono magazyn kopii zapasowych w witrynie platformy Azure.
* Poświadczenia agenta i magazynu zostały pobrane na serwer programu DPM.
* Agent jest zainstalowany na serwerze programu DPM.
* Poświadczenia magazynu zostały użyte do zarejestrowania serwera programu DPM.
* Jeśli chronisz program Exchange 2016, uaktualnij program DPM 2012 R2 UR9 lub nowszą

## <a name="dpm-protection-agent"></a>Agent ochrony programu DPM

Aby zainstalować agenta ochrony programu DPM na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [Konfigurowanie wyjątków zapory dla agenta](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Zainstaluj agenta na serwerze Exchange, klikając pozycję **Agenci > zarządzania > zainstaluj** w konsoli administratora programu DPM. Szczegółowe kroki [można znaleźć w aplikacji Instalowanie agenta ochrony programu DPM.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera Exchange

1. W konsoli administratora programu DPM kliknij pozycję **Ochrona**, a następnie kliknij pozycję **Nowy** na wstążce narzędzia, aby otworzyć **kreatora Tworzenie nowej grupy ochrony.**
2. Na ekranie **powitalnym** kreatora kliknij przycisk **Dalej**.
3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery** i kliknij przycisk **Dalej**.
4. Wybierz bazę danych serwera Exchange, którą chcesz chronić, i kliknij przycisk **Dalej**.

   > [!NOTE]
   > Jeśli chronisz program Exchange 2013, sprawdź [wymagania wstępne programu Exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    W poniższym przykładzie wybrano bazę danych programu Exchange 2010.

    ![Wybieranie członków grupy](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Wybierz metodę ochrony danych.

    Nazwij grupę ochrony, a następnie wybierz obie następujące opcje:

   * Chcę krótkoterminowej ochrony za pomocą dysku.
   * Chcę ochrony online.
6. Kliknij przycisk **alej**.
7. Wybierz opcję **Uruchom Eseutil, aby sprawdzić integralność danych,** jeśli chcesz sprawdzić integralność baz danych programu Exchange Server.

    Po wybraniu tej opcji sprawdzanie spójności kopii zapasowych zostanie uruchomione na serwerze programu DPM, aby uniknąć ruchu we/wy generowanego przez uruchomienie polecenia **eseutil** na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin katalogu na serwerze programu DPM. W przeciwnym razie zostanie wyzwolony następujący błąd:  
   > ![błąd eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Kliknij przycisk **alej**.
9. Wybierz bazę danych dla **kopii zapasowej,** a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Jeśli nie wybierzesz "Pełna kopia zapasowa" dla co najmniej jednej kopii DAG bazy danych, dzienniki nie zostaną obcięty.
   >
   >
10. Skonfiguruj cele dla **krótkoterminowej kopii zapasowej,** a następnie kliknij przycisk **Dalej**.
11. Przejrzyj dostępne miejsce na dysku, a następnie kliknij przycisk **Dalej**.
12. Wybierz godzinę, w której serwer programu DPM utworzy replikację początkową, a następnie kliknij przycisk **Dalej**.
13. Wybierz opcje sprawdzania spójności, a następnie kliknij przycisk **Dalej**.
14. Wybierz bazę danych, której chcesz użyć kopii zapasowej na platformie Azure, a następnie kliknij przycisk **Dalej**. Przykład:

    ![Określanie danych ochrony online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Zdefiniuj harmonogram **tworzenia kopii zapasowych platformy Azure,** a następnie kliknij przycisk **Dalej**. Przykład:

    ![Określ harmonogram tworzenia kopii zapasowych online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Uwaga Punkty odzyskiwania online są oparte na wyraźnych pełnych punktach odzyskiwania. W związku z tym należy zaplanować punkt odzyskiwania online po czasie, który jest określony dla punktu odzyskiwania express pełne.
    >
    >
16. Skonfiguruj zasady przechowywania dla **usługi Azure Backup,** a następnie kliknij przycisk **Dalej**.
17. Wybierz opcję replikacji online i kliknij przycisk **Dalej**.

    Jeśli masz dużą bazę danych, może upłynąć dużo czasu, aby początkowa kopia zapasowa została utworzona za pośrednictwem sieci. Aby uniknąć tego problemu, można utworzyć kopię zapasową w trybie offline.  

    ![Określanie zasad przechowywania w trybie online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Potwierdź ustawienia, a następnie kliknij pozycję **Utwórz grupę**.
19. Kliknij przycisk **Zamknij**.

## <a name="recover-the-exchange-database"></a>Odzyskiwanie bazy danych programu Exchange

1. Aby odzyskać bazę danych programu Exchange, kliknij pozycję **Odzyskiwanie** w konsoli administratora programu DPM.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z listy rozwijanej *czasu odzyskiwania.*
4. Kliknij **przycisk Odzyskaj,** aby uruchomić **Kreatora odzyskiwania**.

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera Exchange.
* **Odzyskiwanie do innej bazy danych na serwerze Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskiwanie do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiowanie do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli na serwerze programu DPM jest dołączona i skonfigurowana autonomiczna kondygnacji taśm, punkt odzyskiwania zostanie skopiowany na bezpłatną taśmę.

    ![Wybieranie replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące kopii zapasowej platformy Azure](backup-azure-backup-faq.md)
