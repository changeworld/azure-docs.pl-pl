---
title: Dołączanie w wartownik platformy Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zbierać dane przez wartownika platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: c9f2f011acb9d815202aa6c6a38ed364ffb0f9cd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619654"
---
# <a name="on-board-azure-sentinel-preview"></a>Dołączyć wartownik platformy Azure (wersja zapoznawcza)

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku Szybki Start dowiesz się jak dołączyć przez wartownika platformy Azure. 

Aby dołączyć przez wartownika platformy Azure należy najpierw włączyć przez wartownika platformy Azure, a następnie nawiązać połączenie źródła danych. Azure wartownik dołączono wiele łączników dla rozwiązań firmy Microsoft, dostępne poza pole, a także integracja w czasie rzeczywistym, w tym rozwiązania ochrony przed zagrożeniami firmy Microsoft, Microsoft 365 źródeł, takich jak usługi Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto ma wbudowanych łączników szerszy ekosystemem zabezpieczenia dla rozwiązań firmy Microsoft. Można również użyć typowego formatu zdarzeń dziennika systemu lub interfejsu API REST, do łączenia źródeł danych z platformy Azure przez wartownika.  

Po nawiązaniu połączenia ze źródłami danych, wybierz z galerii profesjonalnego utworzone pulpity nawigacyjne, które Prezentuj wnioski na podstawie danych. Te pulpity nawigacyjne można łatwo dostosować do swoich potrzeb.


## <a name="global-prerequisites"></a>Globalne wymagania wstępne

- Aktywna subskrypcja platformy Azure, jeśli nie masz, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed przystąpieniem do wykonywania.

- Zaloguj się obszar roboczy usługi Analytics. Dowiedz się, jak [Utwórz obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Aby włączyć przez wartownika platformy Azure, należy uprawnienia współautora do subskrypcji, w której znajduje się obszar roboczy przez wartownika platformy Azure. 
- Korzystanie z platformy Azure przez wartownika, wymaga uprawnienia współautora lub czytelnika na grupy zasobów, do której należy obszar roboczy
- Mogą być potrzebne dodatkowe uprawnienia do łączenia z konkretnych źródeł danych
 
## Włącz wartownik platformy Azure <a name="enable"></a>

1. Przejdź do witryny Azure portal.
2. Upewnij się, czy wybrano subskrypcję, w którym przez wartownika Azure zostanie utworzona. 
3. Wyszukaj wartownik platformy Azure. 
   ![Wyszukiwanie](./media/quickstart-onboard/search-product.png)

1. Kliknij przycisk **+ Dodaj**.
1. Wybierz obszar roboczy, który chcesz użyć lub utworzyć nowy. Przez wartownika Azure można uruchamiać na więcej niż jednego obszaru roboczego, ale dane są izolowane do jednego obszaru roboczego.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Lokalizacja obszaru roboczego** jest ważne dowiedzieć się, że wszystkie dane przesyłania strumieniowego przez wartownika Azure znajduje się w lokalizacji geograficznej wybranego obszaru roboczego.  
   > - Domyślne obszary robocze utworzone przez usługę Azure Security Center nie będą widoczne na liście przez wartownika Azure nie można zainstalować na nich.
   > - Wartownik platformy Azure można uruchamiać na obszarach roboczych, które zostały wdrożone w jednym z następujących regionów:  Australia Południowo-Wschodnia, Kanada Środkowa, Indie środkowe, wschodnie stany USA, wschodnie stany USA 2 — EUAP (Wyspy Kanaryjskie), Japonia Wschodnia, Azja południowo-wschodnia, Zjednoczone Królestwo Południowa, Europa Zachodnia, zachodnie stany USA 2.

6. Kliknij przycisk **Dodaj Azure wartownik**.
  

## <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Wartownik Azure tworzy połączenie usług i aplikacji przez połączenie z usługą i funkcji przekazywania zdarzeń i dzienników do platformy Azure przez wartownika. Dla maszyn wirtualnych i komputerów można zainstalować agenta programu Azure przez wartownika służąca do zbierania dzienników i przekazuje je do platformy Azure przez wartownika. Dla zapór i serwerów proxy przez wartownika Azure korzysta z serwera Syslog systemu Linux. Agent jest zainstalowany na nim i z którego agent zbiera pliki dziennika i przekazuje je do platformy Azure przez wartownika. 
 
1. Kliknij przycisk **zbierania danych**.
2. Ma kafelka dla każdego źródła danych, w którym można się połączyć.<br>
Na przykład kliknij pozycję **usługi Azure Active Directory**. Jeśli można nawiązać połączenia z tego źródła danych przesyłania strumieniowego wszystkie dzienniki z usługi Azure AD na platformie Azure przez wartownika. Można wybrać, jakiego rodzaju dzienniki wan można pobrać - logowania dzienniki i/lub dzienniki inspekcji. <br>
Na dole przez wartownika Azure zapewnia zaleceń, które pulpity nawigacyjne należy zainstalować przez każdy łącznik dzięki czemu możesz można natychmiast uzyskać interesujących szczegółowych informacji danych. <br> Postępuj zgodnie z instrukcjami instalacji lub [można znaleźć w podręczniku właściwego połączenia](connect-data-sources.md) Aby uzyskać więcej informacji. Aby uzyskać informacji na temat łączników danych, zobacz [usług Connect Microsoft](connect-data-sources.md).

Po dane, które są połączone źródła i danych rozpoczyna się przesyłania strumieniowego na platformie Azure przez wartownika i jest gotowe do rozpoczęcia pracy z. Można przeglądać dzienniki w [wbudowane pulpity nawigacyjne](quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w usłudze Log Analytics [badanie danych](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono dotyczące nawiązywania połączenia platformy Azure przez wartownika źródeł danych. Aby dowiedzieć się więcej na temat platformy Azure przez wartownika, zobacz następujące artykuły:
- Dowiedz się, jak [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpoczynanie pracy [wykrywanie zagrożeń za pomocą platformy Azure przez wartownika](tutorial-detect-threats.md).
- Stream dane z [urządzenia typowego formatu błędu](connect-common-event-format.md) do platformy Azure przez wartownika.
