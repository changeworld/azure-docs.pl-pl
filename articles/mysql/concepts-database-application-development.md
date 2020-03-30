---
title: Tworzenie aplikacji — usługa Azure Database for MySQL
description: Przedstawia zagadnienia projektowe, które deweloper powinien obserwować podczas pisania kodu aplikacji, aby połączyć się z usługą Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 02ce6b00b6555f849d162b9f3b381c0ab358d712
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532845"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Omówienie tworzenia aplikacji dla usługi Azure Database for MySQL 
W tym artykule omówiono zagadnienia dotyczące projektu, które deweloper powinien wykonać podczas pisania kodu aplikacji, aby połączyć się z usługą Azure Database for MySQL. 

> [!TIP]
> Aby zapoznać się z samouczkiem przedstawiającym tworzenie serwera, tworzenie zapory opartej na serwerze, wyświetlanie właściwości serwera, tworzenie bazy danych oraz łączenie się i wykonywanie zapytań przy użyciu systemu roboczego i pliku mysql.exe, zobacz [Projektowanie pierwszej bazy danych usługi Azure Database dla mysql](tutorial-design-database-using-portal.md)

## <a name="language-and-platform"></a>Język i platforma
Dostępne są przykłady kodu dla różnych języków programowania i platform programistycznych. Łącza do przykładów kodu można znaleźć pod adresem: [Biblioteki łączności używane do łączenia się z usługą Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Narzędzia
Usługa Azure Database for MySQL używa wersji społeczności MySQL, zgodnej ze wspólnymi narzędziami do zarządzania MySQL, takimi jak Workbench lub MySQL, takimi jak mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql)i innymi. Można również użyć witryny Azure portal, interfejsu wiersza polecenia platformy Azure i interfejsów API REST do interakcji z usługą bazy danych.

## <a name="resource-limitations"></a>Ograniczenia zasobów
Usługa Azure Database for MySQL zarządza zasobami dostępnymi dla serwera przy użyciu dwóch różnych mechanizmów: 
- Zarządzanie zasobami.
- Egzekwowanie limitów.

## <a name="security"></a>Zabezpieczenia
Usługa Azure Database for MySQL udostępnia zasoby do ograniczania dostępu, ochrony danych, konfigurowania użytkowników i ról oraz monitorowania działań w bazie danych MySQL.

## <a name="authentication"></a>Uwierzytelnianie
Usługa Azure Database for MySQL obsługuje uwierzytelnianie serwera użytkowników i logowania.

## <a name="resiliency"></a>Odporność
Gdy wystąpi błąd przejściowy podczas łączenia się z bazą danych MySQL, kod należy ponowić próbę wywołania. Zaleca się, aby logika ponawiania użycia logiki, aby nie przeciąć bazy danych SQL z wielu klientów ponawianie próby jednocześnie.

- Przykłady kodu: przykłady kodu ilustrujące logikę ponawiania prób można znaleźć w przykładach wybranego języka: [Biblioteki łączności używane do łączenia się z usługą Azure Database for MySQL](concepts-connection-libraries.md)

## <a name="managing-connections"></a>Zarządzanie połączeniami
Połączenia z bazą danych są ograniczonym zasobem, dlatego zaleca się rozsądne korzystanie z połączeń podczas uzyskiwania dostępu do bazy danych MySQL w celu uzyskania lepszej wydajności.
- Dostęp do bazy danych przy użyciu buforowania połączeń lub połączeń trwałych.
- Dostęp do bazy danych przy użyciu krótki okres życia połączenia. 
- Użyj logiki ponawiania w aplikacji w punkcie próby połączenia, aby wyładowywać błędy wynikające z równoczesnych połączeń osiągnęły maksymalną dozwoloną wartość. W logice ponawiania ustaw krótkie opóźnienie, a następnie poczekaj na losowy czas, zanim spróbuje się dodatkowe połączenie.