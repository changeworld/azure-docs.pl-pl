---
title: Zarządzanie regułami zapory — hiperskala (Citus) — usługa Azure Database for PostgreSQL
description: Tworzenie reguł zapory dla usługi Azure Database for PostgreSQL — hiperskala (Citus) przy użyciu portalu Azure i zarządzanie nimi
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977543"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Zarządzanie regułami zapory dla usługi Azure Database for PostgreSQL — hiperskala (Citus)
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do węzła koordynatora hiperskali (Citus) z określonego adresu IP lub zakresu adresów IP.

## <a name="prerequisites"></a>Wymagania wstępne
Aby przejść przez ten przewodnik, potrzebujesz:
- Grupa serwerów [Tworzenie bazy danych usługi Azure Database dla grupy serwerów PostgreSQL — hiperskali (Citus).](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

> [!NOTE]
> Te ustawienia są również dostępne podczas tworzenia grupy serwerów Usługi Azure Database for PostgreSQL — Hyperscale (Citus). Na karcie **Sieć** kliknij pozycję **Publiczny punkt końcowy**.
> ![Azure portal — karta sieciowa](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. Na stronie Grupy serwerów PostgreSQL w obszarze Nagłówek Zabezpieczenia kliknij pozycję **Sieć,** aby otworzyć reguły zapory.

   ![Portal Azure — kliknij pozycję Sieć](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. Kliknij **pozycję Dodaj adres IP klienta**, albo na pasku narzędzi (opcja A poniżej) lub w linku (opcja B). W obu przypadkach automatycznie tworzy regułę zapory z publicznym adresem IP komputera, postrzegane przez system platformy Azure.

   ![Portal Azure — kliknij pozycję Dodaj adres IP klienta](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

Alternatywnie kliknięcie **+Dodaj 0.0.0.0 - 255.255.255.255(po** prawej stronie opcji B) pozwala nie tylko na adres IP, ale cały Internet na dostęp do portu węzła koordynatora 5432. W tej sytuacji klienci nadal muszą zalogować się przy użyciu poprawnej nazwy użytkownika i hasła, aby korzystać z klastra. Niemniej jednak zalecamy zezwolenie na dostęp na całym świecie tylko przez krótki okres czasu i tylko dla nieprodukcyjnych baz danych.

3. Przed zapisaniem konfiguracji należy zweryfikować swój adres IP. W niektórych sytuacjach adres IP obserwowany przez witrynę Azure portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może być konieczna zmiana początkowego adresu IP i końcowego adresu IP, aby reguła działała zgodnie z oczekiwaniami.
   Użyj wyszukiwarki lub innego narzędzia online, aby sprawdzić swój własny adres IP. Na przykład wyszukaj hasło "jaki jest mój adres IP".

   ![Wyszukiwanie Bing dla Co to jest mój adres IP](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. Dodaj dodatkowe zakresy adresów. W regułach zapory można określić pojedynczy adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do jednego adresu IP, wpisz ten sam adres w polu Początkowego adresu IP i Końcowego ADRESU IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do węzła koordynatora na porcie 5432.

5. Kliknij **przycisk Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończyła się pomyślnie.

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Istnieje prosty sposób udzielania dostępu do bazy danych w hiperskali do aplikacji hostowanych na platformie Azure (takich jak aplikacja usługi Azure Web Apps lub tych uruchomionych na maszynie Wirtualnej platformy Azure). Wystarczy ustawić opcję **Zezwalaj na usługi i zasoby platformy Azure, aby uzyskać dostęp do tej grupy serwerów** na **Tak** w portalu z okienka **Sieć** i naciśnij **przycisk Zapisz**.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu witryny Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk + **Dodaj adres IP klienta**. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby dodać kolejne adresy IP, wpisz nazwę reguły, początkowy adres IP i końcowy adres IP. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule i wprowadź zmiany. Kliknij przycisk **Zapisz**, aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij wielokropek [...] i kliknij przycisk **Usuń,** aby usunąć regułę. Kliknij przycisk **Zapisz**, aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [koncepcji reguł zapory](concepts-hyperscale-firewall-rules.md), w tym jak rozwiązywać problemy z połączeniem.
