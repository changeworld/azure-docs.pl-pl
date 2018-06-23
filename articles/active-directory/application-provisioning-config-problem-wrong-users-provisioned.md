---
title: Nieprawidłowy zestaw użytkowników są aprowizowany do aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dowiedzieć się, dlaczego inny zestaw użytkowników są aprowizowany do aplikacji niż te, które miały
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: f984db4c06a98f7d131860f8eb5d76a67a35a483
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332555"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nieprawidłowy zestaw użytkowników są aprowizowany do aplikacji w galerii Azure AD

Użytkowników, którzy są udostępnione do aplikacji głównie wynikają z których użytkownicy i grupy zostały **przypisane** do aplikacji.

Użyj następujących zasobów, aby dowiedzieć się, jak sprawdzić, którzy użytkownicy i grupy z przypisanym do aplikacji w usłudze Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Przypisywanie użytkownika bezpośrednio jako administrator

Aby przypisać bezpośrednio co najmniej jednego użytkownika do aplikacji, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Aby otworzyć **Dodaj przydziału** okienku, kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **Pełna nazwa** lub **adres e-mail** użytkownika planuje się przypisanie do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Zaznacz pole wyboru obok zdjęcia profilu użytkownika lub logo, aby dodać użytkownika do **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typu w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwania według nazwy lub adres e-mail** polu wyszukiwania, a następnie kliknij przycisk wyboru, aby dodać użytkownika do **wybrane** listy.

13. Po zakończeniu wybierania użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych użytkowników.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Jeśli Inicjowanie obsługi administracyjnej jest skonfigurowana i uruchomiona już aplikację, nowi użytkownicy powinna być przygotowana do aplikacji w ciągu 10 minut. Sprawdź **dzienników inspekcji** szczegółowe informacje.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Przypisz grupę bezpośrednio do aplikacji jako administrator

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

  * Jeśli nie ma aplikacji ma tutaj będą wyświetlane, użyj **filtru** kontroli nad **listę wszystkich aplikacji** i ustaw **Pokaż** opcji w celu **wszystkich Aplikacje.**

6.  Wybierz aplikacji, którą chcesz przypisać do użytkownika z listy.

7.  Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** z menu nawigacji po lewej stronie aplikacji.

8.  Aby otworzyć **Dodaj przydziału** okienku, kliknij przycisk **Dodaj** przycisk nad **użytkowników i grup** listy.

9.  Kliknij przycisk **użytkowników i grup** selektora z **Dodaj przydziału** okienka.

10. Wpisz w **grupy Pełna nazwa** planuje się przypisanie do grupy **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok profilu zdjęcie lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę**, typu w innym **grupy Pełna nazwa** do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania i Kliknij pole wyboru, aby dodać tę grupę do **wybrane** listy.

13. Po wybraniu grup kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup, które ma być przypisany do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektora w **Dodaj przydziału** okienko, aby wybrać rolę można przypisać do wybranych grup.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Jeśli Inicjowanie obsługi administracyjnej jest skonfigurowana i uruchomiona już aplikację, nowi użytkownicy, zawarte w obrębie grupy powinna być przygotowana do aplikacji w ciągu 10 minut. Sprawdź **dzienników inspekcji** szczegółowe informacje.

>[!IMPORTANT]
>Inicjowanie obsługi administracyjnej Nazwa grupy i szczegóły grupy, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Można włączyć lub wyłączyć tę funkcję przez włączenie lub wyłączenie **mapowania** dla obiektów grupy pokazano **inicjowania obsługi administracyjnej** kartę. 
>
>

Jeśli Inicjowanie obsługi grup jest włączone, należy przejrzeć mapowań atrybutów do upewnij się, że odpowiednie pole jest używany przez "Pasujących ID". Może to być nazwa wyświetlana lub e-mail aliasu, jak grupy i jej elementów członkowskich nie można zainicjować obsługi administracyjnej Jeśli zgodnej właściwości jest pusta lub nie wypełnione grupy w usłudze Azure AD.

## <a name="next-steps"></a>Kolejne kroki
[Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](active-directory-saas-app-provisioning.md)
