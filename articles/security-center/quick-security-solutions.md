---
title: Przewodnik szybkiego startu usługi Azure Security Center — Łączenie rozwiązań zabezpieczeń | Microsoft Docs
description: Przewodnik szybkiego startu usługi Azure Security Center — Łączenie rozwiązań zabezpieczeń
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 3263bb3d-befc-428c-9f80-53de65761697
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: yurid
ms.openlocfilehash: afc72f5b18460578b0cfcd085fe601184a2f898b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561301"
---
# <a name="quickstart-connect-security-solutions-to-security-center"></a>Przewodnik szybkiego startu: Łączenie rozwiązań zabezpieczeń z usługą Security Center

Oprócz zbierania danych zabezpieczeń z komputerów, można integrować dane zabezpieczeń z różnych innych rozwiązań zabezpieczeń, między innymi tych, które obsługują format CEF (Common Event Format). CEF to standardowy format branżowy komunikatów dzienników systemu używany przez wielu dostawców zabezpieczeń w celu umożliwienia integracji zdarzeń między różnymi platformami.

W tym przewodniku szybkiego startu przedstawiono sposób wykonywania następujących czynności:
- Łączenie rozwiązania zabezpieczeń z usługą Security Center przy użyciu dzienników CEF
- Sprawdzanie poprawności połączenia z rozwiązaniem zabezpieczeń

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/free/).

Aby wypróbować funkcje omówione w tym przewodniku szybkiego startu, musisz korzystać ze standardowej warstwy cenowej usługi Security Center. Standardową warstwę cenową usługi Security Center możesz wypróbować bezpłatnie przez pierwsze 60 dni. Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](security-center-get-started.md) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

Potrzebujesz również [komputera z systemem Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux) i usługą Syslog, która jest już połączona z usługą Security Center.

## <a name="connect-solution-using-cef"></a>Łączenie rozwiązania przy użyciu formatu CEF

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu platformy **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

    ![Wybieranie usługi Security Center](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. W menu głównym usługi Security Center wybierz pozycję **Rozwiązania zabezpieczeń**.
4. Na stronie Rozwiązania zabezpieczeń w sekcji **Dodawanie źródeł danych (3)** kliknij pozycję **Dodaj** w obszarze **Common Event Format**.

    ![Dodawanie źródła danych](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Na stronie Dzienniki w formacie CEF rozwiń drugi etap **Konfigurowanie przekazywania usługi Syslog w celu wysyłania wymaganych dzienników do agenta na porcie UDP 25226**, a następnie na komputerze z systemem Linux wykonaj poniższe instrukcje:

    ![Konfigurowanie usługi Syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Rozwiń trzeci etap **Umieszczanie pliku konfiguracji agenta na komputerze agenta**, a następnie na komputerze z systemem Linux wykonaj poniższe instrukcje:

    ![Konfiguracja agenta](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Rozwiń czwarty etap **Ponowne uruchamianie demona usługi Syslog i agenta**, a następnie na komputerze z systemem Linux wykonaj poniższe instrukcje:

    ![Ponowne uruchamianie usługi Syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


## <a name="validate-the-connection"></a>Sprawdzanie poprawności połączenia

Zanim przejdziesz do poniższych czynności, musisz zaczekać, aż usługa Syslog rozpocznie raportowanie do usługi Security Center. Może to zająć trochę czasu (w zależności od wielkości środowiska).

1.  W lewym okienku pulpitu nawigacyjnego usługi Security Center kliknij pozycję **Wyszukaj**.
2.  Wybierz obszar roboczy, z którym jest połączona usługa Syslog (komputer z systemem Linux).
3.  Wpisz *CommonSecurityLog* i kliknij przycisk **Wyszukaj**.

Na poniższym przykładzie pokazano wynik tych kroków: ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli zamierzasz kontynuować pracę z kolejnymi przewodnikami szybkiego startu i samouczkami, w dalszym ciągu korzystaj z warstwy standardowej i zachowaj włączoną automatyczną aprowizację. Jeśli nie zamierzasz kontynuować lub chcesz wrócić do warstwy bezpłatnej:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję lub zasady, którym chcesz przywrócić warstwę bezpłatną. Zostanie otwarte okno **Zasady zabezpieczeń**.
3. W obszarze **SKŁADNIKI ZASAD** wybierz pozycję **Warstwa cenowa**.
4. Wybierz pozycję **Bezpłatna**, aby zmienić subskrypcję z warstwy standardowej na warstwę bezpłatną.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. Wybierz subskrypcję, dla której chcesz wyłączyć automatyczną aprowizację.
3. W sekcji **Zasady zabezpieczeń — zbieranie danych** wybierz pozycję **Wyłącz** w obszarze **Dołączanie**, aby wyłączyć automatyczną aprowizację.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, na których aprowizowano agenta. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybkiego startu przedstawiono sposób łączenia rozwiązania Linux Syslog z usługą Security Center przy użyciu formatu CEF. Łącząc dzienniki CEF z usługą Security Center, możesz korzystać z zalet wyszukiwania i niestandardowych reguł alertów oraz wzbogacić analizę zagrożeń dla każdego dziennika. Aby dowiedzieć się więcej na temat sposobu korzystania z usługi Security Center, przejdź do samouczka konfigurowania zasad zabezpieczeń i oceniania zabezpieczeń Twoich zasobów.

> [!div class="nextstepaction"]
> [Samouczek: Definiowanie i ocenianie zasad zabezpieczeń](./tutorial-security-policy.md)
