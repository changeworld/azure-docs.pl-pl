---
title: Zrozumienie ze szczegółowym zestawieniem użycia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak czytać i zrozumieć sekcje ze szczegółowym zestawieniem użycia woluminów CSV dla subskrypcji platformy Azure
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: a143fc6d9dbd78ae365f943a00ac9f8492d5e51c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60369628"
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Warunki na firma Microsoft nalicza za użycie platformy Azure szczegółowe informacje 

Plik CSV opłaty szczegółowym zestawieniem użycia zawiera opłaty za użycie poziomu codziennie i licznik dla bieżącego okresu rozliczeniowego. 

Aby uzyskać plik szczegółowym zestawieniem użycia, zobacz [sposobu uzyskania usługi Azure billing faktury i dane dziennego użycia](billing-download-azure-invoice-daily-usage-date.md).
Jest ona dostępna w formacie wartości rozdzielanych przecinkami (CSV), który można otworzyć w aplikacji arkusza kalkulacyjnego. Jeśli będą dostępne dwie wersje, pobierz wersję 2. To najbardziej bieżącego formatu pliku.

Opłaty za zużycie to suma **miesięczne** opłaty za subskrypcję. Opłaty za użycie nie brać pod uwagę wszystkie środki na korzystanie z ani rabatów.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>Szczegółowy opis warunków wraz z opisami szczegółowym zestawieniem użycia pliku

Ważne terminy wyświetlane w wersji 2 pliku szczegóły użycia można znaleźć w poniższych sekcjach.

### <a name="statement"></a>Oświadczenie

Górna sekcja pliku CSV szczegółowym zestawieniem użycia zawiera usługi, które były używane podczas miesięcznego okresu rozliczeniowego. W poniższej tabeli wymieniono warunki i opisy przedstawione w tej sekcji.

| Termin | Opis |
| --- | --- |
|Okres rozliczeniowy |Okresu rozliczeniowego, jeśli były używane liczniki |
|Kategoria miernika |Określa usługę najwyższego poziomu do użycia |
|Podkategoria miernika |Definiuje typ usługi platformy Azure, która może wpływać na stawkę |
|Nazwa miernika |Określa jednostkę miary dla mierników, są używane |
|Region miernika |Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych |
|SKU |Określa unikatowy identyfikator systemowy dla poszczególnych mierników platformy Azure |
|Jednostka |Identyfikuje jednostkę, w której rozliczana jest usługa. Na przykład, GB, godziny, 10 000 s. |
|Zużyta ilość |Ilość miernika, używany w trakcie okresu rozliczeniowego |
|Uwzględniona ilość |Wartość licznika, który jest dołączony, bez dodatkowych opłat w trakcie bieżącego okresu rozliczeniowego |
|Ilość nadwyżkowego użycia |Przedstawiono różnice między użytej ilości i ilość uwzględnione. Wyświetlany jest wystawiany na tę kwotę. W przypadku ofert płatności nie uwzględnione ilości z ofertą ta jest taka sama jak wartość zużyte. |
|W ramach zobowiązania |Zawiera opłaty za liczników, które są odejmowane od kwoty zobowiązania związanej z ofertą 6- lub 12-miesięcznym. Licznik opłaty są odejmowane w porządku chronologicznym. |
|Waluta |Waluta używana w trakcie bieżącego okresu rozliczeniowego |
|Nadwyżka |Zawiera opłaty za miernika, które przekraczają kwotę zobowiązania związaną z ofertą 6- lub 12-miesięczny |
|Stawka ze zobowiązania |Zawiera stawkę za zobowiązania opartą na łącznej kwocie zobowiązania związanej z ofertą 6- lub 12-miesięczny |
|Stawka |Szybkość, z którą są naliczane za naliczaną na jednostkę |
|Wartość |Wyświetla wynik mnożenia wartości z kolumny ilość nadwyżkowe użycie przez wartość z kolumny stawka. Jeśli ilość zużyte nie przekracza ilość uwzględnione, nie ma opłat w tej kolumnie. |

### <a name="daily-usage"></a>Dzienne użycie

Dzienne użycie części pliku CSV zawiera szczegóły użycia, które wpływają na stawki rozliczeniowe. W poniższej tabeli wymieniono warunki i opisy przedstawione w tej sekcji.

| Termin | Opis |
| --- | --- |
|Data wykorzystania |Data, kiedy została użyta wartość licznika |
|Kategoria miernika |Określa usługę najwyższego poziomu, dla której dotyczy użycie |
|Identyfikator miernika |Identyfikator miernika rozliczane, który jest używany do wyceny rozliczanego użycia |
|Podkategoria miernika |Definiuje typ usługi platformy Azure, który może wpływać na stawkę |
|Nazwa miernika |Określa jednostkę miary dla mierników, są używane |
|Region miernika |Określa lokalizację centrum danych pewnych usług, które są wyceniane na podstawie lokalizacji centrum danych |
|Jednostka |Identyfikuje jednostkę, której wartość licznika jest rozliczana w. Na przykład, GB, godziny, 10 000 s. |
|Zużyta ilość |Ilość wykorzystanego w danym dniu licznik |
|Lokalizacja zasobu |Identyfikuje centrum danych, w którym uruchomiony jest wartość licznika |
|Użyta usługa |Usługi platformy Azure, która została użyta |
|Grupa zasobów |Grupa zasobów, w których mierniku wdrożonej jest uruchomiony w. <br/><br/>Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
|Identyfikator wystąpienia | Identyfikator miernika. <br/><br/> Identyfikator zawiera nazwę, którą określisz dla licznika podczas jej tworzenia. To nazwa zasobu albo w pełni kwalifikowanego identyfikatora zasobu. Aby uzyskać więcej informacji, zobacz [interfejsu API usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). |
|Tagi | Tag, które można przypisać do licznika. Za pomocą tagów do grupowania rekordów rozliczeń.<br/><br/>Na przykład można użyć tagów, aby dystrybuować koszty według działu, który używa licznika. Usługi obsługujące emitowanie tagów są maszyny wirtualne, Magazyn i usługi sieciowe aprowizowane za pomocą [interfejsu API usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources). Aby uzyskać więcej informacji, zobacz [organizowania zasobów platformy Azure przy użyciu tagów](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/). |
|Dodatkowe informacje |Metadane specyficzne dla usługi. Na przykład typ obrazu dla maszyny wirtualnej. |
|Informacje o usłudze 1 |Nazwa projektu, który usługi należy do subskrypcji |
|Informacje o usłudze 2 |Starszego pole, które zawiera opcjonalne metadane właściwe dla usługi |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>Jak upewnić się, że opłaty w szczegółowym zestawieniem użycia pliku są poprawne?
Jeśli w pliku szczegółowym zestawieniem użycia, które chcesz uzyskać więcej informacji znajduje się na znajduje się opłaty, zobacz [opis zawartości rachunku dla systemu Microsoft Azure.](./billing-understand-your-bill.md)

## <a name="external"></a>Jak wygląda opłat za usługi zewnętrzne?
Zewnętrznych usług (znany także jako zamówienia witryny Marketplace) są dostarczane przez dostawców niezależną usługę i są rozliczane osobno. Opłaty nie ujęte na fakturze platformy Azure. Aby dowiedzieć się więcej, zobacz [o opłatach za usługi platformy Azure zewnętrznej usługi](billing-understand-your-azure-marketplace-charges.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [Utwórz żądanie obsługi](https://go.microsoft.com/fwlink/?linkid=2083458).
