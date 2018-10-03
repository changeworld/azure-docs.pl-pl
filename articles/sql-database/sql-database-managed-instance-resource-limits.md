---
title: Limity zasobów bazy danych SQL platformy Azure — wystąpienie zarządzane | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie limitów zasobów usługi Azure SQL Database dla wystąpienia zarządzanego.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 10/02/2018
ms.openlocfilehash: f2b1a8e18917c1c045c715bd3424d0bbfc0cdc67
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045403"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Limity zasobów wystąpienia zarządzanego Azure SQL Database — omówienie

Ten artykuł zawiera omówienie limitów zasobów wystąpienia zarządzanego Azure SQL Database i zawiera informacje, jak utworzyć żądanie, aby zwiększyć domyślne limity subskrypcji. 

> [!NOTE]
> Aby uzyskać inne ograniczenia wystąpienia zarządzanego, nie odnoszą się do poszczególnych subskrypcji, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-managed-instance.md#vcore-based-purchasing-model) i [wystąpienie zarządzane usługi warstwy](sql-database-managed-instance.md#managed-instance-service-tiers).

## <a name="default-subscription-level-limits-per-region"></a>Domyślne limity poziom subskrypcji na region

Wystąpienie zarządzane obecnie obsługuje wdrożenia tylko dla następujących typów subskrypcji:

- [Umowy Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Płatność za rzeczywiste użycie](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Dostawca usług w chmurze (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> To ograniczenie, pomoc techniczną w przypadku niektórych typów subskrypcji jest tymczasowe.

Wystąpienia zarządzane ma dwa domyślne limity poziom subskrypcji na region platformy Azure. Typy innej subskrypcji mają różne limity regionalne. Te limity można zwiększyć przez tworzenie żądania obsługi specjalne w witrynie Azure portal dla subskrypcji z typem problemu **przydziału**:

- **Limit podsieci**: Maksymalna liczba podsieci, w którym zostały wdrożone wystąpienia zarządzanego
- **Limit liczby wystąpień**: Maksymalna liczba wystąpień na region

> [!IMPORTANT]
> Podczas planowania wdrożenia należy rozważyć, czy wystąpienia biznesowe krytyczne (BC) (z powodu dodane redundancy) zwykle zużywa 4 x większą pojemność niż wystąpienia ogólnego przeznaczenia (GP). Tak więc, na obliczeniach, wystąpienia zasad grupy: 1 = 1 wystąpienie jednostki oraz wystąpienia 1 BC = 4 jednostki wystąpienia. Aby uprościć analizy użycia na wartości domyślne, podsumowanie jednostki wystąpienia we wszystkich podsieciach w regionie, w których są wdrażane wystąpienia zarządzane przez usługę i należy porównać wyniki z granicami jednostki wystąpienia dla typu Twojej subskrypcji.

## <a name="default-limits-by-subscription-type"></a>Domyślne limity przez typ subskrypcji

|Typ subskrypcji| Maksymalna liczba podsieci wystąpienia zarządzanego | Maksymalna liczba wystąpień |Maksymalna liczba GP zarządzane wystąpienia *|Maksymalna liczba BC zarządzane wystąpienia *|
| :---| :--- | :--- |:--- |:--- |
|Płatność zgodnie z rzeczywistym użyciem|1 *|4 *|4 *|1 *|
|CSP |1 *|4 *|4 *|1 *|
|EA|3 **|12 **|12 **|3 **|

\* 1 BC lub 4 wystąpień zasad grupy w jednej podsieci, można wdrożyć albo tak, aby łączna liczba jednostek"wystąpienie" w regionie nigdy nie przekracza 4.

** Maksymalnej liczby wystąpień w jednej warstwie usługi ma zastosowanie, jeśli żadne wystąpienia znajduje się w innej warstwie usługi. W przypadku, gdy użytkownik chce mieszać GP i BC wystąpienia w ramach tej samej podsieci, następująca sekcja służy jako odwołanie dla dozwolonych kombinacji. Zgodnie z zasadą proste całkowita liczba podsieci nie może przekraczać 3, a łączna liczba jednostek wystąpienia nie może przekraczać 12.

## <a name="deployment-options-for-gp-and-bc-deployments-within-the-same-subnet"></a>Opcje wdrażania na potrzeby zasad grupy i BC wdrożeń w ramach tej samej podsieci

Poniższe przykłady obejmują przypadków wdrożenie z podsieciami niepuste i mieszane GP i BC warstwy usług.

|Liczba podsieci|Podsieć 1|Podsieć 2|Podsieci 3|
|:---|:---|:---|:---|
|1|0 BC i maksymalnie 12 zasad grupy<br>1 BC i maksymalnie 8 zasad grupy<br>2 BC i maksymalnie 4 zasad grupy<br>3 BC|ND| ND|
|2|BC 0, GP maksymalnie 4|BC 0, GP maksymalnie 8<br>BC 1, GP maksymalnie 4<br>2 BC|ND|
|2|1 BC|BC 0, GP maksymalnie 8<br>BC 1, GP maksymalnie 4<br>2 BC|ND|
|2|2 BC|BC 0, GP maksymalnie 8<br>BC 1, GP maksymalnie 4<br>2 BC|ND|
|3|BC 1, 0 ZASAD GRUPY|BC 1, 0 ZASAD GRUPY|BC 0, GP maksymalnie 4|
|3|1BC, 0 GP|BC 0, GP maksymalnie 4|BC 1, 0 ZASAD GRUPY|
|3|BC 0, GP maksymalnie 4|BC 1, 0 ZASAD GRUPY|1BC, 0 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Uzyskanie większego limitu przydziału dla wystąpienia zarządzanego usługi SQL

Aby zainicjować proces uzyskiwania większego limitu przydziału:

1. Otwórz **Pomoc i obsługa techniczna**i kliknij przycisk **nowe żądanie obsługi**. 

   ![Pomoc i obsługa techniczna](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na karcie podstawy nowe żądanie pomocy technicznej:
   - Aby uzyskać **typ problemu**, wybierz opcję **limity usług i subskrypcji (przydziały)**.
   - W polu **Subskrypcja** wybierz subskrypcję.
   - Aby uzyskać **typ limitu przydziału**, wybierz opcję **wystąpienie zarządzane usługi SQL Database**.
   - Aby uzyskać **plan pomocy technicznej**, wybierz plan pomocy technicznej.

     ![Problem typu przydziału](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kliknij przycisk **Dalej**.
4. Na karcie Problem nowe żądanie pomocy technicznej:
   - Aby uzyskać **ważność**, wybierz poziom ważności problemu.
   - Aby uzyskać **szczegóły**, zawierają dodatkowe informacje o swoim problemie, w tym komunikaty o błędach.
   - Aby uzyskać **przekazywanie pliku**, Dołącz plik z dodatkowymi informacjami (maksymalnie 4 MB).

     ![Szczegóły problemu](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Prawidłowemu żądaniu powinien zawierać:
     > - Region w subskrypcję, która musi zostać zwiększone limit
     > - Wymagana liczba wystąpień na warstwę usługi w istniejących podsieci po przydział zwiększyć (Jeśli żadna z podsieci istniejących potrzebuje do wyodrębnienia
     > - Wymagana liczba nowych podsieci i łącznej liczby wystąpień na warstwę usług w ramach nowych podsieci (jeśli zajdzie potrzeba wdrożenia wystąpienia zarządzanego w nowych podsieci).
5. Kliknij przycisk **Dalej**.
6. Na karcie informacje kontaktowe nowe żądanie pomocy technicznej wprowadź preferowaną metodę kontaktu (adres e-mail lub telefon) i szczegóły dotyczące kontaktu.
7. Kliknij pozycję **Utwórz**.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat wystąpienia zarządzanego, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md). 
- Aby uzyskać informacje o cenach, zobacz [wystąpienie zarządzane usługi SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Aby dowiedzieć się, jak utworzyć pierwsze wystąpienie zarządzane, zobacz [przewodnika Szybki start](sql-database-managed-instance-get-started.md).