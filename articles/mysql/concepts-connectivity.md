---
title: Obsługa błędów przejściowych łączności dla usługi Azure Database for MySQL | Dokumentacja firmy Microsoft
description: Informacje o sposobie obsługi błędów przejściowych łączności dla usługi Azure Database for MySQL.
keywords: połączenia z serwerem MySQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 8942223ce233d424e2368e90d2fbac92b1a443f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525464"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mysql"></a>Obsługa błędów przejściowych łączności dla usługi Azure Database for MySQL

W tym artykule opisano sposób obsługi błędów przejściowych nawiązaniem połączenia z usługą Azure Database for MySQL.

## <a name="transient-errors"></a>Błędy przejściowe

Błąd przejściowy, znany także jako błędów przejściowych, jest błąd, który zostanie rozwiązany automatycznie. Najczęściej te błędy manifestu, ponieważ połączenie z serwerem bazy danych Trwa porzucanie. Ponadto nie można otworzyć nowego połączenia z serwerem. Błędy przejściowe może wystąpić na przykład po awarii sprzętu lub sieci się dzieje. Inną przyczyną może być nowa wersja usługi PaaS, która jest wdrażana. Większość z tych zdarzeń są automatycznie skorygowany przez system w mniej niż 60 sekund. Najlepszym rozwiązaniem dla projektowania i tworzenia aplikacji w chmurze jest oczekiwany błędów przejściowych. Załóżmy, że ich może nastąpić w dowolnej części w dowolnym momencie i mieć odpowiednią logikę w celu obsługi tych sytuacji.

## <a name="handling-transient-errors"></a>Obsługa błędów przejściowych

Błędy przejściowe z niepoprawnymi przy użyciu logikę ponawiania próby. Sytuacje, które należy wziąć pod uwagę:

* Występuje błąd podczas próby otwarcia połączenia
* Połączenie zostało porzucone po stronie serwera. Podczas próby wydać polecenie nie można wykonać
* Aktywnego połączenia, który aktualnie wykonuje polecenie zostało porzucone.

W przypadku pierwszego i drugiego są stosunkowo proste do obsługi. Spróbuj otworzyć ponownie nawiązać połączenie. Gdy powiedzie, błąd przejściowy ma zostały skorygowane przez system. Można ponownie użyć usługi Azure Database for MySQL. Firma Microsoft zaleca posiadanie czeka przed ponowieniem próby połączenia. Możesz się wycofać w przypadku awarii początkowej ponownych prób. Dzięki temu system można użyć wszystkich zasobów dostępnych do pokonania sytuacji błędu. Dobre wzorzec do wykonania jest:

* Poczekaj 5 sekund przed swoje pierwsze ponowienie.
* Dla każdego następujących ponawiania próby, zwiększyć czas oczekiwania wykładniczo, maksymalnie 60 sekund.
* Ustaw maksymalną liczbę ponownych prób w tym momencie aplikacji uwzględnia operacja nie powiodła się.

Połączenie z aktywnej transakcji nie powiedzie się, jest trudniejsze do odzyskiwania poprawnie. Są dwa przypadki: Jeśli transakcja została tylko do odczytu z natury, jest bezpieczne, aby ponownie otworzyć połączenie i spróbuj ponownie wykonać transakcję. Jeśli jednak jeśli transakcja została również zapisuje w bazie danych, należy określić, jeśli transakcja została wycofana, czy udało mu się przed wystąpił błąd przejściowy. W takiej sytuacji może po prostu nie otrzymano potwierdzenia zatwierdzenie z serwera bazy danych.

Jest jednym ze sposobów to zrobić, można wygenerować unikatowy identyfikator klienta, który jest używany dla wszystkich ponownych prób. Przekażesz ten unikatowy identyfikator w ramach transakcji do serwera i zapisz go w kolumnie z unikatowego ograniczenia. Dzięki temu można bezpiecznie spróbuj ponownie wykonać transakcję. Zakończy się pomyślnie, jeśli poprzednia transakcja została wycofana i unikatowy identyfikator klienta generowany jeszcze nie istnieje w systemie. Zakończy się niepowodzeniem, wskazująca zduplikowane naruszenie klucza, czy unikatowy identyfikator wcześniej została zapisana, ponieważ poprzednia transakcja została ukończona pomyślnie.

Gdy program komunikuje się z usługą Azure Database for MySQL za pomocą oprogramowania pośredniczącego innych firm, należy poprosić dostawcę, czy oprogramowanie pośredniczące zawiera logikę ponowień w przypadku błędów przejściowych.

Upewnij się, że można przetestować Logika ponawiania próby. Na przykład spróbuj wykonywanie kodu podczas skalowania w górę lub w dół zasobów obliczeniowych, bazy danych Azure Database dla serwera MySQL. Aplikacja powinna obsługiwać krótki Przestój, które zostanie osiągnięty podczas tej operacji bez problemów.

## <a name="next-steps"></a>Kolejne kroki

* [Rozwiązywanie problemów z połączeniem z usługą Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
