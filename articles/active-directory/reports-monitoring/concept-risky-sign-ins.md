---
title: Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie dotyczącym ryzykownych logowań w portalu usługi Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e4406a75ea1d9f1968d994ae2294b39ca7613d5
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623863"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Raport dotyczący ryzykownych logowań w portalu usługi Azure Active Directory

Azure Active Directory (Azure AD) wykrywa podejrzane akcje, które są powiązane z kontami użytkowników. Dla każdej wykrytej akcji rekord o nazwie **zdarzenie o podwyższonym ryzyku** zostanie utworzony. Aby uzyskać więcej informacji, zobacz [zdarzeń o podwyższonym ryzyku w usłudze Azure AD](concept-risk-events.md). 

Raporty zabezpieczeń z [witryny Azure portal](https://portal.azure.com) , wybierając **usługi Azure Active Directory** bloku, a następnie przechodząc do **zabezpieczeń** sekcji. 

Istnieją dwa raporty zabezpieczeń, które są obliczane zależnie od zdarzeń o podwyższonym ryzyku:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika.

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/10.png)

Aby dowiedzieć się, jak skonfigurować zasady, które mogą powodować tych zdarzeń o podwyższonym ryzyku, zobacz [sposobu konfigurowania zasad ryzyka użytkownika](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kto ma dostęp do raportów ryzykownych logowań?

Raporty dotyczący ryzykownych logowań są dostępne dla użytkowników w następujących ról:

- Administrator zabezpieczeń
- Administrator globalny
- Odczytywanie zabezpieczeń

Aby dowiedzieć się, jak przypisywać role administracyjne dla użytkownika w usłudze Azure Active Directory, zobacz [widoku i przypisywanie ról administratorów w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure AD umożliwiają raportów ryzykownych logowań. Jednak poziom szczegółowości raportu zależy od wersji: 

- W **wersji usługi Azure Active Directory bezpłatna i podstawowa**, masz dostęp do listy ryzykownych logowań. 

- Ponadto **usługi Azure Active Directory Premium 1** edition pozwala na badanie niektórych podstawowych zdarzeń o podwyższonym ryzyku, które zostały wykryte dla każdego raportu. 

- Wersja **Azure Active Directory Premium 2** oferuje najbardziej szczegółowe informacje na temat wszystkich zdarzeń o podwyższonym ryzyku i umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na wystąpienie skonfigurowanych poziomów ryzyka.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Raport dotyczący ryzykownych logowań dla usługi Azure AD bezpłatna i podstawowa edition

Wersje usługi Azure AD bezpłatna i podstawowa zapewnia listę ryzykownych logowań, które zostały wykryte dla użytkowników. Każdy rekord zawiera następujące atrybuty:

- **Użytkownik** — nazwa użytkownika użyta podczas logowania
- **IP** — adres IP urządzenia, którego użyto do nawiązania połączenia z usługą Azure Active Directory
- **Lokalizacja** — lokalizacja, z której nawiązano połączenie z usługą Azure Active Directory
- **Godzina logowania** — godzina, o której przeprowadzono logowanie
- **Stan** — stan logowania

![Ryzykowne logowania](./media/concept-risky-sign-ins/01.png)

Oparte na badania ryzykownego logowania, możesz przekazywać opinie do usługi Azure AD, wykonując następujące czynności:

- Rozwiąż
- Oznacz jako wynik fałszywie dodatni
- Zignoruj
- Uaktywnij ponownie

![Ryzykowne logowania](./media/concept-risky-sign-ins/21.png)

Ten raport zawiera także opcję, aby:

- Wyszukiwania zasobów
- Pobierania danych raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/93.png)


## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Raport dotyczący ryzykownych logowań dla wersji premium usługi Azure AD

Raport dotyczący ryzykownych logowań w usłudze Azure AD w wersjach premium zapewnia:

- Zagregowane informacje o wykrytych [typach zdarzeń o podwyższonym ryzyku](concept-risk-events.md)

- Opcja pobrania raportu

![Ryzykowne logowania](./media/concept-risky-sign-ins/456.png)

Po wybraniu zdarzenia o podwyższonym ryzyku jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Skonfigurowanie [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/howto-user-risk-policy.md).  

- Przeglądanie i wykrywanie osi czasu dla zdarzenia o podwyższonym ryzyku.  

- Przeglądanie listy użytkowników, dla których wykryto konkretne zdarzenie o podwyższonym ryzyku.

- Ręczne zamykanie zdarzeń o podwyższonym ryzyku. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/457.png)

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Badanie zgłoszonych zdarzeń o podwyższonym ryzyku dla użytkownika. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/324.png)

Aby zbadać zdarzenie o podwyższonym ryzyku, wybierz je z listy.  
Spowoduje to otwarcie bloku **Szczegóły** dla tego zdarzenia o podwyższonym ryzyku. W bloku **Szczegóły** jest opcja ręcznego zamknięcia zdarzenia o podwyższonym ryzyku lub ponownego aktywowania ręcznie zamkniętego zdarzenia o podwyższonym ryzyku. 

![Ryzykowne logowania](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Kolejne kroki

- [Jak skonfigurować zasady ryzyka użytkownika](../identity-protection/howto-user-risk-policy.md)
- [Jak skonfigurować zasady dotyczące usuwania ryzyka](../identity-protection/howto-user-risk-policy.md)
- [Rodzaje ryzykownych zdarzeń](concept-risk-events.md)