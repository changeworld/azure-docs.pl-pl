---
title: Monitorowanie aktywności w chmurze prywatnej
titleSuffix: Azure VMware Solution by CloudSimple
description: W tym artykule opisano informacje dotyczące działania w środowisku Azure VMware Solution by CloudSimple, w tym alerty, zdarzenia, zadania i inspekcje.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019676"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Monitorowanie działania VMware Solution według aktywności CloudSimple

Dzienniki aktywności CloudSimple zapewniają wgląd w operacje wykonywane w portalu CloudSimple.  Lista zawiera alerty, zdarzenia, zadania i inspekcje.  Użyj dzienników aktywności, aby określić, kto, kiedy i jakie operacje zostały wykonane.  Dzienniki aktywności nie zawierają żadnych operacji odczytu wykonywanych przez użytkownika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="activity-information"></a>Informacje o aktywności

Aby uzyskać dostęp do stron Działania, wybierz **polecenie Aktywność** w menu bocznym.

![Omówienie strony aktywności](media/activity-page-overview.png)

Aby wyświetlić szczegółowe informacje o działaniach na stronie działania, wybierz działanie. Po prawej stronie zostanie otwarty panel szczegółów. Akcje w panelu zależą od typu działania. Kliknij **przycisk X,** aby zamknąć panel.

Kliknij nagłówek kolumny, aby posortować ekran.  Można filtrować kolumny dla określonych wartości do wyświetlenia.  Pobierz raport aktywności, klikając ikonę **Pobierz jako CSV.**

## <a name="alerts"></a>Alerty

Alerty są powiadomienia o wszelkich znaczących działań w środowisku CloudSimple.  Alerty obejmują zdarzenia, które wpływają na rozliczenia lub dostęp użytkownika.

Aby potwierdzić alerty i usunąć je z listy, wybierz jedną lub więcej z listy i kliknij przycisk **Potwierdź**.

Następujące kolumny informacji są dostępne dla alertów. Kliknij **pozycję Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Typ alertu | Kategoria alertu.|
| Time | Czas wystąpienia alertu. |
| Ważność | Znaczenie wpisu.|
| Nazwa zasobu | Nazwa przypisana do zasobu, na przykład nazwa chmury prywatnej. |
| Typ zasobu | Kategoria zasobów: Private Cloud, Cloud Rack. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Opis | Opis tego, co wyzwoliło alert. |
| Potwierdzony | Wskazanie, czy wpis jest potwierdzony. |

## <a name="events"></a>Zdarzenia

Zdarzenia pokazują aktywność użytkownika i systemu w portalu CloudSimple. Strona Zdarzenia zawiera listę działań skojarzonych z określonym zasobem i ważność wpływu.

Następujące kolumny informacji są dostępne dla alertów. Kliknij **pozycję Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Time | Data i godzina wystąpienia zdarzenia. |
| Typ zdarzenia | Kod numeryczny identyfikujący zdarzenie. |
| Ważność | Ważność zdarzenia.|
| Nazwa zasobu | Nazwa przypisana do zasobu, na przykład nazwa chmury prywatnej. |
| Typ zasobu | Kategoria zasobów: Private Cloud, Cloud Rack. |
| Opis | Opis tego, co wyzwoliło alert. |

## <a name="tasks"></a>Zadania

Zadania to działania w chmurze prywatnej, których wykonanie ma potrwać co najmniej 30 sekund. (Działania, które mają potrwać mniej niż 30 sekund, są zgłaszane tylko jako zdarzenia). Otwórz strony Zadania, aby śledzić postęp zadań w chmurze prywatnej.

Następujące kolumny informacji są dostępne dla alertów. Kliknij **pozycję Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Identyfikator zadania | Unikatowy identyfikator zadania. |
| Operacja | Akcja, którą wykonuje zadanie. |
| Użytkownik | Użytkownik przypisany do wykonania zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobów: Private Cloud, Cloud Rack. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Rozpoczęcie | Godzina rozpoczęcia zadania. |
| End | Godzina zakończenia zadania. |
| Stan | Bieżący stan zadania. |
| Upłynęło czasu | Czas, który zajęło wykonanie zadania (jeśli zostało ukończone) lub trwa obecnie (jeśli jest w toku). |
| Opis | Opis zadania. |

## <a name="audit"></a>Inspekcja

Dzienniki inspekcji śledzą aktywność użytkowników. Dzienniki inspekcji służy do monitorowania aktywności użytkowników dla wszystkich użytkowników.

Następujące kolumny informacji są dostępne dla alertów. Kliknij **pozycję Edytuj kolumny** i wybierz kolumny, które chcesz wyświetlić.

| Kolumna | Opis |
------------ | ------------- |
| Time | Czas wpisu do audytu. |
| Operacja | Akcja, którą wykonuje zadanie. |
| Użytkownik | Użytkownik przypisany do zadania. |
| Nazwa zasobu | Nazwa przypisana do zasobu. |
| Typ zasobu | Kategoria zasobów: Private Cloud, Cloud Rack. |
| Identyfikator zasobu | Identyfikator zasobu. |
| Wynik | Wynik działania, takich jak **Sukces**. |
| Czas trwania | Czas na wykonanie zadania. |
| Opis | Opis akcji. |

## <a name="next-steps"></a>Następne kroki

* [Używanie maszyn wirtualnych VMware na platformie Azure](quickstart-create-vmware-virtual-machine.md)
* Dowiedz się więcej o [chmurach prywatnych](cloudsimple-private-cloud.md)
