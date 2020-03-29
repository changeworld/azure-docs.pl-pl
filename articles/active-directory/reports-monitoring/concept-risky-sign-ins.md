---
title: Raport ryzykownych logów w portalu | Dokumenty firmy Microsoft
description: Dowiedz się więcej o raporcie dotyczącym ryzykownych logowań w portalu usługi Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273834"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) wykrywa podejrzane akcje związane z kontami użytkowników. Dla każdej wykrytej akcji tworzony jest rekord zwany **wykrywaniem ryzyka.** Aby uzyskać więcej informacji, zobacz [wykrywanie ryzyka usługi Azure AD](concept-risk-events.md). 

Dostęp do raportów zabezpieczeń można uzyskać z poziomu witryny [Azure Portal](https://portal.azure.com), wybierając blok **Azure Active Directory**, a następnie przechodząc do sekcji **Zabezpieczenia**. 

Istnieją dwa różne raporty zabezpieczeń, które są obliczane na podstawie wykrywania ryzyka:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/10.png)

Aby dowiedzieć się, jak skonfigurować zasady wyzwalające te wykrywanie ryzyka, zobacz [Jak skonfigurować zasady dotyczące ryzyka użytkownika](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kto może uzyskać dostęp do raportu ryzykownych logów?

Raporty ryzykownych logów są dostępne dla użytkowników w następujących rolach:

- Administrator zabezpieczeń
- Administrator globalny
- Czytelnik zabezpieczeń

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w usłudze Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratora w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure AD zapewniają ryzykowne raporty logowania. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **bezpłatnej wersji usługi Azure Active Directory**otrzymasz listę ryzykownych logów. 

- Ponadto wersja **usługi Azure Active Directory Premium 1** umożliwia zbadanie niektórych podstawowych wykrywania ryzyka, które zostały wykryte dla każdego raportu. 

- Wersja **usługi Azure Active Directory Premium 2** zawiera najbardziej szczegółowe informacje na temat wszystkich podstawowych wykrywania ryzyka, a także umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na skonfigurowane poziomy ryzyka.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Raport ryzykownych logów dla bezpłatnej wersji usługi Azure AD

Bezpłatna wersja usługi Azure AD zawiera listę ryzykownych logów, które zostały wykryte dla użytkowników. Każdy rekord zawiera następujące atrybuty:

- **Użytkownik** — nazwa użytkownika, który był używany podczas operacji logowania.
- **ADRES IP** — adres IP urządzenia używanego do łączenia się z usługą Azure Active Directory.
- **Lokalizacja** — lokalizacja używana do łączenia się z usługą Azure Active Directory. Jest to najlepsze przybliżenie wysiłku na podstawie śladów, danych rejestru, odwrotnego wyszukiwania i innych informacji.
- **Godzina logowania** — godzina, o której przeprowadzono logowanie
- **Stan** — stan logowania

![Ryzykowne logowania](./media/concept-risky-sign-ins/01.png)

Na podstawie badania ryzykownego logowania można przekazać opinię do usługi Azure AD, wykonując następujące akcje:

- Rozwiąż
- Oznacz jako wynik fałszywie dodatni
- Zignoruj
- Uaktywnij ponownie

![Ryzykowne logowania](./media/concept-risky-sign-ins/21.png)

Ten raport zawiera również możliwość:

- Wyszukiwania zasobów
- Pobierania danych raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Raport o ryzykownych logowaniach dla wersji premium usługi Azure AD

Raport ryzykownych logów w wersjach premium usługi Azure AD zapewnia:

- Zagregowane informacje o [wykrytych typach wykrywania ryzyka.](concept-risk-events.md) W **przypadku usługi Azure AD Premium P1 edition**wykrycie, które nie są objęte licencją, jest wyświetlane jako wykrywanie ryzyka **Zaloguj się z wykrytym dodatkowym ryzykiem.** Dzięki **wersji Azure AD Premium P2**otrzymujesz najbardziej szczegółowe informacje o wszystkich podstawowych wykrywaniach.

- Opcja pobrania raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/456.png)

Po wybraniu wykrywania ryzyka zostanie wyświetlany szczegółowy widok raportu dla tego wykrywania ryzyka, który umożliwia:

- Skonfigurowanie [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/howto-user-risk-policy.md).  

- Przejrzyj oś czasu wykrywania wykrywania ryzyka  

- Przejrzyj listę użytkowników, dla których wykryto to wykrywanie ryzyka

- Ręcznie zamknij wykrywanie ryzyka. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Czasami wykrywanie ryzyka może być wykrywane bez odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Jest tak, ponieważ ochrona tożsamości ocenia ryzyko zarówno **dla logowania interakcyjne,** jak i **nieinterakcyjne,** podczas gdy raport logowania pokazuje tylko interaktywne logowania.

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Zbadaj zgłoszone wykrywanie ryzyka dla użytkownika. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/324.png)

Aby zbadać wykrywanie ryzyka, wybierz jeden z listy.  
Spowoduje to otwarcie **details** bloku dla tego wykrywania ryzyka. Na **szczegóły** bloku, masz możliwość ręcznego zamknięcia wykrywania ryzyka lub ponownie aktywować ręcznie zamknięte wykrywanie ryzyka. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Następne kroki

- [Sposób konfigurowania zasad ryzyka użytkowników](../identity-protection/howto-user-risk-policy.md)
- [Sposób konfigurowania zasad podejmowania działań naprawczych dotyczących ryzyka](../identity-protection/howto-user-risk-policy.md)
- [Typy wykrywania ryzyka](concept-risk-events.md)
