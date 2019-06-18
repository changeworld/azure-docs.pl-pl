---
title: Reguły zapory na poziomie serwera
description: Reguły zapory na poziomie serwera
keywords: połączenie z SQL, ciąg połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66164298"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Na liście po lewej stronie wybierz **wszystkich usług**.

3. Przewiń i wybierz **serwerów SQL**.

    ![Znajdowanie serwera usługi Azure SQL Database w witrynie portal][b21-FindServerInPortal]
5. W polu tekstowym filtru Rozpocznij wpisywanie nazwy serwera. Wyświetlany jest wiersz usługi.

6. Wybierz wiersz dla Twojego serwera. Zostanie wyświetlony blok dla Twojego serwera.

7. W bloku serwera wybierz **ustawienia**.

8. Wybierz **zapory**.

    ![Wybierz pozycję Ustawienia > zapory][b31-SettingsFirewallNavig]
9. Wybierz **Dodaj klienta IP**. W pierwszym polu tekstowym, wpisz nazwę nowej reguły.

10. Wpisz małe i duże wartości adresów IP dla zakresu, który chcesz włączyć.

    * Może być przydatna mają niską wartość kończyć **.0** i o wysokiej wartości, które kończy się **.255**.

11. Wybierz pozycję **Zapisz**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
