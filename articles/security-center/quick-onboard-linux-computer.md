---
title: 'Szybki start: Azure Security Center — dołączanie komputerów z systemem Linux do usługi Security Center | Microsoft Docs'
description: Ten przewodnik Szybki start przedstawia sposób dołączania komputerów z systemem Linux do usługi Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 05e4bed0f9b4dfb6d1879408085447ef53db8655
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630101"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Szybki start: dołączanie komputerów z systemem Linux do usługi Azure Security Center
Po dołączeniu subskrypcji platformy Azure możesz włączyć usługę Security Center dla zasobów z systemem Linux uruchomionych poza platformą Azure, np. lokalnych lub znajdujących się w innych chmurach, aprowizując agenta systemu Linux.

Ten przewodnik Szybki start przedstawia sposób instalacji agenta systemu Linux na komputerze z systemem Linux.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Musisz korzystać z warstwy cenowej Standardowa usługi Security Center przed rozpoczęciem tego przewodnika Szybki start. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni.

## <a name="add-new-linux-computer"></a>Dodawanie nowego komputera z systemem Linux

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu platformy **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

 ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Dołączanie do zabezpieczeń zaawansowanych**.
4. Wybierz pozycję **Czy chcesz dodać komputery spoza platformy Azure**.
   ![Dołączanie do zabezpieczeń zaawansowanych][3]

5. W oknie **Dodawanie nowych komputerów spoza platformy Azure** jest wyświetlana lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

    ![Dodawanie komputera spoza platformy Azure][4]

6.  Na stronie **Agent bezpośredni** w obszarze **POBIERANIE I DOŁĄCZANIE AGENTA DLA SYSTEMU LINUX** wybierz przycisk **kopiowania**, aby skopiować polecenie *wget*.

7.  Otwórz program Notatnik i wklej to polecenie. Zapisz ten plik do lokalizacji dostępnej z komputera z systemem Linux.

## <a name="install-the-agent"></a>Instalowanie agenta

1.  Na komputerze z systemem Linux otwórz uprzednio zapisany plik. Zaznacz całą zawartość, skopiuj ją, otwórz konsolę terminala, a następnie wklej polecenie.
2.  Po zakończeniu instalacji możesz sprawdzić, czy agent *omsagent* jest zainstalowany, uruchamiając polecenie *pgrep*. Polecenie zwróci identyfikator PID agenta *omsagent* (identyfikator procesu), jak przedstawiono poniżej:

  ![Instalowanie agenta][5]

Dzienniki agenta usługi Azure Security Center dla systemu Linux można znaleźć w folderze: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Dzienniki agenta][6]

Po pewnym czasie (do 30 minut) nowy komputer z systemem Linux pojawi się w usłudze Security Center.

Teraz możesz monitorować maszyny wirtualne platformy Azure oraz komputery spoza platformy Azure w jednym miejscu. W obszarze **Obliczanie** zawarto przegląd wszystkich maszyn wirtualnych i komputerów wraz z zaleceniami. Każda kolumna reprezentuje jeden zestaw zaleceń. Kolor reprezentuje bieżący stan zabezpieczeń maszyny wirtualnej lub komputera dla tego zalecenia. Ponadto usługa Security Center przedstawia wszelkie wykrycia dotyczące tych komputerów w alertach zabezpieczeń.

  ![Blok Obliczenia][7] Istnieją dwa typy ikon przedstawianych w bloku **Obliczenia**:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Komputer spoza platformy Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Maszyna wirtualna platformy Azure

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie jest już potrzebny, można usunąć agenta z komputera z systemem Linux.

Aby usunąć agenta:

1. Pobierz na komputer [uniwersalny skrypt](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) agenta systemu Linux.
2. Uruchom na komputerze plik sh pakietu z argumentem *--purge*, co spowoduje całkowite usunięcie agenta i jego konfiguracji.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start aprowizowano agenta na komputerze z systemem Linux. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
