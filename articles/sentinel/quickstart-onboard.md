---
title: Dołączanie do wersji zapoznawczej platformy Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak zbierać dane na platformie Azure — wskaźnik.
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
ms.openlocfilehash: 0c37d6167012af46204cbca29397f5d226b9649b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611907"
---
# <a name="on-board-azure-sentinel-preview"></a>Wersja zapoznawcza platformy Azure — Podgląd

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym przewodniku szybki start dowiesz się, jak przejść do tablicy wskaźnikowej platformy Azure. 

Aby przenieść wskaźnik na platformę Azure, musisz najpierw włączyć wskaźnik na platformie Azure, a następnie połączyć źródła danych. Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań ochrony przed zagrożeniami firmy Microsoft, źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z platformą Azure.  

Po nawiązaniu połączenia ze źródłami danych wybierz z galerii ekspertów, którzy utworzyli szczegółowe informacje na podstawie danych. Te pulpity nawigacyjne mogą być łatwo dostosowane do Twoich potrzeb.


## <a name="global-prerequisites"></a>Ogólne wymagania wstępne

- Aktywną subskrypcję platformy Azure, jeśli jej nie masz, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Log Analytics obszar roboczy. Dowiedz się [, jak utworzyć obszar roboczy log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Aby włączyć wskaźnik na platformie Azure, musisz mieć uprawnienia współautora do subskrypcji, w której znajduje się obszar roboczy wskaźnik platformy Azure. 
- Aby korzystać z platformy Azure, musisz mieć uprawnienia współautora lub czytelnika w grupie zasobów, do której należy obszar roboczy
- Do nawiązania połączenia z określonymi źródłami danych mogą być konieczne dodatkowe uprawnienia.
 
## Włącz platformę Azure — wskaźnik<a name="enable"></a>

1. Przejdź do Azure Portal.
2. Upewnij się, że wybrano subskrypcję, w której jest tworzony wskaźnik "Azure,". 
3. Wyszukaj platformę Azure — wskaźnik. 
   ![wyszukiwania](./media/quickstart-onboard/search-product.png)

1. Kliknij pozycję **+ Dodaj**.
1. Wybierz obszar roboczy, którego chcesz użyć, lub Utwórz nowy. Możesz uruchomić wskaźnik platformy Azure na więcej niż jednym obszarze roboczym, ale dane są izolowane do jednego obszaru roboczego.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Domyślne obszary robocze utworzone przez Azure Security Center nie będą wyświetlane na liście; nie można zainstalować na nich wskaźnikiem na platformie Azure.
   > - Wskaźnik platformy Azure może działać w obszarach roboczych, które są wdrożone w jednym z następujących regionów:  Australia Południowo-Wschodnia, Kanada środkowa, Indie Środkowe, Wschodnie stany USA, Wschodnie stany USA 2 — EUAP (Wyspy Kanaryjskie), Japonia Wschodnia, Azja Południowo-Wschodnia, Południowe Zjednoczone Królestwo, Europa Zachodnia, zachodnie stany USA 2.

6. Kliknij pozycję **Dodaj wskaźnik platformy Azure**.
  

## <a name="connect-data-sources"></a>Łączenie ze źródłami danych

Usługa Azure wskaźnikowego tworzy połączenie z usługami i aplikacjami przez połączenie się z usługą i przekazanie zdarzeń i dzienników do funkcji wskaźnikowej platformy Azure. W przypadku maszyn i maszyn wirtualnych można zainstalować agenta wskaźnikowego platformy Azure, który zbiera dzienniki i przekazuje je do usługi Azure wskaźnikowej. W przypadku zapór i serwerów proxy usługa Azure użytks wykorzystuje serwer dziennika systemu Linux. Agent jest zainstalowany na nim i z którego Agent zbiera pliki dziennika i przekazuje je do usługi Azure wskaźnikowej. 
 
1. Kliknij pozycję **zbieranie danych**.
2. Istnieje kafelek dla każdego źródła danych, które można połączyć.<br>
Na przykład kliknij pozycję **Azure Active Directory**. Jeśli połączysz to źródło danych, przesyłasz strumieniowo wszystkie dzienniki z usługi Azure AD do usługi Azure wskaźnikowej. Możesz wybrać typ dzienników sieci WAN do pobrania i/lub dzienników inspekcji. <br>
Na dole wskaźnik na platformie Azure obejmuje zalecenia dotyczące tego, które pulpity nawigacyjne należy zainstalować dla każdego łącznika, dzięki czemu można natychmiast uzyskać interesujące informacje w danych. <br> Postępuj zgodnie z instrukcjami instalacji lub zapoznaj się z [odpowiednim przewodnikiem połączenia](connect-data-sources.md) , aby uzyskać więcej informacji. Aby uzyskać informacje na temat łączników danych, zobacz [łączenie usług firmy Microsoft](connect-data-sources.md).

Po nawiązaniu połączenia ze źródłami danych rozpocznie się przesyłanie strumieniowe danych do platformy Azure. Możesz wyświetlić dzienniki na [wbudowanych pulpitach nawigacyjnych](quickstart-get-visibility.md) i rozpocząć tworzenie zapytań w log Analytics, aby [zbadać dane](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Następne kroki
Ten dokument zawiera informacje o łączeniu źródeł danych z platformą Azure — wskaźnikiem. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats.md).
- Przesyłaj strumieniowo dane ze [wspólnych formatów błędów](connect-common-event-format.md) do platformy Azure.
