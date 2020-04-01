---
title: Wbudowane komputery z systemem Linux do usługi Azure Security Center | Dokumenty firmy Microsoft
description: Ten przewodnik Szybki start przedstawia sposób dołączania komputerów z systemem Linux do usługi Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 3b7181550fd76f158ec04e9779f4301bd74484e1
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435974"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Szybki start: dołączanie komputerów z systemem Linux do usługi Azure Security Center
Po połączeniu subskrypcji platformy Azure można włączyć usługi Security Center dla zasobów systemu Linux działających poza platformą Azure, na przykład lokalnie lub w innych chmurach, przez inicjowanie obsługi administracyjnej agenta. Agent jest nazywany agentem usługi Log Analytics, ale jest również znany jako agent oms.

Ten przewodnik Szybki start pokazuje, jak zainstalować agenta na komputerze z systemem Linux.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Przed rozpoczęciem tego przewodnika Szybki start musisz ować standardową warstwę cenową centrum zabezpieczeń. Zobacz [Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa](security-center-get-started.md), aby uzyskać instrukcje dotyczące uaktualnienia. Możesz bezpłatnie wypróbować standard centrum zabezpieczeń. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Dodawanie nowego komputera z systemem Linux

1. Zaloguj się do [witryny Azure portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu **Microsoft Azure** wybierz pozycję **Centrum zabezpieczeń**. Zostanie otwarte okno **Security Center — Przegląd**.

   ![Security Center — Przegląd][2]

3. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
4. Wybierz kartę **Wprowadzenie.** ![Wprowadzenie][3]

5. Kliknij przycisk **Konfiguruj** w obszarze **Dodaj nowe komputery spoza platformy Azure**, zostanie wyświetlona lista obszarów roboczych usługi Log Analytics. Jeśli ma to zastosowanie, lista zawiera domyślny obszar roboczy utworzony przez usługę Security Center po włączeniu automatycznej aprowizacji. Wybierz ten obszar roboczy lub inny obszar roboczy, którego chcesz użyć.

    ![Dodawanie komputera spoza platformy Azure](./media/quick-onboard-linux-computer/non-azure.png)

6. Na stronie **Agent bezpośredni** w obszarze **POBIERANIE I DOŁĄCZANIE AGENTA DLA SYSTEMU LINUX** wybierz przycisk **kopiowania**, aby skopiować polecenie *wget*.

7. Otwórz program Notatnik i wklej to polecenie. Zapisz ten plik do lokalizacji dostępnej z komputera z systemem Linux.

## <a name="install-the-agent"></a>Instalowanie agenta

1. Na komputerze z systemem Linux otwórz uprzednio zapisany plik. Zaznacz całą zawartość, skopiuj ją, otwórz konsolę terminala, a następnie wklej polecenie.
2. Po zakończeniu instalacji możesz sprawdzić, czy agent *omsagent* jest zainstalowany, uruchamiając polecenie *pgrep*. Polecenie zwróci identyfikator PID agenta *omsagent* (identyfikator procesu), jak przedstawiono poniżej:

   ![Instalowanie agenta][5]

Dzienniki dla agenta można znaleźć na stronie: */var/opt/microsoft/omsagent/\<workspace id>/log/*

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
W tym przewodniku Szybki start aprowizacji agenta na komputerze z systemem Linux. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
