---
title: Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie dotyczącym ryzykownych logowań w portalu usługi Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629e7bf8ee7100b5927483ecfd4efa1d9223c151
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989918"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory

Azure Active Directory (Azure AD) wykrywa podejrzane akcje powiązane z kontami użytkowników. Dla każdej wykrytej akcji jest tworzony wpis nazywany **zdarzeniem o podwyższonym ryzyku**. Aby uzyskać więcej informacji, zobacz [zdarzenia dotyczące ryzyka związane z usługą Azure AD](concept-risk-events.md). 

Dostęp do raportów zabezpieczeń można uzyskać z poziomu witryny [Azure Portal](https://portal.azure.com), wybierając blok **Azure Active Directory**, a następnie przechodząc do sekcji **Zabezpieczenia**. 

Istnieją dwa różne raporty dotyczące zabezpieczeń, które są obliczane na podstawie zdarzeń o podwyższonym ryzyku:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/10.png)

Aby dowiedzieć się, jak skonfigurować zasady powodujące wyzwalanie tych zdarzeń o podwyższonym ryzyku, zobacz [Sposób konfigurowania zasad ryzyka użytkowników](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kto może uzyskać dostęp do raportu dotyczącego ryzykownych logowań?

Raporty dotyczące ryzykownych logowań są dostępne dla użytkowników w następujących rolach:

- Administrator zabezpieczeń
- Administrator globalny
- Czytelnik zabezpieczeń

Aby dowiedzieć się, jak przypisać role administracyjne do użytkownika w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure AD zapewniają ryzykowne raporty logowania. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **wersjach Azure Active Directory — wersja bezpłatna i Basic**otrzymujesz listę ryzykownych logowań. 

- Wersja **Azure Active Directory Premium 1** umożliwia ponadto badanie niektórych podstawowych zdarzeń związanych z ryzykiem, które uwzględniono w poszczególnych raportach. 

- Wersja **Azure Active Directory Premium 2** oferuje najbardziej szczegółowe informacje na temat wszystkich zdarzeń o podwyższonym ryzyku i umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na wystąpienie skonfigurowanych poziomów ryzyka.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Raport dotyczący ryzykownych logowań w usłudze Azure AD w wersji bezpłatna i podstawowa

Wersje bezpłatna i podstawowa usługi Azure AD zapewniają listę ryzykownych logowań wykrytych dla użytkowników. Każdy rekord zawiera następujące atrybuty:

- **User** — nazwa użytkownika, która była używana podczas operacji logowania.
- **IP** — adres IP urządzenia, który został użyty do nawiązania połączenia z Azure Active Directory.
- **Lokalizacja** — lokalizacja użyta do nawiązania połączenia z Azure Active Directory. Jest to najlepszy sposób zbliżania się na podstawie śladów, danych rejestru, wstecznego wyszukiwania i innych informacji.
- **Godzina logowania** — godzina, o której przeprowadzono logowanie
- **Stan** — stan logowania

![Ryzykowne logowania](./media/concept-risky-sign-ins/01.png)

W oparciu o badanie ryzykownego logowania można przekazać informacje zwrotne do usługi Azure AD, wykonując następujące czynności:

- Rozwiąż
- Oznacz jako wynik fałszywie dodatni
- Zignoruj
- Uaktywnij ponownie

![Ryzykowne logowania](./media/concept-risky-sign-ins/21.png)

Ten raport udostępnia również opcję:

- Wyszukaj zasoby
- Pobierania danych raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Raport dotyczący ryzykownych logowań dla wersji Premium usługi Azure AD

Raport dotyczący ryzykownych logowań w wersjach Premium usługi Azure AD oferuje następujące informacje:

- Zagregowane informacje o [typach zdarzeń ryzyka](concept-risk-events.md) , które zostały wykryte. W przypadku **wersji Azure AD — wersja Premium P1**wykryte wykrywania, które nie są objęte licencją, pojawiają się w przypadku wykrycia zdarzenia dotyczącego ryzyka **przy użyciu dodatkowego ryzyka**. Za pomocą **Azure AD — wersja Premium P2**można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć.

- Opcja pobrania raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/456.png)

Po wybraniu zdarzenia o podwyższonym ryzyku jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Skonfigurowanie [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/howto-user-risk-policy.md).  

- Przeglądanie i wykrywanie osi czasu dla zdarzenia o podwyższonym ryzyku.  

- Przeglądanie listy użytkowników, dla których wykryto konkretne zdarzenie o podwyższonym ryzyku.

- Ręcznie Zamknij zdarzenia ryzyka. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Czasami może się pojawić zdarzenie o podwyższonym ryzyku bez odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Wynika to z faktu, że usługa Identity Protection szacuje ryzyko dla logowania interaktywnego i nieinterakcyjnego, podczas gdy raport logowania zawiera tylko interakcyjne logowania.

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Badanie zgłoszonych zdarzeń o podwyższonym ryzyku dla użytkownika. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/324.png)

Aby zbadać zdarzenie o podwyższonym ryzyku, wybierz je z listy.  
Spowoduje to otwarcie bloku **Szczegóły** dla tego zdarzenia o podwyższonym ryzyku. W bloku **Szczegóły** jest opcja ręcznego zamknięcia zdarzenia o podwyższonym ryzyku lub ponownego aktywowania ręcznie zamkniętego zdarzenia o podwyższonym ryzyku. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Następne kroki

- [Sposób konfigurowania zasad ryzyka użytkowników](../identity-protection/howto-user-risk-policy.md)
- [Sposób konfigurowania zasad podejmowania działań naprawczych dotyczących ryzyka](../identity-protection/howto-user-risk-policy.md)
- [Rodzaje ryzykownych zdarzeń](concept-risk-events.md)
