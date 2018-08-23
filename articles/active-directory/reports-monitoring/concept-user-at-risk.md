---
title: Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej o raporcie o zabezpieczeniach dotyczącym użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 030774716e1af4a7d6817d64ae66ded2bcaf4081
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "41918227"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Raport o zabezpieczeniach dotyczący użytkowników oflagowanych w związku z ryzykiem w portalu usługi Azure Active Directory

Dzięki raportom o zabezpieczeniach w usłudze Azure Active Directory (Azure AD) możesz uzyskać wgląd w prawdopodobieństwo naruszenia bezpieczeństwa kont użytkowników w środowisku. 

Usługa Azure Active Directory wykrywa podejrzane akcje powiązane z kontami użytkowników. Dla każdej wykrytej akcji jest tworzony wpis nazywany *zdarzeniem o podwyższonym ryzyku*. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](concept-risk-events.md). 

Za pomocą wykrytych zdarzeń o podwyższonym ryzyku obliczane są:

- **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz [Ryzykowne logowania](../identity-protection/overview.md#risky-sign-ins). 

- **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz [Użytkownicy oflagowani w związku z ryzykiem](../identity-protection/overview.md#users-flagged-for-risk).  

W witrynie Azure Portal raporty dotyczące zabezpieczeń można znaleźć w bloku **Azure Active Directory** w sekcji **Zabezpieczenia**.  

![Ryzykowne logowania](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?  

Wszystkie wersje usługi Azure Active Directory zapewniają dostęp do raportów użytkowników oflagowanych w związku z ryzykiem.  
Jednak poziom szczegółowości raportu zależy od wersji: 

- W **usłudze Azure Active Directory w wersji Bezpłatna i Podstawowa** masz już dostęp do listy użytkowników oflagowanych w związku z ryzykiem. 

- Wersja **Azure Active Directory Premium 1** rozszerza ten model, umożliwiając również badanie niektórych podstawowych zdarzeń związanych z ryzykiem, które uwzględniono w poszczególnych raportach. 

- Wersja **Azure Active Directory Premium 2** oferuje najbardziej szczegółowe informacje na temat wszystkich zdarzeń o podwyższonym ryzyku i umożliwia konfigurowanie zasad zabezpieczeń, które automatycznie reagują na wystąpienie skonfigurowanych poziomów ryzyka.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory — wersja Bezpłatna i Podstawowa

Raport dotyczący użytkowników oflagowanych w związku z ryzykiem w usłudze Azure Active Directory w wersji Bezpłatna i Podstawowa zapewnia listę kont użytkowników, których bezpieczeństwo mogło zostać naruszone. 


![Ryzykowne logowania](./media/concept-user-at-risk/03.png)

Wybranie użytkownika powoduje otwarcie bloku z danymi tego użytkownika.
W przypadku narażonego użytkownika można przejrzeć jego historię logowania i w razie potrzeby zresetować hasło.

![Ryzykowne logowania](./media/concept-user-at-risk/46.png)


To okno dialogowe oferuje opcję:

- Pobierania raportu

- Wyszukiwania użytkowników

![Ryzykowne logowania](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory — wersje Premium

Raport dotyczący użytkowników oflagowanych w związku z ryzykiem w usłudze Azure Active Directory w wersjach Premium zawiera następujące elementy:

- [Lista kont użytkowników](../identity-protection/overview.md#users-flagged-for-risk), których bezpieczeństwo mogło zostać naruszone 

- Zagregowane informacje o wykrytych [typach zdarzeń o podwyższonym ryzyku](concept-risk-events.md)

- Opcja pobrania raportu

- Opcja skonfigurowania [zasad podejmowania działań naprawczych dotyczących ryzyka związanego z użytkownikiem](../identity-protection/overview.md#user-risk-security-policy)  


![Ryzykowne logowania](./media/concept-user-at-risk/71.png)

Po wybraniu użytkownika jest dla niego wyświetlany szczegółowy widok raportu, który umożliwia wykonanie następujących czynności:

- Otwieranie widoku wszystkich logowań.

- Resetowanie hasła użytkownika.

- Odrzucanie wszystkich zdarzeń.

- Badanie zgłoszonych zdarzeń o podwyższonym ryzyku dla użytkownika. 


![Ryzykowne logowania](./media/concept-user-at-risk/324.png)


Aby zbadać zdarzenia o podwyższonym ryzyku, wybierz zdarzenie z listy w celu otwarcia bloku **Szczegóły** dla tego zdarzenia o podwyższonym ryzyku. W bloku **Szczegóły** jest opcja [ręcznego zamknięcia zdarzenia o podwyższonym ryzyku](../identity-protection/overview.md#closing-risk-events-manually) lub ponownego aktywowania ręcznie zamkniętego zdarzenia o podwyższonym ryzyku. 


![Ryzykowne logowania](./media/concept-user-at-risk/325.png)



## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat ochrony tożsamości w usłudze Azure Active Directory, zobacz [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md).

