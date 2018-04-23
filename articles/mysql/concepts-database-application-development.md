---
title: Omówienie tworzenia aplikacji bazy danych dla bazy danych Azure dla programu MySQL
description: Wprowadza zagadnienia dotyczące projektowania, które dewelopera należy stosować podczas pisania kodu aplikacji do łączenia z bazą danych Azure dla programu MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 1a3f517221c7e22d87dec5d0fc6f11c1bed16505
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Omówienie tworzenia aplikacji dla bazy danych Azure dla programu MySQL 
W tym artykule omówiono zagadnienia dotyczące projektowania, które dewelopera należy stosować podczas pisania kodu aplikacji do łączenia z bazą danych Azure dla programu MySQL. 

> [!TIP]
> Samouczek pokazuje, jak utworzyć serwer, utworzenie zapory na serwerze, wyświetlić właściwości serwera, Utwórz bazę danych i połączyć i zapytania przy użyciu narzędzia workbench i mysql.exe, zobacz [projektowania pierwszej bazy danych Azure, aby baza danych MySQL](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Można znaleźć łącza do przykładów kodu w: [bibliotek łączności używane do łączenia z bazą danych Azure dla programu MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Narzędzia
Wersja ciągu identyfikacyjnego MySQL, zgodne z MySQL popularnych narzędzi do zarządzania takich jak narzędzia Workbench lub MySQL, takich jak mysql.exe, korzysta z bazy danych platformy Azure dla programu MySQL [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)i inne. Do interakcji z usługą baza danych, można użyć portalu Azure, interfejsu wiersza polecenia Azure i interfejsów API REST.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Bazy danych platformy Azure dla programu MySQL zarządza zasoby dostępne na serwerze przy użyciu dwóch różnych mechanizmów: 
- Zarządzanie zasobami.
- Wymuszanie ograniczeń.

## <a name="security"></a>Bezpieczeństwo
Bazy danych platformy Azure dla programu MySQL zawiera zasoby dotyczące dostępu ograniczające, ochrona danych Konfigurowanie użytkowników i ról i monitorowania działań na bazę danych MySQL.

## <a name="authentication"></a>Authentication
Bazy danych platformy Azure dla programu MySQL obsługuje uwierzytelnianie serwera, użytkowników i nazwy logowania.

## <a name="resiliency"></a>Odporność
Po wystąpieniu błędu przejściowego podczas nawiązywania połączenia z bazą danych MySQL, ponów wywołanie kodu. Zalecamy logiki ponawiania próby używania wycofania logiki tak, że nie przeciąży bazy danych SQL z wielu klientów jednocześnie ponowną próbą.

- Przykłady kodu: Logika ponawiania próby przykłady kodu, które ilustrują temacie Przykłady w języku wybranym w: [bibliotek łączności używane do łączenia z bazą danych Azure dla programu MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Zarządzanie połączeniami
Połączenia bazy danych są ograniczone zasobów, dlatego zalecamy użycie za pośrednictwem połączenia podczas uzyskiwania dostępu do bazy danych MySQL osiągnąć większą wydajność.
- Dostęp do bazy danych przy użyciu puli połączeń lub połączeń trwałych.
- Dostęp do bazy danych przy użyciu krótkich połączenia życia. 
- Logika ponawiania użycia w aplikacji w punkcie próba połączenia catch błędy wynikające z równoczesnych połączeń osiągnięto maksymalną dozwoloną. W Logika ponawiania ustawić krótkie opóźnienie i poczekaj losowy czas przed prób dodatkowego połączenia.