---
title: Tworzenie kopii zapasowych serwera exchange za pomocą serwera kopii zapasowych platformy Azure
description: Dowiedz się, jak wykonać kopię zapasową serwera exchange w usłudze Azure Backup przy użyciu serwera kopii zapasowej platformy Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421346"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Tworzenie kopii zapasowej serwera programu Exchange na platformie Azure za pomocą usługi Azure Backup Server

W tym artykule opisano sposób konfigurowania programu Microsoft Azure Backup Server (MABS) do tworzenia kopii zapasowych serwera Microsoft Exchange na platformie Azure.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że program Azure Backup Server jest [zainstalowany i przygotowany](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Środek ochrony MABS

Aby zainstalować agenta ochrony MABS na serwerze Exchange, wykonaj następujące kroki:

1. Upewnij się, że zapory są poprawnie skonfigurowane. Zobacz [Konfigurowanie wyjątków zapory dla agenta](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Zainstaluj agenta na serwerze Exchange, klikając pozycję **Agenci > zarządzania > zainstaluj** w konsoli administratora systemu MABS. Szczegółowe kroki [można znaleźć w aplikacji Instalowanie agenta ochrony MABS.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Tworzenie grupy ochrony dla serwera Exchange

1. W konsoli administratora mabs kliknij pozycję **Ochrona**, a następnie kliknij pozycję **Nowy** na wstążce narzędzia, aby otworzyć **kreatora Tworzenie nowej grupy ochrony.**
2. Na ekranie **powitalnym** kreatora kliknij przycisk **Dalej**.
3. Na ekranie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery** i kliknij przycisk **Dalej**.
4. Wybierz bazę danych serwera Exchange, którą chcesz chronić, i kliknij przycisk **Dalej**.

   > [!NOTE]
   > Jeśli chronisz program Exchange 2013, sprawdź [wymagania wstępne programu Exchange 2013](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016).
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

    Po wybraniu tej opcji sprawdzanie spójności kopii zapasowych będzie uruchamiane na mabs, aby uniknąć ruchu we/wy, który jest generowany przez uruchomienie polecenia **eseutil** na serwerze Exchange.

   > [!NOTE]
   > Aby użyć tej opcji, należy skopiować pliki Ese.dll i Eseutil.exe do katalogu C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin na serwerze MABS. W przeciwnym razie zostanie wyzwolony następujący błąd:  
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
12. Wybierz godzinę, w której serwer MABS utworzy replikację początkową, a następnie kliknij przycisk **Dalej**.
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

1. Aby odzyskać bazę danych programu Exchange, kliknij pozycję **Odzyskiwanie** w konsoli administratora systemu MABS.
2. Znajdź bazę danych programu Exchange, którą chcesz odzyskać.
3. Wybierz punkt odzyskiwania online z listy rozwijanej *czasu odzyskiwania.*
4. Kliknij **przycisk Odzyskaj,** aby uruchomić **Kreatora odzyskiwania**.

W przypadku punktów odzyskiwania online istnieje pięć typów odzyskiwania:

* **Odzyskaj do oryginalnej lokalizacji serwera Exchange:** Dane zostaną odzyskane do oryginalnego serwera Exchange.
* **Odzyskiwanie do innej bazy danych na serwerze Exchange:** Dane zostaną odzyskane do innej bazy danych na innym serwerze Exchange.
* **Odzyskiwanie do bazy danych odzyskiwania:** Dane zostaną odzyskane do bazy danych odzyskiwania programu Exchange (RDB).
* **Kopiowanie do folderu sieciowego:** Dane zostaną odzyskane do folderu sieciowego.
* **Kopiuj na taśmę:** Jeśli masz bibliotekę taśm lub autonomiczny napęd taśmowy dołączony i skonfigurowany w systemie MABS, punkt odzyskiwania zostanie skopiowany na bezpłatną taśmę.

    ![Wybieranie replikacji online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące kopii zapasowej platformy Azure](backup-azure-backup-faq.md)
