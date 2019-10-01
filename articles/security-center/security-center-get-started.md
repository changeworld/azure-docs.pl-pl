---
title: Azure Security Center przewodniku szybki start — dołączanie subskrypcji platformy Azure do Security Center Standard | Microsoft Docs
description: W tym przewodniku szybki start przedstawiono, jak przeprowadzić uaktualnienie do warstwy cenowej standardowa Security Center, aby zwiększyć bezpieczeństwo.
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
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 689949b2b6fea1dcd06741fd3fc19c9371a96784
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676390"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Szybki Start: dołączanie subskrypcji platformy Azure do usługi Security Center Standard
Azure Security Center zapewnia ujednolicone Zarządzanie zabezpieczeniami i ochronę przed zagrożeniami w ramach obciążeń chmury hybrydowej. Chociaż warstwa Bezpłatna oferuje ograniczone zabezpieczenia tylko dla zasobów platformy Azure, warstwa standardowa rozszerza te możliwości do lokalnych i innych chmur. Security Center Standard ułatwia znajdowanie i rozwiązywanie luk w zabezpieczeniach, stosowanie kontroli dostępu i aplikacji w celu blokowania złośliwych działań, wykrywanie zagrożeń przy użyciu analiz i analiz oraz szybkie reagowanie na ataki. Możesz bezpłatnie wypróbować Security Center Standard. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/).

Ten artykuł zawiera uaktualnienie do warstwy Standardowa w celu zwiększenia bezpieczeństwa i zainstalowania Microsoft Monitoring Agent na maszynach wirtualnych w celu monitorowania luk w zabezpieczeniach i zagrożeń.

## <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć pracę z usługą Security Center, musisz mieć subskrypcję, aby Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się w celu uzyskania [bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).

Aby uaktualnić subskrypcję do warstwy Standardowa, musisz mieć przypisaną rolę właściciela subskrypcji, współautora subskrypcji lub administratora zabezpieczeń.

## <a name="enable-your-azure-subscription"></a>Włącz subskrypcję platformy Azure

1. Zaloguj się do [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. W menu **Microsoft Azure** wybierz pozycję **Security Center**. **Security Center — przegląd** zostanie otwarty.

   ![Przegląd Security Center][2]

**Security Center — Omówienie** zapewnia ujednolicony wgląd w stan zabezpieczeń obciążeń chmury hybrydowej, co pozwala odkrywać i oceniać zabezpieczenia obciążeń oraz identyfikować i ograniczać ryzyko. Security Center automatycznie włącza wszystkie subskrypcje platformy Azure, które nie zostały wcześniej dołączone przez Ciebie lub innego użytkownika subskrypcji do warstwy Bezpłatna.

Aby wyświetlić listę subskrypcji i przefiltrować ją, kliknij element menu **subskrypcje** . Security Center rozpocznie ocenianie zabezpieczeń tych subskrypcji w celu zidentyfikowania luk w zabezpieczeniach. Aby dostosować typy ocen, można zmodyfikować zasady zabezpieczeń. Zasady zabezpieczeń definiują żądaną konfigurację obciążeń i zapewniają zgodność z wymaganiami firmy lub przepisami bezpieczeństwa.

W ciągu kilku minut od uruchomienia Security Center po raz pierwszy zobaczysz:

- **Zalecenia** dotyczące sposobów zwiększania bezpieczeństwa subskrypcji platformy Azure. Kliknięcie kafelka **zalecenia** spowoduje uruchomienie listy z priorytetami.
- Spis **obliczeń & aplikacji**, **sieci**, **zabezpieczeń danych**i **tożsamości & dostępu do** zasobów, które są teraz oceniane przez Security Center oraz stan zabezpieczeń każdego z nich.

Aby w pełni wykorzystać Security Center, należy wykonać poniższe kroki, aby przeprowadzić uaktualnienie do warstwy Standardowa i zainstalować Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Uaktualnij do warstwy Standardowa
Security Center na potrzeby przewodników Szybki Start i samouczków, które należy uaktualnić do warstwy Standardowa. Istnieje bezpłatna wersja próbna standardu Security Center. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). 

1. W menu głównym Security Center wybierz opcję **wprowadzenie**.
 
   ![Wprowadzenie][4]

2. W obszarze **uaktualnij**Security Center wyświetla listę subskrypcji i obszarów roboczych kwalifikujących się do dołączenia. 
   - Możesz kliknąć opcję Rozwiń, aby wyświetlić listę wszystkich subskrypcji i obszarów roboczych ze stanem uprawnień **wersji próbnej** .
   -    Możesz uaktualnić subskrypcje i obszary robocze, które nie kwalifikują się do korzystania z wersji próbnej.
   -    Możesz wybrać kwalifikujące się obszary robocze i subskrypcje, aby rozpocząć okres próbny.
3. Kliknij pozycję **Rozpocznij okres próbny** , aby rozpocząć okres próbny dla wybranych subskrypcji.


  ![Alerty zabezpieczeń][9]

## <a name="automate-data-collection"></a>Automatyzacja zbierania danych
Security Center zbiera dane z maszyn wirtualnych platformy Azure i komputerów spoza platformy Azure w celu monitorowania luk w zabezpieczeniach i zagrożeń. Dane są zbierane przy użyciu Microsoft Monitoring Agent, który odczytuje różne konfiguracje związane z zabezpieczeniami i dzienniki zdarzeń z komputera i kopiuje dane do obszaru roboczego w celu przeprowadzenia analizy. Domyślnie Security Center utworzy nowy obszar roboczy.

Po włączeniu automatycznej aprowizacji program Security Center instaluje Microsoft Monitoring Agent na wszystkich obsługiwanych maszynach wirtualnych platformy Azure i utworzonych nowych. Automatyczne Inicjowanie obsługi jest zdecydowanie zalecane.

Aby włączyć automatyczną obsługę administracyjną Microsoft Monitoring Agent:

1. W menu głównym Security Center wybierz pozycję **cennik & Ustawienia**.
2. W wierszu subskrypcji kliknij subskrypcję, dla której chcesz zmienić ustawienia.
3. Na karcie **zbieranie danych** ustaw opcję **samoobsługowego udostępniania** na wartość **włączone**.
4. Wybierz pozycję **Zapisz**.
---
  ![Włącz automatyczną obsługę administracyjną][6]

Dzięki temu nowemu wglądowi w maszyny wirtualne platformy Azure Security Center mogą zapewnić dodatkowe zalecenia związane ze stanem aktualizacji systemu, konfiguracjami zabezpieczeń systemów operacyjnych, programem Endpoint Protection oraz generowaniem dodatkowych alertów zabezpieczeń.

  ![Mając][8]

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Inne Przewodniki Szybki Start i samouczki w tej kolekcji zostały skompilowane w tym przewodniku Szybki Start. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Kontynuuj uruchamianie warstwy Standardowa i Włącz automatyczną obsługę administracyjną. Jeśli nie planujesz kontynuować lub chcesz wrócić do warstwy Bezpłatna:

1. Wróć do menu głównego Security Center i wybierz pozycję **cennik & Ustawienia**.
2. Kliknij subskrypcję, którą chcesz zmienić w ramach warstwy Bezpłatna.
3. Wybierz pozycję **warstwa cenowa** i wybierz opcję **bezpłatna** , aby zmienić subskrypcję z warstwy Standardowa na warstwa Bezpłatna.
5. Wybierz pozycję **Zapisz**.

Jeśli chcesz wyłączyć automatyczne Inicjowanie obsługi:

1. Wróć do menu głównego Security Center i wybierz pozycję **cennik & Ustawienia**.
2. Wyczyść subskrypcję, dla której chcesz wyłączyć automatyczne Inicjowanie obsługi administracyjnej.
3. Na karcie **zbieranie danych** Ustaw **funkcję autoaprowizacji** na **off**.
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia Microsoft Monitoring Agent z maszyn wirtualnych platformy Azure, w przypadku których Agent został zainicjowany. Wyłączenie automatycznej aprowizacji ogranicza monitorowanie zabezpieczeń zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start uaktualniono do warstwy Standardowa i zainicjowano Microsoft Monitoring Agent w celu zapewnienia ujednoliconego zarządzania zabezpieczeniami i ochrony przed zagrożeniami w ramach obciążeń chmury hybrydowej. Aby dowiedzieć się więcej na temat sposobu korzystania z Security Center, przejdź do przewodnika Szybki Start dla komputerów z systemem Windows, które są lokalnie i w innych chmurach.

> [!div class="nextstepaction"]
> [Szybki Start: dołączanie komputerów z systemem Windows do Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
