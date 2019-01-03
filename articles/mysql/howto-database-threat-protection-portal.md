---
title: Zaawansowana ochrona przed zagrożeniami — Azure Database for MySQL | Dokumentacja firmy Microsoft
description: Ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazują możliwe zagrożenia bezpieczeństwa w bazie danych.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 09/20/2018
ms.openlocfilehash: 12497f6a5173e2e4ea9c919fb6500c34f85749fa
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53534757"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Zaawansowana ochrona przed zagrożeniami dla usługi Azure Database for MySQL

Usługa Advanced Threat Protection dla usługi Azure Database for MySQL wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub wykorzystania ich.

Zaawansowana ochrona przed zagrożeniami jest częścią oferty zaawansowane zabezpieczenia danych, która jest ujednoliconego pakietu dla zaawansowane funkcje zabezpieczeń. Zaawansowana ochrona przed zagrożeniami mogą być dostępne i zarządzane za pośrednictwem [witryny Azure portal](https://portal.azure.com) i jest obecnie w wersji zapoznawczej.

> [!NOTE]
> Funkcja zaawansowanej ochrony przed zagrożeniami jest **nie** dostępne w następujących platformy Azure dla instytucji rządowych i regiony należących do suwerennej chmury: Wschodnie DoD programu Microsoft Gov Teksas stany USA, administracja USA — Arizona Iowa Gov, US, — Wirginia, US Gov, US dod — środkowe, Niemcy środkowe, Niemcy Północne, Chiny wschodnie, Chiny wschodnie stany USA 2. Odwiedź stronę [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/) dostępności głównej produktu.
>

## <a name="set-up-threat-detection"></a>Konfigurowanie wykrywania zagrożeń
1. Uruchamianie witryny Azure portal pod [ https://portal.azure.com ](https://portal.azure.com).
2. Przejdź do strony konfiguracji usługi Azure Database for MySQL server, który chcesz chronić. W ustawieniach zabezpieczeń wybierz **zaawansowanej ochrony przed zagrożeniami (wersja zapoznawcza)**.
3. Na **zaawansowanej ochrony przed zagrożeniami (wersja zapoznawcza)** strony konfiguracji:

   - Włącz zaawansowaną ochronę przed zagrożeniami na serwerze.
   - W **Zaawansowane ustawienia ochrony przed zagrożeniami**w **Wyślij alerty do** tekstu Podaj listę adresów e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Konfigurowanie wykrywania zagrożeń](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Zapoznaj się z nietypowe działania bazy danych

Otrzymasz wiadomość e-mail z powiadomieniem po wykryciu nietypowych działań bazy danych. Wiadomość e-mail zawiera informacje o podejrzanych zdarzeniach dotyczących zabezpieczeń tym o naturze nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i rekomendowanych działań umożliwiających sprawdzenie i ograniczenie potencjalnych zagrożeń w bazie danych.
 
1. Kliknij przycisk **przeglądania ostatnich alertów** linku w wiadomości e-mail do uruchomienia witryny Azure portal i wyświetlić na stronie alertów usługi Azure Security Center, które zawiera omówienie aktywne zagrożenia wykryte w bazie danych SQL.
    
    ![Raport nietypowych działań](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Wyświetl aktywne zagrożenia:

    ![Aktywne zagrożenia](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknij określony alert, aby uzyskać dodatkowe informacje i akcje dla tego typu zagrożenia badanie i korygowanie przez przyszłymi zagrożeniami.
    
    ![Określony alert](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Zapoznaj się z alerty wykrywania zagrożeń

Wykrywanie zagrożeń SQL Database integruje jego alerty z usługą [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/). Żywe Kafelki wykrywania zagrożeń SQL na w ramach bazy danych i bloków SQL zaawansowanej ochrony przed zagrożeniami w witrynie Azure portal umożliwia śledzenie stanu aktywne zagrożenia.

Kliknij przycisk **alert wykrycia zagrożeń** można uruchomić usługi Azure Security Center alerty strony i zapoznaj się z omówieniem aktywne zagrożenia SQL w bazie danych wykryto.

   ![Alert wykrycia zagrożeń](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Aby uzyskać więcej informacji na temat cen, zobacz [— Azure Database for MySQL ceny strony](https://azure.microsoft.com/pricing/details/mysql/)  
