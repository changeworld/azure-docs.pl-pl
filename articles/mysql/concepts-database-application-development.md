---
title: Omówienie tworzenia aplikacji bazy danych dla usługi Azure Database for MySQL
description: Wprowadza zagadnienia dotyczące projektowania, które Deweloper należy wykonać podczas pisania kodu aplikacji do połączenia z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 946f7011c51b7c6844e023d03e01e4c2043d2578
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615655"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Omówienie tworzenia aplikacji dla usługi Azure Database for MySQL 
W tym artykule omówiono zagadnienia dotyczące projektowania, które Deweloper należy wykonać podczas pisania kodu aplikacji do połączenia z usługą Azure Database for MySQL. 

> [!TIP]
> Aby uzyskać samouczek omawiający Tworzenie serwera, tworzenie zapory na serwerze, wyświetlanie właściwości serwera, Utwórz bazę danych i połączenie i wykonywać zapytania za pomocą aplikacji workbench i mysql.exe, zobacz [projektowanie pierwszej bazy danych Azure for MySQL database](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Linki do przykładów kodu można znaleźć tutaj: [Biblioteki łączności używane do połączenia z usługą Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Narzędzia
Usługa Azure Database for MySQL korzysta z wersji community MySQL, zgodnej z programem MySQL popularnych narzędzi do zarządzania takich jak narzędzia Workbench i MySQL, takie jak mysql.exe, [narzędzia phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)i innym osobom. Można również użyć witryny Azure portal, interfejsu wiersza polecenia platformy Azure i interfejsów API REST do interakcji z usługą bazy danych.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Usługa Azure Database for MySQL — zarządza zasobami dostępnymi do serwera przy użyciu dwóch różnych mechanizmów: 
- Nadzoru nad zasobami.
- Wymuszanie limitów.

## <a name="security"></a>Bezpieczeństwo
Usługa Azure Database for MySQL zapewnia zasoby ograniczanie dostępu, ochronę danych, ról i użytkowników konfigurowania i monitorowania działań w bazie danych MySQL.

## <a name="authentication"></a>Authentication
Usługa Azure Database for MySQL obsługuje uwierzytelnianie serwera użytkownicy i logowania.

## <a name="resiliency"></a>Odporność
Gdy wystąpi błąd przejściowy podczas nawiązywania połączenia z bazą danych MySQL, kod powinien ponowić wywołanie. Zalecamy Logika ponawiania używania wycofania logiki tak, że nie przeciąży bazy danych SQL za pomocą wielu klientów ponawiania korzystała.

- Przykłady kodu: Aby uzyskać przykłady kodu ilustrujące logika ponowień, zobacz przykłady w języku wybranym na: [Biblioteki łączności używane do połączenia z usługą Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Zarządzanie połączeniami
Połączenia z bazą danych są ograniczone zasobu, tak więc zaleca się używania rozsądne połączeń podczas uzyskiwania dostępu do bazy danych MySQL osiągnąć większą wydajność.
- Dostęp do bazy danych przy użyciu puli połączeń lub połączeń trwałych.
- Dostęp do bazy danych przy użyciu żywotności krótki połączenia. 
- Logiki ponawiania próby użycia aplikacji w momencie próby połączenia do przechwytywania błędów wynikających z połączeń współbieżnych osiągnięto maksymalną dozwoloną. W Logika ponawiania Ustaw krótkie opóźnienie i zaczekaj, aż do czasu losowego przed próbami dodatkowego połączenia.