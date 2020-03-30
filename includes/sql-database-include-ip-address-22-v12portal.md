---
title: Reguły zapory na poziomie serwera
description: Reguły zapory na poziomie serwera
keywords: połączenie sql,parametry połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183310"
---
1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

2. Na liście po lewej stronie wybierz pozycję **Wszystkie usługi**.

3. Przewiń i wybierz **serwery SQL**.

    ![Znajdowanie serwera bazy danych SQL platformy Azure w portalu][b21-FindServerInPortal]
5. W polu tekstowym filtru zacznij wpisywać nazwę serwera. Zostanie wyświetlony wiersz.

6. Wybierz wiersz dla serwera. Zostanie wyświetlony blok dla serwera.

7. W bloku serwera wybierz pozycję **Ustawienia**.

8. Wybierz **Zapora .**

    ![Wybieranie ustawień > Zapory][b31-SettingsFirewallNavig]
9. Wybierz **pozycję Dodaj adres IP klienta**. Wpisz nazwę nowej reguły w pierwszym polu tekstowym.

10. Wpisz niskie i wysokie wartości adresów IP dla zakresu, który chcesz włączyć.

    * Może być przydatny, aby mieć niski koniec wartości z **.0** i koniec wysokiej wartości z **.255**.

11. Wybierz **pozycję Zapisz**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
