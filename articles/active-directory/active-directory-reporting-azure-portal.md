---
title: Raporty usługi Azure Active Directory | Microsoft Docs
description: Zawiera ogólne omówienie raportów usługi Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1bcd7f9e0a134173a8942b71259428a720a3d076
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36222505"
---
# <a name="azure-active-directory-reporting"></a>Raporty w usłudze Azure Active Directory

Dzięki raportom usługi Azure Active Directory możesz uzyskać szczegółowe informacje na temat działania środowiska.  
Na podstawie udostępnionych danych można:

- Określać, jak usługi i aplikacje są wykorzystywane przez użytkowników
- Wykrywać potencjalne zagrożenia wpływające na kondycję środowiska
- Rozwiązywać problemy uniemożliwiające użytkownikom wykonywanie pracy  

Architektura raportów opiera się na dwóch głównych filarach:

- Raporty dotyczące zabezpieczeń
- Raporty dotyczące działań

![Raportowanie](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

Raporty dotyczące zabezpieczeń w usłudze Azure Active Directory pomagają chronić tożsamości w organizacji.  
W usłudze Azure Active Directory istnieją dwa typy raportów dotyczących zabezpieczeń:

- **Użytkownicy oflagowani w związku z ryzykiem** — [raport zabezpieczeń dotyczący użytkowników oflagowanych w związku z ryzykiem](active-directory-reporting-security-user-at-risk.md) zawiera omówienie kont użytkowników, których zabezpieczenia mogły zostać naruszone.

- **Ryzykowne logowania** — [raport zabezpieczeń dotyczący ryzykownych logowań](active-directory-reporting-security-risky-sign-ins.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 

**Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu zabezpieczeń?**  
Wszystkie wersje usługi Azure Active Directory zapewniają dostęp do raportów użytkowników oflagowanych w związku z ryzykiem oraz ryzykownych logowań.  
Jednak poziom szczegółowości raportu zależy od wersji: 

- W **usłudze Azure Active Directory w wersji Bezpłatna i Podstawowa** masz już dostęp do listy użytkowników oflagowanych w związku z ryzykiem i ryzykownych logowań. 

- Wersja **Azure Active Directory Premium 1** rozszerza ten model, umożliwiając również badanie niektórych podstawowych zdarzeń związanych z ryzykiem, które uwzględniono w poszczególnych raportach. 

- Wersja **Azure Active Directory Premium 2** oferuje najbardziej szczegółowe informacje na temat zdarzeń związanych z ryzykiem i umożliwia również konfigurowanie zasad zabezpieczeń, które automatycznie reagują na wystąpienie skonfigurowanych poziomów ryzyka.


## <a name="activity-reports"></a>Raporty dotyczące działań

W usłudze Azure Active Directory istnieją dwa typy raportów dotyczących działań:

- **Dzienniki inspekcji** — [raport działań dotyczący dzienników inspekcji](active-directory-reporting-activity-audit-logs.md) zapewnia dostęp do historii wszystkich zadań wykonanych w dzierżawie.

- **Logowania** — przy użyciu [raportu działań dotyczącego logowań](active-directory-reporting-activity-sign-ins.md) można określić, kto wykonał zadania zgłoszone w raporcie dzienników inspekcji.



**Raport dzienników inspekcji** dostarcza informacji na temat aktywności systemu pod kątem zgodności.
Między innymi udostępnione dane pozwalają obsługę typowych scenariuszy, takich jak:

- Ktoś w mojej dzierżawie uzyskał dostęp do grupy administratorów. Kto udzielił tej osobie prawa dostępu? 

- Chcę zobaczyć listę użytkowników, którzy logowali się do określonej aplikacji od momentu jej dołączenia oraz sprawdzić, jak działa aplikacja

- Chcę wiedzieć, ile operacji resetowania hasła jest wykonywanych w mojej dzierżawie


**Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu dzienników inspekcji?**  
Raport dzienników inspekcji jest dostępny w przypadku funkcji, do których masz licencje. Jeśli masz licencję określonej funkcji, masz również dostęp do informacji z dotyczącego jej dziennika inspekcji.

Aby uzyskać więcej informacji, zobacz sekcję **Porównanie ogólnie dostępnych funkcji w wersjach Bezpłatna, Podstawowa i Premium** na stronie [funkcji i możliwości usługi Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



**Raport działań związanych z logowaniem** zawiera odpowiedzi na pytania, takie jak:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?


**Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do raportu działań związanych z logowaniem?**  
Aby uzyskać dostęp do raportu działań związanych z logowaniem, dzierżawa musi mieć skojarzoną licencję usługi Azure AD w wersji Premium.


## <a name="programmatic-access"></a>Dostęp programowy

Oprócz interfejsu użytkownika funkcja raportowania w usłudze Azure Active Directory zapewnia [dostęp programowy ](active-directory-reporting-api-getting-started-azure-portal.md) do danych raportów. Dane z tych raportów mogą być bardzo przydatne w aplikacjach, takich jak systemy SIEM oraz narzędzia do inspekcji i analizy biznesowej. Interfejsy API raportów usługi Azure AD umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywoływać przy użyciu różnych języków i narzędzi do programowania. 


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej o różnych typach raportów w usłudze Azure Active Directory, zobacz:

- [Raport dotyczący użytkowników oflagowanych w związku z ryzykiem](active-directory-reporting-security-user-at-risk.md)
- [Raport dotyczący ryzykownych logowań](active-directory-reporting-security-risky-sign-ins.md)
- [Raport dotyczący dzienników inspekcji](active-directory-reporting-activity-audit-logs.md)
- [Raport dotyczący dzienników logowania](active-directory-reporting-activity-sign-ins.md)

Jeśli chcesz dowiedzieć się więcej na temat uzyskiwania dostępu do danych raportowania przy użyciu interfejsu API raportów, zobacz: 

- [Wprowadzenie do interfejsu API raportów usługi Azure Active Directory](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png