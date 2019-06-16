---
title: Dostosowywanie SQL zasad usługi information protection w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować zasadami ochrony informacji w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: rkarlin
ms.openlocfilehash: e2bcd92ffc8799b2edf04251b6ce160e96c7609c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875607"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Dostosowywanie SQL zasad usługi information protection w usłudze Azure Security Center (wersja zapoznawcza)
 
Zasady ochrony informacji SQL mogą być definiowane i dostosowane do całej dzierżawie platformy Azure, w usłudze Azure Security Center.

Ochrona informacji jest zabezpieczenia zaawansowane możliwości odnajdywania, klasyfikowania, etykietowania i ochrony poufnych danych w swoich zasobach danych platformy Azure. Odnajdywanie i klasyfikacja najbardziej poufnych danych (biznesowe, finansów, opieki zdrowotnej, dane osobowe, itp.) można odtworzyć zasadniczą rolę w stature ochrony Twojej organizacji. Może służyć jako infrastruktury:
- Pomaga zaspokoić potrzeby dotyczące standardów ochrony prywatności danych i wymagania dotyczące zgodności z przepisami
- Różne scenariusze zabezpieczeń, takich jak monitorowanie (inspekcji) i alarmując nietypowego dostępu do poufnych danych
- Kontrolowanie dostępu do i wzmacniania ochrony bezpieczeństwa danych przechowuje zawierających poufne dane
 
[Usługa SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementuje taki wzorzec pracy dla swoich magazynów danych SQL, obecnie obsługiwane w przypadku usługi Azure SQL Database. Usługa SQL Information Protection automatycznie odnajduje i klasyfikuje potencjalnie poufnych danych, udostępnia mechanizm etykietowania trwałe tagowanie poufnych danych za pomocą atrybutów klasyfikacji i zapewnia wyświetlanie szczegółowego pulpitu nawigacyjnego Klasyfikacja stan bazy danych. Ponadto oblicza wynik ustawić czułość zapytań SQL, dzięki czemu mogą jawnie inspekcji zapytań, które umożliwiają wyodrębnianie danych poufnych, a dane mogą być chronione. Aby uzyskać szczegółowe informacje na temat SQL Information Protection, zobacz [odnajdywanie danych bazy danych SQL Azure i klasyfikacji](../sql-database/sql-database-data-discovery-and-classification.md).
 
Mechanizm klasyfikacji opiera się na dwóch głównymi konstrukcjami, które tworzą taksonomii klasyfikacji - **etykiety** i **typów informacji**.
- **Etykiety** — atrybuty głównego klasyfikacji, używane do definiowania poziomu poufności danych przechowywanych w kolumnie. 
- **Typy informacji** — zapewnia dodatkowy poziom szczegółowości na typ danych przechowywanych w kolumnie.
 
Information Protection zawiera zestaw wbudowanych etykiet i typy informacji, które domyślnie są używane. Aby dostosować je, można dostosować zasad usługi information protection w usłudze Azure Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Dostosowywanie zasad ochrony informacji
Aby dostosować zasad usługi information protection dla Twojej dzierżawy platformy Azure, musisz mieć [uprawnień administracyjnych w grupie zarządzania głównym dzierżawcy](security-center-management-groups.md). 
 
1. W usłudze Security Center menu głównego w obszarze **higieny zabezpieczeń ZASOBU** przejdź do **dane i magazyn** i kliknij pozycję **SQL Information Protection** przycisku.

   ![Konfigurowanie zasad usługi Information protection](./media/security-center-info-protection-policy/security-policy.png) 
 
2. W **SQL Information Protection** strony, można wyświetlić bieżącego zestawu etykiet. Są to atrybutów głównego klasyfikacji, które służą do kategoryzowania poziom poufności danych. W tym miejscu można skonfigurować **etykiety ochrony informacji** i **typów informacji** dla dzierżawy. 
 
### <a name="customizing-labels"></a>Dostosowywanie etykiet
 
1. Można edytować lub usunąć wszelkie istniejące etykiety lub Dodaj nową etykietę. Aby edytować istniejącą etykietę, zaznacz tej etykiety, a następnie kliknij przycisk **Konfiguruj**, u góry lub z menu kontekstowego po prawej stronie. Aby dodać nową etykietę, kliknij **Utwórz etykietę** na pasku menu u góry lub u dołu tabeli etykiety.
2. W **etykieta poufności Konfiguruj** ekranu, można utworzyć lub zmienić nazwę etykiety i opis. Można również ustawić czy etykieta jest aktywna lub wyłączona, przełączając **włączone** włączać i wyłączać. Na koniec można dodawać lub usuwać typy informacji skojarzony z etykietą. Wszelkie dane odnalezione, który odpowiada, że typ informacji automatycznie włączy etykieta poufności skojarzone zaleceń klasyfikacji.
3. Kliknij przycisk **OK**.
 
   ![Konfigurowanie etykiety ważności](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etykiety są wymienione w kolejności rosnącej ważność. Aby zmienić klasyfikację między etykiety, przeciągnij etykiety, aby zmienić ich kolejność, w tabeli lub użyj **Przenieś w górę** i **Przenieś w dół** przycisków, aby zmienić kolejność. 
 
    ![Konfigurowanie zasad usługi Information protection](./media/security-center-info-protection-policy/move-up.png)
 
5. Należy kliknąć przycisk **Zapisz** w górnej części ekranu, po zakończeniu.
 
 
## <a name="adding-and-customizing-information-types"></a>Dodawanie i Dostosowywanie typów informacji
 
1. Możesz zarządzać i dostosować typy informacji, klikając **zarządzać typami informacji**.
2. Aby dodać nowy **typ informacji**, wybierz opcję **utworzyć typ informacji** w górnym menu. Możesz skonfigurować nazwę, opis, a wyszukiwane ciągi wzorca dla **typ informacji**. Ciągi wzorzec wyszukiwania można opcjonalnie używać słów kluczowych z symbolami wieloznacznymi (przy użyciu znaku "%"), które aparat automatyczne odnajdowanie używa do identyfikowania poufnych danych w bazach danych, na podstawie metadanych kolumn.
 
    ![Konfigurowanie zasad usługi Information protection](./media/security-center-info-protection-policy/info-types.png)
 
3. Można również skonfigurować wbudowane **typów informacji** przez dodanie dodatkowych wzorzec ciągów znaków, wyłączając niektóre z istniejących ciągów lub zmiany opisu. Nie można usunąć wbudowanych **typów informacji** lub zmienić ich nazwy. 
4. **Typy informacji** są wymienione w kolejności rosnącej klasyfikacji odnajdywania, co oznacza, że wyżej na liście typów podejmie próbę Dopasuj najpierw. Aby zmienić klasyfikację między typami informacji, przeciągnij typów do odpowiednich miejscach w tabeli lub użyj **Przenieś w górę** i **Przenieś w dół** przycisków, aby zmienić kolejność. 
5. Kliknij przycisk **OK** po zakończeniu.
6. Po zakończeniu zarządzania typów informacji, pamiętaj skojarzyć odpowiednich typów z odpowiednimi etykietami, klikając przycisk **Konfiguruj** dla określonej etykiety i dodawanie lub usuwanie typów informacji, zgodnie z potrzebami.
7. Należy kliknąć przycisk **Zapisz** w głównym **etykiety** bloku, aby zastosować wprowadzone zmiany.
 
Po zasad usługi Information protection jest w pełni zdefiniowany i jest zapisany, będzie stosowana do klasyfikacji danych dla wszystkich baz danych Azure SQL, w dzierżawie.
 
 
## <a name="next-steps"></a>Kolejne kroki
 
W tym artykule opisano definiowanie zasad SQL Information Protection w usłudze Azure Security Center. Aby dowiedzieć się więcej o korzystaniu z programu SQL Information Protection do klasyfikowania i ochrony poufnych danych w bazach danych SQL, zobacz [odnajdywanie danych bazy danych SQL Azure i klasyfikacji](../sql-database/sql-database-data-discovery-and-classification.md). 

Aby uzyskać więcej informacji na temat zasad zabezpieczeń i bezpieczeństwo danych w usłudze Azure Security Center zobacz następujące artykuły:
 
- [Omówienie zasad zabezpieczeń](security-center-policies-overview.md): Zapoznaj się z omówieniem zasad zabezpieczeń w usłudze Security Center
- [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md): Informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów
- [Bezpieczeństwo danych w usłudze Azure Security Center](security-center-data-security.md): Dowiedz się, jak usługa Security Center zarządza i zapewnia bezpieczeństwo danych


