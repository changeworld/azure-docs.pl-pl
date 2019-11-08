---
title: Tworzenie ofert w witrynie Marketplace | Portal Azure Marketplace
description: Twórz oferty na platformie Azure i AppSource Marketplace przy użyciu portal Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: 4aa4e154c99f20963485a98d1a21af4ae2b7a1b1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828100"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Tworzenie ofert w witrynie Azure Marketplace i AppSource

Jednym z podstawowych celów portal Cloud Partner jest umożliwienie wydawcom tworzenia (a następnie publikowania) ofert na Microsoft Azure i AppSource Marketplace.  Ta operacja zawsze zaczyna się od wybrania żądanego typu oferty z [menu nowe oferty](../portal-tour/cpp-new-offer-menu.md).  W odpowiedzi zostanie wyświetlona odpowiednia **Nowa strona oferty** dla tego typu oferty.  Na przykład na poniższej ilustracji przedstawiono domyślną nową stronę **oferty** dla typu aplikacji platformy Azure.

![Nowa strona domyślna oferty](./media/new-offer-page.png)

Dostępne są dwie opcje tabulacji w poziomym pasku menu wyświetlanym w górnej części tej strony: 
- Karta **Edytor** — włącza wprowadzanie informacji i przekazywanie zasobów dla nowego wystąpienia oferty.  Ta karta jest wyświetlana domyślnie.
- Karta **stan** — udostępnia stan publikowania i zawiera listę wszelkich problemów z walidacją i przeglądaniem. 

Podczas tworzenia oferty należy użyć karty **Edytor** , aby wprowadzić informacje o tej ofercie. 

## <a name="editing-operations"></a>Edytowanie operacji

Poziomy pasek narzędzi znajdujący się powyżej obszaru wprowadzania danych wyświetla następujące przyciski:

|   Button    |   Przeznaczenie                                                          |
|   ------    |  --------                                                          |
| **Pisał**    | Zapisuje wszystkie ostatnie zmiany wpisów danych.  Zmiany należy zapisywać ręcznie przed przechodzeniem ze strony lub utracisz zmiany. | 
| **Odrzucone** | Odrzuca ostatnie zmiany wpisów danych (od momentu ostatniego zapisu)             |
| **Porównaniu** | Porównuje stan bieżącej oferty z opublikowaną ofertą.  Włączone tylko po pomyślnym opublikowaniu oferty.  |
| **Publikowanie** | Rozpoczyna proces publikowania dla tej oferty                       |
| **Usuwanie**  | Usuwa tę ofertę po jej utworzeniu, ale przed jej opublikowaniem. |
|   |   |


## <a name="editing-tabs"></a>Edytowanie kart

Podczas tworzenia oferty podawane są wymagane i opcjonalne dane na każdej karcie, która znajduje się w pionowej kolumnie z lewej strony **nowej oferty** .  Standardowe formanty interfejsu użytkownika — takie jak pola tekstowe, menu rozwijane i pola wyboru — są wyświetlane na potrzeby zbierania danych.  Chociaż określona kolekcja kart edycji zależy od typu oferty, w poniższej tabeli wymieniono niektóre typowe karty.

|      Nazwa karty       |   Przeznaczenie                                                            |
|      --------       |   -------                                                            |
| **Ustawienia oferty**  | Zbiera informacje o ofercie i tożsamości wydawcy.                    |
| **Jednostki SKU**            | Definiuje cechy techniczne i biznesowe dla każdej wersji jednostki składowania (SKU) oferty |
| **Wersja testowa**      | Dla tych typów, które obsługują tę opcjonalną funkcję, definiuje demonstrację oferty.  Aby uzyskać więcej informacji, zobacz [co to jest dysk testowy?](../test-drive/what-is-test-drive.md)  |
| Portal **Marketplace** lub **witryna sklepu** | Zbiera ciągi tekstowe, dokumenty i obrazy używane do wyświetlania oferty w portalu Marketplace |
| **Pomoc techniczna**         | Gromadzi informacje kontaktowe dla klientów, inżynierów i pomocy technicznej online  |
|  |  |

Zawartość kart o podobnej nazwie może się różnić między różnymi typami oferty.  Szczegółowe informacje o ofercie tych kart są podane w sekcji "Tworzenie oferty" dla każdego typu oferty.


## <a name="next-steps"></a>Następne kroki

Po utworzeniu i zapisaniu oferty oraz przed lub po jej opublikowaniu można [wyświetlić jej stan](./cpp-view-status-offer.md).
