---
title: Azure VMware Solutions (Automatyczna synchronizacja) — monitorowanie działania chmury prywatnej
description: Zawiera opis informacji dostępnych w ramach działań w rozwiązaniu VMware platformy Azure przez środowisko automatycznej synchronizacji, w tym alerty, zdarzenia, zadania i inspekcje.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6fd1b92db62ab7cc9edd47c601910b8148bb95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019676"
---
# <a name="monitor-vmware-solutions-avs-activity"></a>Monitorowanie aktywności rozwiązań VMware (Automatyczna synchronizacja)

Dzienniki aktywności automatycznej synchronizacji zapewniają wgląd w operacje wykonywane w portalu automatycznej synchronizacji. Lista zawiera alerty, zdarzenia, zadania i inspekcje. Użyj dzienników aktywności, aby określić, kto i kiedy operacje zostały wykonane. Dzienniki aktywności nie obejmują żadnych operacji odczytu wykonywanych przez użytkownika.

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Dostęp do portalu automatycznej synchronizacji

Uzyskaj dostęp do [portalu automatycznej synchronizacji](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informacje o działaniu

Aby uzyskać dostęp do stron działań, wybierz pozycję **działanie** w menu po stronie.

![Przegląd strony działania](media/activity-page-overview.png)

Aby wyświetlić szczegółowe informacje o działaniach na stronie działania, wybierz działanie. Po prawej stronie zostanie otwarty panel szczegóły. Akcje w panelu są zależne od typu działania. Kliknij przycisk **X** , aby zamknąć panel.

Kliknij nagłówek kolumny, aby posortować ekran. Można filtrować kolumny pod kątem określonych wartości do wyświetlenia. Pobierz raport dotyczący aktywności, klikając ikonę **Pobierz jako plik CSV** .

## <a name="alerts"></a>Alerty

Alerty są powiadomieniami o wszelkich znaczących działaniach w środowisku automatycznej synchronizacji. Alerty obejmują zdarzenia, które mają wpływ na rozliczenia lub dostęp użytkownika.

Aby potwierdzić alerty i usunąć je z listy, wybierz co najmniej jedną z listy i kliknij przycisk **Potwierdź**.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Typ alertu | Kategoria alertu.|
| Czas | Czas wystąpienia alertu. |
| Ważność | Istotność alertu.|
| Nazwa zasobu | Nazwa przypisana do zasobu, na przykład nazwa prywatnej chmury automatycznej. |
| Typ zasobu | Kategoria zasobu: automatyczna synchronizacja chmur prywatnych, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Opis | Opis wyzwalania alertu. |
| Potwierdzone | Wskazanie, czy alert został potwierdzony. |

## <a name="events"></a>Wydarzenia

Zdarzenia pokazują aktywność użytkownika i systemu w portalu automatycznej synchronizacji. Na stronie zdarzenia jest wyświetlana aktywność skojarzona z określonym zasobem i ważność wpływu.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Czas | Data i godzina wystąpienia zdarzenia. |
| Typ zdarzenia | Kod liczbowy, który identyfikuje zdarzenie. |
| Ważność | Ważność zdarzenia.|
| Nazwa zasobu | Nazwa przypisana do zasobu, na przykład nazwa prywatnej chmury automatycznej. |
| Typ zasobu | Kategoria zasobu: automatyczna synchronizacja chmur prywatnych, stojak w chmurze. |
| Opis | Opis wyzwalania alertu. |

## <a name="tasks"></a>Zadania

Zadania to działania w chmurze dotyczące automatycznej synchronizacji, które powinny trwać 30 sekund lub dłużej. (Działania, które powinny zająć mniej niż 30 sekund, są raportowane tylko jako zdarzenia). Otwórz strony zadań, aby śledzić postęp zadań w chmurze prywatnej automatycznej synchronizacji.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Identyfikator zadania | Unikatowy identyfikator zadania. |
| Operacja | Akcja wykonywana przez zadanie. |
| Użytkownik | Użytkownik przypisany do ukończenia zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobu: automatyczna synchronizacja chmur prywatnych, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Rozpocznij | Godzina rozpoczęcia zadania. |
| Koniec | Godzina zakończenia zadania. |
| Stan | Bieżący stan zadania. |
| Upłynęło czasu | Czas trwania zadania (jeśli został ukończony) lub trwa jego wykonywanie (jeśli jest w toku). |
| Opis | Opis zadania. |

## <a name="audit"></a>Inspekcja

Dzienniki inspekcji śledzą aktywność użytkownika. Dzienników inspekcji można użyć do monitorowania aktywności użytkowników dla wszystkich użytkowników.

W przypadku alertów dostępne są następujące kolumny informacji. Kliknij pozycję **Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Czas | Godzina wpisu inspekcji. |
| Operacja | Akcja wykonywana przez zadanie. |
| Użytkownik | Użytkownik przypisany do zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobu: automatyczna synchronizacja chmur prywatnych, stojak w chmurze. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Wynik | Wynik działania, na przykład **powodzenie**. |
| Czas trwania | Czas na ukończenie zadania. |
| Opis | Opis akcji. |

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [automatycznej synchronizacji chmur prywatnych](cloudsimple-private-cloud.md)
