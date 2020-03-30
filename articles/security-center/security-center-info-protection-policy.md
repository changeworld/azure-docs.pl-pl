---
title: Dostosowywanie ochrony informacji SQL — Usługa Azure Security Center
description: Dowiedz się, jak dostosować zasady ochrony informacji w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75611070"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Dostosowywanie zasad ochrony informacji SQL w usłudze Azure Security Center (wersja zapoznawcza)
 
Zasady ochrony informacji SQL można zdefiniować i dostosować dla całej dzierżawy platformy Azure w usłudze Azure Security Center.

Ochrona informacji to zaawansowana funkcja zabezpieczeń do odnajdowania, klasyfikowania, etykietowania i raportowania poufnych danych w zasobach danych platformy Azure. Odkrywanie i klasyfikowanie najbardziej wrażliwych danych (biznesowych, finansowych, opieki zdrowotnej, danych osobowych itp.) może odegrać kluczową rolę w rozwoju ochrony informacji organizacji. Może to stanowić infrastrukturę dla następujących działań:
- Pomoc w spełnianiu standardów ochrony danych i wymogów zgodności z przepisami
- Scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i ostrzeganie o nietypowym dostępie do poufnych danych
- Kontrolowanie dostępu do i wzmacnianie bezpieczeństwa magazynów danych zawierających bardzo wrażliwe dane
 
[Sql Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementuje ten paradygmat dla magazynów danych SQL, obecnie obsługiwane dla usługi Azure SQL Database. SQL Information Protection automatycznie odnajduje i klasyfikuje potencjalnie poufne dane, zapewnia mechanizm etykietowania do trwałego oznaczania poufnych danych za pomocą atrybutów klasyfikacji i zapewnia szczegółowy pulpit nawigacyjny pokazujący klasyfikacji bazy danych. Ponadto oblicza czułość zestawu wyników zapytań SQL, dzięki czemu kwerendy, które wyodrębniają poufne dane mogą być jawnie kontrolowane, a dane mogą być chronione. Aby uzyskać więcej informacji na temat ochrony informacji SQL, zobacz [Odnajdowanie i klasyfikacja danych bazy danych SQL Azure](../sql-database/sql-database-data-discovery-and-classification.md).
 
Mechanizm klasyfikacji opiera się na dwóch podstawowych konstrukcjach, które składają się na taksonomię klasyfikacji - **Etykiety** i **typy informacji**.
- **Etykiety** — główne atrybuty klasyfikacji, używane do definiowania poziomu czułości danych przechowywanych w kolumnie. 
- **Typy informacji** — zapewnia dodatkową szczegółowość do typu danych przechowywanych w kolumnie.
 
Ochrona informacji jest wyposażona we wbudowany zestaw etykiet i typów informacji, które są używane domyślnie. Aby dostosować te etykiety i typy, można dostosować zasady ochrony informacji w u centrum zabezpieczeń.
 
## <a name="customize-the-information-protection-policy"></a>Dostosowywanie zasad ochrony informacji
Aby dostosować zasady ochrony informacji dla dzierżawy platformy Azure, musisz mieć [uprawnienia administracyjne w głównej grupie zarządzania dzierżawy.](security-center-management-groups.md) 
 
1. W menu głównym centrum zabezpieczeń w obszarze **HIGIENA BEZPIECZEŃSTWA ZASOBÓW** przejdź do **miejsca Przechowywanie danych &** i kliknij przycisk Ochrona informacji **SQL.**

   ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na stronie **Ochrona informacji SQL** można wyświetlić bieżący zestaw etykiet. Są to główne atrybuty klasyfikacji, które są używane do kategoryzowanie poziomu czułości danych. W tym miejscu można skonfigurować **etykiety ochrony informacji** i **typy informacji** dla dzierżawy. 
 
### <a name="customizing-labels"></a>Dostosowywanie etykiet
 
1. Można edytować lub usuwać istniejącą etykietę lub dodać nową etykietę. Aby edytować istniejącą etykietę, zaznacz tę etykietę, a następnie kliknij przycisk **Konfiguruj**u góry lub z menu kontekstowego po prawej stronie. Aby dodać nową etykietę, kliknij pozycję **Utwórz etykietę** na górnym pasku menu lub u dołu tabeli etykiet.
2. Na ekranie **Konfigurowanie etykiety czułości** można utworzyć lub zmienić nazwę etykiety i opis. Można również ustawić, czy etykieta jest aktywna czy wyłączona, przełączając włączone **włączanie** czy wyłączanie. Na koniec można dodać lub usunąć typy informacji skojarzone z etykietą. Wszystkie wykryte dane, które są zgodne z tym typem informacji, automatycznie dołączą skojarzoną etykietę czułości do zaleceń klasyfikacji.
3. Kliknij przycisk **OK**.
 
   ![Konfigurowanie etykiety czułości](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etykiety są wyświetlane w kolejności rosnącej czułości. Aby zmienić klasyfikację między etykietami, przeciągnij etykiety, aby zmienić ich kolejność w tabeli, lub użyj przycisków **Przenieś w górę** i Przenieś w **dół,** aby zmienić kolejność. 
 
    ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/move-up.png)
 
5. Pamiętaj, aby kliknąć przycisk **Zapisz** u góry ekranu po zakończeniu.
 
 
## <a name="adding-and-customizing-information-types"></a>Dodawanie i dostosowywanie typów informacji
 
1. Typy informacji można zarządzać i dostosowywać, klikając pozycję **Zarządzaj typami informacji**.
2. Aby dodać nowy **typ informacji,** wybierz **pozycję Utwórz typ informacji** w górnym menu. Można skonfigurować ciągi nazw, opisu i wzorca wyszukiwania dla **typu Informacje**. Ciągi wzorców wyszukiwania mogą opcjonalnie używać słów kluczowych ze znakami wieloznacznymi (przy użyciu znaku "%"), których automatyczna wyszukiwarka odnajdywania używa do identyfikowania poufnych danych w bazach danych na podstawie metadanych kolumn.
 
    ![Konfigurowanie zasad ochrony informacji](./media/security-center-info-protection-policy/info-types.png)
 
3. Można również skonfigurować wbudowane **typy informacji,** dodając dodatkowe ciągi wzorców wyszukiwania, wyłączając niektóre istniejące ciągi lub zmieniając opis. Nie można usuwać **wbudowanych typów informacji** ani edytować ich nazw. 
4. **Typy informacji** są wyświetlane w kolejności rosnącej klasyfikacji odnajdywania, co oznacza, że typy wyższe na liście będą próbowały dopasować się jako pierwsze. Aby zmienić klasyfikację między typami informacji, przeciągnij je w odpowiednie miejsce w tabeli lub użyj przycisków Przenieś w **górę** i **Przenieś w dół,** aby zmienić kolejność. 
5. Po zakończeniu kliknij **przycisk OK.**
6. Po zakończeniu zarządzania typami informacji należy skojarzyć odpowiednie typy z odpowiednimi etykietami, klikając **pozycję Konfiguruj** dla określonej etykiety i odpowiednio dodając lub usuwając typy informacji.
7. Pamiętaj, aby kliknąć przycisk **Zapisz** w bloku **Etykiety** główne, aby zastosować wszystkie zmiany.
 
Po zasady ochrony informacji jest w pełni zdefiniowane i zapisane, będzie stosowany do klasyfikacji danych na wszystkich baz danych SQL platformy Azure w dzierżawie.
 
 
## <a name="next-steps"></a>Następne kroki
 
W tym artykule dowiesz się o definiowaniu zasad ochrony informacji SQL w usłudze Azure Security Center. Aby dowiedzieć się więcej na temat korzystania z ochrony informacji SQL do klasyfikowania i ochrony poufnych danych w bazach danych SQL, zobacz [Odnajdowanie i klasyfikacja danych bazy danych SQL azure](../sql-database/sql-database-data-discovery-and-classification.md). 

Aby uzyskać więcej informacji na temat zasad zabezpieczeń i zabezpieczeń danych w usłudze Azure Security Center, zobacz następujące artykuły:
 
- [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center:](tutorial-security-policy.md)Dowiedz się, jak skonfigurować zasady zabezpieczeń dla subskrypcji platformy Azure i grup zasobów
- [Bezpieczeństwo danych usługi Azure Security Center:](security-center-data-security.md)dowiedz się, jak Usługa Security Center zarządza danymi i chroni ich
