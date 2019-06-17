---
title: Rozwiązywać problemy z połączeniem do usługi Azure Database for MySQL | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z połączeniem do usługi Azure Database for MySQL.
keywords: połączenia z serwerem MySQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: faf378a81a6db24acc676bed82fe495cfb108612
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61421846"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Rozwiązywać problemy z połączeniem do usługi Azure Database for MySQL

Problemy z połączeniem, może być spowodowane przez różne rzeczy, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowymi informacjami logowania
* Osiągnięto maksymalny limit na niektórych — Azure Database for MySQL — zasoby
* Problemy związane z infrastrukturą usługi
* Konserwacji wykonywanej w usłudze
* Przydzielić pamięci obliczeniowej serwera zostanie zmieniony przez skalowanie liczby rdzeni, lub przenoszenia do warstwy innej usługi

Ogólnie rzecz biorąc problemy z połączeniem do usługi Azure Database for MySQL — mogą być klasyfikowane w następujący sposób:

* Błędów przejściowych (krótkotrwałe lub sporadycznych)
* Trwałe lub nieprzejściowych błędów (błędy, które regularnie powtarzać)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują, gdy jest wykonywana konserwacja, system napotka błąd za pomocą sprzętowego lub programowego lub zmienić warstwę rdzenie wirtualne lub usługi serwera. Usługa Azure Database for MySQL zapewnia wbudowaną wysoką dostępność i jest przeznaczony do automatycznie rozwiązać tego rodzaju problemy. Jednak aplikacja straci połączenie z serwerem przez krótki okres czasu, zwykle krócej niż 60 sekund co najwyżej. Niektóre zdarzenia od czasu do czasu może zająć dłuższy rozwiązać problem, takie jak kiedy dużej transakcji powoduje, że odzyskiwania długoterminowych.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki, aby rozwiązać problemy z łącznością przejściowe

1. Sprawdź [pulpitu nawigacyjnego usług systemu Azure firmy Microsoft](https://azure.microsoft.com/status) o znanych awariach, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje łączące się usługi w chmurze, takich jak — Azure Database for MySQL — należy spodziewać się błędów przejściowych i zaimplementować ponów logikę obsługującą te błędy zamiast udostępniając je jako błędy aplikacji dla użytkowników. Przegląd [obsługi błędów przejściowych łączności dla usługi Azure Database for MySQL](concepts-connectivity.md) najlepsze rozwiązania i wytyczne dotyczące projektowania dotyczące obsługi błędów przejściowych.
3. Jako serwer zbliża się limit zasobów, błędów, może wydawać się być przejściowy problem łączności. Zobacz [ograniczenia dotyczące usługi Azure Database for MySQL](concepts-limits.md).
4. Jeśli nadal występują problemy z łącznością lub jeśli czas trwania, dla którego aplikacja napotkała błąd przekracza 60 sekund, lub jeśli wiele wystąpień tego błędu jest widoczny w danym dniu pliku żądanie pomocy technicznej platformy Azure, wybierając **uzyskiwanie pomocy technicznej**na [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) lokacji.

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z błędami trwałego

Jeśli aplikacja nie trwałe połączenia z usługą Azure Database for MySQL, zwykle oznacza problem związany z jedną z następujących czynności:

* Konfiguracja zapory serwera: Upewnij się, że usługi Azure Database for MySQL zapory serwera jest skonfigurowany do połączeń z klienta, w tym serwery proxy i bram.
* Konfiguracja zapory klienta: Zapora na komputerze klienckim muszą zezwalać na połączenia z serwerem bazy danych. Adresy IP i porty serwera, na którym można połączyć się muszą być dozwolone, a także nazwy aplikacji, takich jak MySQL w niektóre zapory.
* Błąd użytkownika: Może być błędnie wpisano parametry połączenia, takie jak nazwa serwera w parametrach połączenia lub braku  *\@servername* sufiksu w nazwie użytkownika.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki, aby rozwiązać problemy z łącznością trwałego

1. Konfigurowanie [reguły zapory](howto-manage-firewall-using-portal.md) pozwalającego klientowi adres IP. Dla tymczasowych tylko do celów testowych Skonfiguruj regułę zapory przy użyciu 0.0.0.0 jako początkowego adresu IP i przy użyciu 255.255.255.255 jako końcowy adres IP. Spowoduje to otwarcie serwera dla wszystkich adresów IP. Jeśli to rozwiąże problem z łącznością, usunięcie tej reguły i Utwórz regułę zapory dla odpowiednio ograniczone adresu IP lub zakresu adresów.
2. Upewnij się, że port 3306 został otwarty dla połączeń wychodzących na wszystkie zapory między klientem a Internetem.
3. Sprawdź parametry połączenia i inne ustawienia połączenia. Przegląd [sposób łączenia aplikacji do usługi Azure Database for MySQL](howto-connection-string.md).
4. Sprawdzanie kondycji usług, na pulpicie nawigacyjnym. Jeśli występuje awaria regionalna, zobacz [omówienie ciągłości biznesowej z usługą Azure Database for MySQL](concepts-business-continuity.md) kroków odzyskiwania w nowym regionie.

## <a name="next-steps"></a>Kolejne kroki

* [Obsługa błędów przejściowych łączności dla usługi Azure Database for MySQL](concepts-connectivity.md)
