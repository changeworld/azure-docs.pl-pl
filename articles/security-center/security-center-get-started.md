---
title: 'Szybki start: Azure Security Center — dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa | Microsoft Docs'
description: Ten przewodnik Szybki start przedstawia, jak zmienić warstwę cenową usługi Security Center na wyższą warstwę Standardowa w celu zapewnienia dodatkowych zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 4464796e655cefd28f858cb732ff3d5721a7aa47
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206810"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Szybki start: Dołączanie subskrypcji platformy Azure do usługi Security Center w warstwie Standardowa
Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Warstwa Bezpłatna zapewnia ograniczone bezpieczeństwo jedynie zasobów platformy Azure, natomiast warstwa Standardowa rozszerza te możliwości na środowisko lokalne i inne chmury. Usługa Security Center w warstwie Standardowa pomaga w wyszukiwaniu i naprawianiu luk w zabezpieczeniach, stosowaniu kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywaniu zagrożeń przy użyciu analizy oraz szybkim reagowaniu podczas ataku. Warstwę Standardowa usługi Security Center możesz wypróbować bezpłatnie. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

W tym artykule zmienisz warstwę usługi na wyższą warstwę Standardowa, aby zapewnić dodatkowe zabezpieczenia, i zainstalujesz program Microsoft Monitoring Agent na swoich maszynach wirtualnych w celu monitorowania luk w zabezpieczeniach i zagrożeń.

## <a name="prerequisites"></a>Wymagania wstępne
Do rozpoczęcia korzystania z usługi Security Center wymagana jest subskrypcja usługi Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Aby zmienić warstwę subskrypcji na wyższą warstwę Standardowa, musisz mieć przypisaną rolę właściciela subskrypcji, współautora subskrypcji lub administratora zabezpieczeń.

## <a name="enable-your-azure-subscription"></a>Włączanie subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu platformy **Microsoft Azure** wybierz pozycję **Security Center**. Zostanie otwarte okno **Security Center — Przegląd**.

   ![Security Center — Przegląd][2]

Okno **Security Center — Przegląd** zapewnia ujednolicony wgląd w stan zabezpieczeń obciążeń chmury hybrydowej, dzięki czemu możesz odnajdywać i oceniać zabezpieczenia obciążeń oraz identyfikować i ograniczać ryzyko. Usługa Security Center automatycznie włącza warstwę Bezpłatna dla wszystkich subskrypcji platformy Azure, które nie zostały wcześniej dołączone przez Ciebie lub innego użytkownika subskrypcji.

Klikając element menu **Subskrypcje**, możesz wyświetlić i filtrować listę subskrypcji. Usługa Security Center rozpocznie teraz ocenę zabezpieczeń tych subskrypcji, aby zidentyfikować luki w zabezpieczeniach. Aby dostosować typy ocen, możesz zmodyfikować zasady zabezpieczeń. Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy.

W ciągu kilku minut od pierwszego uruchomienia usługi Security Center mogą zostać wyświetlone następujące informacje:

- **Zalecenia** dotyczące sposobów poprawy zabezpieczeń subskrypcji platformy Azure. Kliknięcie kafelka **Zalecenia** spowoduje uruchomienie priorytetyzowanej listy.
- Spis zasobów **Obliczenia i aplikacje**, **Sieć**, **Bezpieczeństwo danych** oraz **Tożsamość i dostęp**, które są obecnie oceniane przez usługę Security Center wraz ze stanem zabezpieczeń każdego z nich.

Aby w pełni korzystać z usługi Security Center, należy wykonać poniższe kroki w celu zmiany warstwy na wyższą warstwę Standardowa i zainstalowania programu Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Zmiana warstwy na wyższą warstwę Standardowa
Na potrzeby przewodników Szybki start i samouczków usługi Security Center musisz zmienić warstwę na wyższą warstwę Standardowa. Usługa Security Center w warstwie Standardowa jest dostępna w bezpłatnej wersji próbnej. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). 

1. W menu głównym usługi Security Center wybierz pozycję **Wprowadzenie**.
 
   ![Rozpoczęcie pracy][4]

2. W obszarze **Uaktualnienie** usługa Security Center wyświetla listę subskrypcji i obszarów roboczych, które kwalifikują się do dołączenia. 
   - Możesz kliknąć listę rozwijaną **Zastosuj wersję próbną**, aby wyświetlić listę wszystkich subskrypcji i obszarów roboczych ze stanem kwalifikacji do korzystania z wersji próbnej.
   -    Możesz uaktualnić subskrypcje i obszary robocze, które nie kwalifikują się do korzystania z wersji próbnej.
   -    Możesz wybrać kwalifikujące się obszary robocze i subskrypcje i rozpocząć korzystanie z wersji próbnej.
3. Kliknij przycisk **Rozpocznij okres próbny**, aby rozpocząć okres próbny w ramach wybranych subskrypcji.


  ![Alerty zabezpieczeń][9]

## <a name="automate-data-collection"></a>Automatyzacja zbierania danych
Usługa Security Center zbiera dane z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure w celu monitorowania pod kątem luk w zabezpieczeniach i zagrożeń. Dane są zbierane za pomocą programu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z maszyn oraz kopiuje dane do Twojego obszaru roboczego na potrzeby analizy. Domyślnie usługa Security Center utworzy nowy obszar roboczy.

Jeśli jest włączona automatyczna aprowizacja, usługa Security Center instaluje program Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure oraz na wszystkich nowo tworzonych maszynach wirtualnych. Używanie automatycznej aprowizacji jest zdecydowanie zalecane.

Aby włączyć automatyczną aprowizację programu Microsoft Monitoring Agent:

1. W menu głównym usługi Security Center wybierz pozycję **Zasady zabezpieczeń**.
2. W wierszu subskrypcji wybierz pozycję **Edytuj ustawienia>** .
3. Na karcie **Zbieranie danych** ustaw opcję **Automatyczna aprowizacja** na wartość **Wł**.
4. Wybierz pozycję **Zapisz**.
---
  ![Włączanie automatycznej aprowizacji][6]

Dzięki temu nowemu wglądowi w maszyny wirtualne platformy Azure usługa Security Center może udostępniać dodatkowe zalecenia dotyczące stanu aktualizacji systemu, konfiguracji zabezpieczeń systemu operacyjnego i ochrony punktów końcowych, a także generować dodatkowe alerty zabezpieczeń.

  ![Zalecenia][8]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli zamierzasz kontynuować pracę z kolejnymi przewodnikami szybkiego startu i samouczkami, w dalszym ciągu korzystaj z warstwy standardowej i zachowaj włączoną automatyczną aprowizację. Jeśli nie zamierzasz kontynuować lub chcesz wrócić do warstwy bezpłatnej:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. W wierszu subskrypcji, którą chcesz cofnąć do bezpłatnej, wybierz opcję **Edytuj ustawienia>** .
3. Wybierz pozycje **Warstwa cenowa** i **Bezpłatna**, aby zmienić subskrypcję z warstwy standardowej na warstwę bezpłatną.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczną aprowizację:

1. Wróć do menu głównego usługi Security Center i wybierz pozycję **Zasady zabezpieczeń**.
2. W wierszu subskrypcji, dla której chcesz wyłączyć automatyczną aprowizację, wybierz pozycję **Edytuj ustawienia>** .
3. Na karcie **Zbieranie danych** ustaw opcję **Automatyczna aprowizacja** na wartość **Wył**.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia programu Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, na których aprowizowano agenta. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W ramach tego przewodnika Szybki start zmieniono warstwę usługi na wyższą warstwę Standardowa i aprowizowano program Microsoft Monitoring Agent, aby zapewnić ujednolicone zarządzanie zabezpieczeniami i ochronę przed zagrożeniami obciążeń chmury hybrydowej. Aby dowiedzieć się więcej na temat korzystania z usługi Security Center, przejdź do przewodnika Szybki start dotyczącego dołączania komputerów z systemem Windows znajdujących się w środowisku lokalnym i w innych chmurach.

> [!div class="nextstepaction"]
> [Szybki start: Dołączanie komputerów z systemem Windows do usługi Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
