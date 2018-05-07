---
title: Przy użyciu baz danych dostarczonych przez RP karty SQL Azure stosu | Dokumentacja firmy Microsoft
description: Jak utworzyć i zarządzać udostępniane przy użyciu dostawcy zasobów karty SQL bazy danych SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 2808847642639069e60102b195ac97957c8593f0
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-sql-databases"></a>Tworzenie bazy danych SQL
Samoobsługowe baz danych są realizowane za pośrednictwem portalu użytkowników. Użytkownik musi subskrypcji, która ma oferty zawiera usługę bazy danych.

1. Zaloguj się do [stosu Azure](azure-stack-poc.md) portalu użytkowników (Administratorzy usług mogą również korzystać z portalu administratora).

2. Kliknij przycisk **+ nowy** &gt; **dane i Magazyn "** &gt; **bazy danych programu SQL Server** &gt; **dodać**.

3. Wypełnij formularz ze szczegółami bazy danych, w tym **Nazwa bazy danych**, **maksymalny rozmiar**i zmień innych parametrów w razie potrzeby. Zostanie wyświetlona prośba o pobranie wersji bazy danych. Po dodaniu serwerami hostingu, są przydzielone jednostki SKU. Bazy danych są tworzone w tej puli serwerów, które tworzą jednostki SKU hosta.

  ![Nowa baza danych](./media/azure-stack-sql-rp-deploy/newsqldb.png)

  >[!NOTE]
  > Rozmiar bazy danych musi być co najmniej 64 MB. Można zwiększyć, przy użyciu ustawień.

4. Wypełnij ustawienia logowania: **nazwy logowania bazy danych**, i **hasło**. Te ustawienia są utworzonego dla dostępu do tej bazy danych tylko poświadczenia uwierzytelniania SQL. Nazwa użytkownika logowania musi być globalnie unikatowe. Utwórz nowe ustawienie logowania albo wybierz istniejący. Można ponownie użyć ustawień logowania dla innych baz danych przy użyciu tej samej jednostki SKU.

    ![Utwórz nowe nazwy logowania bazy danych](./media/azure-stack-sql-rp-deploy/create-new-login.png)


5. Prześlij formularz i poczekaj, aż do ukończenia wdrożenia.

    W bloku wynikowy Zwróć uwagę, pole "Parametry połączenia". W stosie Azure, można użyć tego ciągu w dowolnej aplikacji, która wymaga dostępu do serwera SQL (na przykład aplikacja sieci web).

    ![Parametry połączenia](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="delete-sql-alwayson-databases"></a>Usuń bazy danych funkcji SQL AlwaysOn
Po usunięciu bazy danych funkcji SQL AlwaysOn od dostawcy zasobów go pomyślnie jest usuwany z podstawowym i grupy dostępności funkcji AlwaysOn, ale przez projekt, SQL AG umieszcza bazy danych w stanie w każdej replice odzyskiwania i nie porzucenia bazy danych, chyba że wyzwolone. Jeśli bazy danych nie został usunięty, replikach pomocniczych przechodzi do stanu nie można zsynchronizować. Ponowne dodanie nowej bazy danych do grupy dostępności o tej samej za pośrednictwem RP nadal działa.

## <a name="verify-sql-alwayson-databases"></a>Sprawdź baz danych funkcji SQL AlwaysOn
Zawsze włączone bazy danych powinny być widoczne zsynchronizowane i są dostępne na wszystkich wystąpień i w grupie dostępności. Po przejściu w tryb failover powinni bezproblemowo połączyć bazy danych. SQL Server Management Studio służy do sprawdzenia, czy trwa synchronizacja bazy danych:

![Sprawdź (AlwaysOn)](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)


## <a name="next-steps"></a>Kolejne kroki

[Obsługa dostawcy zasobów programu SQL Server](azure-stack-sql-resource-provider-maintain.md)
